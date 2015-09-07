---
title: 
layout: default
---

# A Beginner's Guide to Recurrent Networks and LSTMs

The purpose of this post is to give students of neural networks an intuition about the functioning of recurrent networks and purpose and structure of a prominent variation, LSTMs.

Recurrent nets are a type of artificial neural network designed to recognize patterns in sequences of data, such as text, handwriting, the spoken word, or numerical times series data emanating from sensors, stock markets and government agencies.

To understand them, first you have to understand the basics of feedforward nets. Both of these networks are named after the way they channel information through a series of mathematical operations performed by the nodes of the network. One feeds information straight through, the other cycles it through a loop.

In the case of feedforward networks, input examples are fed to the network and transformed into an output; in the case of supervised learning, the output is a label. That is, they map raw data to categories, recognizing patterns that signal, for example, that an input image should be labeled "cat" or "elephant." 

A feedforward network is trained on labeled images until it minimizes the error it makes when guessing their categories. With the trained set of parameters, it sallies forth to categorize data is has never seen. A trained feedforward network can be exposed to any random collection of photographs, and the first photograph it is exposed to will not necessarily alter how it classifies the second. 

![Alt text](../img/feedforward_rumelhart.png)

That is, it has no notion of order in time, and the only input it considers is the current example it has been exposed to. Feedforward networks are amnesiacs regarding their recent past; they remember nostalgically only the formative moments of training. 

Recurrent networks, on the other hand, take as their input not just the current input example they see, but also what they perceived one step back in time. Here's a diagram of an early, [simple recurrent net proposed by Elman](https://web.stanford.edu/group/pdplab/pdphandbook/handbookch8.html), where *BTSXPE* represents the input example and the current moment, and *Context* represents the output of the previous moment. 

![Alt text](../img/srn_elman.png)

The decision a recurrent net reached at time step t-1 affects the decision it will reach one moment later at time step t. So recurrent networks have two sources of input, the present and the recent past, which combine to determine how they respond to new data, much as in life. 

They are distinguished from feedforward networks by that feedback loop, ingesting their own outputs moment after moment as input. It is often said that recurrent networks have memory.[1](#one) Adding memory to neural networks has a purpose: There is information in the sequence itself, and recurrent nets use it to perform tasks that feedforward networks can't. 

That sequential information is preserved in the recurrent network's hidden state, which manages to span many time steps as it cascades forward to affect the processing of each new example. 

What is human memory but information circulating invisibly within a body, affecting our behavior without revealing its full shape. The English language is full of words the describe the feedback loops of memory. When we say a person is haunted by their deeds, for example, we are simply talking about the consequences that past outputs wreak on present time. The French call this "*Le passé qui ne passe pas*," or "The past that does not pass away."

We'll describe the process of carrying memory forward mathematically:

    h_t = f(Wx_t + Uh_t−1)

The hidden state at time step t is h_t. It is a function of the input at the same time step x_t, modified by a weight matrix W (like the one we used for feedforward nets) added to the hidden state of the previous time step h_t-1 multiplied by its own hidden-state-to-hidden-state matrix U. The weight matrices are filters that determine how much importance to accord to both the present input and the past hidden state. The error they generate will return via backpropagation and be used to adjust their weights until error can't go any lower.

The sum of the weight input and hidden state is squashed by the function φ -- either a logistic sigmoid function or tanh, depending -- which is a standard tool for condensing very large or very small values into a logistic space, as well as making gradients workable for backpropagation. 

Because this feedback loop occurs at every time step in the series, each hidden state contains traces not only of the previous hidden state, but also of all those that preceded h_t-1 for as long as memory can persist.

Remember, the purpose of recurrent nets is to accurately classify sequential input. We rely on the backpropagation of error and gradient descent to do so. Backpropagation in feedforward networks moves backward from the final error through the outputs, weights and inputs of each hidden layer, assigning those weights responsibility for a portion of the error by calculating their partial derivatives -- ∂E/∂w, or the relationship between their rates of change -- which are then used by our learning rule, gradient descent, to adjust the weights in a way that decreases error. 

Recurrent networks rely on an extension of backpropagation called [backpropagation through time](https://www.cs.cmu.edu/~bhiksha/courses/deeplearning/Fall.2015/pdfs/Werbos.backprop.pdf), or BPTT. Time, in this case, is simply expressed by a well-defined series of calculations, which is all backpropagation needs to work. 

## Vanishing (and Exploding) Gradients

Like most neural networks, recurrent nets are old. By the early 1990s, the vanishing gradient problem emerged as a major obstacle to recurrent net performance. Just as a straight line expressed the change in x alongside the change in y, the gradient expresses the change in all weights with regard to the change in error. If we can't know the gradient, we can't adjust the weights in a direction that will decrease error, and our network has ceased to learn.

Recurrent nets seeking to establish connections between an output and events many time steps away were hobbled, because it was very difficult to know how much importance to accord to remote inputs. Information flowing through neural nets passes through many stages of multiplication. 

Everyone who has studied compound interest knows that any quantity multiplied frequently by an amount greater than one can become immeasurably large (indeed, that mathematical truth underpins network effects and social inequalities). But its inverse, multiplying by a quantity less than one, is also true.

Because the layers and time steps of deep neural networks relate to each other through multiplication, derivatives are susceptible to vanishing or exploding. 

Exploding gradients treat every weight as though it were the butterfly flapping its wings, which will cause a distant hurricane. Those weights' gradients become saturated on the high end; they are presumed to be too powerful. But exploding gradients can be solved relatively easily, because they can be truncated or squashed, while vanishing gradients can become too small for computers to work with or for networks to learn. 

Below you see the effects of applying a sigmoid function over and over again. The data is flattened until, for large stretches, it has no detectable slope. This is analogous to a gradient vanishing as it passes through many layers. 

![Alt text](../img/sigmoid_vanishing_gradient.png)

## Long Short-Term Memory Units (LSTMs)

In the mid-90s, a variation of recurrent net with so-called Long Short-Term Memory units, or LSTMs, was proposed by the German researchers Sepp Hochreiter and Juergen Schmidhuber as a solution to the vanishing gradient problem. 

LSTMs help preserve the error that can be backpropagated through time and layers. By maintaining a more constant error, they allow recurrent nets to continue to learn over many time steps (over 1000), thereby opening a channel to link causes and effects remotely. 

LSTMs contain information outside the normal flow of the recurrent network in a gated cell. Information can be stored in, written to, or read from a cell, much like data in a computer's memory. The cell makes decisions about what to store, and when to allow reads, writes and erasures, via gates that open and close. 

Those gates act on the signals they receive, and similar to the neural network's nodes, they block or pass on information based on its strength and import, which they filter with their own sets of weights. Those weights, like the weights that modulate input and hidden states, are adjusted via the recurrent networks learning process. That is, the cells learn when to allow data to enter, leave or be deleted through the iterative process of making guesses, backpropagating error, and adjusting weights via gradient descent.

The diagram below illustrates how data flows through a memory cell and is controlled by its gates.

![Alt text](../img/gers_lstm.png)

There are a lot of moving parts here, so if you are new to LSTMs, don't rush this diagram, contemplate it. 

The arrows show where data flows into the cell at multiple points. The combination of present input and past cell state is fed not only to the cell itself, but also to each of its three gates, which will decide how that input will be handled. The black dots are the gates themselves, which determine respectively whether to let new input in, erase the present cell state, and/or let that state impact the networks output at the present time step. S_c is the current state of the memory cell, and g_y_in is the current input to it.   

It's important to note that LSTMs' memory cells, rather than modifying their state through multiplication, do so by addition. Stupidly simple as it may seem, this basic change helps them preserve a constant error when it must be backpropagated at depth. That plus sign is essentially the secret of LSTMs. Instead of determining the subsequent cell state by multiplying its current state with new input, they add the two, and that quite literally makes the difference. 

Different sets of weights filter the input for input, output and forgetting. The forget gate is represented as a linear identity function, because if the gate is open, the current state of the memory cell is simply multiplied by one, to propagate forward one more time step. 

Furthermore, while we're on the topic of simple hacks, [including a bias of 1](http://jmlr.org/proceedings/papers/v37/jozefowicz15.pdf) to the forget gate of every LSTM cell is also shown to [improve performance](http://www.felixgers.de/papers/phd.pdf). 

You may wonder why LSTMs have a forget gate when their purpose is to link distant occurrences to a final output. Well, sometimes it's good to forget. If you're analyzing a text corpus and come to the end of a document, for example, you may have no reason to believe that the next document has any relationship to it whatsoever, and therefore the memory cell should be set to zero before the net ingests the first element of the next document. 

## Diverse Time Scales

You may also wonder what the precise value is of input gates that protect a memory cell from new data coming in, and output gates that prevent it from affecting certain outputs of the RNN. You can think of LSTMs as allowing a neural network to operate on different scales of time at once.

Let's take a human life, and imagine that we are receiving various streams of data about that life in a time series. Geolocation at each time step is pretty important for the next time step, so that scale of time is always open to the latest information. 

Perhaps this human is a diligent citizen who votes every couple years. On democratic time, we would want to pay special attention to what they do around elections, before they return to making a living, and away from larger issues. We would not want to let the constant noise of geolocation affect our political analysis. 

If this human is also a diligent daughter, then maybe we can construct a familial time that learns patterns in phone calls which take place regularly every Sunday and spike annually around the holidays. Little to do with political cycles or geolocation. 

Other data is like that. Music is polyrhythmic. Text contains recurrent themes at varying intervals. Stock markets and economies experience jitters within longer waves. They operate simultaneously on different time scales that LSTMs can capture. 

A gated recurrent unit (GRU) is simply an LSTM without an output gate, which therefore fully writes the contents from its memory cell to the larger net at each time step. 

## Code Sample

A [commented example of a Graves LSTM](https://github.com/deeplearning4j/dl4j-0.4-examples/blob/master/src/main/java/org/deeplearning4j/examples/rnn/GravesLSTMCharModellingExample.java), implemented with Deeplearning4j, can be found here. 

### Resources
* [Gated Feedback Recurrent Neural Networks](http://arxiv.org/pdf/1502.02367v4.pdf)
* [Recurrent Neural Networks](http://people.idsia.ch/~juergen/rnn.html); Juergen Schmidhuber
* [Modeling Sequences With RNNs and LSTMs](https://class.coursera.org/neuralnets-2012-001/lecture/77); Geoff Hinton
* [The Unreasonable Effectiveness of Recurrent Neural Networks](https://karpathy.github.io/2015/05/21/rnn-effectiveness/); Andrej Karpathy

<a name="one">1)</a> *All neural networks whose parameters have been optimized have memory in a sense, because those parameters are the traces of past data. But in feedforward networks, that memory may be frozen in time. That is, after a network is trained, the model it learns may be applied to more data without further adapting itself. In addition, it is monolithic in the sense that the same memory (or set of weights) is applied to all incoming data. Recurrent networks, which also go by the name of dynamic (translation: "changing") neural networks, are distinguished from feedforward nets so much by having memory as by giving particular weight to events that occur in a series. While those events do not  need to follow each other immediately, they are presumed to be linked, however remotely, by the same temporal thread. Feedforward nets do not make such a presumption. They treat the world as a bucket of objects without order or time. It may be helpful to map two types of neural network to two types of human knowledge. When we are children, we learn to recognize colors, and we go through the rest of our lives recognizing colors wherever we see them, in highly varied contexts and independent of time. We only had to learn it once. That knowledge is like memory in feedforward nets; they rely on a past without scope, undefined. Ask them what colors they were fed five minutes ago and they don't or care. They are short-term amnesiacs. On the other hand, we also learn as children to decipher the flow of sound called language, and the meanings we extract from sounds such as "toe" or "roe" or "z" are always highly dependent on the sounds preceding (and following) them. Each step of the sequence builds on what went before, and meaning emerges from their order. Indeed, whole sentences conspire to convey the meaning of each syllable within them, their redundant signals a protection against ambient noise. That is similar to the memory of recurrent nets, which look to a particular slice of the past for help. Both types of nets bring the past, or different pasts, to bear in different ways.* 