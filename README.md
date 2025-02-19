egreedy
=======

[![Build Status](https://travis-ci.org/kurttheviking/egreedy-js.svg?branch=master)](https://travis-ci.org/kurttheviking/egreedy-js)

**An epsilon-greedy algorithm for multi-armed bandit problems**

This implementation is based on [<em>Bandit Algorithms for Website Optimization</em>](http://shop.oreilly.com/product/0636920027393.do) and related empirical research in ["Algorithms for the multi-armed bandit problem"](http://www.cs.mcgill.ca/~vkules/bandits.pdf). In addition, this module conforms to the [BanditLab/2.0 specification](https://github.com/kurttheviking/banditlab-spec/releases).


## Get started

### Prerequisites

- Node.js 6.x+ ([LTS track](https://github.com/nodejs/LTS#lts-schedule1))
- a package manager of your choice

NOTE: Now typescript ready!

### Installing

Install with `npm` (or `yarn`):

```sh
npm install egreedy --save
```

### Caveat emptor

This implementation (and pretty much any binary computer language) often encounters extended floating point numbers. Arm selection is therefore subject to JavaScript's floating point precision limitations. For general information about floating point issues see the [floating point guide](http://floating-point-gui.de).


## Usage

1. Create an optimizer with `3` arms and epsilon `0.25`:

    ```ts
    import { Egreedy } from './egreedy';

    const algorithm = new Egreedy({
      arms: 3,
      epsilon: 0.25
    });
    ```

2. Select an arm (for exploration or exploitation, according to the algorithm):

    ```ts
    algorithm.select().then((arm) => {
      // do something based on the chosen arm
    });
    ```

3. Report the reward earned from a chosen arm:

    ```ts
    algorithm.reward(arm, value);
    ```


## API

### `Egreedy(config)`

Create a new optimization algorithm.

#### Arguments

- `config` (`Object`): algorithm instance parameters

The `config` object supports two optional parameters:

- `arms` (`Number`, Integer): The number of arms over which the optimization will operate; defaults to `2`
- `epsilon` (`Number`, Float, `0` to `1`):  lower leads to more exploration (and less exploitation); defaults to `0.5`

Alternatively, the `state` object resolved from [`Egreedy#serialize`](https://github.com/kurttheviking/egreedy-js#algorithmserialize) can be passed as `config`.

#### Returns

An instance of the egreedy optimization algorithm.

#### Example

```ts
import { Egreedy } from 'egreedy';
const algorithm = new Egreedy();

assert.equal(algorithm.arms, 2);
assert.equal(algorithm.epsilon, 0.5);
```

Or, with a passed `config`:

```ts
import { Egreedy } from 'egreedy';
const algorithm = new Egreedy({ arms: 4, epsilon: 0.75 });

assert.equal(algorithm.arms, 4);
assert.equal(algorithm.epsilon, 0.75);
```

### `Egreedy#select()`

Choose an arm to play, according to the specified bandit algorithm.

#### Arguments

_None_

#### Returns

A `Promise` that resolves to a `Number` corresponding to the associated arm index.

#### Example

```ts
import { Egreedy } from 'egreedy';
const algorithm = new Egreedy();

algorithm.select().then(arm => console.log(arm));
```

### `Egreedy#reward(arm, reward)`

Inform the algorithm about the payoff earned from a given arm.

#### Arguments

- `arm` (`Number`, Integer): the arm index (provided from `Egreedy#select()`)
- `reward` (`Number`): the observed reward value (which can be 0 to indicate no reward)

#### Returns

A `Promise` that resolves to an updated instance of the algorithm. (The original instance is mutated as well.)

#### Example

```ts
import { Egreedy } from 'egreedy';
const algorithm = new Egreedy();

algorithm.reward(0, 1).then(updatedAlgorithm => console.log(updatedAlgorithm));
```

### `Egreedy#serialize()`

Obtain a plain object representing the internal state of the algorithm.

#### Arguments

_None_

#### Returns

A `Promise` that resolves to a stringify-able `Object` with parameters needed to reconstruct algorithm state.

#### Example

```ts
import { Egreedy } from 'egreedy';
const algorithm = new Egreedy();

algorithm.serialize().then(state => console.log(state));
```


## Development

### Contribute

PRs are welcome! For bugs, please include a failing test which passes when your PR is applied. [Travis CI](https://travis-ci.org/kurttheviking/egreedy-js) provides on-demand testing for commits and pull requests.

### Workflow

1. Feature development and bug fixing should occur on a non-master branch.
2. Changes should be submitted to master via a [Pull Request](https://github.com/kurttheviking/egreedy-js/compare).
3. Pull Requests should be merged via a merge commit. Local "in-process" commits may be squashed prior to pushing to the remote feature branch.

To enable a git hook that runs `npm test` prior to pushing, `cd` into the local repo and run:

```sh
touch .git/hooks/pre-push
chmod +x .git/hooks/pre-push
echo "npm test" > .git/hooks/pre-push
```

### Tests

To run the unit test suite:

```sh
npm test
```

Or, to run the test suite and view test coverage:

```sh
npm run coverage
```

**Note:** Tests against stochastic methods (e.g. `Egreedy#select`) are inherently tricky to test with deterministic assertions. The approach here is to iterate across a semi-random set of conditions to verify that each run produces valid output. As a result, each test suite run encounters slightly different execution state. In the future, the test suite should be expanded to include a more robust test of the distribution's properties &ndash; though because of the number of runs required, should be triggered with an optional flag.
