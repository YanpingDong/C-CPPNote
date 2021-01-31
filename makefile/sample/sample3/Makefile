sample3:	sample3.o model.o
	g++  sample3.o model.o -o sample3

sample3.o:   sample3.cpp model.h
	g++ -c sample3.cpp -o sample3.o

model.o:    model.cpp model.h
	g++ -c model.cpp -o model.o

clean:
	rm -f sample3 model.o sample3.o

