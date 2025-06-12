# tbxmanager index for Apple Silicon

This is an index to hold toolboxes compiled for MATLAB on Apple Silicon (ARM64).

**Original source**: [ETH Zurich MPT Toolbox Pool](http://people.ee.ethz.ch/~mpt/tbx/pool/)

## Quick Start

1. **Install tbxmanager** following the [official instructions](https://github.com/mpt/tbxmanager)

2. **Add this repository as a source**:

   ```matlab
   tbxmanager source add https://github.com/MarekWadinger/tbx-index-arm64/
   ```

3. **Install toolboxes**:

   ```matlab
   tbxmanager install glpkmex lcp cddmex hysdel fourier
   ```

## Toolboxes Included

| Toolbox | Version | Description | License |
|---------|---------|-------------|---------|
| **GLPKMEX** | 1.0.0 | MATLAB interface to GLPK linear programming solver | GPL v2 |
| **LCP** | 1.0.3 | Linear Complementarity Problem solver | GPL v2 |
| **CDDMEX** | 1.0.1 | MATLAB interface to CDD convex hull library | GPL v2 |
| **HYSDEL** | 2.0.6 | Hybrid Systems Description Language compiler | Proprietary |
| **Fourier** | 1.0.0 | Fourier-Motzkin elimination for linear inequalities | LGPL v2.1 |

## Compatibility

- **MATLAB**: R2023b or later with native Apple Silicon support
- **Architecture**: ARM64 (Apple Silicon M1/M2/M3)
- **Platform**: macOS Monterey 12.0 or later

## Compilation from Source

### Prerequisites

```bash
# Install Xcode Command Line Tools
xcode-select --install
# Without full Xcode installation, we need to manually introduce license acceptance
defaults write com.apple.dt.Xcode IDEXcodeVersionForAgreedToGMLicense 13.0

# Install Homebrew dependencies
brew install autoconf glpk@4

# For GLPK 4.x compatibility (required for GLPKMEX)
brew unlink glpk && brew install glpk@4.42
```

### Build Instructions

```bash
# Clone and navigate to source
git clone https://github.com/MarekWadinger/tbx-index-arm64.git
cd tbx-index-arm64/src

# Compile individual toolboxes
cd glpkmex && mex glpkcc.cpp -lglpk
cd ../lcp && mex lcp.c lcp_matrix.c lcp_sfun.c lumod_dense.c
cd ../cddmex && mex cddmex.c -lcdd
cd ../fourier && mex fourier.cc vertices.cc lp.cc
```

### Known Issues and Solutions

- **GLPK Version**: Modern GLPK 5.x is incompatible. Use GLPK 4.42 for GLPKMEX
- **Rosetta Compatibility**: HYSDEL uses Rosetta 2 wrapper for x86_64 binary
- **MEX Architecture**: All MEX files compiled for native ARM64 (`mexmaca64`)

## Testing

Verify installations with provided test scripts:

```matlab
% Test individual components
test_glpk;    % GLPKMEX functionality
test_lcp;     % LCP solver
test_cddmex;  % Convex hull operations
test_fourier; % Fourier elimination
```

## Directory Structure

```bash

tbx-index-arm64/
├── src/ # Source code for compilation
│ ├── glpkmex/ # GLPK MATLAB interface
│ ├── lcp/ # Linear complementarity solver
│ ├── cddmex/ # CDD library interface
│ ├── hysdel/ # HYSDEL compiler
│ └── fourier/ # Fourier elimination
├── lib/ # Compiled MEX files by platform
│ └── darwin-arm64/ # ARM64 binaries
├── matlab/ # MATLAB interface files
├── tests/ # Test scripts
└── docs/ # Documentation
```

## License Information

This package contains multiple components under different licenses:

### GPL v2 Components

- LCP solver (GPL v2)
- GLPKMEX (GPL v2)
- CDDMEX (GPL v2)

### LGPL v2.1 Components  

- Fourier elimination (LGPL v2.1)

### BSD Components

- lumod_dense.c (BSD 3-Clause, Stanford SOL)

### Overall License

Due to GPL components, this package is distributed under **GPL v2**.

## Contributing

When contributing compiled binaries:

1. Ensure ARM64 native compilation (`mexmaca64` extension)
2. Test on multiple MATLAB versions (R2023b+)
3. Include source code and build instructions
4. Verify license compatibility

## Troubleshooting

### Common Issues

**Permission Denied (HYSDEL)**:

```bash
chmod +x lib/darwin-arm64/hysdel
```

**MEX Compilation Errors**:

- Verify Xcode Command Line Tools: `xcode-select -p`
- Check MATLAB architecture: `computer('arch')`
- Ensure Homebrew libraries are ARM64: `file $(brew --prefix)/lib/libglpk.dylib`

**Path Issues**:

```matlab
% Add to MATLAB startup.m
addpath('/usr/local/bin');  % For Homebrew binaries
```

## References

- [tbxmanager documentation](https://github.com/mpt/tbxmanager)
- [MATLAB MEX compilation guide](https://www.mathworks.com/help/matlab/ref/mex.html)
- [Apple Silicon MATLAB compatibility](https://www.mathworks.com/support/requirements/apple-silicon.html)
