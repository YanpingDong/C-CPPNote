CFLAGS += -g -Wall -Werror -O2 
CPPFLAGS += -I. -I../../include

SRC_FILES = $(wildcard *.cpp) 
SRC_OBJ = $(SRC_FILES:.cpp=.o) 
SRC_LIB = libmodel.a  

all:  $(SRC_LIB) 

$(SRC_LIB): $(SRC_OBJ) 
	$(AR) rcs $@ $^  
	mv $@ ../../libs

clean:
	$(RM) $(SRC_OBJ) $(SRC_LIB) 