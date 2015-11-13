
#***********************************************************************
#* Description:                                                        *
#* ------------                                                        *
#*                                                                     *
#* Author: Martin Ettl                            *
#* Date  : 2011-12-07                                                  *
#*                                                                     *
#* LINUX-Makefile (gmake): Ubuntu linux                                *
#* Makefile to compile and build all necessary programms.              *
#*                                                                     *
#* Usage and basic features:                                           *
#*                                                                     *
#*  - make build  [creates the whole project]                          *
#*  - make clean  [removes all object and binary files]                *
#*  - make depend [detects header dependencies]                        *
#*  - make rpc    [generates files using idl2rpc]                      *
#*                                                                     *
#***********************************************************************

# ============================================================
# DEFINITIONS
# ============================================================

# path settings:
APPLICATION_NAME    = wxPendulum
SOURCE_DIR			= ../src
OBJECT_DIR			= ../obj
MAKE_DIR 			= ../make
BIN_DIR 			= ../bin
CONFIG_DIR			= ../config
RPC_DIR 			= ../rpcgen
ICON_DIR			= $(SOURCE_DIR)/image

#===============================================================================
# Determine the number cores of the machine, where the makefile is executed.
# This helps to set the -j option (from make), to speedup the build
# process of 'large' projects.  
NPROCS=1
OS:=$(shell uname -s)
ifeq ($(OS),Linux)
    NPROCS:=$(shell grep -c ^processor /proc/cpuinfo) 
endif
#===============================================================================

#===============================================================================
# path to the idl2rpc generated clients
IDL2RPC_CALL 				= idl2rpc.pl
#===============================================================================
# configure wxWidgets build
WXVERSION		= 3.1
WXCONFIG_CALL		= `wx-config --cxxflags --version=$(WXVERSION)`
WXCONFIG_LINKER_CALL	= `wx-config --version=$(WXVERSION) --libs adv,aui,core,base,html,richtext,xml`

# if no c++-compiler is specified, choose g++ by default
ifndef $(CPPCOMPILER)
         CPPCOMPILER:= g++
endif
CPP_WFLAGS	  = -Wsequence-point -Wno-old-style-cast -Wno-unknown-pragmas -Wno-deprecated \
		    -W -Wall -Wno-variadic-macros -Wextra -Wno-long-long -pedantic -Wunused  -Wformat=2 -ansi -Wno-write-strings  
CPPCOMPILER_FLAGS = $(WXCONFIG_CALL) $(CPP_WFLAGS)
CPPCOMPILER_CALL  = $(CPPCOMPILER) -c $(CPPCOMPILER_FLAGS) -o $@ $< 

# if no c-compiler is specified, choose gcc by default
ifndef $(CCOMPILER)
         CCOMPILER:= gcc
endif
CC_WFLAGS	= -W -Wall -Wunused -ansi -g -pipe 
CCOMPILER_FLAGS = $(WXCONFIG_CALL) $(CC_WFLAGS)
CCOMPILER_CALL  = $(CCOMPILER) -c $(CCOMPILER_FLAGS) -o $@ $< 

LINKER          = $(CPPCOMPILER)
LINKER_FLAGS    = $(WXCONFIG_LINKER_CALL)
LINKER_CALL     = $(LINKER) -o $@ $^ $(LINKER_FLAGS)

all: ../bin/$(APPLICATION_NAME)

# ============================================================
# LINKER-RULES
# ===========================================================
../bin/$(APPLICATION_NAME) : ../obj/wxPendulumDlg.o
	    
	$(LINKER_CALL)
# 
# ===========================================================
# SOURCES
# ===========================================================
SRCS = ../src/wxPendulumDlg.cpp

 
#CSRCS = 
#	 

# ===========================================================
# SUPPORTING-RULES
# ===========================================================


build: mrproper depend 
	# after generating the dependencies it is necessary to call make again
	make -j$(NPROCS) all 

touch:
	touch $(SOURCE_DIR)/*

rpc:
	# This option is not suppored in this project

clean:
	rm -f $(BIN_DIR)/wx*
	rm -f $(OBJECT_DIR)/*

mrproper:
	rm -f $(MAKE_DIR)/.Makefile.dep
	rm -f $(MAKE_DIR)/*.bck
	rm -f $(MAKE_DIR)/*.bak
	rm -f $(BIN_DIR)/*
	rm -f $(OBJECT_DIR)/*
	rm -f $(SOURCE_DIR)/*.bck

install: build
	install -d /usr/bin/
	install ../bin/$(APPLICATION_NAME) /usr/bin/

depend:
	@cat < /dev/null > makedep
	@cat < /dev/null > cmakedep
# First C++
	@for i in ${SRCS}; do \
		 (echo $$i; $(CPPCOMPILER) $(CPPCOMPILER_FLAGS) -MM $$i >> makedep); done
#       Write full path information before each *.o-dependency
	@echo 's/.*\.o:/..\/obj\/&/g' > eddep
	@sed -f eddep makedep > makedep1
	@mv makedep1 makedep
	@rm eddep
#       Add the rule to each dependency
	@echo 'a' > eddep
	@echo '	$$(CPPCOMPILER_CALL)' >> eddep
	@echo '.' >> eddep
	@echo 'g/.*\.o:/i\' >> eddep
	@echo '	$$(CPPCOMPILER_CALL)\' >> eddep
	@echo '.' >> eddep
	@echo '1,1d' >> eddep
	@echo 'w' >> eddep
	@ed - makedep < eddep
	@rm eddep
# Then C
	@for i in ${CSRCS}; do \
		 (echo $$i; $(CCOMPILER) $(CCOMPILER_FLAGS) -MM $$i >> cmakedep); done
#       Write full path information before each *.o-dependency
	@echo 's/.*\.o:/..\/obj\/&/g' > ceddep
	@sed -f ceddep cmakedep > cmakedep1
	@mv cmakedep1 cmakedep
	@rm ceddep
##       Add the rule to each dependency
	@echo 'a' > ceddep
	@echo '	$$(CCOMPILER_CALL)' >> ceddep
	@echo '.' >> ceddep
	@echo 'g/.*\.o:/i\' >> ceddep
	@echo '	$$(CCOMPILER_CALL)\' >> ceddep
	@echo '.' >> ceddep
	@echo '1,1d' >> ceddep
	@echo 'w' >> ceddep
	@ed - cmakedep < ceddep
	@rm ceddep
	@cat < cmakedep >> makedep
	@rm cmakedep
	@echo '# DO NOT EDIT THIS FILE HERE.' > .Makefile.dep
	@cat < makedep >> .Makefile.dep
	@echo '# DO NOT EDIT THIS FILE HERE.' >> .Makefile.dep
	@rm makedep

.Makefile.dep: 
	@cat < /dev/null > .Makefile.dep

include .Makefile.dep
