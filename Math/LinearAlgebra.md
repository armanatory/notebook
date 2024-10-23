# Matrix Course Cheat Sheet

## Day 1: Core and Intermediate Concepts

### Vector Operations
- **Vector Addition**:  
  $$ \mathbf{u} + \mathbf{v} = (u_1 + v_1, u_2 + v_2, \dots, u_n + v_n) $$

  - **Commutative**:  
    $$ \mathbf{u} + \mathbf{v} = \mathbf{v} + \mathbf{u} $$

  - **Associative**:  
    $$ (\mathbf{u} + \mathbf{v}) + \mathbf{w} = \mathbf{u} + (\mathbf{v} + \mathbf{w}) $$

- **Scalar Multiplication**:  
  $$ c \mathbf{u} = (c u_1, c u_2, \dots, c u_n) $$

  - **Distributive**:  
    $$ c(\mathbf{u} + \mathbf{v}) = c\mathbf{u} + c\mathbf{v} $$

  - **Associative with Scalars**:  
    $$ c(d\mathbf{u}) = (cd)\mathbf{u} $$

- **Dot Product**:  
  $$ \mathbf{u} \cdot \mathbf{v} = u_1 v_1 + u_2 v_2 + \dots + u_n v_n $$

  - **Commutative**:  
    $$ \mathbf{u} \cdot \mathbf{v} = \mathbf{v} \cdot \mathbf{u} $$

  - **Distributive**:  
    $$ \mathbf{u} \cdot (\mathbf{v} + \mathbf{w}) = \mathbf{u} \cdot \mathbf{v} + \mathbf{u} \cdot \mathbf{w} $$

---

### Length and Angle of Vectors
- **Length (Magnitude)**:  
  $$ |\mathbf{u}| = \sqrt{\mathbf{u} \cdot \mathbf{u}} = \sqrt{u_1^2 + u_2^2 + \dots + u_n^2} $$

- **Cosine of the Angle Between Vectors**:  
  $$ \cos \theta = \frac{\mathbf{u} \cdot \mathbf{v}}{|\mathbf{u}| |\mathbf{v}|} $$

- **Cauchy-Schwartz Inequality**:  
  $$ |\mathbf{u} \cdot \mathbf{v}| \leq |\mathbf{u}| |\mathbf{v}| $$

- **Triangle Inequality**:  
  $$ |\mathbf{u} + \mathbf{v}| \leq |\mathbf{u}| + |\mathbf{v}| $$

---

### Linear Combinations and Span
- **Linear Combination**:  
  $$ c_1\mathbf{v}_1 + c_2\mathbf{v}_2 + \dots + c_n\mathbf{v}_n $$

  - The span of vectors \( \mathbf{v}_1, \mathbf{v}_2, \dots, \mathbf{v}_n \) is the set of all possible linear combinations of those vectors.

---

### Projections and Orthogonality
- **Projection of \( \mathbf{v} \) onto \( \mathbf{u} \)**:  
  $$ \text{Proj}_{\mathbf{u}}(\mathbf{v}) = \frac{\mathbf{u} \cdot \mathbf{v}}{\mathbf{u} \cdot \mathbf{u}} \mathbf{u} $$

- **Orthogonality**:  
  Vectors \( \mathbf{u} \) and \( \mathbf{v} \) are orthogonal if:  
  $$ \mathbf{u} \cdot \mathbf{v} = 0 $$

- **Pythagorean Theorem (in \( \mathbb{R}^n \))**:  
  If \( \mathbf{u} \perp \mathbf{v} \), then:  
  $$ |\mathbf{u} + \mathbf{v}|^2 = |\mathbf{u}|^2 + |\mathbf{v}|^2 $$

---

### Gaussian Elimination
1. Write the system of linear equations as an augmented matrix.
2. Use **row operations** to convert the matrix into **row echelon form** (REF):
   - Swap rows
   - Multiply a row by a non-zero scalar
   - Add or subtract multiples of one row from another
3. **Back substitution** to find the solution.

---

### Matrix Operations
- **Matrix Addition**:  
  $$ A + B = \begin{bmatrix} a_{11} + b_{11} & a_{12} + b_{12} \\ a_{21} + b_{21} & a_{22} + b_{22} \end{bmatrix} $$

- **Matrix Multiplication**:  
  $$ (AB)_{ij} = \sum_{k} a_{ik} b_{kj} $$

- **Transpose**:  
  $$ A^T_{ij} = A_{ji} $$

---

### Key Properties
- **Distributive**:  
  $$ A(B + C) = AB + AC $$

- **Associative (Matrix Multiplication)**:  
  $$ A(BC) = (AB)C $$

- **Non-Commutative (Matrix Multiplication)**:  
  $$ AB \neq BA $$

---

## Day 2: Advanced Concepts

### Matrix Inverses
- **Matrix Inverse** (for square matrix \( A \)):  
  $$ A^{-1}A = I = AA^{-1} $$

  - \( A \) is invertible if and only if \( \det(A) \neq 0 \).

---

### Determinants
- **Determinant of a 2x2 Matrix**:  
  $$ \det(A) = \begin{vmatrix} a & b \\ c & d \end{vmatrix} = ad - bc $$

- **Properties of Determinants**:
  - $$ \det(AB) = \det(A)\det(B) $$
  - $$ \det(A^T) = \det(A) $$
  - If \( \det(A) = 0 \), then \( A \) is not invertible.

---

### Eigenvalues and Eigenvectors
- **Eigenvalue Equation**:  
  $$ A\mathbf{v} = \lambda \mathbf{v} $$

  - \( \lambda \) is an eigenvalue and \( \mathbf{v} \) is an eigenvector of \( A \).

  - To find eigenvalues, solve:  
    $$ \det(A - \lambda I) = 0 $$

  - To find eigenvectors, solve:  
    $$ (A - \lambda I)\mathbf{v} = 0 $$

---

### Diagonalization
- A matrix \( A \) is diagonalizable if there exists a matrix \( P \) such that:  
  $$ A = PDP^{-1} $$

  where \( D \) is a diagonal matrix of eigenvalues, and the columns of \( P \) are the eigenvectors of \( A \).

---

### Practice Problems
- Focus on mixed problems that combine concepts like determinants, eigenvalues, and projections.
