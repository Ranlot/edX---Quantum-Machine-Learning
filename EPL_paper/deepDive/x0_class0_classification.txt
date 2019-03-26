// This code performs a distance-based classification
// of the Iris flower sample #??. Expected class: 0.
// This code is written for the ibmqx2 (bowtie) device with 5 qubits.

// written by Mark Fingerhuth, 2016.

include "qelib1.inc";

// define 5 quantum and classical registers
qreg q[5];
creg c[5];

// ancilla => q[0]
// index => q[1]
// data => q[2]
// class => q[3]

// bring index and ancilla qubits into superposition
h q[0];
h q[1];

// barriers are used to visually separate
// different steps of the algorithm
barrier q;

// --- LOADING THE INPUT VECTOR ---
// [-0.549, 0.836] -> class 0

cx q[0],q[2];
u3(-4.30417579487669/2,0,0) q[2];
cx q[0],q[2];
u3(4.30417579487669/2,0,0) q[2];

barrier q;

// flip the ancilla qubit
// this 'moves' the input vector
// to the |0> state of the ancilla
x q[0];

barrier q;

// --- LOADING THE FIRST TRAINING VECTOR ---
// [0,1] -> class 0
// we can load this with a straightforward Toffoli

// no direct support for Toffoli gates
// so we need to decompose it:
h q[2];
cx q[1],q[2];
tdg q[2];
cx q[0],q[2];
t q[2];
cx q[1],q[2];
tdg q[1];
tdg q[2];
cx q[0],q[2];
cx q[0],q[1];
t q[0];
tdg q[1];
t q[2];
cx q[0],q[1];
s q[1];
h q[2];

barrier q;

// flip the index qubit
// (moves the first training vector to the |0> state of the index qubit)
x q[1];

barrier q;

// --- LOADING THE SECOND TRAINING VECTOR ---
// [0.78861, 0.61489] -> class 1

// another decomposed Toffoli
h q[2];
cx q[1],q[2];
tdg q[2];
cx q[0],q[2];
t q[2];
cx q[1],q[2];
tdg q[1];
tdg q[2];
cx q[0],q[2];
cx q[0],q[1];
t q[0];
tdg q[1];
t q[2];
cx q[0],q[1];
s q[1];
h q[2];

barrier q;

cx q[1],q[2];
u3(1.3245021469658966/4,0,0) q[2];
cx q[1],q[2];
u3(-1.3245021469658966/4,0,0) q[2];

barrier q;

// decomposed Toffoli
h q[2];
cx q[1],q[2];
tdg q[2];
cx q[0],q[2];
t q[2];
cx q[1],q[2];
tdg q[1];
tdg q[2];
cx q[0],q[2];
cx q[0],q[1];
t q[0];
tdg q[1];
t q[2];
cx q[0],q[1];
s q[1];
h q[2];

barrier q;

cx q[1],q[2];
u3(-1.3245021469658966/4,0,0) q[2];
cx q[1],q[2];
u3(1.3245021469658966/4,0,0) q[2];

// at this point the second training vector is loaded

barrier q;

// swap class and data qubits
// to match the connectivity of the ibmqx2 chip
cx q[3],q[2];
h q[2];
h q[3];
cx q[3],q[2];
h q[2];
h q[3];
cx q[3],q[2];

// NOW: class => q[2], data => q[3]

barrier q;

// flip the class label for training vector #2
cx q[1],q[2];

barrier q;

// THE CORE ALGORITHM
// interfere the input vector with the training vectors
h q[0];

barrier q;

// measure all 4 qubits that were involved in the algorithm
measure q[0] -> c[0];
measure q[1] -> c[1];
measure q[2] -> c[2];
measure q[3] -> c[3];
