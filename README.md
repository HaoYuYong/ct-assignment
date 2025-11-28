# ct-assignment
Computational Theory Assignment

## Project Structure
ct-assignment/
├── roughwork/ # Experimental code and notes (ignored for grading)
├── .gitignore # Git ignore rules for Python and Jupyter
├── README.md # Project documentation (this file)
├── requirements.txt # Python dependencies
└── problems.ipynb # Jupyter notebook with all problem solutions

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
- ✅ Problem 1: Complete
- 🔄 Problems 2-5: In progress



## Assessment Problems

### Problem 1: Binary Words and Operations
Implementation of SHA-256 logical functions as defined in the Secure Hash Standard (FIPS PUB 180-4).

**Implemented Functions:**
- `Parity(x, y, z)` - Parity function (x XOR y XOR z)
- `Ch(x, y, z)` - Choose function (uses y if x is 1, z if x is 0)
- `Maj(x, y, z)` - Majority function (returns majority at each bit position)
- `Sigma0(x)` - Uppercase Sigma0 with three rotations
- `Sigma1(x)` - Uppercase Sigma1 with three rotations  
- `sigma0(x)` - Lowercase sigma0 with two rotations and one shift
- `sigma1(x)` - Lowercase sigma1 with two rotations and one shift