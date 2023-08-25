<div align="center">
  <img src="https://github.com/gonnafaraway/go-testing/assets/35832930/814ef67c-288d-4245-ba57-3e290e337c52">
  <h1>Collection of testing methodics for Golang</h1>
</div>

* Repository created to collect different methodics of writing and using tests with Golang language.

## Unit tests
* https://github.com/stretchr/testify
```go
func Foo() error {}

func TestFoo(t *testing.T) {
  err := Foo()
  assert.NoError(t, err)
})
}
```
* testing (std)
```go
func Foo() {}

func TestFoo(t *testing.T) {
  t.Run("Foo", func(t *testing.T) {
    Foo()
})
}
```
## Mocks(generally)
* https://github.com/uber-go/mock
```go
func TestFoo(t *testing.T) {
  ctrl := gomock.NewController(t)
  defer ctrl.Finish()

  m := NewMockFoo(ctrl)

  // Does not make any assertions. Executes the anonymous functions and returns
  // its result when Bar is invoked with 99.
  m.
    EXPECT().
    Bar(gomock.Eq(99)).
    DoAndReturn(func(_ int) int {
      time.Sleep(1*time.Second)
      return 101
    }).
    AnyTimes()

  // Does not make any assertions. Returns 103 when Bar is invoked with 101.
  m.
    EXPECT().
    Bar(gomock.Eq(101)).
    Return(103).
    AnyTimes()

  SUT(m)
}
``` 

## Integration tests
* use any contributed official driver for database to connect to real DEV db
* use fixtures to simulate database fillment
* use https://github.com/ariga/atlas to simulate database schema
* use https://github.com/pashagolub/pgxmock or any similar driver to simulate connection with fake client

## Fuzzing tests
```go
func FuzzReverse(f *testing.F) {
    testcases := []string{"Hello, world", " ", "!12345"}
    for _, tc := range testcases {
        f.Add(tc)  // Use f.Add to provide a seed corpus
    }
    f.Fuzz(func(t *testing.T, orig string) {
        rev := Reverse(orig)
        doubleRev := Reverse(rev)
        if orig != doubleRev {
            t.Errorf("Before: %q, after: %q", orig, doubleRev)
        }
        if utf8.ValidString(orig) && !utf8.ValidString(rev) {
            t.Errorf("Reverse produced invalid UTF-8 string %q", rev)
        }
    })
}
```
## Benchmark tests
```go
func BenchmarkRandInt(b *testing.B) {
    for i := 0; i < b.N; i++ {
        rand.Int()
    }
}
```
## Mocking
### Vault
* https://dev.to/lucassha/testing-vault-in-go-3pcg (for example)
```go
package main

import (
    "net"
    "testing"

    "github.com/hashicorp/vault/api"
    "github.com/hashicorp/vault/http"
    "github.com/hashicorp/vault/vault"
)

func TestVaultStuff(t *testing.T) {
    ln, client := createTestVault(t)
    defer ln.Close()

    // Pass the client to the code under test.
    myFunction(client)
}

func createTestVault(t *testing.T) (net.Listener, *api.Client) {
    t.Helper()

    // Create an in-memory, unsealed core (the "backend", if you will).
    core, keyShares, rootToken := vault.TestCoreUnsealed(t)
    _ = keyShares

    // Start an HTTP server for the core.
    ln, addr := http.TestServer(t, core)

    // Create a client that talks to the server, initially authenticating with
    // the root token.
    conf := api.DefaultConfig()
    conf.Address = addr

    client, err := api.NewClient(conf)
    if err != nil {
        t.Fatal(err)
    }
    client.SetToken(rootToken)

    // Setup required secrets, policies, etc.
    _, err = client.Logical().Write("secret/foo", map[string]interface{}{
        "secret": "bar",
    })
    if err != nil {
        t.Fatal(err)
    }

    return ln, client
}
```
### Redis
* https://github.com/alicebob/miniredis
* https://github.com/elliotchance/redismock
* github.com/go-redis/redis
### PostgreSQL
* in progress...
### K8s 
* in progress...
### Logging
* in progress...
### Timestamp
* in progress...
### API
* in progress...
### ...
