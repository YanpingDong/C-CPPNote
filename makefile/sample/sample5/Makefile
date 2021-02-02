foo = test
all:
#不能写成 $(foo)这样实际检测的是test
ifdef foo
	@echo yes
else
	@echo  no
endif

ifeq ($(CC),gcc)
	@echo gcc
else
	@echo $(CC)
endif