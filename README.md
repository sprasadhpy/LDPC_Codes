# LDPC_Codes
Coursework of the course COMP0080: GRAPHICAL MODELS

# Readme File for Encoding and Decoding Process

## 2.1.1 Encoding Method

### Overview
This section explains how to find a generator matrix \(G\) given a parity check matrix \(H\). 

### Process
- **Basis Vectors and Generator Matrix**: To form matrix \(G\), use the basis vectors of the codeword subspace as its columns. This makes \(x = Gt\) a valid codeword.
- **Systematic Encoding**: In systematic encoding, all bits of \(t\) are directly copied to a specific location in the transmitted message \(x\). Commonly, this is done in the first \(K\) bits. This makes it easy to reconstruct the signal from the decoded message by reading the first \(K\) bits.
- **Building Systematic Encoder \(G\)**: Gaussian elimination can be utilized to construct a systematic encoder \(G\). Following the guidelines in the tutorial, we achieve the echelon form of \(H\) as \([P|N-K]\). Then, select \(G\) to be \([I|KP]^T\). This ensures that \(HG^T = (I+I)^T = 0\) for every \(t\).

## 2.1.2 Decoding Method

### Probabilistic Model of LDPC Decoder

#### Definitions
- **Transmitted and Received Vectors**: Let \(x\) be the transmitted vector and \(y\) be the received one.
- **Noise Model**: The noise model defines the conditional probability distributions \(P(y|x)\). In the Binary Symmetric Channel model, each bit is independently flipped with probability \(p\). Therefore, the probability is given by:
  
P(y|x) = Product(n=1 to N) of p(y_n|x_n) = Product(n=1 to N) of p^(x_n - y_n) * (1-p)^(1 - x_n + y_n)


#### Components of the Equation
- `P(y|x)`: Represents the conditional probability of receiving vector `y` given that vector `x` was transmitted.
- `Product(n=1 to N)`: Indicates the multiplication of a sequence of factors from `n=1` to `N`, where `N` is the total number of bits in the vector.
- `p(y_n|x_n)`: Probability of receiving bit `y_n` given the transmitted bit `x_n`.
- `p`: Probability of a bit flip in the Binary Symmetric Channel (BSC) model.
- `x_n - y_n`: Difference between the transmitted bit and the received bit for each bit position `n`.

### Usage
This formula is essential in the decoding process, particularly in communication systems where noise may cause bit errors during transmission. It helps in estimating the original transmitted message by considering the likelihood of each bit being altered during the transmission process.

- **Joint Distribution**: The joint distribution of \((x, y)\) is defined as \(p(x, y) = p(y|x)p(x)\). Here, \(p(x)\) is a uniform prior distribution over all valid codewords 

#### Decoding Process
- **Method**: Decoding is performed using Loopy Belief Propagation.
- **Reference**: The method is detailed in the tutorial slides and in the literature [1, pp. 560-561].


# Loopy Belief Propagation for Binary Symmetric Channels

## Overview
Given a transmitted word `x`, a received word `y` is decoded using Loopy Belief Propagation for Binary Symmetric Channels. This iterative algorithm involves passing messages between bit nodes and check nodes to determine the most probable original transmitted word.

## Algorithm Process

### Iterative Process
- **Message Passing:** Messages are passed from bit node to check node and back at each step of the algorithm.
- **Probability Representation:** Messages represent probabilities about the values of the bit nodes, based on information from previous rounds and other messages passed along the edges of the factor graph.
- **Decoding Aim:** The goal is to find a decoding (x*_n = arg max p(x_n|y)), ensuring that x* is a valid codeword.

### Implementation in 'decode' Function
The implementation involves a parity check matrix `H`, a received word `y`, and a noise ratio `p`. Logarithmic computations are used to address numerical underflow concerns.

#### Steps
1. **Initialize Messages from Bit Node to Check Node:**
   - Initialize with H_ij = 1 if there is a connection between check node i and bit node j, otherwise H_ij = 0.
   - Set initial messages in matrix `P` based on the received word `y` and noise ratio `p`.

2. **Send Messages from Check Node to Bit Node:**
   - Calculate messages based on the set of bit nodes incident to the check node and update matrix `P`.

3. **Generate a Tentative Decoding:**
   - Sum the latest messages for each bit node from associated check nodes.
   - Decode each bit node based on the sum, and verify if the resulting vector is a valid codeword.

4. **Send Messages from Bit Node to Check Node:**
   - Update messages based on the set of check nodes incident to the bit node.
   - Repeat the process from Step 2.

**Execution Details**

Input Vector and Parity Check Matrix: The algorithm was run using the given vector y_1 and the parity check matrix H_1.
Outcome: After running the algorithm, convergence was achieved to a decoded vector of size 1000x1.
Number of Iterations: The convergence occurred in 8 iterations.
![image](https://github.com/sprasadhpy/LDPC_Codes/assets/40602129/61fae737-a862-4905-b0a2-883709fbf1a3)

## Post-Decoding Steps

After decoding the data, follow these steps to process the decoded elements and retrieve the message:

### Steps
1. **Integer Casting:**
   - Cast the elements of the decoded vector to integers to ensure binary operations can be performed.

2. **Reshaping to 2D Array:**
   - Reshape the 1D decoded array into a 2D array `x` with 125 rows and 8 columns, where each row corresponds to a byte.

3. **Conversion to String:**
   - Convert each number in array `x` to a string. This step facilitates the concatenation of binary digits.

4. **Initialize List:**
   - Create an empty list `b`.

5. **Concatenate Binary Strings:**
   - For each row in `x`, concatenate all string elements to form a binary string and append this string to the list `b`.

6. **Initialize Message String:**
   - Create an empty string `message`.

7. **Constructing the ASCII Message:**
   - For each binary string in the first 31 elements of `b`:
     a) Convert the binary string to its decimal equivalent.
     b) Convert the decimal number to its corresponding ASCII character.
     c) Concatenate this character to `message`.

8. **Display the Message:**
   - Print `message` to display the decoded ASCII string.

### Decoded Message
Using these steps on the first 248 bits of the decoded vector, we retrieve the message:
- "Happy Holidays! Dmitry&David :)"

