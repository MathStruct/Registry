# MathStruct registry

The Julia package registry of the [MathStruct](https://github.com/MathStruct) projects and the
packages they grew out of. It is used next to the General registry, not in place of it. Every
dependency that is not in this list comes from General.

## Using it

Once per Julia installation (Julia 1.12 or later):

```julia
using Pkg
Pkg.Registry.add("General")   # only if it is not there yet
Pkg.Registry.add(RegistrySpec(url = "https://github.com/MathStruct/Registry.git"))
```

or in the Pkg REPL: `registry add https://github.com/MathStruct/Registry.git`. After that the
packages install by name, and the packages here that depend on each other are resolved
automatically:

```julia
Pkg.add("Lenticulum")
Pkg.add("VariationalDiffusion")
```

`Pkg.Registry.update()` (or `registry up`) fetches the versions registered after that.

## Packages

### Lenticulum: factor graphs whose factors are learned relations

All of these come from [MathStruct/Lenticulum.jl](https://github.com/MathStruct/Lenticulum.jl),
with one package for each directory under `lib/`. Documentation:
[mathstruct.org/Lenticulum.jl](https://mathstruct.org/Lenticulum.jl/api/dev/).

| package | what it gives you |
|---|---|
| `LenticulumCore` | what a factor is: channels, polarities, beliefs, energies |
| `Mycelium` | inference: graphs, messages, schedules, plates, cluster nodes, the free energy |
| `MyceliumLearning` | learning: free-energy gradients, expectation maximisation, the optimiser step |
| `Lenticulum` | the factors: linear-Gaussian, nonlinear, constraints, switching and regime factors, likelihoods |
| `VariationalDiffusion` | a diffusion model as a relation: implicit inference, sampling, RED-Diff, ProxDM |
| `ImplicitLayers` | DEQs and neural ODEs as factors |
| `AdversarialFactors` | implicit generative models: generators and density ratios |
| `AlgebraicFactors` | finite toric models: exact discrete messages and free energy |

The last three have not yet been reviewed as closely as the core packages.

### Electrical impedance tomography

| package | repository | what it gives you |
|---|---|---|
| `ModularEIT` | [DanielBoigk/ModularEIT.jl](https://github.com/DanielBoigk/ModularEIT.jl) | EIT built from exchangeable parts: discretisations, electrode models, solvers, adjoint gradients, regularisers |
| `ModularEITFerrite` | same, `lib/ModularEITFerrite` | the Ferrite.jl backend |
| `ModularEITGridap` | same, `lib/ModularEITGridap` | the Gridap.jl backend |
| `FerriteAdaptiveTriangular` | [DanielBoigk/FerriteAdaptiveTriangular.jl](https://github.com/DanielBoigk/FerriteAdaptiveTriangular.jl) | conforming adaptive refinement of triangle meshes for Ferrite.jl (newest vertex bisection) |
| `KrylovBlockCG` | [DanielBoigk/KrylovBlockCG.jl](https://github.com/DanielBoigk/KrylovBlockCG.jl) | block conjugate gradients for Krylov.jl, with an optional projection onto a null space |

### Finite fields

| package | repository | what it gives you |
|---|---|---|
| `FiniteFieldArithmetic` | [MathStruct/FiniteFields](https://github.com/MathStruct/FiniteFields), `julia/FiniteFieldArithmetic.jl` | finite-field arithmetic, characteristic 2 first: the nimber fields GF(2) ⊂ GF(4) ⊂ … ⊂ GF(2^128) |

## Versions

Most of these packages are research prototypes with pre-release versions (`0.0.1-dev`). A
registered version is a fixed snapshot of its repository. It falls behind the development branch
until the next version is registered. For the newest code, use a clone instead:

```julia
Pkg.develop(path = "/path/to/Lenticulum.jl/lib/Mycelium.jl")
```

### Renamed and removed

| was | now | why |
|---|---|---|
| `Adversarial` (UUID `5b7e1c94-…`) | `AdversarialFactors` | General has an unrelated package with the same name |

For an environment that still lists the old name, run `Pkg.rm("Adversarial"); Pkg.add("AdversarialFactors")`.
In code, `using Adversarial` becomes `using AdversarialFactors`.

## Registering a version

This is done by the maintainers, with [LocalRegistry](https://github.com/GunnarFarneback/LocalRegistry.jl).

1. Give the package a version number that this registry does not have yet for it (`version`
   in its `Project.toml`). A number cannot be registered a second time with different content.
2. Commit the package and push it. The registry records the git tree of the package directory,
   and Pkg can install that tree only once it is on GitHub.
3. Register from a clean checkout at that commit:

   ```julia
   using Pkg
   Pkg.activate(temp = true); Pkg.add("LocalRegistry")
   using LocalRegistry
   register("path/to/Lenticulum.jl/lib/Mycelium.jl";
            registry = "path/to/Registry")   # a clone of this repository, or "MathStruct" if it is installed
   ```

   `register` writes the entry, commits it in the registry clone and pushes it. With
   `push = false` it stops after the commit.
4. Check `repo` in the new `Package.toml`. It is copied from the package's git remote, so a clone
   made over SSH writes `git@github.com:…`. The registry uses the `https://github.com/…` form,
   because that works without a GitHub key.

A package that depends on another package of this registry has to be registered after that
package.

The `[sources]` entries of a package tie the packages of one repository together during
development, as paths. An installation from the registry ignores them: there the dependencies
are resolved by version, through the registry.

### Renaming a package

A package is renamed by registering it again as a new package with a new UUID:

1. Rename it in its repository: the directory, the module, and `Project.toml` with a new `uuid`.
2. In one registry commit, delete the old package directory and its line in `Registry.toml`.
3. Register the renamed package, and add a row to the table above.

Do this only while no registered package depends on the old name.
