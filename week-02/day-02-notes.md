# 🚀 Comprehensive Guide to Neural NLP with PyTorch: From N-Grams to Document Classification

This documentation provides an exhaustive breakdown of neural network architectures for text, focusing on representation, modelling, and the optimisation engine that drives performance.

---

## 1. 🔡 Text Representation: From Sparse to Dense

To process text, machine learning models require words to be converted into numerical features [1].

### 📍 One-Hot Encoding
*   **Definition**: Categorical data is converted into feature vectors where the vector dimension equals the vocabulary size [2].
*   **Structure**: For a specific token, the element at its index is set to `1`, while all others are `0` [2].
*   **Limitation**: These vectors are sparse and high-dimensional, making them computationally expensive [3].

### 👜 Bag of Words (BoW)
*   **Mechanism**: A document is represented as the **aggregate or average** of one-hot encoded vectors [4, 5].
*   **Nuance**: This representation ignores word order; for example, "I like cats" and "cats like I" would have identical vectors [6].

### 💎 Embeddings and Embedding Bags
*   **Embedding Layer**: Instead of sparse vectors, tokens are represented by **dense, lower-dimensional vectors** [3]. These weights are learned during training and form an **embedding matrix** where each row represents a unique word [3, 7].
*   **Embedding Bag**: This layer efficiently computes the sum or average of word embeddings for a document [5, 7].
    *   **Offsets**: In PyTorch, 1D tensors represent datasets. An **offset parameter** is required to identify the starting position of each document within that tensor [8, 9].
    *   **Computation**: Feeding a BoW vector into a hidden layer results in an output that is the sum of the embeddings [3, 5].

---

## 2. 🎡 The Mechanics of N-Gram Language Modelling

Language modelling involves predicting the next word in a sequence based on context [10].

### 🔍 Bi-grams vs. Tri-grams
*   **Bi-gram**: A conditional probability model where the context size is **one** (predicting word $t$ based on word $t-1$) [11, 12].
*   **Tri-gram**: Increases context size to **two**, using words $t-2$ and $t-1$ to predict word $t$ [12, 13].
*   **Generalisation**: An **n-gram model** allows for an **arbitrary context size** [14, 15].

### 🧠 Neural N-Grams
While traditional n-gram probabilities can become complex at scale, **neural networks approximate these probabilities** using functions like **Softmax** [14].
*   **The Context Vector**: In a neural n-gram model, the context vector is constructed by **concatenating embedding vectors** rather than adding them [6, 15].
*   **Input Dimensions**: The input layer dimension must be the **product of the context size and the embedding dimension** [16, 17].
*   **Sliding Window**: Training involves a **sliding window** that shifts across the text to create batches of context (input) and target (predicted) words [18-20].

---

## 3. 🏗️ Neural Network Architecture for Classification

A document classifier categorizes text by analyzing its content through a sequence of mathematical operations [21].

### 층 Layers and Neurons
*   **Input Layer**: Accepts the feature vector (e.g., BoW or Context Vector) [22, 23].
*   **Hidden Layer**: Performs matrix multiplication and adds a bias [22]. If it uses BoW, it functions as an **embedding layer** [22].
*   **Output Layer**: The number of neurons here **must equal the number of classes** (e.g., World, Sports, Business, Sci/Tech) [24, 25].
*   **Logits**: The raw scores output by the final layer before activation [22, 24, 26].

### 🛠️ Hyperparameters
Hyperparameters are **externally set configurations** [23, 27]:
*   Number of hidden layers.
*   Number of neurons per layer.
*   Vocabulary size and embedding dimensions.
*   These are often selected via **empirical validation** [23, 25].

---

## 4. 📉 Optimisation and Loss Function Minimisation

Training is the process of fine-tuning **learnable parameters ($\Theta$)** to minimize the discrepancy between predictions ($\hat{y}$) and actual labels ($y$) [28, 29].

### ⚖️ Loss Functions
*   **Cross-Entropy Loss**: Primarily used for classification. It compares the model's predicted conditional distribution to the true distribution [30-32].
*   **Softmax**: Transforms logits into **probabilities** by exponentiating and normalizing them, which enhances the distinction between class scores [14, 30, 33].
*   **KL Divergence**: A measure of the difference between two distributions; cross-entropy is the term within this calculation that depends on the parameters $\Theta$ [31].
*   **Monte Carlo Sampling**: When the true distribution is unknown, it is estimated by **averaging the function applied to a set of samples** [31, 34].

### 🏃 Gradient Descent (SGD)
*   **Mechanism**: Parameters are updated by moving in the **reverse direction of the gradient** of the loss function [35].
*   **Learning Rate ($\eta$)**: Sets the step size for updates [35].
*   **Scheduler**: Reduces the learning rate over time (scaled by a factor **Gamma**) to enhance optimization [36].
*   **Gradient Clipping**: A technique used to improve optimization stability [29].

---

## 5. 🛠️ Implementation Pipeline in PyTorch

A standard workflow for training a neural text model includes several critical phases:

### 📊 Data Preparation
1.  **Tokenization & Indexing**: Converting raw text into numerical indices [37, 38].
2.  **Data Splitting**: Partitioning the corpus into **Training** (for learning), **Validation** (for hyperparameter tuning), and **Test** (for evaluating real-world performance) sets [34, 36, 39, 40].
3.  **DataLoaders**: Set up with a specific **Batch Size** for gradient approximation; **shuffling** is essential for better optimization [41, 42].

### 🔄 The Training Loop
*   **Init Weights**: Initializing weights properly helps with optimization [41, 42].
*   **Zero Gradients**: All gradients must be reset to zero before a new iteration to avoid accumulation [36].
*   **Forward Pass**: The model makes a prediction and calculates the loss [29, 32].
*   **Backward Pass**: The `backward()` function computes the derivatives of the loss [29].
*   **Step**: The optimizer updates the parameters based on the gradients [40].
*   **Epochs**: The process repeats for a set number of full passes through the dataset [41].

### 🎯 Prediction and Evaluation
*   **Argmax**: This function identifies the **index of the highest logit**, indicating the most likely predicted class [27, 43, 44].
*   **KPIs**: In training, **loss is often prioritized over accuracy** as the key performance indicator [20, 45].
*   **Decoding**: The `index_to_token` mapping (often via `vocab.get_itos`) translates numerical outputs back into human-readable words [38, 45].

---

## 6. 🏗️ Architectural Foundations of Seq2Seq Models

### 📡 The Evolution of Sequence Representation
Sequence-to-sequence (Seq2Seq) models have revolutionized tasks such as **machine translation, text summarization, chatbots, and code generation** [1]. Unlike traditional models that assume samples are independent and identically distributed (IID), sequence models acknowledge that the probability of a token often depends on preceding draws, necessitating a model with "memory" [2, 3]. 

**Key Model Types:**
*   **Sequence-to-Label:** Processes multiple inputs to produce a single label (e.g., document classification) [4, 5].
*   **Label-to-Sequence:** Generates a full sequence from a single input (e.g., image captioning) [4, 5].
*   **Sequence-to-Sequence:** Transforms an input sequence into an output sequence, where lengths do not need to be equal [1, 4, 6].

### 🧩 The Encoder-Decoder Framework
The core of many Seq2Seq models is the **Encoder-Decoder architecture** [6]. 

#### 📥 The Encoder
The encoder consists of a series of RNN units that process input tokens one by one [7, 8].
*   **Input Transformation:** Each token is transformed into an embedded vector via an embedding layer [8, 9].
*   **Hidden State Propagation:** These vectors traverse through RNN cells (like LSTMs or GRUs) to produce hidden states ($h_t$) [9, 10].
*   **Context Passing:** The encoder's primary goal is to encode the input; its output vectors are discarded, and only the **final hidden state (context)** is passed to the decoder [8, 10, 11].

#### 📤 The Decoder
The decoder is responsible for **autoregressively** generating the output sequence one token at a time [7, 11, 12].
*   **Input Recycling:** The output (predicted token) from the previous step is recycled as the input for the next step [8, 12].
*   **Mapping to Vocabulary:** A linear layer maps the RNN output to the target vocabulary dimension, followed by a **softmax activation** to generate a probability distribution over possible tokens [12, 13].
*   **Termination:** Generation continues until an "End of Sequence" (EOS) token is produced [7, 14].

---

## 7. 🧠 Recurrent Neural Networks (RNN) & Enhancements

RNNs are designed to remember past information to influence future decisions using a **hidden state ($h_t$)**, which acts as the network's memory [15, 16]. 

### ⚙️ Simple RNN Mechanics
*   **Input Layer ($x_t$):** Receives data at each specific timestep [15].
*   **Activation:** Usually employs a `tanh` function to capture and retain information from previous inputs [15].
*   **Greedy Decoding:** A common method where the model simply picks the token with the highest score at each step [17].

### 🚀 Advanced Units: LSTM and GRU
Standard RNNs struggle with long-term dependencies and are challenging to train [16, 18].
*   **Gated Recurrent Units (GRU):** Feature a **reset gate ($r$)** to disregard irrelevant past info and an **update gate ($z$)** to determine how much of the previous state to carry forward [19].
*   **Long Short-Term Memory (LSTM):** Maintains both a **hidden state ($h$)** for short-term memory and a **cell state ($c$)** for long-term recall [5, 12, 19]. LSTMs use input, forget, and output gates to selectively transport crucial data through time [19].

---

## 8. 🔢 Word Embeddings: Word2Vec and GloVe

Word embeddings are numerical representations that capture the "essence" or semantic meaning of words [20, 21]. A famous example is that the vector calculation `King - Man + Woman` results in a vector very close to `Queen` [20, 22].

### 🎯 Word2Vec Architectures
Word2Vec utilizes neural networks with an input, embedding, and output layer to refine these vectors [21, 22].
1.  **Continuous Bag of Words (CBOW):** Uses surrounding **context words to predict a target word** [23, 24]. In PyTorch, this can be implemented using `nn.EmbeddingBag`, which averages context embeddings [25, 26].
2.  **Skip-Gram:** The reverse of CBOW; it **predicts surrounding context words from a specific target word** [27, 28]. It breaks complex context prediction into smaller, manageable tasks by predicting one context word at a time [29, 30].

### 🌎 Global Vectors (GloVe)
Developed by Stanford, **GloVe** leverages large-scale data for pre-trained embeddings [28, 31]. These can be integrated into PyTorch via `TorchText` to improve performance on tasks like text classification without needing to train embeddings from scratch [31].

---

## 9. 📉 Training and Inference Strategies

### 🏗️ Data Preparation
Training requires specific preprocessing steps:
*   **Demarcation:** Adding **BOS** (Beginning of Sequence) and **EOS** (End of Sequence) tags [3, 32].
*   **Batching:** Sorting sentences by length and using **padding** to ensure consistent batch sizes for frameworks like PyTorch [3, 32].

### 👨‍🏫 Training Techniques
*   **Teacher Forcing:** A technique where the model is provided with the **true target token** from the training data as the next input, rather than its own (potentially incorrect) prediction [13]. This significantly boosts training efficiency [33].
*   **Loss Minimization:** The goal is typically to minimize **Cross-Entropy Loss**, comparing predicted outcomes with actual labels [14, 34, 35].

### 🔮 Inference & Prediction
During inference, the model must handle tokens index-by-index, converting them back into words and removing special symbols [14, 36]. Performing translations requires a complex function that feeds the previous output back as input until the EOS token is reached [36, 37].

---

## 10. 📏 Metrics and Evaluation

Evaluating generative models is difficult because there can be multiple valid outputs for a single input [38].

### 😵 Perplexity
Perplexity measures how "surprised" or uncertain a model is when predicting the next word [39].
*   It is calculated as the **exponent of the average cross-entropy loss** [39, 40].
*   **Lower values** indicate better performance and better alignment with the true data distribution [34, 41].

### ⚖️ Precision, Recall, and F1
*   **Precision:** Measures the accuracy of generated text (count of matching n-grams divided by total generated n-grams) [40, 42, 43].
*   **Recall:** Measures the completeness or coverage of the reference content [40, 43].
*   **F1 Score:** The harmonic mean of precision and recall [40, 43].

### 📚 N-gram Matching
*   **BLEU & ROUGE:** Popular metrics that compare hypothesis sequences against reference sequences using n-gram overlaps [38, 43].
*   **Libraries:** Tools like **NLTK** and **PyTorch** provide standard implementations for these metrics [43, 44].

---

## 11. ⚖️ Ethical Frameworks

As these models become more pervasive, addressing ethical implications is critical [45].

### 🚫 Bias in Embeddings
Models like Word2Vec can inadvertently capture societal biases present in training data (e.g., associating certain genders with specific professions) [45].
*   **Mitigation:** Techniques include "debiasing" (re-centering vectors) and regular fairness evaluations [46, 47].

### 🔒 Privacy and Data Usage
Large models may memorize sensitive personal information found in training sets [46].
*   **Protection:** Developers use **data anonymization** and **differential privacy** to ensure specific individual details are not retained [48].

### 🤝 Fair Representation
Models must be trained on diverse data representing various demographics and cultural nuances [48].
*   **Inclusive Evaluation:** Using metrics that assess performance across different demographic groups ensures the model doesn't underperform for underrepresented users [47, 49].
*   **Continuous Updates:** Models must be regularly updated as social norms and languages evolve [49].

---
