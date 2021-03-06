# A sample Makefile for building Google Test and using it in user
# tests.  Please tweak it to suit your environment and project.  You
# may want to move it to your project's root directory.
#
# SYNOPSIS:
#
#   make [all]  - makes everything.
#   make TARGET - makes the given target.
#   make clean  - removes all files generated by make.

# Please tweak the following variable definitions as needed by your
# project, except GTEST_HEADERS, which you can use in your own targets
# but shouldn't modify.

# Points to the root of Google Test, relative to where this file is.
# Remember to tweak this if you move this file.
GTEST_DIR = ../gtest-1.7.0
BOOST_DIR = ..
INCLUDES_DIR = ../includes
SRC_DIR = ../src

# Where to find user code.
USER_DIR = ..

# Flags passed to the preprocessor.
CPPFLAGS += -I$(GTEST_DIR)/include -I$(BOOST_DIR)

# Flags passed to the C++ compiler.
CXXFLAGS += -g -Wall -Wextra -fms-extensions -std=c++0x 

UNAME_S := $(shell uname -s)
ifeq ($(UNAME_S),Darwin)
    CCFLAGS += -stdlib=libc++ 
endif

# created to the list.
TESTS = cubecode_unittest

# All Google Test headers.  Usually you shouldn't change this
# definition.
GTEST_HEADERS = $(GTEST_DIR)/include/gtest/*.h \
                $(GTEST_DIR)/include/gtest/internal/*.h

# House-keeping build targets.

all : $(TESTS)

clean :
	rm -f $(TESTS) gtest.a gtest_main.a *.o

# Builds gtest.a and gtest_main.a.

# Usually you shouldn't tweak such internal variables, indicated by a
# trailing _.
GTEST_SRCS_ = $(GTEST_DIR)/src/*.cc $(GTEST_DIR)/src/*.h $(GTEST_HEADERS)

# For simplicity and to avoid depending on Google Test's
# implementation details, the dependencies specified below are
# conservative and not optimized.  This is fine as Google Test
# compiles fast and for ordinary users its source rarely changes.
gtest-all.o : $(GTEST_SRCS_)
	$(CXX) $(CPPFLAGS) -I$(GTEST_DIR) $(CXXFLAGS) -c \
            $(GTEST_DIR)/src/gtest-all.cc

gtest_main.o : $(GTEST_SRCS_)
	$(CXX) $(CPPFLAGS) -I$(GTEST_DIR) $(CXXFLAGS) -c \
            $(GTEST_DIR)/src/gtest_main.cc

gtest.a : gtest-all.o
	$(AR) $(ARFLAGS) $@ $^

gtest_main.a : gtest-all.o gtest_main.o
	$(AR) $(ARFLAGS) $@ $^

# Builds a sample test.  A test should link with either gtest.a or
# gtest_main.a, depending on whether it defines its own main()
# function.
hypercube.o: $(SRC_DIR)/hypercube.cc $(INCLUDES_DIR)/hypercube.h
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -c $(SRC_DIR)/hypercube.cc

unitcube.o: $(SRC_DIR)/unitcube.cc $(INCLUDES_DIR)/unitcube.h $(INCLUDES_DIR)/hypercube.h
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -c $(SRC_DIR)/unitcube.cc

cubecode.o: $(SRC_DIR)/cubecode.cc $(INCLUDES_DIR)/cubecode.h $(INCLUDES_DIR)/hypercube.h \
				    $(USER_DIR)/hypercube.h
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -c $(SRC_DIR)/cubecode.cc

cubecode_test.o : $(SRC_DIR)/test/cubecode_test.cc $(INCLUDES_DIR)/unitcube.h  \
                     $(INCLUDES_DIR)/cubecode.h $(INCLUDES_DIR)/hypercube.h $(GTEST_HEADERS)
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -c $(SRC_DIR)/test/cubecode_test.cc

hypercube_test.o : $(SRC_DIR)/test/hypercube_test.cc $(INCLUDES_DIR)/hypercube.h $(GTEST_HEADERS)
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -c $(USER_DIR)/test/hypercube_test.cc

unitcube_test.o : $(SRC_DIR)/test/unitcube_test.cc $(INCLUDES_DIR)/unitcube.h  \
                     $(INCLUDES_DIR)/hypercube.h $(GTEST_HEADERS)
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -c $(SRC_DIR)/test/unitcube_test.cc

cubecode_unittest : cubecode_test.o hypercube_test.o unitcube_test.o cubecode.o hypercube.o unitcube.o gtest_main.a
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) -lpthread $^ -o $@
