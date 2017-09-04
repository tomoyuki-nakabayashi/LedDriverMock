SRC_DIR=./
GTEST_DIR=../googletest/googletest
GMOCK_DIR=../googletest/googlemock
OBJ_DIR=./obj
LIB_DIR=./lib

CXX = g++
CXXFLAGS = -g -Wall
TARGET = $(LIB_DIR)/libleddriver.a

default: $(TARGET)
.PHONY: default

$(TARGET): leddriver.o
	ar ruc $(TARGET) leddriver.o

leddriver.o: $(SRC_DIR)/LedDriver.c
	$(CXX) $(CXXFLAGS) -o $@ -c $<



TEST_SRCS = $(SRC_DIR)/LedDriverTest.cpp
TEST_TARGET = $(SRC_DIR)/LedDriver
TEST_OBJS  = $(addprefix $(OBJ_DIR)/, $(notdir $(TEST_SRCS:.cpp=.o)))
LIBS += -L$(LIB_DIR)
LIBS += -lleddriver

CPPFLAGS += -isystem $(GTEST_DIR)/include -isystem $(GMOCK_DIR)/include
CXXFLAGS = -g -Wall -Wextra -pthread

# All Google Test headers.  Usually you shouldn't change this
# definition.
GTEST_HEADERS = $(GTEST_DIR)/include/gtest/*.h \
                $(GTEST_DIR)/include/gtest/internal/*.h
GTEST_SRCS_ = $(GTEST_DIR)/src/*.cc $(GTEST_DIR)/src/*.h $(GTEST_HEADERS)

# All Google Mock headers. Note that all Google Test headers are
# included here too, as they are #included by Google Mock headers.
# Usually you shouldn't change this definition.	
GMOCK_HEADERS = $(GMOCK_DIR)/include/gmock/*.h \
                $(GMOCK_DIR)/include/gmock/internal/*.h \
                $(GTEST_HEADERS)
GMOCK_SRCS_ = $(GMOCK_DIR)/src/*.cc $(GMOCK_HEADERS)

# For simplicity and to avoid depending on Google Test's
# implementation details, the dependencies specified below are
# conservative and not optimized.  This is fine as Google Test
# compiles fast and for ordinary users its source rarely changes.

$(OBJ_DIR)/gtest-all.o : $(GTEST_SRCS_)
	$(CXX) $(CPPFLAGS) -I$(GTEST_DIR) $(CXXFLAGS) -c \
		    -o $@ $(GTEST_DIR)/src/gtest-all.cc

$(OBJ_DIR)/gmock-all.o : $(GMOCK_SRCS_)
	$(CXX) $(CPPFLAGS) -I$(GTEST_DIR) -I$(GMOCK_DIR) $(CXXFLAGS) \
            -c -o $@ $(GMOCK_DIR)/src/gmock-all.cc

$(OBJ_DIR)/gtest_main.o : $(GTEST_SRCS_)
	$(CXX) $(CPPFLAGS) -I$(GTEST_DIR) $(CXXFLAGS) -c \
		    -o $@ $(GTEST_DIR)/src/gtest_main.cc

$(OBJ_DIR)/gmock_main.o : $(GMOCK_SRCS_)
	$(CXX) $(CPPFLAGS) -I$(GTEST_DIR) -I$(GMOCK_DIR) $(CXXFLAGS) \
            -c -o $@ $(GMOCK_DIR)/src/gmock_main.cc

$(LIB_DIR)/gmock.a : $(OBJ_DIR)/gmock-all.o $(OBJ_DIR)/gtest-all.o
	$(AR) $(ARFLAGS) $@ $^

$(LIB_DIR)/gmock_main.a : $(OBJ_DIR)/gmock-all.o $(OBJ_DIR)/gtest-all.o $(OBJ_DIR)/gmock_main.o
	$(AR) $(ARFLAGS) $@ $^

$(LIB_DIR)/gtest.a : $(OBJ_DIR)/gtest-all.o
		$(AR) $(ARFLAGS) $@ $^

$(LIB_DIR)/gtest_main.a : $(OBJ_DIR)/gtest-all.o $(OBJ_DIR)/gtest_main.o
		$(AR) $(ARFLAGS) $@ $^

test: $(TEST_TARGET)
.PHONY: test

$(TEST_TARGET): $(TARGET) $(TEST_OBJS) $(LIB_DIR)/gtest_main.a $(LIB_DIR)/gmock_main.a
		$(CXX) $(LDFLAGS) -o $@ $(TEST_OBJS) \
		$(LIB_DIR)/gtest_main.a $(LIBS) -lpthread


$(OBJ_DIR)/%.o: LedDriverTest.cpp $(GTEST_HEADERS)
		@[ -d $(OBJ_DIR) ] || mkdir -p $(OBJ_DIR)
		$(CXX) $(CPPFLAGS) $(CXXFLAGS) $(INCS) -o $@ -c $<

clean:
		rm -f $(TARGET) $(TEST_TARGET) $(OBJS) $(TEST_OBJS)