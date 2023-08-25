<div align="center">
  <img src="https://github.com/gonnafaraway/go-testing/assets/35832930/814ef67c-288d-4245-ba57-3e290e337c52">
  <h1>Collection of testing methodics for Golang</h1>
</div>

* Repository created to collect different methodics of writing and using tests with Golang language.

## Unit tests
* https://github.com/stretchr/testify
```
func Foo() error {}

func TestFoo(t *testing.T) {
  err := Foo()
  assert.NoError(t, err)
})
}
```
* testing (std)
```
func Foo() {}

func TestFoo(t *testing.T) {
  t.Run("Foo", func(t *testing.T) {
    Foo()
})
}
```

  

## Mocks(generally)
* in progress...

## Integration tests
* in progress...

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
