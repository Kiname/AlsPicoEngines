#makefile to compile Rodent IV on Linux
# PREFIX=/usr/local
PREFIX=/opt/picochess/engines/rodent4

#define the directory for the executable file
BINDIR = $(PREFIX)/bin

#define the directory for the data files
# DATADIRBOOKS = $(PREFIX)/share/rodentIV-books
DATADIRBOOKS = $(PREFIX)/books
# DATADIRPERSONALITIES = $(PREFIX)/share/rodentIV-persons
DATADIRPERSONALITIES = $(PREFIX)/personalities

# Define the CPP compiler
CXX = g++-7.2.0

# Define features
# Other options are -DBOOKGEN -DNO_THREADS
CFG = -DNDEBUG -DUSEGEN -DUSE_THREADS -DBOOKSPATH=$(DATADIRBOOKS) -DPERSONALITIESPATH=$(DATADIRPERSONALITIES)
CFG1 = -DUSEGEN -DUSE_THREADS -DBOOKSPATH=$(DATADIRBOOKS) -DPERSONALITIESPATH=$(DATADIRPERSONALITIES)

# Define compiler flags
CXXFLAGS = -g -std=c++14 -Wall -Wno-unknown-pragmas -O3 -fno-rtti -pthread $(CFG)
CXX1FLAGS = -g -std=c++14 -Wall -Wno-unknown-pragmas -pthread $(CFG1)

# Define linker options
LDFLAGS = -s -Wl,--no-as-needed,--gc-sections
LD1FLAGS = -lm

# Command line switches
lto = yes
native = no

# Compile with Link Time Optimisation
ifeq ($(lto),yes)
	CXXFLAGS += -flto -latomic
	LDFLAGS += -flto
endif

# Optimise for local machines
ifeq ($(native),yes)
	CXXFLAGS += -march=native
	LDFLAGS += -march=native
endif

# Define outpout name and settings file
EXENAME= rodentIV
CONFIGFILE = basic.ini

.PHONY: clean install update remove help

default: build

build:
	@echo "Type make help for additional options"
	$(CXX) $(CXXFLAGS) $(LDFLAGS) -o $(EXENAME) src/*.cpp
	@echo "SHOW_OPTIONS" > $(CONFIGFILE)

build-popcnt:
	@echo "Type make help for additional options"
	$(CXX) $(CXXFLAGS) -msse3 -mpopcnt $(LDFLAGS) -o $(EXENAME) src/*.cpp
	@echo "SHOW_OPTIONS" > $(CONFIGFILE)

clang-profiled:
	@echo "Type make help for additional options"
#	$(CXX) $(LDFLAGS) $(CXXFLAGS) -fprofile-instr-generate -o $(EXENAME) src/*.cpp
	$(CXX) $(LDFLAGS) $(CXXFLAGS) -fprofile-generate -o $(EXENAME) src/*.cpp
	@echo bench | ./rodentIV > /dev/null
	@llvm-profdata merge -output=default.profdata default.profraw
	$(CXX) $(CXXFLAGS) -fprofile-instr-use=default.profdata $(LDFLAGS) -o $(EXENAME) src/*.cpp
	@echo "SHOW_OPTIONS" > $(CONFIGFILE)
	@rm -f *.profdata *.profraw

clang-popcnt-profiled:
	@echo "Type make help for additional options"
	$(CXX) $(CXXFLAGS) -msse3 -mpopcnt -fprofile-instr-generate $(LDFLAGS) -o $(EXENAME) src/*.cpp
	@echo bench | ./rodentIV > /dev/null
	@llvm-profdata merge -output=default.profdata default.profraw
	$(CXX) $(CXXFLAGS) -msse3 -mpopcnt -fprofile-instr-use=default.profdata $(LDFLAGS) -o $(EXENAME) src/*.cpp
	@echo "SHOW_OPTIONS" > $(CONFIGFILE)
	@rm -f *.profdata *.profraw

gcc-profiled:
	@echo "Type make help for additional options"
	$(CXX) $(CXXFLAGS) -fprofile-generate $(LDFLAGS) -o $(EXENAME) src/*.cpp
	@echo bench | ./rodentIV > /dev/null
	$(CXX) $(CXXFLAGS) -fprofile-use $(LDFLAGS) -o $(EXENAME) src/*.cpp
	@echo "SHOW_OPTIONS" > $(CONFIGFILE)
	@rm -f *.gcda

gcc-popcnt-profiled:
	@echo "Type make help for additional options"
	$(CXX) $(CXXFLAGS) -msse3 -mpopcnt -fprofile-generate $(LDFLAGS) -o $(EXENAME) src/*.cpp
	@echo bench | ./rodentIV > /dev/null
	$(CXX) $(CXXFLAGS) -msse3 -mpopcnt -fprofile-use $(LDFLAGS) -o $(EXENAME) src/*.cpp
	@echo "SHOW_OPTIONS" > $(CONFIGFILE)
	@rm -f *.gcda

build-static:
	@echo "Type make help for additional options"
	$(CXX) $(CXXFLAGS) $(LDFLAGS) -static -o $(EXENAME) src/*.cpp
	@echo "SHOW_OPTIONS" > $(CONFIGFILE)

build-debug:
	@echo "Type make help for additional options"
	$(CXX) $(CXX1FLAGS) $(LD1FLAGS) -o $(EXENAME) src/*.cpp
	@echo "SHOW_OPTIONS" > $(CONFIGFILE)

gcc-lcov:
	@echo "Type make help for additional options"
	$(CXX) $(CXXFLAGS) -fprofile-generate -ftest-coverage $(LDFLAGS) -o $(EXENAME) src/*.cpp
	@echo bench | ./rodentIV > /dev/null
	@lcov --capture --directory . --no-external --gcov-tool gcov --output-file coverage.info > /dev/null
	@genhtml coverage.info --output-directory coveragedir > /dev/null
	@rm -f rodentIV *.gcda *.gcno coverage.info

bookgen:
	@echo "Type make help for additional options"
	$(CXX) $(CXXFLAGS) -DBOOKGEN $(LDFLAGS) -o $(EXENAME)-bookgen src/*.cpp
	@echo "SHOW_OPTIONS" > $(CONFIGFILE)
	@./$(EXENAME)-bookgen
	@rm -f $(EXENAME)-bookgen

clean:
	rm -rf $(EXENAME) $(EXENAME).exe book_gen.h basic.ini *.profdata *.profraw *.gcda *.gcno coverage.info coveragedir

install:
	mkdir -p $(BINDIR)
	mkdir -p $(DATADIRBOOKS)
	mkdir -p $(DATADIRPERSONALITIES)
	cp $(EXENAME) $(BINDIR)
	cp $(CONFIGFILE) $(DATADIRPERSONALITIES)
	#find ../books/ -name '*.bin' -type f -exec cp '{}' $(DATADIRBOOKS) \;
	#find ../personalities/ -name '*.ini' -type f -exec cp '{}' $(DATADIRPERSONALITIES) \;
	cp -r ../books/* $(DATADIRBOOKS)
	cp -r ../personalities/* $(DATADIRPERSONALITIES)
	chmod 755 $(BINDIR)/$(EXENAME)
	chmod -R 644 $(DATADIRBOOKS)/*
	find $(DATADIRBOOKS) -type d -exec chmod 755 {} \;
	chmod -R 644 $(DATADIRPERSONALITIES)/*
	find $(DATADIRPERSONALITIES) -type d -exec chmod 755 {} \;

# This just performs a copy of rodentIV engine
update:
	cp $(EXENAME) $(BINDIR)

remove:
	rm -f $(BINDIR)/$(EXENAME)
	rm -rf $(DATADIRBOOKS)
	rm -rf $(DATADIRPERSONALITIES)

help:
	@echo ""
	@echo "To compile RodentIV type:"
	@echo ""
	@echo "make build			> Build Rodent IV"
	@echo "make build-popcnt		> Build with fast popcnt"
	@echo "make clang-profiled		> Build with pgo optimisations (clang++ required)"
	@echo "make clang-popcnt-profiled	> Build with pgo+popcnt optimisations (clang++ required)"
	@echo "make gcc-profiled		> Build with pgo optimisations (g++ required)"
	@echo "make gcc-popcnt-profiled	> Build with pgo+popcnt optimisations (g++ required)"
	@echo "make build-static		> Build a static binary"
	@echo "make build-debug		> Build a debug version"
	@echo "make gcc-lcov			> Code coverage with lcov (g++ required)"
	@echo "make bookgen			> Build internal book"
	@echo "make clean			> Clean up"
	@echo "make install			> Install RodentIV (root privileges required)"
	@echo "make update			> Update RodentIV engine (root privileges required)"
	@echo "make remove			> Remove RodentIV from your system (root privileges required)"
