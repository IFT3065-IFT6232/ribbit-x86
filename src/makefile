# These settings will bootstrap the x86 AOT compiler using Gambit:
TARGET=x86
EXT=.s
LIB=max
RSC=rsc-x86


# To bootstrap the original AOT compiler with the C host uncomment these lines:
#TARGET=c
#EXT=.c
#LIB=max
#RSC=rsc


UNAME := $(shell uname)

ifeq ($(UNAME), Darwin)
# On macOS "gcc" refers to the LLVM-based C compiler that is part of XCode
# so it is important to install the GNU gcc using "brew".
# Another issue is that on M1 and M2 based machines it is necessary to install
# the x86_64 version of GNU gcc using the command:
#    arch -x86_64 /usr/local/bin/brew install gcc@10
# See: https://github.com/orgs/Homebrew/discussions/3526
GCC=gcc-10
else
# On other systems, just assume that "gcc" is the x86_64 version of GNU gcc.
GCC=gcc
endif


all: test1 test2

$(RSC).bundle.scm: shims.scm asm.scm x86.scm rts.scm $(RSC).scm
	sed -e 's/^#!/;;#!/g' $^ > $@

# rule to build $(RSC).bootstrap1.exe using $(RSC).scm run with Gambit
$(RSC).bootstrap1.exe: $(RSC).bundle.scm
	./rsc -t $(TARGET) -l $(LIB) -m -c "gsi -:d4 $(RSC).bundle.scm" -o $@$(EXT) $<
	$(GCC) -o $@ $@$(EXT)

# rule to build $(RSC).bootstrap2.exe using $(RSC).bootstrap1.exe
$(RSC).bootstrap2.exe: $(RSC).bootstrap1.exe $(RSC).bundle.scm
	./rsc -t $(TARGET) -l $(LIB) -m -c ./$< -o $@$(EXT) $(RSC).bundle.scm
	$(GCC) -o $@ $@$(EXT)

test1: $(RSC).bundle.scm
	./rsc -t $(TARGET) -l empty -m -c "gsi -:d4 $<" -o test1.exe$(EXT) test1.scm
	$(GCC) -o test1.exe test1.exe$(EXT)
	echo "ok" | ./test1.exe
	@echo "exit code = $$?"
#	rm test1.exe$(EXT) test1.exe

test2: $(RSC).bundle.scm
	./rsc -t $(TARGET) -l $(LIB) -m -c "gsi -:d4 $<" -o test2.exe$(EXT) test2.scm
	$(GCC) -o test2.exe test2.exe$(EXT)
	echo "(this '(is a list) containing 5 \"elements\")" | ./test2.exe
#	rm test2.exe$(EXT) test2.exe

test-bootstrap1: $(RSC).bootstrap1.exe
	./rsc -t $(TARGET) -l empty -m -c ./$< -o test1-bootstrap1.exe$(EXT) test1.scm
	$(GCC) -o test1-bootstrap1.exe test1-bootstrap1.exe$(EXT)
	echo "ok" | ./test1-bootstrap1.exe
#	rm test1-bootstrap1.exe$(EXT) test1-bootstrap1.exe
	./rsc -t $(TARGET) -l $(LIB) -m -c ./$< -o test2-bootstrap1.exe$(EXT) test2.scm
	$(GCC) -o test2-bootstrap1.exe test2-bootstrap1.exe$(EXT)
	echo "(this '(is a list) containing 5 \"elements\")" | ./test2-bootstrap1.exe
#	rm test2-bootstrap1$.exe(EXT) test2-bootstrap1.exe

test-bootstrap2: $(RSC).bootstrap2.exe
	./rsc -t $(TARGET) -l empty -m -c ./$< -o test1-bootstrap2.exe$(EXT) test1.scm
	$(GCC) -o test1-bootstrap2.exe test1-bootstrap2.exe$(EXT)
	echo "ok" | ./test1-bootstrap2.exe
#	rm test1-bootstrap2.exe$(EXT) test1-bootstrap2.exe
	./rsc -t $(TARGET) -l $(LIB) -m -c ./$< -o test2-bootstrap2.exe$(EXT) test2.scm
	$(GCC) -o test2-bootstrap2.exe test2-bootstrap2.exe$(EXT)
	echo "(this '(is a list) containing 5 \"elements\")" | ./test2-bootstrap2.exe
#	rm test2-bootstrap2$.exe(EXT) test2-bootstrap2.exe

repl-min: $(RSC).bundle.scm
	./rsc -t $(TARGET) -l min -m -c "gsi -:d4 $<" -o repl-min.exe$(EXT) repl-min.scm
	$(GCC) -o repl-min.exe repl-min.exe$(EXT)
	echo "(* 6 7)" | ./repl-min.exe
#	rm repl-min.exe$(EXT) repl-min.exe

repl-max: $(RSC).bundle.scm
	./rsc -t $(TARGET) -l max -m -c "gsi -:d4 $<" -o repl-max.exe$(EXT) repl-max.scm
	$(GCC) -o repl-max.exe repl-max.exe$(EXT)
	echo "(* 6 7)" | ./repl-max.exe
#	rm repl-max.exe$(EXT) repl-max.exe

clean:
	rm -f *.bundle.scm *.bootstrap1.* *.bootstrap2.* test*.exe
