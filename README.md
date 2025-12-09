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
- 🔄 Problem 4: In progress - SHA-256 hash computation



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