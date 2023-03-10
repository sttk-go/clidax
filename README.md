# [CliDax][repo-url] [![Go Reference][pkg-dev-img]][pkg-dev-url] [![CI Status][ci-img]][ci-url] [![MIT License][mit-img]][mit-url]

A dax of sabi to operate command line interface of Golang application.

- [Usage](#usage)
- [Supporting Go versions](#support-go-version)
- [License](#license)

<a name="usage"></a>
## Usage examples

### Parse CLI arguments without configurations

```
// os.Args[1:]  ==>  [--foo-bar=A -a --baz -bc=3 qux]
args, _ := Parse()
args.HasOpt("a")          // true
args.HasOpt("b")          // true
args.HasOpt("c")          // true
args.HasOpt("foo-bar")    // true
args.HasOpt("baz")        // true
args.OptParam("foo-bar")  // A
args.OptParams("foo-bar") // [A]
args.OptParam("c")        // 3
args.OptParams("c")       // [3]
args.CmdParams()          // [qux]
```

### Parse CLI arguments with configurations

```
osArgs := []string{"--foo-bar", "quz", "--baz", "1", "-z=2", "-X", "quux"}
optCfgs := []OptCfg{
  OptCfg{Name:"foo-bar"},
  OptCfg{Name:"baz", Aliases:[]string{"z"}, HasParam:true, IsArray:true},
  OptCfg{Name:"*"},
}

args, err := ParseWith(osArgs, optCfgs)
args.HasOpt("foo-bar")  // true
args.HasOpt("baz")      // true
args.HasOpt("X")        // true, due to "*" config
args.OptParam("baz")    // 1
args.OptParams("baz")   // [1 2]
args.CmdParams()        // [qux quux]
```

### Parse CLI arguments for an option store with struct tags

```
type MyOptions struct {
  FooBar bool     `opt:"foo-bar,f"`
  Baz    int      `opt:"baz,b=99"`
  Qux    string   `opt:"=XXX"`
  Quux   []string `opt:"quux=[A,B,C]"`
  Corge  []int
}
options := MyOptions{}

osArgs := []string{
  "--foo-bar", "c1", "-b", "12", "--Qux", "ABC", "c2",
  "--Corge", "20", "--Corge=21",
}

cmdParams, err := ParseFor(osArgs, &options)
cmdParams      // [c1 c2]
options.FooBar // true
options.Baz    // 12
options.Qux    // ABC
options.Quux   // [A B C]
options.Corge  // [20 21]
```

Or

```
optCfgs, err0 := MakeOptCfgsFor(&options)

args, err1 := ParseWith(osArgs, optCfgs)
args.CmdParams() // [c1 c2]
options.FooBar   // true
options.Baz      // 12
options.Qux      // ABC
options.Quux     // [A B C]
options.Corge    // [20 21]
```

<a name="support-go-versions"></a>
## Supporting Go versions

This library supports Go 1.18 or later.

### Actual test results for each Go version:

```
% gvm-fav
Now using version go1.18.10
go version go1.18.10 darwin/amd64
ok  	github.com/sttk-go/clidax/libarg	0.131s	coverage: 97.0% of statements

Now using version go1.19.5
go version go1.19.5 darwin/amd64
ok  	github.com/sttk-go/clidax/libarg	0.133s	coverage: 97.0% of statements

Now using version go1.20
go version go1.20 darwin/amd64
ok  	github.com/sttk-go/clidax/libarg	0.137s	coverage: 97.0% of statements

Back to go1.20
Now using version go1.20
%
```


<a name="license"></a>
## License

Copyright (C) 2023 Takayuki Sato

This program is free software under MIT License.<br>
See the file LICENSE in this distribution for more details.


[repo-url]: https://github.com/sttk-go/clidax
[pkg-dev-img]: https://pkg.go.dev/badge/github.com/sttk-go/clidax.svg
[pkg-dev-url]: https://pkg.go.dev/github.com/sttk-go/clidax
[ci-img]: https://github.com/sttk-go/clidax/actions/workflows/go.yml/badge.svg?branch=main
[ci-url]: https://github.com/sttk-go/clidax/actions
[mit-img]: https://img.shields.io/badge/license-MIT-green.svg
[mit-url]: https://opensource.org/licenses/MIT

