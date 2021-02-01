CFLAGS += -g -Wall -Werror -O2 
CPPFLAGS += -I. -I../include
LDFLAGS += -L../libs 
LIBS += -lmodel 

SRC_FILES = $(wildcard *.cpp) 
SRC_OBJ = $(SRC_FILES:.cpp=.o)   
SRC_BIN = sample4  

all : $(SRC_BIN)


$(SRC_BIN) : $(SRC_OBJ)
	g++ -o $@ $^ $(LDFLAGS) $(LIBS)
	mv $@ ../build

clean:
	$(RM) $(SRC_OBJ) $(SRC_BIN) 
