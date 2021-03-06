CC     = gcc -std=c99
CFLAGS = -pedantic -Wall -Wextra

.PHONY: all clean

VEC     = chacha20    hchacha20  xchacha20     aead_ietf poly1305 \
          blake2b     sha512     argon2i                          \
          edDSA       edDSA_pk   ed_25519      ed_25519_check     \
          x25519      x25519_pk  key_exchange                     \
          monokex_xk1 monokex_x
VEC2    = $(patsubst %, %.all.vec, $(VEC))
HEADERS = $(patsubst %, %.h.vec  , $(VEC))
VECTORS = ../vectors.h

all: $(VECTORS)

clean:
	rm -f *.out *.vec *.o
	rm -f $(VECTORS)

%.vec: %.out
	./$< > $@

%.o: %.c ../utils.h ../ed25519-donna/ed25519.h
	$(CC) $(CFLAGS) -c $<     \
            -I ..                 \
            -I ../ed25519-donna   \
            -I ../../src          \
            -I ../../src/optional \
            $$(pkg-config --cflags libsodium)

%.out: %.o ed25519.o
	$(CC) $(CFLAGS) -o $@ $^ \
            $$(pkg-config --libs libsodium)

ed25519.o: ../ed25519-donna/ed25519.c  $(wildcard ../ed25519-donna/*.h)
	$(CC) $(CFLAGS) -c $<                 \
            -I ../../src                      \
            -I ../../src/optional             \
            $$(pkg-config --cflags libsodium) \
            -DED25519_CUSTOMHASH              \
            -DED25519_TEST                    \
            -DED25519_NO_INLINE_ASM           \
            -DED25519_FORCE_32BIT

vector_to_header.out: ../vector_to_header.c
	$(CC) $(CFLAGS) $< -o $@

chacha20.all.vec      : chacha20.vec    ../vectors/chacha20
poly1305.all.vec      : poly1305.vec    ../vectors/poly1305
x25519.all.vec        : x25519.vec      ../vectors/x25519
x25519_pk.all.vec     : x25519_pk.vec
hchacha20.all.vec     : hchacha20.vec
xchacha20.all.vec     : xchacha20.vec
aead_ietf.all.vec     : aead_ietf.vec
blake2b.all.vec       : blake2b.vec
sha512.all.vec        : sha512.vec
argon2i.all.vec       : argon2i.vec     ../vectors/argon2i
edDSA.all.vec         : edDSA.vec
edDSA_pk.all.vec      : edDSA_pk.vec
ed_25519.all.vec      : ed_25519.vec
ed_25519_check.all.vec:                 ../vectors/ed_25519_check
key_exchange.all.vec  :                 ../vectors/key_exchange
monokex_xk1.all.vec   : monokex_xk1.vec
monokex_x.all.vec     : monokex_x.vec
$(VEC2):
	mkdir -p $(@D)
	cat $^ > $@

%.h.vec: %.all.vec vector_to_header.out
	./vector_to_header.out  $(patsubst %.all.vec,%,$<) < $< > $@

 $(VECTORS): $(HEADERS)
	cat $^ > $@
