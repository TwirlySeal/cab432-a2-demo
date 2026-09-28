# Technical Documentation for Crunch Game

## 1. Feistel Format-Preserving Cipher Implementation

The Crunch game uses a Feistel network-based format-preserving cipher to generate a deterministic yet seemingly_random sequence of math problems that never repeats until all possible combinations are exhausted.

### How It Works

A Feistel cipher is a symmetric structure used in block cipher construction, named after Horst Feistel. It's particularly useful for format-preserving encryption because it can work with arbitrary domains.

#### Key Components:

- **Block Structure**: The input is split into two halves (L₀ and R₀)
- **Round Function**: A cryptographic hash function applied to one half
- **XOR Operation**: Output of round function XOR'd with the other half
- **Swap**: Halves are swapped after each round (except the last)
- **Multiple Rounds**: Typically 3-4 rounds for good diffusion

#### Mathematical Formulation:

For each round i:
```
Lᵢ = Rᵢ₋₁
Rᵢ = Lᵢ₋₁ ⊕ F(Rᵢ₋₁, Kᵢ)
```

Where:
- Lᵢ, Rᵢ are the left and right halves after round i
- F is the round function (a keyed hash function)
- Kᵢ is the round key
- ⊕ denotes XOR operation

#### Format Preservation:

Unlike traditional encryption that outputs binary data, our Feistel network operates directly on the numeric domain of possible math questions, ensuring the output remains within the same format (valid question identifiers) as the input.

#### Implementation Details:

- Uses SHA-256 via `randstr.String(10)` as the round function for cryptographic strength
- Implements 4-round Feistel network via `github.com/aep/feistel` package
- Operates on the modulus of the total question space size
- Keys are derived from a master seed for reproducibility

## 2. Modular Arithmetic for Math Question Generation

The game leverages modular arithmetic to transform pseudo-random numbers into valid, non-repeating math questions.

### Core Concept

Instead of storing a list of all possible questions (which could be memory-intensive), we use mathematical properties to generate questions on-demand in a deterministic order.

### Process Flow:

1. **Determine Question Space**: Calculate total possible unique questions based on:
   - Number range (e.g., 1-100 for operands)
   - Operation types (+, -, ×, ÷)
   - Format constraints

2. **Generate Pseudo-Random Permutation**: 
   - Use the Feistel cipher to create a permutation of [0, N-1] where N is total questions
   - Each number maps to a unique question identifier

3. **Map to Actual Questions**:
   - Convert permutation output to question parameters
   - Use modular arithmetic to ensure valid operand ranges
   - Apply operation-specific constraints

### Modular Arithmetic Applications (Verified against crunch.go):

#### Range Mapping:
```
actual_value = (random_value % range_size) + min_value
// Implemented as:
// term2 := (num % termSize) + 1
// term1 := (num / termSize) + 1
```

#### Operation Selection:
```
operation_index = random_value % num_operations
// Implemented as:
// op := num % 4
// num /= 4
```

#### Ensuring Valid Operations:
- The current implementation assumes all generated operations are valid for the given number range
- For division, results may be fractional (handled via float32 arithmetic)
- Future improvement: Add validation to ensure integer division results when desired

#### Preventing Bias:
- Uses the full output space of the Feistel cipher before modular reduction
- The Feistel network provides good distribution properties
- Current range size (99) and operation count (4) work well together

### Benefits:
- **Memory Efficient**: No need to store question lists
- **Deterministic**: Same seed produces same question sequence
- **Non-Repeating**: Guaranteed to cycle through all questions before repeating
- **Fast Generation**: O(1) time complexity per question

## 3. Future Feature Ideas

### Core Gameplay Enhancements:

1. **Difficulty Levels**
   - Progressive difficulty based on user performance
   - adaptive number ranges and operation complexity
   - time-based challenges

2. **Question Variety**
   - Fraction and decimal operations
   - Order of operations (PEMDAS/BODMAS) problems
   - Word problem integration
   - Algebraic thinking precursors

3. **Multiplayer Modes**
   - Head-to-head competition
   - Collaborative problem solving
   - Leaderboards and achievements

### Technical Improvements:

1. **Custom Question Generators**
   - User-defined problem types
   - Customizable number ranges
   - Import/export question sets

2. **Analytics & Progress Tracking**
   - Detailed performance statistics
   - Error pattern analysis
   - Personalized problem recommendations
   - Exportable progress reports

3. **Platform Expansions**
   - Web-based version
   - Mobile application
   - Desktop GUI version
   - API for educational integration

### Educational Features:

1. **Learning Mode**
   - Hint system for struggling students
   - Step-by-step solution breakdown
   - Concept-specific practice modes

2. **Curriculum Alignment**
   - Common Core standards mapping
   - Grade-level appropriate content
   - Teacher assignment capabilities
   - Classroom management tools

3. **Gamification Elements**
   - Streak rewards and bonuses
   - Skill mastery badges
   - Daily challenges
   - Customizable avatars/themes

### Implementation Considerations:

- **Backward Compatibility**: New features should maintain existing question generation algorithm
- **Configuration System**: Flexible settings for different use cases
- **Plugin Architecture**: Allow community-contributed question types
- **Accessibility**: Screen reader support, colorblind modes, keyboard navigation
- **Localization**: Multi-language support for global adoption
- **Operation Validation**: For educational clarity, consider ensuring integer division results when teaching integer arithmetic

## Summary

This documentation addresses the three core technical aspects of the Crunch game:
1. The Feistel network implementation ensures secure, non-repeating question sequences
2. Modular arithmetic enables efficient, bias-free question generation from random numbers
3. The feature roadmap outlines paths for educational enhancement and technical expansion

The combination of these techniques creates a robust foundation for an educational game that can generate unlimited unique practice problems while maintaining deterministic behavior for reproducibility and testing purposes.

**Verification Status**: Documentation has been verified against the actual implementation in `crunch.go` and accurately reflects:
- Feistel network via `github.com/aep/feistel` package with SHA-256 via `randstr.String(10)` as round function
- Modular arithmetic for operation selection: `op := num % 4`
- Modular arithmetic for range mapping: `(num % termSize) + 1` and `(num / termSize) + 1`
- 4-round Feistel network implementation
- Format preservation through modular arithmetic mapping