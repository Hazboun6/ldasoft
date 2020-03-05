# OpenMP
#CC = gcc-mp-5
CC = gcc
#CCFLAGS = -fopenmp

LIBS  = gsl gslcblas m fftw3 gbmcmc
LDFLAGS = -L../../gbmcmc/src
LDFLAGS += -L$(CONDA_PREFIX)/lib
LD_LIBRARY_PATH +=
#CCFLAGS += -g -ffast-math -Wall -O3 -ftree-vectorize -std=gnu99 -Werror
CCFLAGS += -g -ffast-math -Wall -O3 -ftree-vectorize -std=gnu99
LIBDIR += $(CONDA_PREFIX)/lib
# Compile src with git hash
GIT_VERSION := $(shell git describe --abbrev=4 --dirty --always --tags)
CCFLAGS += -DVERSION=\"$(GIT_VERSION)\"

# include gb_mcmc routines
CCFLAGS += -I../../gbmcmc/src/
CCFLAGS += -I$(CONDA_PREFIX)/include
RPATH = $(CONDA_PREFIX)/lib
OBJS = arrays.o Subroutines.o

all: $(OBJS) Confusion_Fit Bright_Remove Galaxy OrbitFile Setup Fisher_Galaxy Full_Residual

arrays.o : arrays.c
	$(CC) $(CCFLAGS) -c arrays.c #$(RPATH:%=-Wl,-rpath,%)

Subroutines.o : Subroutines.c Detector.h
	$(CC) $(CCFLAGS) -c Subroutines.c #$(RPATH:%=-Wl,-rpath,%)

Confusion_Fit : Confusion_Fit.c Subroutines.o arrays.o
	$(CC) $(CCFLAGS) -o Confusion_Fit Confusion_Fit.c $(OBJS) $(RPATH:%=-Wl,-rpath,%) $(INCDIR:%=-I%) $(LIBDIR:%=-L%) $(LDFLAGS) $(LIBS:%=-l%)

Full_Residual : Full_Residual.c Subroutines.o arrays.o
	$(CC) $(CCFLAGS) -o Full_Residual Full_Residual.c $(OBJS) $(RPATH:%=-Wl,-rpath,%) $(INCDIR:%=-I%) $(LIBDIR:%=-L%) $(LDFLAGS) $(LIBS:%=-l%)

Bright_Remove : Bright_Remove.c Subroutines.o arrays.o
	$(CC) $(CCFLAGS) -o Bright_Remove Bright_Remove.c $(OBJS) $(RPATH:%=-Wl,-rpath,%) $(INCDIR:%=-I%) $(LIBDIR:%=-L%) $(LDFLAGS) $(LIBS:%=-l%)

Galaxy : Galaxy.c Subroutines.o arrays.o
	$(CC) $(CCFLAGS) -o Galaxy Galaxy.c $(OBJS) $(INCDIR:%=-I%) $(RPATH:%=-Wl,-rpath,%) $(LIBDIR:%=-L%) $(LDFLAGS) $(LIBS:%=-l%)

OrbitFile : OrbitFile.c Subroutines.o arrays.o
	$(CC) $(CCFLAGS) -o OrbitFile OrbitFile.c $(OBJS) $(INCDIR:%=-I%) $(RPATH:%=-Wl,-rpath,%) $(LIBDIR:%=-L%) $(LDFLAGS) $(LIBS:%=-l%)

Setup : Setup.c Detector.h
	$(CC) $(CCFLAGS) -o Setup Setup.c $(OBJS) $(INCDIR:%=-I%) $(RPATH:%=-Wl,-rpath,%) $(LIBDIR:%=-L%) $(LDFLAGS) $(LIBS:%=-l%)

Fisher_Galaxy : Fisher_Galaxy.c Subroutines.o arrays.o
	$(CC) $(CCFLAGS) -o Fisher_Galaxy Fisher_Galaxy.c $(OBJS) $(RPATH:%=-Wl,-rpath,%) $(INCDIR:%=-I%) $(LIBDIR:%=-L%) $(LDFLAGS) $(LIBS:%=-l%)


install : Confusion_Fit Bright_Remove Galaxy OrbitFile Setup Fisher_Galaxy Full_Residual
	install Confusion_Fit Bright_Remove Galaxy OrbitFile Setup Fisher_Galaxy Full_Residual $(CONDA_PREFIX)/bin/

clean:
	rm -rf *.dSYM/ *.o Confusion_Fit Bright_Remove Galaxy OrbitFile Setup Fisher_Galaxy Full_Residual
