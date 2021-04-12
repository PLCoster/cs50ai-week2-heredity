# CS50AI - Week 2 - Heredity

## Task:

Write an AI to predict the likelihood of a person having a genetic trait, using a Bayesian Network, and Inference by Enumeration.


## Background:

Mutated versions of the GJB2 gene are one of the leading causes of hearing impairment in newborns. Each person carries two versions of the gene, so each person has the potential to possess either 0, 1, or 2 copies of the hearing impairment version GJB2. Unless a person undergoes genetic testing, though, it’s not so easy to know how many copies of mutated GJB2 a person has. This is some “hidden state”: information that has an effect that we can observe (hearing impairment), but that we don’t necessarily directly know. After all, some people might have 1 or 2 copies of mutated GJB2 but not exhibit hearing impairment, while others might have no copies of mutated GJB2 yet still exhibit hearing impairment.

Every child inherits one copy of the GJB2 gene from each of their parents. If a parent has two copies of the mutated gene, then they will pass the mutated gene on to the child; if a parent has no copies of the mutated gene, then they will not pass the mutated gene on to the child; and if a parent has one copy of the mutated gene, then the gene is passed on to the child with probability 0.5. After a gene is passed on, though, it has some probability of undergoing additional mutation: changing from a version of the gene that causes hearing impairment to a version that doesn’t, or vice versa.

We can attempt to model all of these relationships by forming a Bayesian Network of all the relevant variables. In this network each person has a Gene random variable representing how many copis of a particular gene a person has which can be either 0, 1 or 2 copies of the gene. Each person in the family also has a Trait random variable, which can be yes or no depending on whether that person expresses a trait (hearing impairment) caused by the gene.


## Bayesian Network Model

In the model we are using, each person's Gene random variable has a conditional probability based on their parent's Gene variable. If the person is a root of the family tree (no parent information), then the Gene random variable is given an unconditional probability for having 0, 1 or 2 Genes.

Each person's Trait random variable has conditional probability based on the number of Genes they posses - a person with 0 Genes has a very low probability of having the Trait, while


## Inference by Enumeration

Given a family tree with several members, some being parents to others, and some knowledge of certain family members having the genetic trait or not, how can the conditional probabilities of each family member having 0, 1 or 2 copies of the gene be calculated?

One way to do this is using Inference by enumeration, which can be expressed using the equation below:

![equation](https://latex.codecogs.com/svg.latex?%5Cboldsymbol%7BP%7D%28X%7Ce%29%20%3D%20%5Calpha%20%5Cmathbf%7BP%7D%28X%2C%20e%29%20%3D%20%5Calpha%20%5Csum_%7By%7D%20%5Cmathbf%7BP%7D%28X%2C%20e%2C%20y%29)

<!-- ![\boldsymbol{P}(X|e) = \alpha \mathbf{P}(X, e) = \alpha \sum_{y} \mathbf{P}(X, e, y)](https://render.githubusercontent.com/render/math?math=%5Cboldsymbol%7BP%7D(X%7Ce)%20%3D%20%5Calpha%20%5Cmathbf%7BP%7D(X%2C%20e)%20%3D%20%5Calpha%20%5Csum_%7By%7D%20%5Cmathbf%7BP%7D(X%2C%20e%2C%20y)) -->

where:
- X is the query variable
- e is the evidence (knowledge we hold about the family)
- **P**(X | e) is the probability distribution of variable X given knowledge e
- _&alpha;_ is a normalisation factor (the sum of probabilities for query variable X must be 1)
- y ranges over all values of all hidden variables

In essence, to find the probability distribution for the number of genes each person possesses, and whether or not they will have the trait (if not already known), we must:
1. Generate every possible combination of genes and traits for the family.
2. Discard any combination that disagrees with the known evidence about the family.
3. Calculate the joint probability of each combination.
4. Add the joint probability of each combination to each person's probability of having 0,1 or 2 Genes and having the trait or not.
5. Normalise each person's Gene probability distrinution and trait probability distribution.


## Specification

Complete the implementations of joint_probability, update, and normalize.

The joint_probability function should take as input a dictionary of people, along with data about who has how many copies of each of the genes, and who exhibits the trait. The function should return the joint probability of all of those events taking place.

* The function accepts four values as input: people, one_gene, two_genes, and have_trait.
  * people is a dictionary of people. The keys represent names, and the values are dictionaries that contain mother and father keys. You may assume that either mother and father are both blank (no parental information in the data set), or mother and father will both refer to other people in the people dictionary.
  * one_gene is a set of all people for whom we want to compute the probability that they have one copy of the gene.
  * two_genes is a set of all people for whom we want to compute the probability that they have two copies of the gene.
  * have_trait is a set of all people for whom we want to compute the probability that they have the trait.
  * For any person not in one_gene or two_genes, we would like to calculate the probability that they have no copies of the gene; and for anyone not in have_trait, we would like to calculate the probability that they do not have the trait.
* For example, if the family consists of Harry, James, and Lily, then calling this function where one_gene = {"Harry"}, two_genes = {"James"}, and trait = {"Harry", "James"} should calculate the probability that Lily has zero copies of the gene, Harry has one copy of the gene, James has two copies of the gene, Harry exhibits the trait, James exhibits the trait, and Lily does not exhibit the trait.
* For anyone with no parents listed in the data set, use the probability distribution PROBS["gene"] to determine the probability that they have a particular number of the gene.
For anyone with parents in the data set, each parent will pass one of their two genes on to their child randomly, and there is a PROBS["mutation"] chance that it mutates (goes from being the gene to not being the gene, or vice versa).
* Use the probability distribution PROBS["trait"] to compute the probability that a person does or does not have a particular trait.

The update function adds a new joint distribution probability to the existing probability distributions in probabilities.

* The function accepts five values as input: probabilities, one_gene, two_genes, have_trait, and p.
  * probabilities is a dictionary of people. Each person is mapped to a "gene" distribution and a "trait" distribution.
  * one_gene is a set of people with one copy of the gene in the current joint distribution.
  * two_genes is a set of people with two copies of the gene in the current joint distribution.
  * have_trait is a set of people with the trait in the current joint distribution.
  * p is the probability of the joint distribution.
* For each person person in probabilities, the function should update the probabilities[person]["gene"] distribution and probabilities[person]["trait"] distribution by adding p to the appropriate value in each distribution. All other values should be left unchanged.
* For example, if "Harry" were in both two_genes and in have_trait, then p would be added to probabilities["Harry"]["gene"][2] and to probabilities["Harry"]["trait"][True].
* The function should not return any value: it just needs to update the probabilities dictionary.
The normalize function updates a dictionary of probabilities such that each probability distribution is normalized (i.e., sums to 1, with relative proportions the same).

* The function accepts a single value: probabilities.
  * probabilities is a dictionary of people. Each person is mapped to a "gene" distribution and a "trait" distribution.
* For both of the distributions for each person in probabilities, this function should normalize that distribution so that the values in the distribution sum to 1, and the relative values in the distribution are the same.
* For example, if probabilities["Harry"]["trait"][True] were equal to 0.1 and probabilities["Harry"]["trait"][False] were equal to 0.3, then your function should update the former value to be 0.25 and the latter value to be 0.75: the numbers now sum to 1, and the latter value is still three times larger than the former value.
* The function should not return any value: it just needs to update the probabilities dictionary.

You should not modify anything else in heredity.py other than the three functions the specification calls for you to implement, though you may write additional functions and/or import other Python standard library modules. You may also import numpy or pandas, if familiar with them, but you should not use any other third-party Python modules.

## Usage

Requires Python(3) to run:

$python heredity.py (data/familyX.csv)
