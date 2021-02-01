CFLAGS += -g -c -Wall -Werror -O2  -o
CPPFLAGS += -I. -I../../include

SRC_FILES = $(wildcard *.cpp) 
SRC_OBJ = $(SRC_FILES:.cpp=.o) 
SRC_LIB = libmodel.a  

all:  $(SRC_LIB) 

$(SRC_LIB): $(SRC_OBJ) 
	$(AR) rcs $@ $^  
	mv $@ ../../libs
# 可以省略通过隐含规则推导
%.o:%.cpp
	$(CXX) $(CPPFLAGS) $(CFLAGS) $@ $^
	
clean:
	$(RM) $(SRC_OBJ) $(SRC_LIB) 
