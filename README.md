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
* https://github.com/go-redis/redis
### PostgreSQL
* https://github.com/pashagolub/pgxmock
### K8s 
* https://github.com/gardener/gardener/blob/master/pkg/mock/client-go/kubernetes/mocks.go
### Logging
* use assert.NotNil to check that logs generated correctly
### API
```go
package main

import (
   "net/http"
   "net/http/httptest"
   "testing"

   "github.com/gorilla/mux"
)

func Test_bookmarkIndex(t *testing.T) {
   r := mux.NewRouter()
   r.Handle("/v1/bookmark", bookmarkIndex())
   ts := httptest.NewServer(r)
   defer ts.Close()
   res, err := http.Get(ts.URL + "/v1/bookmark")
   if err != nil {
      t.Errorf("Expected nil, received %s", err.Error())
   }
   if res.StatusCode != http.StatusOK {
      t.Errorf("Expected %d, received %d", http.StatusOK, res.StatusCode)
   }
}

func Test_bookmarkFind(t *testing.T) {
   r := mux.NewRouter()
   r.Handle("/v1/bookmark/{id}", bookmarkFind())
   ts := httptest.NewServer(r)
   defer ts.Close()
   t.Run("not found", func(t *testing.T) {
      res, err := http.Get(ts.URL + "/v1/bookmark/1")
      if err != nil {
         t.Errorf("Expected nil, received %s", err.Error())
      }
      if res.StatusCode != http.StatusNotFound {
         t.Errorf("Expected %d, received %d", http.StatusNotFound, res.StatusCode)
      }
   })
   t.Run("found", func(t *testing.T) {
      res, err := http.Get(ts.URL + "/v1/bookmark/2")
      if err != nil {
         t.Errorf("Expected nil, received %s", err.Error())
      }
      if res.StatusCode != http.StatusOK {
         t.Errorf("Expected %d, received %d", http.StatusOK, res.StatusCode)
      }
   })
}
```
### Pubsub
* https://cloud.google.com/go/pubsub/pstest
