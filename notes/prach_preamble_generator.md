# Notes

## Attention Note

Any other notes written by my own hands can be found at [index](/notes/_index.html).

---

## LTE PRACH Preamble Generation

### Date: 24.11.2025

---

This note describes the PRACH preamble generation procedure used in LTE Release 10.

---

## Root Zadoff–Chu Sequence Generation

A PRACH preamble begins with a Zadoff–Chu root sequence of length $N_\text{ZC} = 839$ for all PRACH formats except Format 4. The root index $u$ is configured by the network.

The ZC sequence is defined as:

$$
x_u(n) = e^{-j \frac{\pi u n (n+1)}{N_\text{ZC}}}, \qquad n = 0,\ldots,N_\text{ZC}-1
$$

---

## Cyclic Shift to Generate Individual Preambles

Each preamble corresponds to one cyclic shift of the root sequence. The cyclically shifted sequence is:

$$
x_{u,v}(n) = x_u\left((n + v\,N_\text{CS}) \bmod N_\text{ZC}\right)
$$

Where:

- $v = 0,1,2,\ldots$ is the preamble index
- $N_\text{CS}$ is the cyclic shift value
- $x_{u,v}(n)$ retains ZC length (839)

---

## Frequency-Domain Mapping

The PRACH occupies 6 resource blocks → 72 subcarriers. Procedure:

1. Compute DFT of the cyclically shifted ZC sequence:
   $$
   X_{u,v}(k) = \text{DFT}\{x_{u,v}(n)\}
   $$

2. Sample/extract 72 tones and map them into the PRACH subcarrier region based on the PRACH configuration index.

3. Set all remaining (non-PRACH) subcarriers to zero, forming a frequency-domain vector of length $N_\text{IFFT}$.

---

## IFFT to Time Domain

The mapped frequency-domain sequence is transformed into the time domain:

$$
s_{u,v}(m) = \text{IFFT}\{X_{u,v}(k)\}, \qquad m = 0,\ldots,N_\text{IFFT}-1
$$

---

## Cyclic Prefix (CP) Insertion

Each PRACH format defines a cyclic prefix length $N_\text{CP}$. The CP is inserted by copying the last $N_\text{CP}$ samples of the IFFT output:

$$
s_{u,v}^\text{CP}(m) =
\begin{cases}
s_{u,v}(m + N_\text{IFFT} - N_\text{CP}), & m = 0,\ldots,N_\text{CP}-1 \\
s_{u,v}(m - N_\text{CP}), & m = N_\text{CP},\ldots,N_\text{CP}+N_\text{IFFT}-1
\end{cases}
$$

This forms a single CP-extended OFDM symbol.

---

## Power Ramping and Frequency Adjustment

Before transmission, the UE applies power ramping and frequency correction. The final signal is:

$$
y(m) = A \, s_{u,v}^\text{CP}(m)\, e^{j 2\pi f_\text{off} m T_s}
$$

Where:

- $A$ = transmit amplitude after power control
- $f_\text{off}$ = frequency offset
- $T_s$ = sampling period

---

## Summary

1. Generate ZC root sequence of length $839$.
2. Apply cyclic shift: $v \cdot N_\text{CS}$.
3. Perform DFT and map tones to PRACH’s 6-RB region.
4. Apply $N_\text{IFFT}$-point IFFT.
5. Insert CP of length $N_\text{CP}$.
6. Apply power control and frequency correction.
7. Transmit during the configured PRACH opportunity.
