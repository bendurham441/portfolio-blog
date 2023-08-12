---
title: "Artificial Intelligence from the Perspective of an Economics Student"
date: 2023-07-15T16:43:43-04:00
tags: ["testing"]
draft: true
---

Artificial intelligence has been on my mind for quite some time. No doubt that is true of most other people since AI is constantly in the news. Recently, I have started to dig deeper into the methods used by data scientists and machine learning engineers. While learning new things, my prior experiences and education give me a unique perspective into how methods used by data scientists and machine learning engineers differ from those used by economists.

The most fundamental difference in the methods used by these two different disciplines stems from their difference in purpose. In artificial intelligence, while the "why" is not completely cast aside, it is not the center of attention. Meanwhile, the "why" is all economists care about. For this reason, economists are limited in the types of models they use. Economists must use models that the research community knows all the "ins and outs" of. This is driven by a need for interpretability. The primary goal of an economist is to explain something, or to capture a causal relationship. Predictive power is a "nice to have" and only matters to economists if it is rooted in an explainable causal relationship.

Meanwhile, in AI practitioners are almost exactly opposte. Predictive power is crucial, while knowing the causal relationships behind this is a "nice to have." This allows AI practitioners to make use of "black box" models such as deep neural networks that lack interpretability, or at least easy interpretability. This is not to say that neural networks are impossible to interpret, as there are some notable examples of showing how neural networks trained for image classification use specific nodes to detect edges or shapes, for example. However, I think it is reasonable to say that these models are not nearly as transparent or flexible as the models commonly used in economics including linear and logistic regression.

At its root, the differences in methods arise from the more fundamental difference between these fields: the questions that practitioners in each of these fields are trying to answer. This is not to say that there is no overlap, as both economists and AI practitioners might care about profit forecasting, for example. However, one would be hard pressed to find an economist who needs to classify images in his or her job.

As I continue to learn, I wonder if economics will ever shift to incorporate more machine learning methods. Some of this comes from my learning that neural networks are a generalization of linear/logistic regression, some of the most commonly used methods in economic research. Though this may be a far reach, as neural networks just seem to have too many parameters.