# Grover's Algorithm to Solve Sudoku $n^2 \times n^2$ when $n = 2$

## Overview
This notebook presents a complete, executable Python code using IBM's Qiskit framework to solve an exact $2^2 \times 2^2$ (4x4) Sudoku board. It demonstrates the application of Grover's algorithm to a combinatorial problem, encoding Sudoku constraints into a quantum oracle to find the unique solution.

## Sudoku Instance
The specific 4x4 Sudoku puzzle being solved is:

```
[1, 2, 3, 4]
[3, 4, 1, 2]
[2, 3, 4, 1]
[4, 1, 0, 0]
```

Where '0' represents an empty cell. The algorithm is designed to find the values for `instance[3][2]` and `instance[3][3]`, which are expected to be 2 and 3 respectively. This corresponds to the quantum state $|1001\rangle$ (using a 2-qubit representation for each number: `00`->1, `01`->2, `10`->3, `11`->4, and reading qubits right-to-left).

## Setup and Installation
To run this notebook, you need to install the following Python libraries:

```bash
!pip install qiskit
!pip install qiskit-aer
!pip install pylatexenc # Needed for circuit drawing using Matplotlib
```

## How it Works

### 1. Prepare Sudoku Instance and Define Basic Parameters
The `prepare_sudoku_instance()` function sets up the 4x4 board. Parameters `n=2` and `N=4` are defined for the $2^2 \times 2^2$ board.

### 2. Identify Empty and Given Cells
The code iterates through the Sudoku board to identify empty cells (where values need to be found) and given cells (pre-filled values). This determines the number of variable qubits and constraints.

### 3. Extract Sudoku Constraints (Clauses)
Constraints are generated based on Sudoku rules:
-   **Empty vs. Given:** An empty cell's value cannot conflict with a given cell's value in the same row, column, or 2x2 block.
-   **Empty vs. Empty:** Two empty cells in the same group cannot have the same value.
These clauses are fundamental for constructing the quantum oracle.

### 4. Setup Quantum Registers and Circuit Initialization
Quantum registers are defined for:
-   `var_qubits`: Qubits representing the possible values for empty cells (2 qubits per empty cell).
-   `clause_qubits`: Qubits to store the result of each Sudoku constraint.
-   `and_ancillas`: Ancillary qubits for multi-qubit logical AND operations.
-   `out_qubit`: Output qubit for phase kickback.
-   `cbits`: Classical bits for measurement results.
A `QuantumCircuit` is initialized with these registers.

### 5. Initialize Quantum State
Hadamard gates are applied to `var_qubits` to create a uniform superposition. The `out_qubit` is initialized to the $|-\rangle$ state (X gate followed by Hadamard) for phase kickback.

### 6. Define the Oracle Function (`apply_oracle`)
The oracle marks valid Sudoku solutions by applying a phase shift. It implements:
-   **Empty != Given Constraints:** Checks for conflicts between empty and given cells.
-   **Empty != Empty Constraints:** Checks for conflicts between empty cells.
-   **Logical AND:** Combines all clause results. If all are satisfied, the `out_qubit`'s phase is flipped.
-   **Uncomputation:** Reverses operations to reset ancillary qubits.

### 7. Define the Diffuser Function (`apply_diffuser`)
The diffuser amplifies the amplitudes of marked states (solutions) and diminishes unmarked states. It involves Hadamard gates, X gates, a multi-controlled Z gate (MCZ), and then reverses the X and Hadamard gates.

### 8. Apply Grover Iterations
The oracle and diffuser are repeatedly applied.

### 9. Measure and Execute the Circuit
`var_qubits` are measured and mapped to `cbits`. The `AerSimulator` runs the circuit, and `plot_histogram` visualizes the measurement outcomes, showing the probability distribution of solutions.

### 10. Interpret and Display the Sudoku Solution
The measurement with the highest count is selected as the solution. The binary string from this measurement is converted back to numbers (1-4) and filled into a copy of the initial Sudoku instance to display the solved board.

## Circuit Visualization
The notebook includes commands to visualize the quantum circuit in two ways:
-   **ASCII Text:** `print(qc.draw())` provides a console-based representation.
-   **Matplotlib Plot:** `qc.draw(output='mpl')` generates a graphical, detailed layout of the gates and qubits.

## Conclusion
This notebook effectively demonstrates Grover's algorithm's capability to solve a 4x4 Sudoku puzzle. By encoding the puzzle's rules into a quantum oracle and using iterative amplitude amplification, the quantum circuit efficiently identifies the correct values for the empty cells, showcasing quantum computing's potential for combinatorial problem-solving.

## References

*   Czelusta, G., Verma, D. R., & Wanjalkar, G. (2024). Grover's algorithm on two-way quantum computer. arXiv. https://doi.org/10.48550/arxiv.2406.09450.
*   Jones, D., & Varcoe, B. (2022). A Method for Application of a Quantum Search Algorithm to Classical Databases. arXiv. https://doi.org/10.48550/arxiv.2206.03938.

This notebook demonstrates how to solve a 4x4 Sudoku puzzle using Grover's algorithm within IBM's Qiskit framework. It covers the setup of the Sudoku instance, identification of empty and pre-filled cells, and extraction of Sudoku constraints (clauses). The quantum circuit is initialized with variable, clause, ancillary, and output qubits. The core of the algorithm involves defining an oracle function to mark valid Sudoku solutions by phase shifting, and a diffuser function to amplify these marked states. These functions are applied iteratively, and the circuit is then executed on a simulator. Finally, the measurement results are interpreted to display the solved Sudoku board. The notebook also includes visualizations of the quantum circuit in both ASCII and Matplotlib formats.

Key steps include:
1.  **Preparation:** Setting up the 4x4 Sudoku board and defining parameters.
2.  **Identification:** Distinguishing between empty and given cells.
3.  **Constraints:** Generating clauses for conflicts between empty and given cells, and between empty cells themselves.
4.  **Circuit Setup:** Defining quantum registers and initializing the quantum circuit.
5.  **State Initialization:** Creating a uniform superposition and setting the output qubit for phase kickback.
6.  **Oracle:** Implementing Sudoku rules to mark valid solutions.
7.  **Diffuser:** Amplifying marked solutions and diminishing unmarked ones.
8.  **Grover Iterations:** Repeatedly applying the oracle and diffuser.
9.  **Execution & Measurement:** Running the circuit on a simulator and obtaining measurement results.
10. **Interpretation:** Converting the most probable measurement into the solved Sudoku board.

The notebook concludes by showcasing the potential of quantum computing for combinatorial problem-solving with relevant references.
