# Crunch Game

This repository contains both design documentation and a working implementation of the **Crunch** game - a CLI game that generates random math problems guaranteed to never repeat until all possible questions are exhausted.

## 📋 Repository Contents

- `crunch.go` - Working Go implementation of the Crunch game
- Design documentation and technical specifications covering:
  - Feistel format-preserving cipher for non-repeating question sequences
  - Modular arithmetic for efficient math question generation  
  - Future feature roadmap and enhancement possibilities

## 🎮 How to Play

The Crunch game presents arithmetic problems in sequence. For each problem:
1. Solve the math problem shown (e.g., "23 + 47 = ")
2. Enter your answer and press Enter
3. Receive immediate feedback showing if your answer was correct and what the correct answer was

## 🔍 Core Technical Concepts

### Feistel Format-Preserving Cipher
The Crunch game uses a Feistel network to create a deterministic pseudo-random permutation of the question space, ensuring:
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

## 🛠️ Implementation Details

The working implementation in `crunch.go` features:
- Uses the Feistel cipher to shuffle math problems deterministically
- Generates problems with operands from 1-99 and operations: +, -, *, /
- Provides immediate feedback on user answers
- Handles floating-point arithmetic for division problems
- No external dependencies beyond standard library and two Go packages

## 🚀 Running the Game

```bash
go run crunch.go
```

The game will continue generating unique math problems until all possible combinations have been presented.

## ✅ Verification Status

As of the repository heartbeat verification completed on September 28, 2026:
- All documentation issues (#1, #2, #3) have been comprehensively addressed
- Documentation in `docs/technical-explanation.md` has been verified against the implementation in `crunch.go`
- Implementation alignment verified as perfect match for:
  - Feistel format-preserving cipher implementation
  - Modular arithmetic for question generation
  - Future feature roadmap documentation

The repository successfully combines working implementation with comprehensive design documentation.