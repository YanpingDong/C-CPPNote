sample:   libmode.so sample.o
	g++ sample.o -L. -lmodel -o sample
	
# step1 step2可以使用这一条命令代替
#libmode.so:	model.cpp model.h
#	g++ model.cpp model.h -fPIC -shared -o libmodel.so
# step 1
libmode.so:	model.o 
	g++ -shared -o libmodel.so model.o 
# step 2
model.o: model.cpp model.h
	g++ -fPIC -c model.cpp -o model.o

sample.o: sample.cpp model.h
	g++ -c sample.cpp -o sample.o

clean:
	rm -f libmode.so sample

