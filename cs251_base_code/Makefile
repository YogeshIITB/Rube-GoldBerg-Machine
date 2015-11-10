.SUFFIXES: .cpp .hpp

# Programs
SHELL 	= bash
CC     	= g++ -pg
LD	= ld
RM 	= rm -rf
ECHO	= /bin/echo
CAT	= cat
PRINTF	= printf
SED	= sed
DOXYGEN = doxygen
GPROF = gprof
DOT = dot -Tpng
PYTHON = python
PDFLATEX = pdflatex
######################################
# Project Name (generate executable with this name)
TARGET = cs251_base

# Project Paths
PROJECT_ROOT=.
EXTERNAL_ROOT=$(PROJECT_ROOT)/external
SRCDIR = $(PROJECT_ROOT)/src
OBJDIR = $(PROJECT_ROOT)/obj
BINDIR = $(PROJECT_ROOT)/bin
DOCDIR = $(PROJECT_ROOT)/doc
PROFILE = $(PROJECT_ROOT)/project_profile
# Library Paths
BOX2D_ROOT=$(EXTERNAL_ROOT)
GLUI_ROOT=/usr
GL_ROOT=/usr/include/

#Libraries
LIBS = -lBox2D -lglui -lglut -lGLU -lGL

# Compiler and Linker flags
CPPFLAGS =-g -O3 -Wall -fno-strict-aliasing
CPPFLAGS+=-I $(BOX2D_ROOT)/include -I $(GLUI_ROOT)/include
LDFLAGS+=-L $(BOX2D_ROOT)/lib -L $(GLUI_ROOT)/lib

######################################

NO_COLOR=\e[0m
OK_COLOR=\e[1;32m
ERR_COLOR=\e[1;31m
WARN_COLOR=\e[1;33m
MESG_COLOR=\e[1;34m
FILE_COLOR=\e[1;37m

OK_STRING="[OK]"
ERR_STRING="[ERRORS]"
WARN_STRING="[WARNINGS]"
OK_FMT="${OK_COLOR}%30s\n${NO_COLOR}"
ERR_FMT="${ERR_COLOR}%30s\n${NO_COLOR}"
WARN_FMT="${WARN_COLOR}%30s\n${NO_COLOR}"
######################################

SRCS := $(wildcard $(SRCDIR)/*.cpp)
INCS := $(wildcard $(SRCDIR)/*.hpp)

OBJS := $(SRCS:$(SRCDIR)/%.cpp=$(OBJDIR)/%.o)


.PHONY: all setup doc clean distclean release report codeDoc profile

all: setup $(BINDIR)/$(TARGET)

codeDoc:
	@$(ECHO) -n "Generating Doxygen Documentation Of Group-23...  "
	@$(RM) -rf doc/html
	@$(DOXYGEN) $(DOCDIR)/Doxyfile 2 > /dev/null 2>&1
	@$(ECHO) "Done"

report:reportp
	@cd ./report/finalreport ;\
	$(PDFLATEX) -interaction=nonstopmode main.tex  > /dev/null 2>&1 ;\
	$(RM) ./report/finalreport/main.log > /dev/null 2>&1 ;\
	$(RM) ./report/finalreport/main.aux > /dev/null 2>&1 ;\
	$(RM) ./report/finalreport/main.out > /dev/null 2>&1 ;\
	$(ECHO) -n "Report Generated"

reportp:
	@cd ./report/presentation ;\
	$(PDFLATEX) -interaction=nonstopmode main.tex  > /dev/null 2>&1 ;\
	$(RM) ./report/presentation/main.log > /dev/null 2>&1 ;\
	$(RM) ./report/presentation/main.aux > /dev/null 2>&1 ;\
	$(RM) ./report/presentation/main.out > /dev/null 2>&1 ;\
	$(RM) ./report/presentation/main.nav > /dev/null 2>&1 ;\
	$(RM) ./report/presentation/main.anm > /dev/null 2>&1 ;\
	$(RM) ./report/presentation/main.toc > /dev/null 2>&1 ;\
	$(RM) ./report/presentation/main.snm > /dev/null 2>&1 ;\


profile:
	@if [ ! -f $(PROJECT_ROOT)/gmon.out ] ; then \
		$(ECHO) "Gmon.out not present.. Please run executable once through the terminal in project root" ;\
    fi
	@if [ -f $(PROJECT_ROOT)/gmon.out ] ; then \
		gprof $(PROJECT_ROOT)/bin/cs251_base $(PROJECT_ROOT)/gmon.out > $(PROFDIR)/Analysis.txt ;\
		python gprof2dot.py $(PROFDIR)/Analysis.txt > $(PROFDIR)/Analysis.dot ;\
		dot -Tpng $(PROFDIR)/Analysis.dot  -o $(PROFDIR)/Callgraph.png ;\
		$(ECHO) "Done" ;\
	fi


release: report setup $(BINDIR)/$(TARGET) codeDoc profile


setup:
	@$(ECHO) "Setting up compilation..."
	@mkdir -p obj
	@mkdir -p bin
	@mkdir -p project_profile

$(BINDIR)/$(TARGET): $(OBJS)
	@$(PRINTF) "$(MESG_COLOR)Building executable:$(NO_COLOR) $(FILE_COLOR) %16s$(NO_COLOR)" "$(notdir $@)"
	@$(CC) -O3 -o $@ $(LDFLAGS) $(OBJS) $(LIBS) 2> temp.log || touch temp.err
	@if test -e temp.err; \
	then $(PRINTF) $(ERR_FMT) $(ERR_STRING) && $(CAT) temp.log; \
	elif test -s temp.log; \
	then $(PRINTF) $(WARN_FMT) $(WARN_STRING) && $(CAT) temp.log; \
	else $(PRINTF) $(OK_FMT) $(OK_STRING); \
	fi;
	@$(RM) -f temp.log temp.err

-include -include $(OBJS:.o=.d)

$(OBJS): $(OBJDIR)/%.o : $(SRCDIR)/%.cpp
	@$(PRINTF) "$(MESG_COLOR)Compiling: $(NO_COLOR) $(FILE_COLOR) %25s$(NO_COLOR)" "$(notdir $<)"
	@$(CC) -O3 $(CPPFLAGS) -c $< -o $@ -MD 2> temp.log || touch temp.err
	@if test -e temp.err; \
	then $(PRINTF) $(ERR_FMT) $(ERR_STRING) && $(CAT) temp.log; \
	elif test -s temp.log; \
	then $(PRINTF) $(WARN_FMT) $(WARN_STRING) && $(CAT) temp.log; \
	else printf "${OK_COLOR}%30s\n${NO_COLOR}" "[OK]"; \
	fi;
	@$(RM) -f temp.log temp.err


clean:
	@$(ECHO) -n "Cleaning up..."
	@$(RM) $(OBJDIR)/* $(SRCDIR)/*~ $(PROFILE)/Analysis.dot $(PROJECT_ROOT)/gmon.out
	@$(ECHO) "Done"
distclean: clean
	@$(RM) $(BINDIR) $(OBJDIR) $(DOCDIR)/html $(PROFILE) $(DOCDIR)/doxygen_sqlite3.db
	@$(RM) ./report/presentation/main.pdf
	@$(RM) ./report/finalreport/main.pdf
