# Invariant Enforcement

Tennis Scoring Rules:
* Player points: Love, 15, 30, 40
* 40 points && win the ball ⇒ win game
* both player 40 ⇒ players are deuce
* deuce: the winner of a ball ⇒ advantage
* advantage && wins the ball ⇒  win game
* player without advantage wins ⇒ back at deuce

## 1st Try

```reasonml
type points = int;

type score = {
  playerOne: points,
  playerTwo: points,
};

let s = {
  playerOne: 1000, /* 1000, -200 */
  playerTwo: -15,
};
```

## 2nd Try

```reasonml
type points =
  | Love
  | Fifteen
  | Thirty
  | Forty;

type score = {
  playerOne: points,
  playerTwo: points,
};

let s2 = {playerOne: Fifteen, playerTwo: Love};
let even = {playerOne: Forty, playerTwo: Forty};
```

## Better Solution

```reasonml
type player =
  | PlayerOne
  | PlayerTwo;

type points =
  | Love
  | Fifteen
  | Thirty;

type score =
  | Points(points, points)
  | Forty(player, points /* of other player */)
  | Deuce
  | Advantage(player)
  | Game(player);

let startScore: score = Points(Love, Love);
let anotherScore: score = Forty(PlayerTwo, Thirty);
let anotherScore2: score = Deuce;
let anotherScore3: score = Advantage(PlayerOne);

/*
 let impossibleScore1: score = Points(Seven, Eleven);
 let impossibleScore2: score = Points(Forty, Forty);
 let impossibleScore3: score = Forty(PlayerTwo, Forty);
 */
 ```

# Algebraic Data Types (ADT)

### Sum Types (OR)
(also Variants, Unions)

```reasonml
type score =
  | Points(points, points)
  | Forty(player, points /* of other player */)
  | Deuce
  | Advantage(player)
  | Game(player);

### Product Types (AND)

/* Product: Tuple */
type coordinate = (int, int);

/* Product: Structure */
type coordinate = {x: int, y: int}
```
# Functions

```reasonml
type areaSize = shape => size
type add = (float, float) => float;
```

# Lists

```reasonml
let x: list(shape) = [];

type simplePicture = { shape: list(shape),  backgroundColor: color};
```

# Optional, ie. Billion Dollar Mistake

```reasonml
 /* type option('a) = None | Some('a); */

let reciprocal = x => x == 0.0 ? None : Some(1.0 /. x);

type name = Name(string);
type phone = Phone(string);

type customer = {
  name,
  phone: option(phone),
};
```

# Tactical Design Pattern and idioms

## Model with Abstract Types

 1. reduce implementation detail
 2. model top-down

```reasonml
type address;

type customer = {
  name,
  address,
};
```

## Model Values explicitly!

```reasonml
/* Don't */

let cellIsAlive: bool = true;

/*  Do */
type livingState =
  | ALIVE
  | DEAD;
let cell = ALIVE;
```

## Model State Changes explicitly!
 * Move Runtime to Compile Time
 * Bool -> Type

```reasonml
 /* Bad Idea */
type emailAdress = {
  email: string,
  verified: bool,
};

/* Better */
type unverifiedEmail =
  | UnverifiedEmailAdress(string);
type verifiedEmail =
  | VerifiedEmailAdress(string);

type email =
  | UnverifiedEmailAdress(string)
  | VerifiedEmailAdress(string);

type verifyEmail = unverifiedEmail => option(verifiedEmail);

