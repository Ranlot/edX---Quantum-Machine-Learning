// This code performs a distance-based classification
// of the Iris flower sample #36. Expected class: 0.
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

//--- LOADING THE INPUT VECTOR ---
//[0.053 , 0.999] -> class 0

cx q[0],q[2];
u3(-3.0357101997648965/2,0,0) q[2];
cx q[0],q[2];
u3(3.0357101997648965/2,0,0) q[2];

barrier q;

// flip the ancilla qubit
x q[0];

barrier q;

//--- LOADING FIRST TRAINING VECTOR ---
// [0,1] -> class 0
// we can load this simply with a Toffoli gate

// the IBM simulator supports Toffoli gates
// directly like this: ccx q[0],q[1],q[2];
// but for the actual hardware we need
// to decompose the Toffoli gate:
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
x q[1];

barrier q;

//--- LOADING SECOND TRAINING VECTOR ----
// [ 0.78861006 0.61489363 ] -> class 1

//decomposed Toffoli
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

//decomposed Toffoli
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

barrier q;

// at this point the second training vector is loaded

// swap class and data qubits
// to match the connectivity of the ibmqx2 chip
cx q[3],q[2];
h q[2];
h q[3];
cx q[3],q[2];
h q[2];
h q[3];
cx q[3],q[2];

//NOW: class => q[2] and data => q[3]

barrier q;

//flip the class label for training vector #2
cx q[1],q[2];

barrier q;

// --- THE CORE ALGORITHM ---
//interfere the input vector with the training vectors
h q[0];

barrier q;

// measure all 4 qubits
measure q[0] -> c[0];
measure q[1] -> c[1];
measure q[2] -> c[2];
measure q[3] -> c[3];
