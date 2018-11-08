# Tensor
The central unit of data in TensorFlow.js is the tensor: a set of numerical values shaped into an array of one or more dimensions. A Tensor instance has a shape attribute that defines the array shape (i.e., how many values are in each dimension of the array).

The primary Tensor constructor is the tf.tensor function:

~~~~// 2x3 Tensor
const shape = [2, 3]; // 2 rows, 3 columns
const a = tf.tensor([1.0, 2.0, 3.0, 10.0, 20.0, 30.0], shape);
a.print(); // print Tensor values
// Output: [[1 , 2 , 3 ],
//          [10, 20, 30]]

// The shape can also be inferred:
const b = tf.tensor([[1.0, 2.0, 3.0], [10.0, 20.0, 30.0]]);
b.print();
// Output: [[1 , 2 , 3 ],
//          [10, 20, 30]]
~~~~
###### In TensorFlow.js, tensors are immutable; once created, you cannot change their values. Instead you perform operations on them that generate new tensors.

# Variables
Variables are initialized with a tensor of values. Unlike Tensors, however, their values are mutable. You can assign a new tensor to an existing variable using the assign method:

~~~~
const initialValues = tf.zeros([5]);
const biases = tf.variable(initialValues); // initialize biases
biases.print(); // output: [0, 0, 0, 0, 0]

const updatedValues = tf.tensor1d([0, 1, 0, 1, 0]);
biases.assign(updatedValues); // update values of biases
biases.print(); // output: [0, 1, 0, 1, 0]
~~~~
Variables are primarily used to store and then update values during model training.

# Operations (Ops)
TensorFlow.js provides a wide variety of ops suitable for linear algebra and machine learning that can be performed on tensors. Because tensors are immutable, these ops do not change their values; instead, ops return new tensors.

Example:
~~~~
const d = tf.tensor2d([[1.0, 2.0], [3.0, 4.0]]);
~~~~
List of operations are:
`const d_squared = d.square();`

>const e = tf.tensor2d([[1.0, 2.0], [3.0, 4.0]]);
>const f = tf.tensor2d([[5.0, 6.0], [7.0, 8.0]]);

>const e_plus_f = e.add(f);
