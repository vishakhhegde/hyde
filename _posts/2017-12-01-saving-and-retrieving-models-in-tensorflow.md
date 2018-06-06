Let's say you have initialised a session, have created a graph of your neural network in TensorFlow and would like to save it, along with all the variables and weights you have trained so far. This is important if you want to control the way you perform your training, and obviously while running inference at a later point in time.

To save variables (or weights of the network), we use checkpoints, whereas to save the graph structure (operations, variable names etc.) we can use meta-graphs (people also use protobufs for saving graphs. They are usually .pb files). 

#### Saving your model - parameters and graph structure

##### Initialize a saver:  
```python
saver = tf.train.Saver(tf.global_variables())
```
Make sure that you use initialise the saver after the full graph is defined (in case you want to store the full graph). Otherwise, it will only save variables defined up until now in the graph. You can also save only the variables you need by replacing ''tf.global_variables()' with the list of variables you want to save.

##### Save all weights in a checkpoint file and all meta-data in the meta graph:
```python
saver.save(sess, '<checkpoint_name>', global_step)
```
This automatically saves the meta-graph in `<checkpoint_name>.meta` file. i.e. it saves the graph structure with all the nodes, variables and tensors defining the graph. It will also save all the parameters (also called weights) of the variables in '<checkpoint.data>' file.

#### Retrieving your model and graph:
- You have created a session named `sess`
- The name of your metagraph is `<checkpoint_name>.meta`

##### Get the graph first:
```python
new_graph = tf.train.import_meta_graph(<checkpoint_name>.meta)
```

This loads up all the variables, tensors and nodes defined by the graph into the current session. The variables have not been loaded with the values yet. This happens with the following line of code:
##### Restore the weights of your neural network:
```python
new_graph.restore(sess, '<checkpoint_name>')
```
It restores all the weights of the variables in the meta-graph that are also in the current session.

Cross-check that you have imported the right graph:
If you want to verify that you have indeed imported the graph completely, you can print out all the trainable variables:
```python
print(tf.trainable_variables())
```

If you find this page useful, feel free to tweet about it and cc me [@vishakhhegde](https://twitter.com/vishakhhegde). Feel free to contact me otherwise.
