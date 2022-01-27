# lwcrypto
NIST Lightweight Cryptography finalists

## Usage
```go
package main

import (
	"crypto/rand"

	"github.com/pedroalbanese/lwcrypto/ascon"
)

func main() {
	// Keys must be KeySize bytes long. Anything else is an
	// error.
	key := make([]byte, ascon.KeySize)
	if _, err := rand.Read(key); err != nil {
		// rand.Read failing is almost always catastrophic.
		panic(err)
	}

	// Nonces must be NonceSize bytes long. Anything else is an
	// error.
	nonce := make([]byte, ascon.NonceSize)
	if _, err := rand.Read(nonce); err != nil {
		// rand.Read failing is almost always catastrophic.
		panic(err)
	}

	aead, err := ascon.New128(key)
	if err != nil {
		// New128 (and New128a) should only return an error if
		// the key is not KeySize bytes long.
		panic(err)
	}

	// Plaintext is encrypted and authenticated.
	plaintext := []byte("example plaintext")

	// Additional data is authenticated alongside the plaintext,
	// but not included in the ciphertext.
	additionalData := []byte("example additional authenticated data")

	// Encrypt and authenticate |plaintext| and authenticate
	// |additionalData|.
	ciphertext := aead.Seal(nil, nonce, plaintext, additionalData)

	// Decrypt and authentiate |ciphertext| and authenticate
	// |additionalData|.
	plaintext, err = aead.Open(nil, nonce, ciphertext, additionalData)
	if err != nil {
		// Authentication failed. Either the ciphertext or
		// additionalData (or both) were invalid for the 
		// (key, nonce) pair.
		[...]
	}
}
```

### BSD 3-Clause License
Copyright (c) 2021, Eric Lagergren All rights reserved.
