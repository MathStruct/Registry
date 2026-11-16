# MathStruct registry

The Julia package registry of the [MathStruct](https://github.com/MathStruct) projects. It is used
next to the General registry, not in place of it.

## Using it

Once per Julia installation:

```julia
using Pkg
Pkg.Registry.add("General")   # only if it is not there yet
Pkg.Registry.add(RegistrySpec(url = "https://github.com/MathStruct/Registry.git"))
```

or in the Pkg REPL: `registry add https://github.com/MathStruct/Registry.git`. After that the
packages install by name:

```julia
Pkg.add("Lenticulum")
```

## Packages

| package | repository | directory |
|---|---|---|
| `Lenticulum` | [MathStruct/Lenticulum.jl](https://github.com/MathStruct/Lenticulum.jl) | `lib/Lenticulum.jl` |
| `LenticulumCore` | same | `lib/LenticulumCore.jl` |
| `Mycelium` | same | `lib/Mycelium.jl` |
| `MyceliumLearning` | same | `lib/MyceliumLearning.jl` |
| `VariationalDiffusion` | same | `lib/VariationalDiffusion.jl` |
| `ImplicitLayers` | same | `lib/ImplicitLayers.jl` |
| `Adversarial` | same | `lib/Adversarial.jl` |
| `AlgebraicFactors` | same | `lib/AlgebraicFactors.jl` |
| `FerriteAdaptiveTriangular` | [DanielBoigk/FerriteAdaptiveTriangular.jl](https://github.com/DanielBoigk/FerriteAdaptiveTriangular.jl) | (root) |
| `KrylovBlockCG` | [DanielBoigk/KrylovBlockCG.jl](https://github.com/DanielBoigk/KrylovBlockCG.jl) | (root) |

`Adversarial` shares its name with an unrelated package in General. `Pkg.add("Adversarial")`
therefore asks which one is meant; to choose without the prompt:

```julia
Pkg.add(PackageSpec(name = "Adversarial", uuid = "5b7e1c94-8d3a-4f26-b0e1-7a9c25d4f318"))
```

## Registering a version

A version is a tree of a commit that is on GitHub, so push the package first. Each registered
version needs a version number of its own in the package's `Project.toml`: the same number cannot
be registered a second time with other content.

```julia
using LocalRegistry                       # Pkg.add("LocalRegistry") once
register("path/to/Lenticulum.jl/lib/Mycelium.jl"; registry = "MathStruct")
```

The package checkout has to be clean and at the commit to register. `register` writes the entry,
commits it in the registry and pushes; with `push = false` it stops after the commit. A package
that depends on another package of this registry is registered after it.

`[sources]` entries of a package (the paths that tie the packages of one repository together
during development) play no part in an installation from the registry: there the dependencies are
resolved by version, through the registry.
