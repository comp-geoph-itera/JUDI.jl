# Seismic Preconditioners

JUDI provides a selected number of preconditioners known to be beneficial to FWI and RTM. We welcome additional preconditionners from the community. Additionnaly, any JOLI operator can be used as a preconditiner in conbination with JUDI operator thanks to the fundamental interface between JUDI and JOLI.

```@contents
Pages = ["precinditioners.md"]
```

## Model domain preconditioners

Model space preconditioners acts on model size arrays such as the velocity model or the FWI/RTM gradient. These preconditioners are indepenedent of the number of sources and therefore should not be indexed.

### Water column muting (top mute)

Create a linear operator for a 2D model topmute, i.e. for muting the water column:

```@docs
TopMute
```

**Usage:**

```julia
# Forward
m_mute = Mr*vec(m)

# Adjoint
m_mute = Mr'*vec(m)
```

As `Mr` is self adjoint, `Mr` is equal to `Mr'`.

**legacy:**

The legacy constructor `judiTopmute(n, wb, taperwidth)` is still available to construct a muting operator with user specified muting depth.


### Model depth scaling

Create a 2D model depth scaling. This preconditionenr is the most simple form of inverse Hessain approximation compensating for illumination in the subsurface. We also describe below a more accurate diagonal approximation of the Hessian with the illlumination operator. Additionnaly, as a simple diagonal approximation, this operator is invertible and can be inverted with the standard julia `inv` function.

```@docs
DepthScaling
```

**Usage:**

```julia
# Forward
m_mute = Mr*vec(m)

# Adjoint
m_mute = Mr'*vec(m)
```

### Illumination


The illumination computed the energy of the wavefield along time for each grid point. This provides a first order diagonal approximation of the Hessian of FWI/LSRTM helping the ocnvergence and quality of an update.

```@docs
judiIllumination
```

**Usage:**

```julia
# Forward
m_mute = I*vec(m)

# Adjoint
m_mute = I'*vec(m)
```

## Data preconditioners

These preconditioners are design to act on the shot records (data). These preconditioners are indexable by source number so that working with a subset of shot is trivial to implement.


### Data topmute

Create a data topmute for the data based on the source and receiver geometry (i.e based on the offsets between each souurce-receiver pair). THis operator allows two modes, `:reflection` for the standard "top-mute" direct wave muting and `:turning` for its opposite muting the reflection to compute gradients purely based on the turning waves. The muting operators uses a cosine taper at the mask limit to allow for a smooth transition.

```@docs
DataMute
```

### Band pass filter

While not purely a preconditioner, because this operator acts on the data and is traditionally used fro frequency continuation in FWI, we implemented this operator as a source indexable linear operator as well. Additionally, the filtering function is available as a standalone julia function for general usage

```@docs
FrequencyFilter
low_filter
```


