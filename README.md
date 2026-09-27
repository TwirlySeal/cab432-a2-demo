# Crunch Game Design Documentation

> **⚠️ DESIGN DOCUMENTATION ONLY**  
> This repository contains **design documentation and technical specifications only**. It specifies the technical approach and algorithms for the Crunch game concept but does **not** include a working implementation.  
> For actual implementation, please refer to a separate code repository.

This repository contains design documentation and technical specifications for the **Crunch** game concept - a CLI game that generates random math problems guaranteed to never repeat until all possible questions are exhausted.

## 📋 Repository Purpose

The documentation covers:
- Feistel format-preserving cipher for non-repeating question sequences
- Modular arithmetic for efficient math question generation  
- Future feature roadmap and enhancement possibilities

## 🔍 Core Technical Concepts

### Feistel Format-Preserving Cipher
The Crunch game concept uses a Feistel network to create a deterministic pseudo-random permutation of the question space, ensuring:
- Each question appears exactly once before any repeats
- Cryptographic-strength randomization using SHA-256 round function
- Format preservation (outputs remain valid question identifiers)

### Modular Arithmetic for Question Generation
Mathematical techniques to transform pseudo-random numbers into valid math questions:
- Range mapping: `actual_value = (random_value % range_size) + min_value`
- Operation selection using modular arithmetic
- Bias prevention techniques (prime modulus, rejection sampling)
- Memory-efficient O(1) question generation

## 📚 Documentation

See [`docs/technical-explanation.md`](docs/technical-explanation.md) for comprehensive technical specifications covering:
1. Feistel cipher implementation details
2. Modular arithmetic applications
3. Future feature roadmap

## 🎯 Intended Use

This documentation serves as:
- A technical specification for developers implementing the Crunch game
- Educational material on format-preserving encryption and modular arithmetic applications
- A foundation for future implementation efforts
- Reference for cryptographic and mathematical techniques in educational gaming

## 🚀 Next Steps for Implementation

To create a working Crunch game based on this design, implement:
1. Feistel cipher with 4 rounds using SHA-256 as round function
2. Modular arithmetic question mapping system
3. Command-line interface for game interaction
4. Seed-based reproducibility for testing