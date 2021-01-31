# g++ -o sample2 sample2.cpp model.cpp
sample2:	sample2.o model.o
	g++  sample2.o model.o -o sample2

sample2.o:   sample2.cpp model.h
	g++ -c sample2.cpp -o sample2.o

model.o:    model.cpp model.h
	g++ -c model.cpp -o model.o

clean:
	rm -f sample2 model.o sample2.o

