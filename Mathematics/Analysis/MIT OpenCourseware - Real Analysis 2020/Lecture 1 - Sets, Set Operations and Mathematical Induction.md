
Purpose:
	1. Gain experience with proofs
	2. Prove statements about real numbers, functions and limits.


Set - is a collection of objects called elements or members.

Empty Set ( {} ) - is the set with no elements denoted by ∅

## Notation

a ∈ S - a is an element of S
a ∉ S - a is not an element of S
∀ - for all
∃ - there exists
⟹ or → - implies
⟺ or ↔ - if and only if

⊂ (proper subset)
⊆ (equal or subset)
∧ (and)
∨ (or)
¬ (not)

## Definitions

**Definition 1.** `Set A` is a subset of `B`, `A` ⊂ `B` if `a` is an Element of `Set A` Implies `a` is an Element of `B`. Can be written as:

A ⊂ B if a ∈ A ⟹ a ∈ B

**Definition 2.** Two sets are equal, `A = B`, if A is a subset of `B` and `B` is a subset of `A`.

A = B if A ⊂ B and B ⊂ A

**Definition 3.** `Set A` is a proper subset of `B` if `A` is a subset of `B` and `A` is not equal to `B`.

A ⊆ B ⟹ A ⊂ B and A ≠ B 


Natural Numbers
ℕ = { 1, 2, 3, 4, 5 } 

Well ordering property of Natural numbers:
∃ x ∈ S such that x <= y for all y ∈ S

Integers
ℤ = { -3, -2 , -1, 0, 1, 2, 3 }

Rational Numbers
ℚ = { m / n,  (m, n) ∈ Z, n ≠ 0 }

Real Numbers
ℝ = Irrational numbers 

Odd Numbers:
{ 2m - 1 : m ∈ ℕ }

ℕ ⊂ ℤ ⊂ ℚ ⊂ ℝ


## First Goal: Describe ℝ

**Definition 1.** The Union of A, B is the Set
A ∨ B = { x: x ∈ A or x ∈ B }

**Definition 2.** The intersection of A and B is the set
A ∧ B = { x: x ∈ A and C ∈ B }

**Definition 3.** The set difference of A with relation to B
A \ B = { x: x ∈ A ; x ∉B }

**Definition 4.** The complement of A is 
A<superscript>c</superscript> = { x: x ∉ A }

**Definition 5.** A and B is disjoint
if A ∧ B = ∅


## De Morgan's Laws

If A, B, C are sets then
1) (B ∨ C)<sup>c</sup>  = B<sup>c</sup> ∧ C<sup>c</sup> 
2) (B ∧ C)<sup>c</sup> = B<sup>c</sup> ∨ C<sup>c</sup> 
3) A \ (B ∨ C) =  (A \ B) ∧ (A \ C)
4) A \ (B ∧ C) = (A \ B) U (A ∨ C)



## Proofs

Theorem: If P then Q
`Pf`: Assume P { Logic + calculations} 
Q is true

Proof 1:
Let B, C be sets
We want to prove (B ∨ C)<sup>c</sup> ⊂ B<sup>c</sup> ∧ C<sup>c</sup> 

## Induction

Axiom is not something you prove, but is something you assume.

S ⊂ ℕ and S ≠ ∅ then S has a least element.
∃ x ∈ S such that x <= y for all y ∈ S


Let Pn be a statement
1. (Base Case) P(1) is true
2. (Inductive step) If P(m) is true, then P(m+1) is true
3. Then Pn is true for all natural numbers


## Contradiction

S ≠ ∅ and derive a false statement
Rule of logic then say that S ≠ ∅ is false