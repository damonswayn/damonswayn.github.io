---
layout: post
title:  "What Exactly is AI?"
date:   2025-10-10 12:27:00 +1000
categories: ["ai", "llm"]
tags: ["ai", "llm"]
preamble: "A longform post about my understanding of how AI works and what the future of AI may be."
---

AI is obviously the hot technology to write about. Billions of dollars of investment and numerous people extolling it’s virtues, talking about how it will change the world, render humans obsolete in many ways or just selling snake oil to get a slice of the money flying around right now.

I’m not going to either shit on AI or tell you how great it is, too many people do that as it is right now, instead I’d rather talk about how AI technology works so that we can better understand why it does what it does and perhaps better understand what AI is currently capable of.

<aside>
<p>📓 Throughout this article I tend to use AI and LLM interchangeably. Truthfully, they are not entirely interchangeable terms as LLM’s are only one type of AI, with other types like computer vision, reinforcement learning and symbolic systems do not use the transformer model and aren’t Large-Language Models.</p>

<p>The reason I use them interchangeably is because I am referring to the current offerings from major AI companies which are focused on developing LLM’s as their primary product offering and the overall discussion in this post is specific to LLM’s.</p>
</aside>

## Is ChatGPT going to rise up and destroy the world with terminators? Can Gemini feel? Does Grok dream?

The answer to all these questions, thankfully, is no. This is due to the fact that the current technology around AI’s is what is called a Large Language Model (LLM). In simple terms, when you ask your AI a question it gives you an answer by essentially using a massive database of written content and mathematics to generate a best guess at the next word in the response. Effectively, the AI will use math to continuously generate the next word until you have an answer.

<aside>
<p>📓 LLM’s don’t actually query a database to generate their responses as we will see below as we talk about how they work, but for the purposes of the broad overview I am painting here, likening it to a database speaks more to the large corpus of data that LLM’s are trained on.</p>
</aside>

Now, this is a somewhat simplified description of how an LLM works, but I think illustrates that AI is not any kind of sentient super-AI, but rather, a really cool, super fancy auto-complete.

In fact, you can prove this by going to whatever AI agent you want and asking it

> *Generate an image of a wristwatch with the hands pointing to exactly 4:37pm.*
> 

The AI will happily generate an image of a wristwatch, but it will not be pointing at 4:37pm. Why? Because a large majority of the images of watches on the internet are taken from websites that sell watches, and those images often have the time set on those watches to best display the watch-face or the logo on the watch-face, so what you may end up with is your image will display something closer to 10:10 and 35 seconds approximately.

*Below is what I got when I gave ChatGPT that exact prompt.*

![ChatGPT Image Oct 2, 2025, 04_41_26 PM.png](What%20exactly%20is%20AI%20280a4215bcdd80c5ae25e2f8c3fbf7e2/ChatGPT_Image_Oct_2_2025_04_41_26_PM.png)

Now, of course if someone provided a whole bunch of training data to the AI to teach it how a watch looks like and how a watch looks at all sorts of different times, then it can use that training data to guess at what the pixels should look like to get close to that.

<aside>
<p>📓 Over time, examples like this will improve as companies improve their models. From what I’ve been led to believe, models like DALL-E 3 can get times on watches correct now, most likely due to improved training alongside explicit constraints and/or fine-tuning.</p>
</aside>

## Ok, so how does that work then?

So first of all you need data. Like, **A LOT** of data. Companies like OpenAI, Gemini, Claude etc have massive data ingestion pipelines that basically slurp up as much of the open internet as possible. This data is used to train the AI on what words relate to what other words.

<aside>
<p>⚠️ Web-crawling used to be solely the domain of search engines like Google etc to build up their search database, however with so many AI’s ingesting data at such a high rate, many public-facing websites have had their web-hosting costs skyrocket because of the sheer number of AI ingestion engines pulling their data and using it to train their AI’s.</p>
</aside>

Now, because our LLM is a computer, it doesn’t understand language, so while we have this dataset, we need to convert it to a language that a computer does understand and a language that we can use mathematics on to achieve the outcome we want (give us the predicted next word).

## Introducing the token

Language is complex, and turning that language into a computable state is also complex, hence we begin by tokenizing our input. Tokenization basically involves taking the words in the data and breaking them into sub-words. For example, Unhappiness might be broken down into “Un” and “happiness” as two separate tokens.

<aside>
<p>⚠️ Tokenization does consider the linguistic meaning of subwords, but rather focuses on splitting words into statistically common subunits. If you look below at the Antidisestablishmentarianism example below you can see that more clearly than the Unhappiness example.</p>
</aside>

When you hear AI companies talk about “vocab size”, what they are talking about is the number of potentially different tokens the AI handles. For example, if a company says their vocab size is 50k tokens, then that means that their AI will take whatever you write into it and that will be broken down into one of 50,000 different words, subwords, characters etc.

Let’s look at this in practice. Let’s say we have the following sentence.

> **"Antidisestablishmentarianism is unbelievably complicated."**
> 

For an AI that has a vocab size of 10k, this sentence might be broken down into the following tokens.

*"Anti", "dis", "est", "ab", "lish", "ment", "arian", "ism", " is", " un", "believ", "ably", " com", "plic", "ated", "."*

Whereas, for an AI with a vocab size of 50k, you might get the following.

*"Antidisestablishmentarianism", " is", " unbelievably", " complicated", ".”*

Bigger vocabulary means larger words may not get split up into subwords.

Which option is better? Well that comes down to a tradeoff between how detailed your AI vocabulary is and the amount of data you need to store, how fast your AI can train itself and how much computer resources (CPU and memory) the AI uses. As of this writing, most AI’s use 30k to 100k as the sweet spot of vocabulary size, although some models can go as high as 200k-300k.

## So we have our tokens, what next?

So our tokens right now are still just words and subwords (and maybe letters), but computers still don’t work with words, subwords, letters etc, they prefer to work with numbers. So each token we generated maps to a number, this number is basically an index used for looking up data in a big lookup table, this is called the embedding matrix, but lets call it our lookup table for now. Lets for now work with one word/token (**cat**) and say its number/ID is **1000**.

We run through our lookup table until we find the entry for number **1000**, and we find this really long collection of weird numbers that looks something like

```json
[0.12, -0.44, 0.07, ...]
```

This is called a vector, it is a mathematical concept which allows us to represent a list of numbers, again, this is a simplified description of a vector, but for now this will suffice.

Vectors are special lists in that you can use them to represent some point on a graph, for example if we had the vector of [2, 3], then our point is 2 across and 3 up, like the below (terribly) drawn example.

![vector.jpg](What%20exactly%20is%20AI%20280a4215bcdd80c5ae25e2f8c3fbf7e2/vector.jpg)

This example is easy when there are only two numbers in our vector, but much harder to draw when our vector has, for example, 1,024 numbers in it. Luckily, neither I nor the AI have to actually draw this vector on a graph, but rather, simply perform maths on it, something computers are exceedingly good at.

During training, these numbers change as the AI learns which words/subwords are related or similar to each other so that if you were to draw that immensely 1,024 dimension graph, you would be able to see that those words/subwords are now closer to each other. This is why the big set of training data is important, you basically use that massive set of data to tweak the numbers in a tokens vector based on how the words are used in that big set data.

Now, jumping back to language being complicated again, words by themselves do have meaning, but the order in which those words appear also convey meaning, for example the phrases “dog bites man” and “man bites dog” have two very different meanings. To account for this, when we are moving our points closer or further apart based on their relation or similarity to each other, we are also including in the position of those tokens in the overall sentence.

The reason our vector has a lot of numbers is to help us encode all these various relationships, similarities and positional contexts so that one meaning or positional usage of a word does not impact other usages, meanings or contexts.

If I were to simplify this, think of it like taking words and subwords, writing them on a number of tennis balls and scattering the tennis balls all over the floor, then moving those tennis balls around based on what the word/subword written on them is so that tennis balls with similar words/subwords are closer together.

This would start getting harder the more things you try to account for when moving tennis balls, such as the position of the word in the sentence, the surrounding words changing the context, perhaps the tone the sentence is trying to convey. However, it would became easier the more directions you had to move the ball in while still remaining close to other balls. This is why the vectors have a lot of numbers.

## Robots in disguise?

Now we reach the transformer (hence the Transformers TV show pun). Transformers are essentially the brain of the AI and functions by taking all the vectors we figured out from our tokens and passing them through potentially hundreds of thousands of interlinked “calculators” (again, simplified but will expand later) where each calculator figures out which of the other linked calculators to pass to next, over time calculating different values until eventually the output is a list of all the items in our vocabulary along with a probability which indicates which item in our vocab is most likely to be the next expected word. Once we have the list of probabilities we select the one that is most likely and return that.

So, how does it calculate the probabilities? How does the calculation end up giving relatively good answers such that AI’s generally seem to make sense when you talk to them? This comes back to how the AI is trained.

Let’s say we have the following input: `"The cat sat on the mat."` where this input gets turned into the tokens `[The, cat, sat, on, the, mat, .]` . The AI gets trained by taking a subset of the tokens (in this example `[The, cat, sat]`) and running this through the AI with the expected answer being `on`. Initially the AI will be wildly wrong as the probabilities will be all over the place, but we know what the word *should* be, so we perform a process called *back propagation*, which basically means we go back through all the interlinked calculators and modify the formula’s they use to output their result so that they are more likely to return `on` as the most likely result.

That’s all well and good for getting your AI good at telling you the next word is `on` , but we want our AI to be a little smarter than that, so we run this process over a massive data set (our web dataset) and train the AI trillions of times, slightly nudging our calculators each time until finally they are in a position where they are tuned enough that we can get a pretty good guess each time we request a different word until the output is something that makes sense.

Now, I’ve been referring to these calculators a lot up until now, so lets try and describe what they really are.

## Neural nets

In the field of AI people commonly talk of “neural nets”, basically the idea is that we create a computer simulation of neurons and their interconnectedness with the end goal of simulating the human brain. Each time I refer to a calculator, what I am referring to is a simulated neuron. In a neural network we build up **layers** of these neurons with each neuron in one layer having a connection to every neuron in the layer below it. When AI companies talk about the number of **parameters** that their AI has, that is the total number of connections across the entire network plus the number of biases.

<aside>
<p>⚠️ Interesting side-note about layers, different companies and research papers count the number of layers differently. The reason is whether or not the company/writer counts the input layer as an actual layer or not. The input layer while technically a layer, does not have any trainable weights associated with it, therefore in a hypothetical 3-layer network you would have the following:</p>
<ul>
	<li>Input layer</li>
	<li>Hidden layer #1</li>
	<li>Hidden layer #2</li>
	<li>Output layer</li>
</ul>

<p>As only Hidden layer #1, Hidden layer #2 and Output Layer have trainable weights.</p>
</aside>

Each neuron continuously outputs a calculated value (called the *activation value*). The larger the activation value is, the more “active” or more impact it has on further layers, and subsequently the lower the value, the less impact it has on further layers.

Now, I’ll talk briefly about the math, but try not to get too in the weeds.

The first step is the input step, the calculated input value can be given as:

$$
\text{calculated input} = (\text{received input} \cdot \text{weights}) + \text{bias}
$$

Where *received input* is the value that we got from the previous layer, the weights are our trained and tweaked values and our bias is some fixed amount we shift everything. Shifting things by a bias is important because it allows neurons to activate even when all the inputs passing through it are zero, giving the network the flexibility to learn more complex patterns.

Next we have what is called the *activation function*, this is another formula which takes the calculated input and performs another calculation to determine whether this neuron should be activated or deactivated. The formula used is different from place to place and has changed as research has determined better formula’s. I won’t go into too much detail as to the math behind those formula’s as some such as Gaussian Error Linear Unit (GELU) can get complicated quickly, however what I will say is that these formulas effectively calculate some value that is between -1 and +1, with any negative value being treated as the neuron being deactivated and any positive value being treated as the neuron is activated.

## Output

We’ve now reached the output layer of our network. The output layer takes all of these various values which have been passed through our various layers and neurons, and performs some final calculations to get us our list of probabilities as to what the AI thinks the next token from our vocabulary should be.

So, we need some way of calculating a list of probabilities from all the inputs we’ve calculated through the network. Currently we have a list of numbers from each of the output neurons, which we can gather together as a vector. However, the number of values inside the vector does not equal the number of tokens in our vocabulary, thus we need to convert this small list of numbers into a larger list of numbers.

To do this, we take our output vector and pass it through what is called a **linear layer**, this layer essentially projects the output vector from vector size to vocab size. This resulting projection essentially has one value per potential token in our vocabulary. These projected values are called **logits** which is just a number representing how “favored” a token is, the larger the number the more favored it is.

From there, we convert each of those values inside the projection vector into a probability, this is done most often using the **softmax** formula, giving you a list of probabilities indicating how likely a certain token is to be the recommended next word.

And with that, our AI has given a one-word answer!

## But my conversations are longer than one word!

Yes, this is because the process of generating a response to your input is basically a loop of this process. It generates one token at a time with the input of the next iteration of the loop being the output of the previous iteration.

So how does the AI know when to stop? LLM’s are trained with a special token, often called `<EOS>` or `<END>`, this token represents the stopping point at which the LLM can stop generating words. Annoyingly, there is no guarantee however that the LLM will ever actually output the special stop token while it is generating a response, as such, most LLM’s also have a built in maximum response length limit so that we can prevent the LLM for just rambling infinitely.

Some other systems build in special punctuation or token sequences, repetition detection or other smart systems to determine their stopping point.

## So, if it’s just a next-word guesser based on probabilities, why does it seem to work?

As ChatGPT likes to say, “you’ve touched on the core question”. As of current writing the answer is that we don’t really know, but we have some theories.

<aside>
<p>📓 Ok, so a more accurate answer is that we do know how they work in terms of understanding the mechanisms by which an LLM can do what it does (this article seems a pretty good argument in that direction), but what we don’t understand is how LLM’s take these mechanisms and display “emergent behaviour”.</p>
</aside>

The reason we don’t really know is that once our AI is trained, it is almost impossible to properly understand all the different weights, how the neurons react to the weights, how and why they modify values because of the sheer number of values and variables that go into the calculations, which leads many people to describe the network of neurons as a black box.

One potential answer is that a large enough model becomes incredibly efficient at compressing its training data, and because it is a computer it has a perfect index of that compressed data. Once the data is big enough and compressed enough, it becomes very good at combining different patterns from different parts to answer new questions. It’s not really reasoning, it just has so much information perfectly encoded that it it can pick out patterns in just about anything because it has seen a similar pattern somewhere in its gargantuan training set.

Another potential answer is that the neural network when trained is learning to simulate the kinds of thought processes humans go through when generating text. It’s not that the simulation is perfect or equal to a humans neurons and thought processes, but it could be a primitive example of simulating the human brain and a learned imitation of human cognitive trajectories.

The third answer is that as the training progresses, the neural network begins forming “concept spaces” where similar ideas are grouped together. At a certain model size and training quality those spaces become smooth and densely connected enough that they are capable of generalization as the path to navigate from one concept to another becomes easier. This I find to be the easiest to imagine because it is a very visual description of what is happening.

## Why do we have a reasoning/thinking switch? If an LLM is not capable of reason, why is it an option?

Simple answer is marketing. Ok, maybe that is a bit of a cop-out answer, but in essence that is the truth of the matter.

What happens when you turn on the “thinking” switch is that the LLM performs a sort of intermediary meta-step where it outputs extra detail about how it “understands” the prompt and how it “forms” the answer (in as much as an LLM can understand and form answers), this additional output gets fed into the context of the initial prompt, with the idea being that more detailed context better tunes the next-word generation to get better responses.

## Why has AI been such a boon in software development?

The cynical answer would be that whether it has been a boon or not is relative, some people have been able to leverage AI to improve their software and speed up software delivery, but others have let the AI generate them broken, insecure slop that barely works. Success or failure with AI in software is down to the user, not the AI.

Programming has two things going for it that have helped AI be successful in this field. The first is the amount of high-quality open-source software that is freely available on the internet. Free and Open-Source software has been around since the 50’s/60’s in some form or another and became a movement under Richard Stallman and the GNU project around 1983. In that time, people have freely provided the source code to operating systems, programming languages and compilers, web browsers, web servers, video games, routers, phones and almost anything else you can think of that runs software. With all this publicly available source code, LLM companies have a gargantuan corpus of examples and data to ingest and train off of to improve themselves, especially on software that has been shown to be some of the most reliable and important to the infrastructure of computing and the internet.

The other key point is that programming and programming languages are a very structured language with a very specific and limited syntax. Unlike spoken languages like english etc, programming languages have strict rules that create clear patterns, something that AI’s and LLM’s work well with.

The combination of a massive training corpus and a highly formalized, structured and pattern-oriented language makes programming a task that LLM’s can excel at.

## Why has AI made Nvidia one of the most valuable companies in the world?

Nvidia are one of the worlds largest manufacturers of graphics cards, but what do graphics cards have to do with LLM’s?

Graphics cards work by having highly specialized chips that are well suited to performing huge numbers of calculations at the same time. These calculations are intended to calculate the color, shading etc of individual pixels, and to allow for doing this across the large number of complex pixels that can appear on a screen, graphics cards use their specialized chips to calculate large numbers of pixels at the same time.

AI companies however have figured out that these chips because of their highly concurrent and powerful computation chips can be repurposed so that instead of calculating pixels you can calculate complex formulae in the same highly concurrent manner. As such, rather than using traditional CPU’s for training AI, companies are building massive computation arrays out of graphics cards and using their chips to vastly improve the training time and throughput of their AI models.

<aside>
<p>📓 Once Nvidia realized the market available to them, they began producing specialized AI products, both so they could capitalize on people buying their products without all those pesky advanced graphics features getting in the way, but also because the consumer market was being hit with massive graphics card shortages due to all the people buying up graphics cards for AI work.</p>
</aside>

## So what does the future of AI look like?

Honestly, this part is going to be mostly speculation on my part as I don’t have any kind of first-hand knowledge of what companies like OpenAI, Google, Grok etc are researching.

In my opinion, LLM’s will eventually reach an improvement ceiling. I believe that if we haven’t already reached it, the era of large-step improvements in AI models is nearing an end as we struggle to scale the resources necessary to improve LLM’s (electricity, graphics cards, storage etc). 

Once that ceiling is reached, one of two things are likely to happen.

1. Without the promise of improvement, the large amounts of money invested in AI starts to dry up and the AI bubble bursts, potentially leading to another “AI Winter”.
2. Research shifts towards a non-LLM solution as companies look to figure out how they can stay sustainable in a world where LLM’s are no longer feasible.

The pessimist in me feels that option one is most likely given the sheer amount money currently invested in AI companies which aren’t making a profit as well as the number of companies which have large investments in integrating AI into their organization but are struggling to find ways to generate a return on that investment.

The other path forward is potentially the idea of moving away from massive all-purpose LLM’s, but instead building a network of multiple highly specialized LLM’s with a routing layer where the specialized LLM’s have the ability to ask each other questions or gather specialized feedback which forms the overall output. I believe this is a more scalable and realistic solution than taking a “super-model” like ChatGPT and trying to make it better.

## Conclusion

There is a lot of money invested in AI, and a lot of people who stand to profit a great deal from the public believing that AI is achieving things that seem like magic. That’s not to say that LLM’s are incapable of doing amazing things, in my opinion the fact that LLM’s like ChatGPT are as good as they are is an amazing feat of engineering and maths.

What I hope people take away from this is that people better understand exactly what AI is, why it works and also why when it doesn’t work, it doesn’t work in the ways we see (hallucinations etc) and that the discourse around AI stops being so beholden to hype and snake-oil salesmen.