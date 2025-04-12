obj-m += plat_driver.o

all: module userapp

module:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules

userapp: plat_app.c
	gcc -o plat_app plat_app.c -fopenmp -O3

clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
	rm -f plat_app *.ppm


