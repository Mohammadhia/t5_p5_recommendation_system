# t5_p5_recommendation_system

## Background

This repo is an attempt to replicate the recommendation system presented in the paper [Recommendation as Language Processing (RLP):
A Unified Pretrain, Personalized Prompt & Predict Paradigm (P5)](https://arxiv.org/pdf/2203.13366.pdf), using a small custom dataset I made, and a publicly available T5 model. 

The P5 recommendation system is solely a large language model (LLM), using a T5 architecture as the backbone. Based on the nature of LLMs and how they embed text (allowing for similar words/terms to have similar embeddings), the researchers behind P5 sought to leverage these relationships to recommend the next product for a person, based on their purchase history (see __"How it works"__ section for details). 

The P5 team also has a corresponding [repository](https://github.com/jeykigung/P5) and [Hugging Face Hub page](https://huggingface.co/makitanikaze/P5).


## How it works

Because LLMs can "understand" relationships between words/terms, and can tell which ones are more similar via embedding representations, they can be leveraged for recommendation system purposes. The P5 team uses a number of input-output prompts, essentially giving the model a list of items a user has already purchased, and asking what item to recommend next (from a list of candidates).

Embedding the purchase history as a series of tokens allows the model to determine what genres/types of products the person is interested in, solely based on the words and their embeddings/"meanings". In turn, the selection of the next product becomes similarity based in a sense.
- Expanding this by including thousands of examples of training data (different customers with different purchase histories), the language model can essentially replicate collaborative filtering in a sense
  - Ex: Say there are 3 users (1, 2, 3) and we want to recommend an item for "User_3". Their purchase history is as follows:
    - User_1: A, B, C
    - User_2: A, B, D
    - User_3: C, D
  - The model could learn from the embeddings and relationships of the tokens (in the transformer architecture), and from there, it could recommend products "A" or "B".


## Benefits

Some benefits include getting past the cold-start problem with ease (when new items are introduced, the model will be able to understand what's similar to it from the name alone) and avoiding tedious, manual feature engineering (using natural language, the LLM will automatically learn).


## My attempt

### English

I attempted to replicate the P5 system by fine-tuning a T5 model (T5-large) with a small custom training set of ~100 rows. The dataset consisted of sports equipment that different users purchased, along with the inventory available, and the next product purchased (the target for the model's output). After training for 10 or so epochs, the results were surprisingly very good! 
- You can view them in the `T5P5_Recommendation_System.ipynb` notebook.

- Example input prompt:
  - "_ITEMS PURCHASED: {Soccer Shin Guards} - CANDIDATES FOR RECOMMENDATION: {Soccer Jersey, Basketball Jersey, Football Jersey, Baseball Jersey, Tennis Shirt, Hockey Jersey, Soccer Ball, Basketball, Football, Baseball, Tennis Ball, Hocket Puck, Soccer Cleats, Basketball Shoes, Football Cleats, Baseball Cleats, Tennis Shoes, Hockey Helmet, Goalie Gloves, Basketball Arm Sleeve, Football Shoulder Pads, Baseball Cap, Tennis Racket, Hockey Skates, Soccer Goal Post, Basketball Hoop, Football Helmet, Baseball Bat, Hockey Stick, Soccer Cones, Basketball Shorts, Baseball Glove, Hockey Pads, Soccer Shorts} - RECOMMENDATION:_"
- Example output:
  - "_Basketball Arm Sleeve_"

The fine-tuned model can be accessed on the [Hugging Face Hub](https://huggingface.co/mohammadhia/t5_recommendation_sports_equipment_english?text=ITEMS+PURCHASED%3A+%7BSoccer+Shin+Guards%7D+-+CANDIDATES+FOR+RECOMMENDATION%3A+%7BSoccer+Jersey%2C+Basketball+Jersey%2C+Football+Jersey%2C+Baseball+Jersey%2C+Tennis+Shirt%2C+Hockey+Jersey%2C+Soccer+Ball%2C+Basketball%2C+Football%2C+Baseball%2C+Tennis+Ball%2C+Hocket+Puck%2C+Soccer+Cleats%2C+Basketball+Shoes%2C+Football+Cleats%2C+Baseball+Cleats%2C+Tennis+Shoes%2C+Hockey+Helmet%2C+Goalie+Gloves%2C+Basketball+Arm+Sleeve%2C+Football+Shoulder+Pads%2C+Baseball+Cap%2C+Tennis+Racket%2C+Hockey+Skates%2C+Soccer+Goal+Post%2C+Basketball+Hoop%2C+Football+Helmet%2C+Baseball+Bat%2C+Hockey+Stick%2C+Soccer+Cones%2C+Basketball+Shorts%2C+Baseball+Glove%2C+Hockey+Pads%2C+Soccer+Shorts%7D+-+RECOMMENDATION%3A).

### Arabic

I also attempted to replicate this in Arabic, but the T5 variants available for Arabic did not perform well under the same parameters and translated training set
- This is likely due to the data paucity in the pre-training of LLMs in Arabic (such as MT5)
- Even after trying several models (going up to MT5-xlarge), the model kept recommending the same 1-2 products
- The attempt and its results can be viewed in the `T5P5_Recommendation_System_Copy_Arabic.ipynb` notebook.

I also tried using a google translate API on the fine-tuned english T5 model to see if that would get me the desired results in Arabic (take an Arabic input, translate to English, feed into fine-tuned model, take the output and translate back to Arabic). However, the results were also suboptimal due to certain words being translated differently (ex: "Soccer" in Arabic is "Football", so when translated from Arabic to English, the model got "Football" where it expected "Soccer").
- This can be viewed in the `T5P5_Recommendation_System.ipynb` notebook as well.
