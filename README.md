# DeferredFutures

[![Build Status](https://travis-ci.org/invenia/DeferredFutures.jl.svg?branch=master)](https://travis-ci.org/invenia/DeferredFutures.jl)
[![Build status](https://ci.appveyor.com/api/projects/status/5sp5i4ewkfgw4cum/branch/master?svg=true)](https://ci.appveyor.com/project/iamed2/deferredfutures-jl/branch/master)
[![codecov](https://codecov.io/gh/invenia/DeferredFutures.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/invenia/DeferredFutures.jl)

A `DeferredFuture` is like a regular Julia `Future`, but is initialized when `put!` is called on it.
This means that the data in the `DeferredFuture` lives with the process the data was created on.
The process the `DeferredFuture` itself lives on never needs to fetch the data to its process.
This is useful when there is a lightweight controller process which handles scheduling work on and transferring data between multiple machines.

## Usage

Use a `DeferredFuture` as you would a `Future`.
```julia
julia> DeferredFuture()
DeferredFutures.DeferredFuture(RemoteChannel{Channel{Any}}(1,1,1))

julia> DeferredFuture(3)
DeferredFutures.DeferredFuture(RemoteChannel{Channel{Any}}(3,1,2))
```

You can also use a `DeferredChannel` as you would a `RemoteChannel`.
```julia
julia> DeferredChannel(()->Channel{Int}(10), 4)
DeferredFutures.DeferredChannel(RemoteChannel{Channel{Any}}(4,1,3),#1)

julia> DeferredChannel(4)
DeferredFutures.DeferredChannel(RemoteChannel{Channel{Any}}(4,1,4),DeferredFutures.#2)

julia> DeferredChannel(4, 128; content=Int)
DeferredFutures.DeferredChannel(RemoteChannel{Channel{Any}}(4,1,5),DeferredFutures.#2)
```
Note that `DeferredChannel()` will create a `RemoteChannel` with `RemoteChannel(()->Channel{Any}(1), myid())` by default.

Furthermore, `@defer` can be used when creating a `Future` or `RemoteChannel` to create their deferred counterparts.
```julia
julia> @defer Future()
DeferredFutures.DeferredFuture(RemoteChannel{Channel{Any}}(1,1,6))

julia> @defer RemoteChannel(()->Channel{Int}(10))
DeferredFutures.DeferredChannel(RemoteChannel{Channel{Any}}(1,1,7),#3)
```

Note that `DeferredFuture(n)` does not control where the data lives, only where the `RemoteChannel` which refers to the data lives.

## License

DeferredFutures.jl is provided under the [MIT "Expat" License](LICENSE.md).
