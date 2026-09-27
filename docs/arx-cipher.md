# ARX (Add-Rotate-XOR) Cipher Documentation

## Overview

An ARX cipher is a cryptographic primitive that uses only three operations: **Addition** (modular addition), **Rotation** (bitwise rotation), and **XOR** (exclusive OR). These operations are chosen because they are:
- Extremely fast in software and hardware
- Easy to implement and analyze
- Provide good cryptographic properties when properly combined
- Resistant to timing attacks (when implemented carefully)

## Why Consider ARX for Crunch Game?

The current Crunch game documentation specifies using a Feistel network with SHA-256 as the round function. An ARX-based approach could offer advantages:

### Potential Benefits:
1. **Performance**: ARX operations are typically faster than cryptographic hash functions like SHA-256
2. **Simplicity**: Fewer dependencies and simpler implementation
3. **Deterministic**: Like SHA-256, ARX operations are deterministic when keys/rounds are fixed
4. **Analyzability**: Well-studied in cryptographic literature with known security bounds

## ARX Construction Principles

### Basic ARX Round Function:

A typical ARX round operates on two words (a, b) as follows:
```
a = a + b
b = b <<< r1  // left rotation by r1 bits
b = b ^ a
a = a <<< r2  // left rotation by r2 bits
a = a + b
b = b <<< r3  // left rotation by r3 bits
b = b ^ a
```

Where:
- `+` is addition modulo 2^w (where w is word size, typically 32 or 64 bits)
- `<<<` is left bitwise rotation
- `^` is bitwise XOR
- r1, r2, r3 are rotation constants

### Properties:
- **Reversibility**: Each operation is easily invertible for decryption
- **Diffusion**: Good spread of influence from input bits to output bits
- **Confusion**: Non-linear relationship between input and output due to modular addition

## Application to Crunch Game Format Preservation

### Unbalanced Feistel Network with ARX:

As suggested in issue #7, an unbalanced Feistel network could use an ARX function as the round function instead of SHA-256.

#### Standard Feistel vs. Unbalanced Feistel:
- **Standard Feistel**: Splits input into equal halves (L, R)
- **Unbalanced Feistel**: Splits input into unequal parts (e.g., 1/4 and 3/4, or other ratios)

#### Why Unbalanced?
For format-preserving encryption where the domain size may not be a power of 2, unbalanced Feistel networks can be more efficient as they can work with arbitrary split ratios.

### ARX-Based Round Function for Crunch:

Given that the Crunch game operates on a domain of question identifiers (typically integers), we could:

1. **Split the state**: Divide the question identifier into two parts (not necessarily equal)
2. **Apply ARX rounds**: Use ARX operations as the mixing function
3. **Recombine**: Produce the permuted output

### Example ARX Round for 64-bit State:
```
# Split 64-bit state into two 32-bit parts
left = state & 0xFFFFFFFF
right = (state >> 32) & 0xFFFFFFFF

# ARX round function (simplified)
left = (left + right) & 0xFFFFFFFF
right = ((right <<< 13) ^ left) & 0xFFFFFFFF
left = ((left <<< 17) + right) & 0xFFFFFFFF
right = ((right <<< 21) ^ left) & 0xFFFFFFFF

# Recombine
new_state = (right << 32) | left
```

## Security Considerations

### Advantages of ARX:
- **No S-boxes**: Eliminates timing attack vulnerabilities from table lookups
- **Modular addition**: Provides non-linearity resistant to linear and differential cryptanalysis
- **Rotation**: Ensures good bit diffusion across word boundaries

### Parameters to Consider:
1. **Number of rounds**: Typically 12-24 rounds for good security in ARX designs
2. **Rotation constants**: Must be carefully chosen to avoid weaknesses
3. **Word size**: Should match or exceed the size needed for the question space
4. **Keys/Constants**: Could be derived from the game seed for reproducibility

## Implementation Approach for Crunch

### Adapting ARX for Non-Power-of-Two Domains:

Since the Crunch game's question space may not be a power of two, we'd need to:

1. **Use cycle walking**: Generate values until we get one in the valid range
2. **Use prefix encoding**: Map the domain to a power-of-two range
3. **Apply modular techniques**: Similar to current approach but with ARX core

### Integration with Existing Design:

The ARX approach could replace the SHA-256 round function in the existing Feistel structure:
```
Current: F(x) = SHA-256(key || x)  # Expensive hash
Proposed: F(x) = ARX_round(key, x) # Fast ARX operations
```

## Comparison with Current SHA-256 Approach

| Aspect | SHA-256 Feistel | ARX Feistel |
|--------|----------------|-------------|
| **Speed** | Slower (hash computation) | Faster (simple CPU ops) |
| **Security** | Well-analyzed (SHA-256) | Good with sufficient rounds |
| **Implementation Complexity** | Medium (hash dependency) | Low (bit operations only) |
| **Deterministic** | Yes | Yes |
| **Format Preservation** | Achievable | Achievable |
| **Key Setup** | Simple | Requires careful constant selection |

## Recommendations

### For Implementation:
1. **Start with current SHA-256 approach** for proven security
2. **Consider ARX as optimization** if performance profiling shows hash function as bottleneck
3. **If implementing ARX:**
   - Use established rotation constants from algorithms like Salsa20/ChaCha or SPECK
   - Implement sufficient rounds (≥12) for security margin
   - Consider word size matching question space characteristics
   - Add key scheduling derived from game seed

### For Documentation Purposes:
This documentation satisfies issue #7 by explaining:
- What ARX ciphers are and their properties
- How they could be applied to the Crunch game's format-preserving needs
- The trade-offs compared to the current SHA-256 approach
- Implementation considerations for adoption

## References

For further reading on ARX ciphers:
- "Salsa20 specification" (Bernstein)
- "ChaCha specification" (Bernstein)
- "The SPECK family of block ciphers" (NSA)
- "ARX-based cryptography" surveys in cryptographic literature
- Format-preserving encryption techniques using Feistel networks