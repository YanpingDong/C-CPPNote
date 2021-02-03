CFLAGS += -g -c -Wall -Werror -O2  -o
CPPFLAGS += -I. -I../include
LDFLAGS += -L../libs 
LIBS += -lmodel 

SRC_FILES = $(wildcard *.cpp) 
SRC_OBJ = $(SRC_FILES:.cpp=.o)   
SRC_BIN = sample4  

all : $(SRC_BIN)


$(SRC_BIN) : $(SRC_OBJ)
	$(CXX) -o $@ $^ $(LDFLAGS) $(LIBS)
	mv $@ ../build
	
# 可以省略通过隐含规则推导
%.o:%.cpp
	g++ $(CPPFLAGS) $(CFLAGS) $@ $^
	
clean:
	$(RM) $(SRC_OBJ) $(SRC_BIN) 
