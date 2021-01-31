sample:   libmodel.a  sample.o
	g++ -o sample sample.o -L. -lmodel
    
sample.o: sample.cpp model.h
	g++ -c sample.cpp 

libmodel.a:    model.o
	ar cr libmodel.a model.o

model.o:	model.cpp model.h
	g++ -c model.cpp


clean:
	rm -f libmode.a sample

