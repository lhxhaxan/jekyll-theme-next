---
title: One of the way to search what package is installed in julia(version ≥ 0.7.0)
date: 2018-09-01 23：00：00
description: Sometimes we need to konw a package is installed or not. Than we do something after in the code.'Pkg.installed()'can tell us about this, but it's not intact. There is a way to this with PKG.jl TOML.jl
categories:
 - JuliaLanguage
tags:[julia, package, toml]
---

 Sometimes we need to konw a package is installed or not. Than we do something after in the code.`Pkg.installed()`can tell us about this, but it's not intact.
 There is a way to do this with PKG.jl TOML.jl in julia(version ≥ 0.7.0)


## Check TOML.jl is installed or not
First we want to check that is TOML.jl installed.
```julia
(v1.0) pkg> status
```
When installed, you can see it in the `REPL` Like this:
```julia
[191fdcea] TOML v0.4.0
```
Now, you must make sure the the version greater than or equa `v0.4.0`. If the version less than taht, the package will make a error when `using`:

```julia
julia> using TOML
[ Info: Precompiling TOML [037cace4-c66a-5006-a6b7-c26ba1b2f83e]
ERROR: LoadError: syntax: extra token "ParserState" after end of expression
Stacktrace:
 [1] include at .\boot.jl:317 [inlined]
 [2] include_relative(::Module, ::String) at .\loading.jl:1038
 [3] include(::Module, ::String) at .\sysimg.jl:29
 [4] top-level scope at none:2
 [5] eval at .\boot.jl:319 [inlined]
 [6] eval(::Expr) at .\client.jl:389
 [7] top-level scope at .\none:3
in expression starting at C:\Users\haxan\.julia\packages\TOML\FjcHF\src\TOML.jl:18
ERROR: Failed to precompile TOML [037cace4-c66a-5006-a6b7-c26ba1b2f83e] to C:\Users\haxan\.julia\compiled\v1.0\TOML\ZlZJk.ji.
Stacktrace:
 [1] error(::String) at .\error.jl:33
 [2] macro expansion at .\logging.jl:313 [inlined]
 [3] compilecache(::Base.PkgId, ::String) at .\loading.jl:1184
 [4] _require(::Base.PkgId) at .\logging.jl:311
 [5] require(::Base.PkgId) at .\loading.jl:852
 [6] macro expansion at .\logging.jl:311 [inlined]
 [7] require(::Module, ::Symbol) at .\loading.jl:834
 ```

You can remove it like this:
```julia
(v1.0) pkg> rm TOML; gc
```

## Install the newer TOML.jl
The `TOML.jl` poject is [here](https://github.com/wildart/TOML.jl).
And you can install it like this:
```julia
(v1.0) pkg> add https://github.com/wildart/TOML.jl.git
```
Success:
```julia
  Cloning git-repo `https://github.com/wildart/TOML.jl.git`
  Updating git-repo `https://github.com/wildart/TOML.jl.git`
 Resolving package versions...
  Updating `C:\Users\haxan\.julia\environments\v1.0\Project.toml`
  [191fdcea] + TOML v0.4.0 #master (https://github.com/wildart/TOML.jl.git)
  Updating `C:\Users\haxan\.julia\environments\v1.0\Manifest.toml`
  [191fdcea] + TOML v0.4.0 #master (https://github.com/wildart/TOML.jl.git)
```
### About Manifest.toml
>Manifest file: a file in the root directory of a project, named `Manifest.toml` describing a complete dependency graph and exact versions of each package and library used by a project.

We can find the path in `REPL` use `Pkg.envdir()` (when use this function to check, you need to `using Pkg` first:
linux：
```julia
julia> Pkg.envdir()
"/home/haxan/.julia/environments"
```
windows：
```julia
julia> Pkg.envdir()
"C:\\Users\\haxan\\.julia\\environments"
```
But it is not intact. There are different folders for your installed Julia version.(`julia v1.0.0`=>`v1.0`)

Now, we find the `Manifest.toml` and we can see the installed package information in this file:
```toml
[[ASTInterpreter2]]
deps = ["DebuggerFramework"]
git-tree-sha1 = "8df3d36e0286777d226f4fd4956a432b73425186"
uuid = "e6d88f4b-b52a-544c-a8d3-7a4f12cb39c3"
version = "0.1.1"

[[Atom]]
deps = ["ASTInterpreter2", "Base64", "CodeTools", "DocSeeker", "Hiccup", "JSON", "Juno", "LNR", "Lazy", "Logging", "MacroTools", "Markdown", "Media", "Profile", "REPL", "Reexport", "Sockets", "Test", "TreeViews"]
git-tree-sha1 = "7a2778f893005f374cd119416dead281c4c78687"
uuid = "c52e3926-4ff0-5f6e-af25-54175e0327b1"
version = "0.7.5"

[[Base64]]
uuid = "2a0f44e3-6c83-55bd-87e4-b1978d98bd5f"

[[CodeTools]]
deps = ["LNR", "Lazy", "Markdown", "Pkg", "Test", "Tokenize"]
git-tree-sha1 = "13c8d66f30716972c5c9abb24784eaa62cced171"
uuid = "53a63b46-67e4-5edd-8c66-0af0544a99b9"
version = "0.6.1"

...
```
## Do it by code

First `using` package:

```julia
using Pkg;
using TOML;
```

As mentioned above, we need to extract the package environments path:

```julia
Pkg.envdir()
```
And we need version of `Julia`(constant `VERSION`)
```julia
"/v$(VERSION.major).$(VERSION.minor)/Manifest.toml"
```
Now we get the full path:
```julia
Pkg.envdir()*"/v$(VERSION.major).$(VERSION.minor)/Manifest.toml"
```
For work with julia, we need to prase it to `Dict` by `TMOL.parsefile`:
```julia
manifest = TOML.parsefile(Pkg.envdir()*"/v$(VERSION.major).$(VERSION.minor)/Manifest.toml")
```
output in `REPL`:
```julia
Dict{AbstractString,Any} with 50 entries:
  "Pkg"               => Dict{AbstractString,Any}[Dict("deps"=>["Dates", "LibGit2", "Markdown", "Printf", "REPL", "Random", "SHA", "UUIDs"],"…
  "Juno"              => Dict{AbstractString,Any}[Dict("deps"=>["Base64", "Logging", "Media", "Profile", "Test"],"git-tree-sha1"=>"f2d5537197…
  "Lazy"              => Dict{AbstractString,Any}[Dict("deps"=>["Compat", "MacroTools", "Test"],"git-tree-sha1"=>"1c2c5566f0eeaaad6979c156562…
  "TreeViews"         => Dict{AbstractString,Any}[Dict("deps"=>["Test"],"git-tree-sha1"=>"8d0d7a3fe2f30d6a7f833a5f19f7c7a5b396eae6","uuid"=>"…
  "Compat"            => Dict{AbstractString,Any}[Dict("deps"=>["Base64", "Dates", "DelimitedFiles", "Distributed", "InteractiveUtils", "LibG…
  "Base64"            => Dict{AbstractString,Any}[Dict("uuid"=>"2a0f44e3-6c83-55bd-87e4-b1978d98bd5f")]
  "LNR"               => Dict{AbstractString,Any}[Dict("deps"=>["Compat", "Lazy", "Test"],"git-tree-sha1"=>"bae0010daaba5f122cefcbee9a97f5d82…
  "CodeTools"         => Dict{AbstractString,Any}[Dict("deps"=>["LNR", "Lazy", "Markdown", "Pkg", "Test", "Tokenize"],"git-tree-sha1"=>"13c8d…
  "Sockets"           => Dict{AbstractString,Any}[Dict("uuid"=>"6462fe0b-24de-5631-8697-dd941f90decc")]
  "Markdown"          => Dict{AbstractString,Any}[Dict("deps"=>["Base64"],"uuid"=>"d6f4376e-aef5-505a-96c1-9c027394607a")]
  "Requires"          => Dict{AbstractString,Any}[Dict("deps"=>["Test"],"git-tree-sha1"=>"f6fbf4ba64d295e146e49e021207993b6b48c7d1","uuid"=>"…
  "UUIDs"             => Dict{AbstractString,Any}[Dict("deps"=>["Random"],"uuid"=>"cf7118a7-6976-5b1a-9a39-7adc72f591a4")]
  "SHA"               => Dict{AbstractString,Any}[Dict("uuid"=>"ea8e919c-243c-51af-8825-aaa63cd721ce")]
  "LinearAlgebra"     => Dict{AbstractString,Any}[Dict("deps"=>["Libdl"],"uuid"=>"37e2e46d-f89d-539d-b4ee-838fcccc9c8e")]
  "Media"             => Dict{AbstractString,Any}[Dict("deps"=>["MacroTools", "Test"],"git-tree-sha1"=>"9f390271c9a43dcbe908a10b5b9632cf58cba…
  "LibGit2"           => Dict{AbstractString,Any}[Dict("uuid"=>"76f85450-5226-5b5a-8eaa-529ad045b433")]
  "DebuggerFramework" => Dict{AbstractString,Any}[Dict("git-tree-sha1"=>"0288f278a5f58c28c67ad1cf55dce950069709b7","uuid"=>"67417a49-6d77-5db…
  "StringDistances"   => Dict{AbstractString,Any}[Dict("deps"=>["Distances", "IterTools", "Test"],"git-tree-sha1"=>"41fddd579b75e0cd0d1bbdb2d…
  "Dates"             => Dict{AbstractString,Any}[Dict("deps"=>["Printf"],"uuid"=>"ade2ca70-3891-5945-98fb-dc099432e06a")]
  "Printf"            => Dict{AbstractString,Any}[Dict("deps"=>["Unicode"],"uuid"=>"de0858da-6303-5e67-8744-51eddeeeb8d7")]
  "Test"              => Dict{AbstractString,Any}[Dict("deps"=>["Distributed", "InteractiveUtils", "Logging", "Random"],"uuid"=>"8dfed614-e22…
  "Random"            => Dict{AbstractString,Any}[Dict("deps"=>["Serialization"],"uuid"=>"9a3f8284-a2c9-5f02-9a11-845980a1fd5c")]
  "Atom"              => Dict{AbstractString,Any}[Dict("deps"=>["ASTInterpreter2", "Base64", "CodeTools", "DocSeeker", "Hiccup", "JSON", "Jun…
  "Libdl"             => Dict{AbstractString,Any}[Dict("uuid"=>"8f399da3-3557-5675-b5ff-fb832c97cbdb")]
  "Conda"             => Dict{AbstractString,Any}[Dict("deps"=>["Compat", "JSON", "VersionParsing"],"git-tree-sha1"=>"a47f9a2c7b80095e6a93553…
  ⋮                   => ⋮
```
From now we can check the version of any package is installed:
```julia
julia> manifest["PyCall"][]["version"]
"1.18.3"
```

## Be more intelligent

```julia
using Pkg;
using TOML;

manifest = TOML.parsefile(Pkg.envdir()*"/v$(VERSION.major).$(VERSION.minor)/Manifest.toml");

function pkgversion(pkgname::String)
 if haskey(manifest, pkgname) == false
  error("There is not pakckage \"$pkgname\" installed");
 elseif haskey(manifest[pkgname][], "version") == false
  error("No version record in pakckage \"$pkgname\"")
 else
  return manifest[pkgname][]["version"];
 end
end
 ```

 Test it:
```julia
julia> pkgversion("PyCall")
"1.18.3"

julia> pkgversion("PyCalls")
ERROR: There is not pakckage "PyCalls" installed
Stacktrace:
 [1] error(::String) at .\error.jl:33
 [2] pkgversion(::String) at .\REPL[28]:3
 [3] top-level scope at none:0
 ```

### At last

In fact, Julia provides [a way](https://docs.julialang.org/en/stable/stdlib/Pkg/#Adding-dependencies-to-the-project-1) to inline the required packages.
It add packages to the project’s `Project.toml` file. When `Pkg.add`your project it also install those
packages.By the way [`Pkg.instantiate()`](https://docs.julialang.org/en/stable/stdlib/Pkg/#Pkg.instantiate)can install packages which mark in `Project.toml`.
