
# Frege

<div align="center">
<h3 align="center">
<img src="https://raw.githubusercontent.com/Etec-SA/diagrams/main/logos/fregeblu.png">
</h3>
<img src="https://img.shields.io/github/commit-activity/t/Etec-SA/frege?style=for-the-badge"> <img alt="GitHub top language" src="https://img.shields.io/github/languages/top/etec-sa/frege?style=for-the-badge"> <img src="https://img.shields.io/github/last-commit/etec-sa/frege?style=for-the-badge"> <img alt="Repository size" src="https://img.shields.io/github/repo-size/etec-sa/frege?style=for-the-badge"> <img alt="Repository issues" src="https://img.shields.io/github/issues/etec-sa/frege?style=for-the-badge"> <img alt="GitHub" src="https://img.shields.io/github/license/etec-sa/frege?style=for-the-badge">
</div>
<hr>
  
</p>

## 📋 Table of Contents

- [Overview](#overview)
- [Technologies](#technologies)
- [Getting started](#getting-started)
  - [Requirements](#requirements)
  - [Installing and configuring the project](#Installing-and-configuring-the-project)
 - [But why "Frege?"](#but-why-frege?)
- [How to contribute](#how-to-contribute)
- [License](#license)

## 👀 Overview

**"Frege"** is a lib created by the <a href="https://github.com/etec-sa/" target="_blank">Boolestation Team</a> in order to assist in tasks involving propositional logic.

Constructing formulas, simplifying them, checking semantic or syntactic validity are responsibilities that Frege proposes to resolve.



Parse:
```typescript
import frege from 'src';

frege.parse.toFormulaObject('P -> Q'); // {1} Implication
frege.parse.toFormulaObject('P <-> Q'); // {2} Biconditional
frege.parse.toFormulaObject('P ∧ Q'); // {3} Conjunction
frege.parse.toFormulaObject('P ∨ Q'); // {4} Disjunction
frege.parse.toFormulaObject('¬P'); // {5} Negation
```

```typescript
{ operation: 'Implication', left: 'P', right: 'Q' } // {1} Implication
{ operation: 'Biconditional', left: 'P', right: 'Q' } // {2} Biconditional
{ operation: 'Conjunction', left: 'P', right: 'Q' }   // {3} Conjunction
{ operation: 'Disjunction', left: 'P', right: 'Q' }   // {4} Disjunction
{ operation: 'Negation', value: 'P' } // {5} Negation
```

Evaluate:
```typescript
import  frege  from  "src";

const first = frege.evaluate('P->(Q->P)', { P:  false, Q:  true }); // {1}

const second = frege.evaluate(
	{
		operation:  'Conjunction',
		left:  'P',
		right: { operation:  'Negation', value:  'P' }
	},
	{ P:  true }
); // {2}

const third = frege.evaluate('¬¬P ∨ ¬P', { P:  true, Q:  true }); // {3}

console.log('first:', first)
console.log('second:', second);
console.log('third:', third);
```

```typescript
first: true
second: false
third: true  
```
Reduce:
```typescript
import  frege  from  "src";
frege.reduce('( P -> Q ) ∨ (A ∧ B)'); // {1}
frege.reduce('P <-> ( Q -> P )'); // {2}
```
```
((¬(P) ∨ Q) ∨ (A ∧ B)) // {1}
((¬(P) ∨ (¬(Q) ∨ P)) ∧ (¬((¬(Q) ∨ P)) ∨ P)) // {2}
```

Generate Truth Table:
```typescript
import  frege  from  "src";

frege.generateTruthTable('P->(Q->P)'); // {1}
frege.generateTruthTable({operation:  'Conjunction', left:  'P', right:  'Q'}); // {2}
```

```typescript
// {1}
[
  [ 'P', 'Q', 'P->(Q->P)' ], // headers 
  [ [ 0, 0 ], [ 0, 1 ], [ 1, 0 ], [ 1, 1 ] ], // truth combinations
  [ true, true, true, true ] // truth values
]

// {2}
[
  [ 'P', 'Q', '(P ∧ Q)' ],
  [ [ 0, 0 ], [ 0, 1 ], [ 1, 0 ], [ 1, 1 ] ],
  [ false, false, false, true ]
]
```

Check Proof:
```typescript
import  frege  from  "src";
import { Proof } from  "src/types/syntactic/proof";
const { toFormulaObject } = frege.parse;

const  proof: Proof = {
	1: {
		id:  1,
		expression:  toFormulaObject('¬P ∧ ¬Q'),
		type:  'Premisse'
	},
	2: {
		id:  2,
		expression:  toFormulaObject('(P ∨ Q)'),
		type:  'Hypothesis'
	},
	
	3: {
		id:  3,
		expression:  toFormulaObject('¬P'),
		from: [[1], 'Conjunction Elimination'],
		type:  'Knowledge'
	},
	4: {
		id:  4,
		expression:  toFormulaObject('¬Q'),
		from: [[1], 'Conjunction Elimination'],
		type:  'Knowledge'
	},
	5: {
		id:  5,
		expression:  toFormulaObject('P'),
		from: [[2, 4], 'Disjunctive Syllogism'],
		type:  'Knowledge'
	},
	6: {
		id:  6,
		expression:  toFormulaObject('P ∧ ¬P'),
		type:  'End of Hypothesis',
		hypothesisId:  2,
		from: [[5, 3], 'Conjunction Introduction']
	},
	7: {
		id:  7,
		expression:  toFormulaObject('(P ∨ Q) -> (P ∧ ¬P)'),
		type:  'Knowledge',
		from: [[2,6], 'Conditional Proof']
	},
	8: {
		id:  8,
		expression:  toFormulaObject('¬(P ∨ Q)'),
		type:  'Conclusion',
		from: [[7], 'Reductio Ad Absurdum']
	}
}

frege.checkProof(proof); // {1}
```

```
 Applied Conjunction Elimination with success at line 3 ✔️
 Applied Conjunction Elimination with success at line 4 ✔️ 
 Applied Disjunctive Syllogism with success at line 5 ✔️   
 Applied Conjunction Introduction with success at line 6 ✔️
 Applied Conditional Proof with success at line 7 ✔️       
 Applied Reductio Ad Absurdum with success at line 8 ✔️    
 
 { (¬(P) ∧ ¬(Q)) } ⊢ ¬((P ∨ Q))
```


## 🚀 Technologies

The main technologies used are:

- [Node.js](https://nodejs.org/en/)
- [TypeScript](https://www.typescriptlang.org/)
- [llang](https://github.com/pnevyk/llang)

## 💻 Getting started

### Requirements

- [Node.js](https://nodejs.org/en/)
- [Yarn](https://classic.yarnpkg.com/), [NPM](https://www.npmjs.com/) or similar.

### Installing and configuring the project

_Cloning Project_

```bash
$ git clone https://github.com/etec-sa/frege && cd frege/
```

_Next Steps:_

```bash
# Install dependencies
$ npm install

# Now you can explore the code.

#Run tests
$ npm run test

#Build
$ npm run build
```

## But why Frege?

**But why "Frege?"**

> "**Friedrich Ludwig Gottlob Frege** ([/ˈfreɪɡə/](https://en.wikipedia.org/wiki/Help:IPA/English 'Help:IPA/English');[[15]](https://en.wikipedia.org/wiki/Gottlob_Frege#cite_note-15) German: [[ˈɡɔtloːp ˈfreːɡə]](https://en.wikipedia.org/wiki/Help:IPA/Standard_German "Help:IPA/Standard German"); 8 November 1848 – 26 July 1925) was a German [philosopher](https://en.wikipedia.org/wiki/Philosopher 'Philosopher'), [logician](https://en.wikipedia.org/wiki/Mathematical_logic 'Mathematical logic'), and [mathematician](https://en.wikipedia.org/wiki/Mathematician 'Mathematician'). He was a mathematics professor at the [University of Jena](https://en.wikipedia.org/wiki/University_of_Jena 'University of Jena'), and is understood by many to be the father of [analytic philosophy](https://en.wikipedia.org/wiki/Analytic_philosophy 'Analytic philosophy'), concentrating on the [philosophy of language](https://en.wikipedia.org/wiki/Philosophy_of_language 'Philosophy of language'), [logic](https://en.wikipedia.org/wiki/Philosophy_of_logic 'Philosophy of logic'), and [mathematics](https://en.wikipedia.org/wiki/Philosophy_of_mathematics 'Philosophy of mathematics')." via <a href="https://en.wikipedia.org/wiki/Gottlob_Frege" target="_blank">Wikipedia.</a>

## 🤔 How to contribute

Thanks for taking an interest in contributing. New features, bug fixes, better performance and better typing are extremely welcome. To get a broader view of the project, check out the <a target="_blank" href="https://github.com/Etec-SA/diagrams/tree/main/frege">diagram folder</a>.

### 🚧 Advice:

**(the folder are under construction, soon we will have a README to explain, in detail, how each module should work)**

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
