# OpenMP
#CC = gcc-mp-5
#CC = clang-mp-9.0
CC = gcc
#CCFLAGS = -fopenmp

#LIBS  = gsl gslcblas m fftw3
LIBS  = gsl gslcblas m
CCFLAGS += -O3 -g -ffast-math -Wall -ftree-vectorize -std=gnu99 -Werror
LDFLAGS += -L$(CONDA_PREFIX)/lib
LIBDIR += $(CONDA_PREFIX)/lib
# Compile src with git hash
GIT_VERSION := $(shell git describe --abbrev=4 --dirty --always --tags)
CCFLAGS += -DVERSION=\"$(GIT_VERSION)\"
# CCFLAGS += -Wno-unused-variable
CCFLAGS += -I$(CONDA_PREFIX)/include
RPATH = $(CONDA_PREFIX)/lib


OBJS = LISA.o GalacticBinaryIO.o GalacticBinaryModel.o GalacticBinaryWaveform.o GalacticBinaryMath.o GalacticBinaryData.o GalacticBinaryPrior.o GalacticBinaryProposal.o GalacticBinaryFStatistic.o

all: gitversion.h $(OBJS) gb_mcmc gb_match gb_catalog gb_mcmc_chirpmass gb_mcmc_brans_dicke gb_residual libgbmcmc.a

gitversion.h: ../../.git/HEAD ../../.git/index
	echo "const char *gitversion = \"$(shell git rev-parse HEAD)\";" > $@

LISA.o : LISA.c LISA.h
	$(CC) $(CCFLAGS) -c LISA.c

GalacticBinaryIO.o : GalacticBinaryIO.c GalacticBinaryIO.h LISA.h
	$(CC) $(CCFLAGS) -c GalacticBinaryIO.c

GalacticBinaryMath.o : GalacticBinaryMath.c GalacticBinaryMath.h GalacticBinary.h
	$(CC) $(CCFLAGS) -c GalacticBinaryMath.c

GalacticBinaryPrior.o : GalacticBinaryPrior.c GalacticBinaryPrior.h GalacticBinary.h
	$(CC) $(CCFLAGS) -c GalacticBinaryPrior.c

GalacticBinaryData.o : GalacticBinaryData.c GalacticBinaryData.h GalacticBinary.h GalacticBinaryModel.o GalacticBinaryMath.o GalacticBinaryIO.o LISA.o
	 $(CC) $(CCFLAGS) -c GalacticBinaryData.c

GalacticBinaryWaveform.o : GalacticBinaryWaveform.c GalacticBinaryWaveform.h GalacticBinaryMath.o Constants.h LISA.o
	$(CC) $(CCFLAGS) -c GalacticBinaryWaveform.c

GalacticBinaryFStatistic.o : GalacticBinaryFStatistic.c GalacticBinaryWaveform.c GalacticBinary.h Constants.h LISA.o
	$(CC) $(CCFLAGS) -c GalacticBinaryFStatistic.c

GalacticBinaryModel.o : GalacticBinaryModel.c GalacticBinaryModel.h LISA.o GalacticBinary.h GalacticBinaryMath.o GalacticBinaryWaveform.o
	$(CC) $(CCFLAGS) -c GalacticBinaryModel.c

GalacticBinaryProposal.o : GalacticBinaryProposal.c GalacticBinary.h GalacticBinaryWaveform.o GalacticBinaryIO.o GalacticBinaryModel.o Constants.h
	$(CC) $(CCFLAGS) -c GalacticBinaryProposal.c

gb_mcmc : GalacticBinaryMCMC.c $(OBJS) GalacticBinary.h
	$(CC) $(CCFLAGS) -o gb_mcmc GalacticBinaryMCMC.c $(OBJS) $(RPATH:%=-Wl,-rpath,%) $(INCDIR:%=-I%) $(LIBDIR:%=-L%) $(LIBS:%=-l%)

gb_catalog : GalacticBinaryCatalog.c $(OBJS) GalacticBinary.h
	$(CC) $(CCFLAGS) -o gb_catalog GalacticBinaryCatalog.c $(OBJS) $(RPATH:%=-Wl,-rpath,%) $(INCDIR:%=-I%) $(LIBDIR:%=-L%) $(LIBS:%=-l%)

gb_residual : GalacticBinaryResidual.c $(OBJS) GalacticBinary.h
	$(CC) $(CCFLAGS) -o gb_residual GalacticBinaryResidual.c $(OBJS) $(RPATH:%=-Wl,-rpath,%) $(INCDIR:%=-I%) $(LIBDIR:%=-L%) $(LIBS:%=-l%)

gb_mcmc_chirpmass : gb_mcmc_chirpmass.c
	$(CC) $(CCFLAGS) -o gb_mcmc_chirpmass gb_mcmc_chirpmass.c  $(RPATH:%=-Wl,-rpath,%) $(INCDIR:%=-I%)  $(LIBDIR:%=-L%) $(LIBS:%=-l%)

gb_match : GalacticBinaryMatch.c $(OBJS) GalacticBinary.h
	$(CC) $(CCFLAGS) -o gb_match GalacticBinaryMatch.c $(OBJS) $(RPATH:%=-Wl,-rpath,%) $(INCDIR:%=-I%) $(LIBDIR:%=-L%) $(LIBS:%=-l%)

gb_mcmc_brans_dicke : gb_mcmc_brans_dicke.c
	$(CC) $(CCFLAGS) -o gb_mcmc_brans_dicke gb_mcmc_brans_dicke.c $(RPATH:%=-Wl,-rpath,%) $(INCDIR:%=-I%) $(LIBDIR:%=-L%) $(LIBS:%=-l%)

libgbmcmc.a : $(OBJS)
#	$(CC) -shared -o libgb.so $(OBJS) $(LIBS:%=-l%)
	ar rcs libgbmcmc.a $(OBJS) #$(RPATH:%=-Wl,-rpath,%)

install : gitversion.h gb_mcmc gb_match gb_catalog gb_mcmc_chirpmass gb_mcmc_brans_dicke gb_residual libgbmcmc.a
	install gb_mcmc gb_match gb_catalog gb_mcmc_chirpmass gb_mcmc_brans_dicke $(CONDA_PREFIX)/bin

clean:
	rm *.o *.a gb_mcmc gb_match gb_catalog gb.so gb_mcmc_chirpmass gb_mcmc_brans_dicke gb_residual gitversion.h
