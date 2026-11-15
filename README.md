# Exploring Quantum Accuracy and Noise

A project by Leo, Muhammad, Myra, and Nahry for USCxIBM Qiskit Fall Fest 2026.

**Main prompt:** Beginner Quantum Error Mitigation  
**Challenge:** Hamiltonian Simulation

## What we wanted to explore

A quantum circuit can give a more accurate answer in an ideal simulation
but perform worse on a real processor. More operations can improve the
approximation while also creating more opportunities for noise.

We wanted to understand that tradeoff and test whether error mitigation
could help. We used the Hamiltonian simulation challenge as our application
and zero-noise extrapolation as our mitigation method.

## What we built

We built a Google Colab notebook using Qiskit to simulate four interacting
spins. We compared first-order and second-order Trotter formulas against
an exact classical calculation, then introduced simulated gate noise.

We also added interactive sliders to explore how changing the number of
Trotter steps affects accuracy, circuit depth, and gate count.

Finally, we ran circuits on an IBM quantum processor to see how the
tradeoff appeared on actual hardware.

## Our simulation

We used a four-spin Ising chain with open boundaries:

- Interaction strength: J = 0.2
- Transverse field: h = 1.0, alpha = 0
- Initial state: |0110⟩
- Final evolution time: 1.6

Our main observable was the average neighboring ZZ correlation. Matching
neighboring measured bits contribute +1, while different bits contribute -1.

At four Trotter steps, the second-order formula improved ideal state
accuracy but used 52 CNOT gates instead of 28. Under our simulated noise
model, its state accuracy became worse than the first-order formula.

## Error mitigation

We tested zero-noise extrapolation, or ZNE, by evaluating the same circuit
at three simulated noise scales: 1, 2, and 3. We fitted a line to those
noisy correlation values and extrapolated to zero noise.

For the four-step circuit, the correlation error against the noise-free
Trotter reference decreased from **0.04350040** to **0.00654800**.
That is approximately an **84.9% reduction** for this observable under
our simulated-noise model.

The reference answer was used to evaluate the result, not to fit the line.

## Real hardware results

We submitted first-order circuits with 2, 4, and 8 Trotter steps together
on **ibm_marrakesh**, using **1,024 shots per circuit**.

| Steps | Compiled depth | Two-qubit gates | Hardware correlation | Absolute error vs exact |
|---|---:|---:|---:|---:|
| 2 | 90 | 22 | -0.246094 | 0.084672 |
| 4 | 130 | 34 | -0.152344 | 0.178422 |
| 8 | 210 | 58 | 0.013672 | 0.344437 |

The exact correlation was approximately **-0.330766**.

More steps improved the ideal approximation, but measured hardware accuracy
got worse as the circuits grew. Two steps gave the smallest observed
correlation error among the three settings in this batch.

Hardware comparison job: `dbsjpuhabdes73fr86eg`

We also completed a separate four-step validation run:
`dbsjn1gk09pc73b08i30`.

## What we learned

Improving the mathematical approximation does not automatically improve
the answer from a noisy processor. Circuit cost matters, and mitigation
can help recover a more accurate estimate in the conditions we tested.

Our ZNE experiment used simulated noise. We did not apply ZNE to the
hardware measurements, and the 84.9% improvement does not describe
hardware fidelity.

The simulated ZNE results use density-matrix expectations without
finite-shot sampling. Hardware error bars describe sampling uncertainty
and exclude systematic device bias and drift.

These results cover one small model, a selected observable, and one
hardware comparison batch. They do not establish a universal best step
count. A next step would be to repeat the experiments and test ZNE
directly on hardware.

## Tools

Python, Qiskit, Qiskit Algorithms, Qiskit Aer, IBM Quantum Runtime,
NumPy, SciPy, pandas, Matplotlib, ipywidgets, and Google Colab.
