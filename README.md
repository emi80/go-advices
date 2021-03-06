# go-advices

- [ ] go fmt your code
  - make everyone happier
- [ ] do not omit `defer`
  - 200ns speedup is neglecatble in a most cases
- [ ] dump goroutines https://stackoverflow.com/a/27398062/433041
  ```go
    go func() {
      sigs := make(chan os.Signal, 1)
      signal.Notify(sigs, syscall.SIGQUIT)
      buf := make([]byte, 1<<20)
      for {
          <-sigs
          stacklen := runtime.Stack(buf, true)
          log.Printf("=== received SIGQUIT ===\n*** goroutine dump...\n%s\n*** end\n", buf[:stacklen])
      }
  }()
  ```
- [ ] multiple if statements can be collapsed into switch
- [ ] check interface implementation during compilation
  ```go
    var _ io.Reader = (*MyFastReader)(nil)
  ```
- [ ] use `chan struct{}` to pass signal
  - `chan bool` makes it less clear, btw `struct{}` is more optimal
- [ ] strip your binaries with this command `go build -ldflags="-s -w" ...`
- [ ] skip test deppending on architecture
  ```go
  if runtime.GOARM == "arm" {
    t.Skip("this doesn't work under ARM")
  }
  ```
- [ ] prefer `30 * time.Seconds` instead of `time.Duration(30) * time.Seconds`
- [ ] always wrap for-select idiom to a function
- [ ] implement `Stringer` interface for integers const values
- [ ] filtering without allocating
  ```go
    b := a[:0]
    for _, x := range a {
    	if f(x) {
		    b = append(b, x)
    	}
    }
  ```
- [ ] run `go format` on CI and compare diff
  - this will ensure that everything was generated and commited
- [ ] if a param of len is nil then it's zero
  - https://golang.org/pkg/builtin/#len
- [ ] anonymous structs are cool
  ```go
  var hits struct {
    sync.Mutex
    n int
  }
  hits.Lock()
  hits.n++
  hits.Unlock()
  ```
- [ ] `time.Time` has pointer field `time.Location` and this is bad go GC
  - it's relevant only for big number of `time.Time`, use timestamp instead
- [ ] always close http body aka `defer r.Body.Close()`
  - unless you need leaked goroutine
- [ ] `httputil.DumpRequest` is very useful thing
  - https://godoc.org/net/http/httputil#DumpRequest
- [ ] `go test -short` allows to reduce set of tests to be runned
  ```go
  func TestSomething(t *testing.T) {
    if testing.Short() {
      t.Skip("skipping test in short mode.")
    }
  }
  ```
- [ ] check if there are mistakes in code formatting `diff -u <(echo -n) <(gofmt -d .)`
- [ ] group `const` declarations by type and `var` by logic and/or type
- [ ] to run Travis-CI with the latest Go use `travis 1`
  - see more: https://github.com/travis-ci/travis-build/blob/master/public/version-aliases/go.json
- [ ] easy way to split test into different builds
  - use `// +build integration` and run them with `go test -v --tags integration .`
- [ ] quick replace `gofmt -w -l -r "panic(err) -> log.Error(err)" .`
