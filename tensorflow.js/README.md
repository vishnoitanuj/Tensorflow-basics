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

~~~~
const e = tf.tensor2d([[1.0    , 2.0], [3.0, 4.0]]);
const f = tf.tensor2d([[5.0, 6.0], [7.0, 8.0]]);

const e_plus_f = e.add(f);
~~~~

# Memory Management: dispose and tf.tidy
Because TensorFlow.js uses the GPU to accelerate math operations, it's necessary to manage GPU memory when working with tensors and variables.

TensorFlow.js provide two functions to help with this: dispose and tf.tidy.

### dispose
You can call dispose on a tensor or variable to purge it and free up its GPU memory:

~~~~
const x = tf.tensor2d([[0.0, 2.0], [4.0, 6.0]]);
const x_squared = x.square();

x.dispose();
x_squared.dispose();
~~~~
### tf.tidy
Using dispose can be cumbersome when doing a lot of tensor operations. TensorFlow.js provides another function, tf.tidy, that plays a similar role to regular scopes in JavaScript, but for GPU-backed tensors.

tf.tidy executes a function and purges any intermediate tensors created, freeing up their GPU memory. It does not purge the return value of the inner function.

~~~~
// tf.tidy takes a function to tidy up after
const average = tf.tidy(() => {
  // tf.tidy will clean up all the GPU memory used by tensors inside
  // this function, other than the tensor that is returned.
  //
  // Even in a short sequence of operations like the one below, a number
  // of intermediate tensors get created. So it is a good practice to
  // put your math ops in a tidy!
  const y = tf.tensor1d([1.0, 2.0, 3.0, 4.0]);
  const z = tf.ones([4]);

  return y.sub(z).square().mean();
});

average.print() // Output: 3.5

~~~~
Using tf.tidy will help prevent memory leaks in your application. It can also be used to more carefully control when memory is reclaimed.

######### Two important notes
1) The function passed to tf.tidy should be synchronous and also not return a Promise. We suggest keeping code that updates the UI or makes remote requests outside of tf.tidy.

2) tf.tidy will not clean up variables. Variables typically last through the entire lifecycle of a machine learning model, so TensorFlow.js doesn't clean them up even if they are created in a tidy; however, you can call dispose on them manually.