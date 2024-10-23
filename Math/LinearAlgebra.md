# Matrix Course Detailed Cheat Sheet

---

## Day 1: Core and Intermediate Concepts

---

### Vector Operations

- **Vector Addition**:  
  Formula:  
  $$ \mathbf{u} + \mathbf{v} = (u_1 + v_1, u_2 + v_2, \dots, u_n + v_n) $$

  - **Commutative**:  
    $$ \mathbf{u} + \mathbf{v} = \mathbf{v} + \mathbf{u} $$

  - **Associative**:  
    $$ (\mathbf{u} + \mathbf{v}) + \mathbf{w} = \mathbf{u} + (\mathbf{v} + \mathbf{w}) $$

- **Scalar Multiplication**:  
  Formula:  
  $$ c \mathbf{u} = (c u_1, c u_2, \dots, c u_n) $$

  - **Distributive**:  
    $$ c (\mathbf{u} + \mathbf{v}) = c \mathbf{u} + c \mathbf{v} $$

  - **Associative with Scalars**:  
    $$ c (d \mathbf{u}) = (cd) \mathbf{u} $$

- **Dot Product**:  
  Formula:  
  $$ \mathbf{u} \cdot \mathbf{v} = u_1 v_1 + u_2 v_2 + \dots + u_n v_n $$

  - **Commutative**:  
    $$ \mathbf{u} \cdot \mathbf{v} = \mathbf{v} \cdot \mathbf{u} $$

  - **Distributive**:  
    $$ \mathbf{u} \cdot (\mathbf{v} + \mathbf{w}) = \mathbf{u} \cdot \mathbf{v} + \mathbf{u} \cdot \mathbf{w} $$

---

### Length and Angle of Vectors

- **Length (Magnitude)**:  
  Formula:  
  $$ |\mathbf{u}| = \sqrt{ \mathbf{u} \cdot \mathbf{u} } = \sqrt{u_1^2 + u_2^2 + \dots + u_n^2} $$

- **Cosine of the Angle Between Vectors**:  
  Formula:  
  $$ \cos \theta = \frac{ \mathbf{u} \cdot \mathbf{v} }{ |\mathbf{u}| |\mathbf{v}| } $$

- **Cauchy-Schwartz Inequality**:  
  Formula:  
  $$ |\mathbf{u} \cdot \mathbf{v}| \leq |\mathbf{u}| |\mathbf{v}| $$

- **Triangle Inequality**:  
  Formula:  
  $$ |\mathbf{u} + \mathbf{v}| \leq |\mathbf{u}| + |\mathbf{v}| $$

---

### Linear Combinations and Span

- **Linear Combination**:  
  A vector \( \mathbf{v} \) can be written as a linear combination of other vectors.  
  Formula:  
  $$ c_1 \mathbf{v}_1 + c_2 \mathbf{v}_2 + \dots + c_n \mathbf{v}_n $$

- **Span**:  
  The span of vectors \( \mathbf{v}_1, \mathbf{v}_2, \dots, \mathbf{v}_n \) is the set of all possible linear combinations of those vectors.

---

### Projections and Orthogonality

- **Projection of \( \mathbf{v} \) onto \( \mathbf{u} \)**:  
  Formula:  
  $$ \text{Proj}_{\mathbf{u}}(\mathbf{v}) = \frac{\mathbf{u} \cdot \mathbf{v}}{\mathbf{u} \cdot \mathbf{u}} \mathbf{u} $$

- **Orthogonality**:  
  Two vectors \( \mathbf{u} \) and \( \mathbf{v} \) are orthogonal if their dot product is zero:  
  $$ \mathbf{u} \cdot \mathbf{v} = 0 $$

- **Pythagorean Theorem (in \( \mathbb{R}^n \))**:  
  If \( \mathbf{u} \perp \mathbf{v} \), then:  
  $$ |\mathbf{u} + \mathbf{v}|^2 = |\mathbf{u}|^2 + |\mathbf{v}|^2 $$

---

### Gaussian Elimination

1. **Forming the Augmented Matrix**:  
   Write the system of linear equations in matrix form.

2. **Row Operations**:
   - **Swap rows**.
   - **Multiply a row by a non-zero scalar**.
   - **Add/subtract multiples of one row to/from another**.

3. **Back Substitution**:
   - Solve the system by simplifying to row echelon form (upper triangular matrix) and using back substitution.

---

### Matrix Operations

- **Matrix Addition**:  
  $$ A + B = \begin{bmatrix} a_{11} + b_{11} & a_{12} + b_{12} \\ a_{21} + b_{21} & a_{22} + b_{22} \end{bmatrix} $$

- **Matrix Multiplication**:  
  Formula for the element in the \( i \)-th row and \( j \)-th column of the product matrix:  
  $$ (AB)_{ij} = \sum_k a_{ik} b_{kj} $$

- **Transpose of a Matrix**:  
  Formula:  
  $$ A^T_{ij} = A_{ji} $$

---

### Key Properties

- **Distributive**:  
  $$ A(B + C) = AB + AC $$

- **Associative** (Matrix Multiplication):  
  $$ A(BC) = (AB)C $$

- **Non-Commutative** (Matrix Multiplication):  
  $$ AB \neq BA $$ in general.

---

## Day 2: Advanced Concepts

---

### Matrix Inverses

- **Matrix Inverse** (for square matrix \( A \)):  
  $$ A^{-1}A = I = AA^{-1} $$
  - A matrix \( A \) is invertible if and only if \( \det(A) \neq 0 \).

---

### Determinants

- **Determinant of a 2x2 Matrix**:  
  $$ \det(A) = \begin{vmatrix} a & b \\ c & d \end{vmatrix} = ad - bc $$

- **Properties of Determinants**:
  - \( \det(AB) = \det(A)\det(B) \)
  - \( \det(A^T) = \det(A) \)
  - If \( \det(A) = 0 \), then \( A \) is not invertible.

---

### Eigenvalues and Eigenvectors

- **Eigenvalue Equation**:  
  $$ A\mathbf{v} = \lambda \mathbf{v} $$
  - Where \( \lambda \) is the eigenvalue, and \( \mathbf{v} \) is the corresponding eigenvector.

- **Finding Eigenvalues**:  
  Solve \( \det(A - \lambda I) = 0 \) for \( \lambda \).

- **Finding Eigenvectors**:  
  Solve \( (A - \lambda I)\mathbf{v} = 0 \) for \( \mathbf{v} \).

---

### Diagonalization

- A matrix \( A \) is diagonalizable if there exists an invertible matrix \( P \) such that:  
  $$ A = P D P^{-1} $$
  - Where \( D \) is a diagonal matrix whose diagonal entries are the eigenvalues of \( A \), and the columns of \( P \) are the corresponding eigenvectors of \( A \).

---

### Practice Problems

#### Problem 1: Vector Orthogonality
- Let \( \mathbf{u} \) and \( \mathbf{v} \) be n-dimensional vectors. Define the vectors:  
  $$ p = \frac{\mathbf{u} \cdot \mathbf{v}}{\mathbf{u} \cdot \mathbf{u}} \mathbf{u}, \,\, h = \mathbf{v} - p $$
- Prove that \( h \) is orthogonal to \( \mathbf{u} \).  
- **Solution**: Compute \( \mathbf{u} \cdot h \) and show that it equals zero.

#### Problem 2: Gaussian Elimination
Solve the system of equations using Gaussian Elimination:
$$
\begin{aligned}
2x + 3y + z &= 1 \\
4x + y - z &= 2 \\
-2x + 2y + 3z &= 4
\end{aligned}
$$

#### Problem 3: Determinants and Eigenvalues
Find the determinant and eigenvalues of the matrix:
$$ A = \begin{bmatrix} 4 & 1 \\ 2 & 3 \end{bmatrix} $$

---

This cheat sheet now has a **more expanded version**, following the requested format with LaTeX-like syntax that can be used in platforms that
