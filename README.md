# CT-Assignment
Computational Theory Assignment

## Project Structure
```
ct-assignment/
├── roughwork/          # Experimental code and notes
├── .gitignore          # Git ignore rules for Python and Jupyter
├── README.md           # Project documentation (this file)
├── requirements.txt    # Python dependencies
└── problems.ipynb      # Jupyter notebook with all problem solutions
```

## Setup Instructions
### Prerequisites
- Python 3.8 or higher
- pip (Python package installer)

### Installation
1. Clone repository:
   1. Go to repository > find "<> Code" > Copy HTTPS URL
   2. Terminal > cd to location that wanted to clone 
   3. run: git clone <repository-url>
   4. cd ct-assignment

2. Install required dependencies:
   - pip install -r requirements.txt

## Current Progress
- ✅ Problem 1: Complete - SHA-256 logical functions
- ✅ Problem 2: Complete - SHA-256 constants with numpy implementation
- ✅ Problem 3: Complete - Message padding with Problem 4 integration
- ✅ Problem 4: Complete - SHA-256 hash computation



# Assessment Problems

## Problem 1: Binary Words and Operations
Implementation of SHA-256 logical functions as defined in the Secure Hash Standard (FIPS PUB 180-4).

### **Implemented Functions:**
- `Parity(x, y, z)` - Parity function (x XOR y XOR z)
- `Ch(x, y, z)` - Choose function (uses y if x is 1, z if x is 0)
- `Maj(x, y, z)` - Majority function (returns majority at each bit position)
- `Sigma0(x)` - Uppercase Sigma0 with three rotations
- `Sigma1(x)` - Uppercase Sigma1 with three rotations  
- `sigma0(x)` - Lowercase sigma0 with two rotations and one shift
- `sigma1(x)` - Lowercase sigma1 with two rotations and one shift



## Problem 2: SHA-256 Constants Calculation
Implementation of SHA-256 constant generation as defined in **FIPS PUB 180-4 Section 4.2.2** using numpy for precision.

### **Prime Number Generation**
- **Algorithm:** Sieve of Eratosthenes with numpy optimization 
- **Function:** `primes(n)` – generates the first *n* prime numbers
- **Features:**  
  - Uses numpy boolean arrays for efficient memory usage  
  - Mathematical upper bound estimation for nth prime  
  - Input validation and edge-case handling 


### **Constants Calculation Process**
**Method:** First 32 bits of fractional parts of cube roots of the first 64 primes.

**Steps:**
1. Generate first 64 prime numbers using optimized sieve
2. Compute cube root of each prime using np.power() with float64 precision
3. Extract fractional part using np.modf() for accuracy
4. Multiply fractional part by 2³² and take floor
5. Store as numpy uint32 array for Problem 4 integration


### **Key Functions**
- `get_fractional_bits_corrected(number, num_bits=32)`  
  Extracts fractional bits with numpy precision.

- `compute_sha256_constants()`  
  Computes all 64 SHA-256 constants, returns hex strings and numpy array.
  
- `K256` - Numpy array containing all 64 constants as `uint32`

### **Verification**
- 100% match with FIPS PUB 180-4 published constants
- Comprehensive verification with checkmarks for each constant
- Numpy array validation ensuring correct shape and type
- Statistical analysis of constant values

### **Constants Array for SHA-256 Compression**
- `K256` array stored as `numpy.uint32` for use in Problem 4's hash computation
- Shape: `(64,)` - 1D array for direct indexing in compression rounds
- Memory efficient: 256 bytes total storage
- Verified against SHA-256 standard
- Directly usable in compression formula: `T1 = h + Σ1(e) + Ch(e,f,g) + K256[t] + W[t]`


## Problem 3: SHA-256 Messages Padding and Block Parsing

Implemententation of SHA-256 message preprocessing according to FIPS PUB 180-4, Section 5.1.1 and 5.2.1

### **Padding Algorithm**
The SHA-256 standard requires messages to be padded to a multiple of 512 bits:

For message of length ℓ bits:
1. Append bit "1" to the message
2. Append k zero bits where: ℓ + 1 + k ≡ 448 (mod 512)
3. Append 64-bit block containing ℓ (original message length in bits)
4. Total padded message length: ℓ + 1 + k + 64 ≡ 0 (mod 512)

### **Implementation Details**
- Generator Function: block_parse(msg: bytes) -> Generator[bytes, None, None]
- Input: bytes object containing the message
- Output: Yields successive 512-bit (64-byte) blocks
- Memory Efficient: Generator yields blocks one at a time

### **Key Features**
- Generator Pattern: Yields blocks one at a time for memory efficiency
- Mathematical Correctness: Implements exact SHA-256 padding formula
- Comprehensive Testing: Tests edge cases and boundary conditions
- FIPS Compliance: "abc" example matches FIPS PUB 180-4 exactly
- Problem 4 Ready: Includes `block_to_words()` helper for seamless integration

### **Helper Function for Problem 4 Integration**

`def block_to_words(block: bytes) -> List[int]`


Converts 512-bit block (64 bytes) to 16 32-bit words in big-endian format, preparing Problem 3 output for Problem 4's hash computation.

### **Testing Strategy**
The implementation is tested with 5 different message lengths:
1. Empty message (0 bytes) - Edge case testing
2. "abc" (3 bytes) - Verifies against FIPS 180-4 standard example
3. Exactly 55 bytes - Tests boundary where message fits in one block
4. Exactly 56 bytes - Tests case requiring two blocks
5. 100-byte message - Tests longer messages with multiple blocks

## Problem 4: SHA-256 Hash Computation
Implementation of the SHA-256 compression function according to **FIPS PUB 180-4 Section 6.2.2**, pages 22-23. This completes the SHA-256 implementation by integrating components from Problems 1-3.

### **Core Function**
`hash(current: np.ndarray, block: bytes) -> np.ndarray`

Calculates the next SHA-256 hash value given the current hash value and the next message block.

**Parameters:**
- `current`: Current hash value as array of 8 uint32 values (H₀⁽ⁱ⁻¹⁾ to H₇⁽ⁱ⁻¹⁾)
- `block`: Next 512-bit message block (64 bytes) from `block_parse()`

**Returns:**
- Next hash value as array of 8 uint32 values (H₀⁽ⁱ⁾ to H₇⁽ⁱ⁾)

**Algorithm Implementation (Section 6.2.2):**
1. **Prepare message schedule {Wₜ}**
- First 16 words: Directly from message block (big-endian)
- Remaining 48 words: Expanded using σ₀ and σ₁ functions from Problem 1
- Formula: Wₜ = σ₁(Wₜ₋₂) + Wₜ₋₇ + σ₀(Wₜ₋₁₅) + Wₜ₋₁₆

2. **Initialize working variables** with current hash:
- a = H₀⁽ⁱ⁻¹⁾, b = H₁⁽ⁱ⁻¹⁾, ..., h = H₇⁽ⁱ⁻¹⁾

3. **Perform 64 compression rounds** (t = 0 to 63):
- T1 = h + Σ₁(e) + Ch(e, f, g) + Kₜ + Wₜ
- T2 = Σ₀(a) + Maj(a, b, c)
- Update working variables:
  - h = g, g = f, f = e, e = d + T1
  - d = c, c = b, b = a, a = T1 + T2

4. **Compute intermediate hash value**:
- H₀⁽ⁱ⁾ = a + H₀⁽ⁱ⁻¹⁾, H₁⁽ⁱ⁾ = b + H₁⁽ⁱ⁻¹⁾, ..., H₇⁽ⁱ⁾ = h + H₇⁽ⁱ⁻¹⁾

### **Integration with Previous Problems**
- **Problem 1 Functions Used**: Ch, Maj, Σ₀, Σ₁, σ₀, σ₁
- **Problem 2 Constants Used**: K256 array of 64 uint32 constants
- **Problem 3 Integration**: Compatible with output from `block_parse()` generator

### **Helper Functions**
1. `prepare_message_schedule(block: bytes) -> np.ndarray`
- Implements Step 1 of Section 6.2.2
- Converts 64-byte block to 64-word schedule array
- Uses σ₀ and σ₁ functions for word expansion

2. `bytes_to_hash_hex(hash_array: np.ndarray) -> str`
- Converts hash array to standard 64-character hex string
- Format: 8 words concatenated (each 8 hex characters)

3. **Complete Pipeline Example** (using Problems 1-4 together):
   ```python
   # Example of using all components together
   message = "abc"
   blocks = list(block_parse(message.encode('utf-8')))  # Problem 3
   current = INITIAL_HASH.copy()
   
   for block in blocks:
       current = hash(current, block)  # Problem 4
   
   final_hash = bytes_to_hash_hex(current)  # Helper function
   print(f"SHA-256('{message}'): {final_hash}")

### **Comprehensive Testing Strategy**

#### **Test 1: Basic Functionality**
- Initial hash with zero block
- Verification that hash changes after processing

#### **Test 2: Determinism**
- Same inputs produce identical outputs
- Multiple calls with same parameters yield same result

#### **Test 3: Avalanche Effect**
- 1-bit changes in input produce drastically different outputs
- Current hash sensitivity: 8/8 words different
- Block sensitivity: 61/64 hex characters different

#### **Test 4: Standard Test Vectors**
- **"abc"**: `ba7816bf8f01cfea414140de5dae2223b00361a396177a9cb410ff61f20015ad`
- **Empty string**: `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855`
- **Multi-block messages**: Verified against `hashlib.sha256()`

#### **Test 5: Integration Testing**
- Works seamlessly with `block_parse()` from Problem 3
- Processes multi-block messages correctly
- Maintains chaining across blocks

#### **Test 6: Error Handling**
- Validates input types and sizes
- Rejects non-uint32 arrays
- Rejects blocks not 64 bytes
- Clear error messages for debugging

#### **Test 7: Cryptographic Properties**
- No fixed points found in random trials
- No collisions found in statistical testing
- Demonstrates expected SHA-256 behavior

### **Performance Characteristics**
- **Output Size**: Fixed 256-bit (64 hex characters)
- **Memory Usage**: Efficient numpy uint32 operations
- **Deterministic**: Same input always produces same output
- **Avalanche Effect**: Small input changes produce large output changes

### **Verification Against Standards**
- **FIPS PUB 180-4 Compliance**: Implements Section 6.2.2 exactly
- **NIST Test Vectors**: All standard vectors pass
- **Cross-Validation**: Matches Python `hashlib.sha256()` results
- **Intermediate Values**: Debug output shows correct round-by-round computation

### **Key Implementation Details**
- **32-bit Arithmetic**: All operations performed modulo 2³²
- **Numpy Optimization**: Uses uint32 dtype for efficient bitwise operations
- **Big-endian Encoding**: Proper byte ordering for SHA-256 specification
- **Error Prevention**: Input validation prevents incorrect usage
- **Modular Design**: Each component reusable and testable independently

### **Educational Value**
This implementation demonstrates:
1. **Cryptographic Algorithm Implementation**: How SHA-256 compression works
2. **Bitwise Operations**: 32-bit arithmetic and rotation operations
3. **Standard Compliance**: Following cryptographic specifications exactly
4. **Testing Methodology**: Comprehensive validation of cryptographic functions
5. **Integration Patterns**: Combining multiple components into complete system

### **Usage Example**
```python
# Direct compression function usage
current_hash = INITIAL_HASH.copy()
block = b'A' * 64  # 512-bit block
next_hash = hash(current_hash, block)
print(f"Next hash: {bytes_to_hash_hex(next_hash)}")

# Example of full message processing (using Problems 1-4)
# message_bytes = b"Hello, World!"
# for block in block_parse(message_bytes):
#     current_hash = hash(current_hash, block)
# final_hash = bytes_to_hash_hex(current_hash)
```