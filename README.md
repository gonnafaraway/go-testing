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
* in progress...

## Benchmark tests
* in progress...

## Mocking
### Vault
* in progress...
### Redis
* in progress...
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
