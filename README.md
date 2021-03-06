# MoLE-Final-project
### Sophia Schulze-Weddige & Malin Spaniol WS 18/19
Artificial neural network to model iterated learning in language evolution 

## Background
This is the final project for the course "Models of Language Evolution", offered by Michael Franke at the winter term 18/19 at the University of Osnabrück. In the scope of this final project, our aim is to model iterated learning by implementing a neural network. 

When conducting a literature search on "iterated learning" and "neural networks", we were surpirised how few useful papers we found about the topic. Many results refered to iteratred learning in the sense of recurrent neural networks, but not aiming to model language evolution or language development, but to process text comprehension, or to produce text. Other findings refered to the use of neural networks in "iterative learning control systems", which refers to an approach for a control system used in engineering, and is not applicable to us at all. 

The current work is inspired by the iterated learning approach of Simon Kirby and James R. Hurford (2002), Smith (2014), Kirby and Smith (2014) and Smith (2003), which were found to be useful to some degree. In the different papers, they make use of the implementation of neural networks in order to model iterated learning. The network structures of the different papers vary slightly, however, the idea behind it is the same. Still, none of the papers is really new, and none of them includes access to a full implementation. 
Initially, we planned a quite deep and complex itearted learning implementation, but as we saw that no implementations for basic approaches on neural networks for iteratred learning are available online, and no examination of the basic structure of e.g. Kirby and Hurford 2002 has been made yet, we decided to focus mainly on their paper, for the current work. We used their theoretical description to apply it to an implementation. 

Kirby and Hurford explain that from an evolutionary perspective it is interesting to understand how human language, in which each utterance carries a semantic content and conveyes information about its own construction, could have evolved. 
In this context, they explain that language emerges at the intersection of learning, cultural evolution and biological evolution (Kirby at al. 2002). With the term "biological evolution", they refer to learning and processing mechanisms with which humans have been equipped with. Biological evolution happens through changes in inhterited traits over successive generations. With the term "cultural evolution", they refer to changes which are not inherited by individuals of a population, but changes such as meaning shifts, or phonological and syntactic adjustments in the ruleset of a language. 

The authors stress that the evolution of language depends on social learning and cultural transmission in addition to biological evolution. In the literature about language evolution, the “cultural evolution” part is often modeled by “iterated learning”. This process descripes the learning process when there is a teacher and a learner, and the output of a teacher is the input to the next learner, and once the learner has learned, he becomes a new teacher and his output becomes the new input of a new learner. 

Previous studies using iterated learning in the context of language evolution demonstrated that transmitted behaviour become easier to learn and increasingly structured. It is shown that if an incomplete simulutsset is available to the learner, linguistic structure emerges. This development is considered to be cumulative and non-intentional. So to say, the literature states that the languages adaption to stimulus poverty is compositionality. 

The Iterated Learning Model (ILM) which Kirby and Hurford introduce, aims to model the structure of the mapping from meanings to signals, and the other way around. By this, they want to explain the emergence of linguistic structure. Their ILM consists of the 4 components: meaning space, signal space, language-learning agents and language-using adult agents. 

During each iteration of the ILM, an adult agent is given a set of randomly chosen meanings to produce signals for. The resulting meaning-signal pairs are used as training data for another learning agent. The learning agent has a learning period and then becomes an adult himself. This process is repeated several thousand times, or until a stable point attractor in the dynamical system is reached (Kirby et al. 2002). 

In the mentioned paper (2002), Kirby and Hurford use simple neural networks in order to model learners and show the process of iterated learning.

Our aim is now to repuduce their study. We want to implement neural networks with the same network architecture as used in the paper and see if we can replicate the same results. 

## Current work

### The simple neural network of Kirby and Hurford 
The simple iterated learning model of Kirby and Hurford (2002) uses one neural network for each language learning agent. As the model is very simplified, one agents exemplifies one whole generation. One agent consists of a feed-forward network with 8x8x8 structure, meaning that we have an input signal of 8 neurons, a hidden layer with 8 neurons and an output layer with 8 neurons. All neurons are fully connected.  
Each network maps from 8bit binary numbers representing a signal to 8-bit binary numbers representing a meaning. So to say, given an appropriate input, the agents can learn to parse utterances. 

Each network is unidirectional (therefore we need some production mechanism to map from meaning back to signals). For this, the authors assume an obverter strategy (2002). The underlying assumption is that the speaker's own mapping is similar to the hearer's one. The signal producing agent produces the signal which maximizes the chance that the hearer can understand the correct meaning. 

At first, the model is presented with a certain number of random signals chosen from the set of binary numbers between 00000000 to 11111111 (256 possible combinations). This number should be large enough to ensure that each meaning appears at least once. The output of this first run is saved in a lookup table, which will then be used to train the first generation of learners. This means a certain number (namely the bottleneck size) of signal-meaning pairs is randomly selected from the lookup table. The weights of the network are initialized randomly. After the training phase the weights are fixed and the model becomes an adult, which means it will now generate its own meanings for the different signals. Those meanings are again stored in a lookup table. Before initializing the model with new random weights (aka starting a new generation, aka creating a new language learner) the two lookup tables are compared in order to have a measure for the similarity of the two languages of consecutive generations. This process is repeated over and over again for as many generations as denoted.

Using a lookup table in the model replaces the computationally expensive obverter strategy and should yield results that are just as good, because when the agent generates a certain meaning for a signal this in turn would be the signal the agent would chose when trying to convey this particular meaning.

The set of signal-meaning pairs is used to train the hearer network using backpropagation of error learning algorithm, with a learning rate of 0.1, and no momentum term. 

Each learner is trained for 100 epochs. After that, the speaker is removed, and the previos hearer is the new speaker. A new hearer is added, which means that a new network is created with randomly initialized weights. The number of how often this cycle is repeated depents on the number of generation that should be modeled. In the paper, this number varies between 50 and 250. The size of the training set (number of utterences from which each network learns) also varies. In the figures, the authors show graphs for randomly chosen 20, 50, and 2000 meanings. 

For visualization, the authors show graphs for the expressivity and stability of the language over generations. This is represented by the difference between learners' and adults' languages after training and the meaning space which is covered by the language.

The authors claim that if the number of training examples is "not too small and not too large" (Kirby and Hurford 2002), a completely stable and completely expressive language is found. 

### Extras of our network

All given information about the neural network architecture was implemented by us. At some points, insufficient information was given about the implementation. Therefore, some additional decisions were made. The code in the project_spaniol_schulzeweddige.ipynb file is commented in a way, that all decisions are explained at every step, when they were made.

However, here we will summarize the most important decisions, too:

We decided to cut the training data into batches, in order to improve the time needed for computation and to avoid local minima. However, this should not have a significant effect on the results. 

Concerning the model, there was no information given about which activation functions were used for the output layer, nor for the hidden layer. Therefore, we chose to use a sigmoid function for the output layer. The weights in the output layer are randomly initialized using the He initializer, which depends on the number of neurons of the previous layer (see further explanation in the code). For the hidden layer, we use the tangens hyperbolicus, as it is a frequently used actiavtion function. The weights in the hidden layer are initialized using the Xavier initializer, which again depends on the number of neurons in the previous layer (or in our case the number of input neurons). The paper doesn't give any information about the initialization of the biases, therefore we use the golden standard of initializing the biases with zeros.

In the paper it is not mentioned specifically what kind of training loss estimation was used, as a goodness measure. We now use the Sigmoid Cross-Entropy loss, which is a Sigmoid activation plus a Cross-Entropy loss. Unlike Softmax loss, it is independent for each vector component (class), meaning that the loss is computed for every output vector component and is not affected by other component values. That’s why it is used for multi-label classification, were the insight of an element belonging to a certain class should not influence the decision for another class. In our case the classes are 0 and 1 and we want to decide for each position in the 8-bit vector, whether it should be 1 or a 0.

Kirby & Hurford mention that no momentum is used and that a learning rate is 0.1. This was kept, the optimizer we chose is a simple gradient descent.  


## Evaluation of results

As a measure of evaluation, we used the expressivity, by considering the covered meaning space and the stability of the language, considering the mean squared difference of signal-meaning-pairs between two consecutive generations.

We use the size of the meaning space as a measure for the expressivity of the learned language of each generation. It is calculated by counting the number of different meanings in the output table. For this, we plotted a graph for 20, 50 and 2000 utterances, (such as in the paper), showing the percentage of covered meaning space across the number of generations.
Further, we calculate the mean squared difference between the output tables giving the signal-meaning pairs of two consecutive generations, as a measure for stability of the language. Again, we plotted three graphs (for 20, 50 and 2000 utterances), which can be found in the "Original_Graphs" folder. 

All six graphs visualize, that there are huge fluctuations in our results, for all three cases (20, 50 and 2000 utterances). This means, that the covered meaning space and the mean squared difference between generations both fluctuate a lot and do not converge to a stable point. 

The authors showed in their study, that eventually, at some point, if the number of training exaples is "not too small and not too large" (Kirby & Hurfod 2002), a completely stable and completely expressive language is found. Our implementation did not reach this point. We did not reach a stable point at all, and in the most expressive generation, only 40% of the meaning space was covered. 

We don't know why this is the case, but the information about the implementation of the authors is quite vague, which leaves a lot of space for differences in the code. At first, we played a lot around at points in the implementation, which were left open by the authors, such as the activation functions, different measures for the standard deviations for the initialization of the random weights, normalization of the input or not, using differnt measures for calculating the difference between generations and so on. The result presented in the "Original_Graphs" folder and in the python file is the best result which we reached by this. From the calculation of the loss, we can see that each network/agent is able to "learn", as the loss values always start around 2 and sometimes go down as far as 0.2. Also the percentage of meaning space covered varies a lot, dependent on the previously mentioned changes.

In a second step, we explored the results further, by changing some of the parameters which were given by the authors. For the bottleneck size, we also used values such as 150 and 200, we let the network train up to 2000 generations, and we changed the number of epochs of training per generation from 100 to values between 200 and 1000. The most interesting graphs are given in the "Exploratory_Graphs" folder. For a larger number of training epochs per generation (and a bottleneck of 150), we see that the expressivity, as well as the stability go down to nearly zero, already before the 50th generation. For a bottleneck of 200 and an even larger number of training epochs per generation (e.g. 1000), this convergence towards zero happens even faster. The pattern of expressivity and stability for the case of a bottleneck of 50, and 200 epochs per generation looks more interesting. The plots are heavily flucutating, however, towards the 400th generation, one can observe, that the expressivity gets smaller and the stability better. When the measures are now again slightly changed, so that the bottleneck size is regulated a bit up (70), and the number of epochs as well as the number of generations stayes the same, it does not look as if there is a convergence pattern, but more random fluctuations. However, some of the differences can of course be more/less visible if the same parameters will be run again, as the weights are initialized randomly and might therefore lead to different results.

So what we could observe is that in general, our language gets more stable if the expressivity gets worse. This makes sense, because if there are fewer words included in our language, we expect that the differences between generations are shrinking aswell. Our goal was however, to reach the emergence of compositionality, and if that would arise, our expressivity would go up, while our language also gets more stable (which would mean that the graph goes down). So to say, we succeeded to play around with different parameters of our network, which also had a visible effect on the performance and dynamics across generations. However, we did not succeed to reach a stable and expressive language, such as we have hoped for. 


## Bibliography
Kirby, S., & Hurford, J. R. (2002). The Emergence of Linguistic Structure : An Overview of the Iterated Learning Model.

Kirby, S., Griffiths, T., & Smith, K. (2014). ScienceDirect Iterated learning and the evolution of language. Current Opinion in Neurobiology, 28, 108–114. https://doi.org/10.1016/j.conb.2014.07.014

Smith, K., Kirby, S. & Brighton, H. (2003). Iterated Learning : A Framework for the Emergence of Language, 386, 371–386.

Smith, A. D. M. (2014). Models of language evolution and change, 5(June). https://doi.org/10.1002/wcs.1285
