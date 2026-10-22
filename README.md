<div align="center">

# AlgebrikAttackOnSmallPresent

**Build the polynomial system over GF(2) for an algebraic attack on a 2-round, 8-bit SMALL-PRESENT.**

![SageMath](https://img.shields.io/badge/SageMath-attack.sage-38E1FF?style=flat-square&labelColor=0A1428)
![Python](https://img.shields.io/badge/Python-3-38E1FF?style=flat-square&labelColor=0A1428&logo=python&logoColor=38E1FF)
![Topic](https://img.shields.io/badge/topic-algebraic%20cryptanalysis-38E1FF?style=flat-square&labelColor=0A1428)

</div>

---

## Overview

This repo generates the equations for a linear-algebraic attack on the SMALL-PRESENT block cipher
(original note: *"Implementasi pembuatan persamaan serangan linear algebrik attack pada algoritma small present"*,
which means "implementation of building the linear algebraic attack equations for the small PRESENT algorithm").

The script models a reduced, 8-bit version of PRESENT symbolically. The 24 key bits are unknowns in a polynomial ring over GF(2).
It pushes a known plaintext through the cipher and writes out the polynomial for every intermediate bit.
The final-state polynomials, set equal to a known ciphertext, form the system to be solved for the key.

## The cipher model

The block is 8 bits, i.e. two 4-bit S-boxes side by side. Each step is computed as a vector of 8 polynomials:

| Step | File | Operation |
| --- | --- | --- |
| `Rk` | `Rk.txt` | Plaintext XOR round key 1 (`k0..k7`) |
| `g1` | `g1.txt` | PRESENT S-box on both nibbles, written as ANF (`phiSbox1..4`) |
| `h1` | `h1.txt` | Bit permutation `Pbox = [0,2,4,6,1,3,5,7]` |
| `f2` | `f2.txt` | XOR round key 2 (`k19..k26`) |
| `g2` | `g2.txt` | S-box layer |
| `h2` | `h2.txt` | Bit permutation |
| `f3` | `f3.txt` | XOR round key 3 (`k38..k45`) giving the ciphertext polynomials |

The round keys are treated as independent variables, so no key schedule is modelled.
`phiSbox1..4` are the four coordinate functions of the standard PRESENT S-box `C56B90AD3EF84712`, with `x1` as the most significant bit.

After building `f3`, the script prints the number of terms in each ciphertext polynomial.
It then forms the equations against a hard-coded ciphertext `C`. A Gröbner-basis step (`ideal(...).groebner_basis()`, written to `grobner.txt`) is in the code but switched off (`inp = "n"`).

## Files

| Path | Description |
| --- | --- |
| `attack.sage` | The equation generator (SageMath source) |
| `attack.sage.py` | The same script after Sage preparsing (auto-generated) |
| `Rk.txt`, `g1.txt`, `h1.txt`, `f2.txt`, `g2.txt`, `h2.txt`, `f3.txt` | Polynomials for the default plaintext `10001010` (identical to `output/0/`) |
| `output/<n>/` | One folder per plaintext byte `n = 0..255` (name suffixed with the character, e.g. `97[a]`, when it is a letter or digit), each holding the same seven files |

## Requirements

- [SageMath](https://www.sagemath.org/) (uses `PolynomialRing(GF(2))` and `ideal`)

## Usage

```bash
sage attack.sage
```

Two modes are set at the top of `attack.sage`:

- `mode = 0` (default): one run with the fixed plaintext `PLaintext = [1,0,0,0,1,0,1,0]`, written to `output/0/`.
- `mode = 1`: loops over all 256 plaintext bytes and writes `output/<n>/` for each one.

The `output/` folder must exist, because the script creates only the per-plaintext subfolders.
To compute the Gröbner basis, set `inp = "y"` in the script.

---

<div align="center">

Part of [wondping0](https://github.com/wondping0)'s cryptography notebooks · [wondping0.github.io](https://wondping0.github.io)

</div>
