CFLAGS += -g -Wall -Werror -O2 
CPPFLAGS += -I. -I./model
LDFLAGS += -L./libs 
LIBS += -lmodel 

SRC_FILES = $(wildcard *.cpp) 
SRC_OBJ = $(SRC_FILES:.cpp=.o)   
SRC_BIN = sample3  

all :  submode $(SRC_BIN)

submode:
	$(MAKE) -C model

$(SRC_BIN) : $(SRC_OBJ)
	g++ -o $@ $^ $(LDFLAGS) $(LIBS)


clean:
	$(RM) $(SRC_OBJ) $(SRC_BIN) 
