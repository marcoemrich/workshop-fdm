# Basic Building Blocks
### Primitives

```reasonml
/* primitives (type alias) */
type name = string
type age = int
type size = float
type isAlive = bool


let a: int = 27
let a: age = 27
let a = 27
let a = 5.7;

27;

"foo";
```

### Abstract Types

important usage:

 1. reduce implementation detail
 2. model top-down

```reasonml
type notYetSpecified;
```

# Algebraic Data Types (ADT)

### Sum Types (OR)
(also Variants, Unions)

```reasonml
type color = Red | Green | Blue | Yellow;
type shape = Circle | Rectangle;

/* 4 possible Values (sum) */
Red;

### Product Types (AND)

/* Product: Tuple */
type coordinate = (int, int);
type coloredShape = (color, shape);

/* 4 Colors * 2 Shapes = 8 possible Values (product) */
let x: coloredShape = (Red, Circle);
(7, 3)

/* Product: Record */
type coloredShape = {shape, color};

{shape: Circle, color: Red};
{color: Green, shape: Rectangle};
```


### Sum Type with Payload

```reasonml
/* Sum with Payload aka Type Constructors */
type shape = Circle(int) | Square(int, int);

Square(3, 4); /* (3) */
Circle(4); /* (3, 4) */
```

# Functions

```reasonml
type areaSize = shape => size
type add = (float, float) => float;
```

# Primitive Anti-Pattern

```reasonml
let durationToNextRoom = 10.0;
let wayToNextRoom = 5.0;

let speed = (way: float, time: float) => way /. time;

speed(durationToNextRoom, wayToNextRoom);

## Tagged Types (Sum Type of 1)

type secs = Secs(float);
type meter = Meter(float);

let speed: (meter, secs) => float =
  (Meter(m), Secs(s)) => m /. s;
/* let speed = (Meter(m), Secs(s)) => m /. s; */

let durationToNextRoom = Secs(10.0);
let wayToNextRoom =  Meter(5.0);

/* speed(durationToNextRoom, wayToNextRoom); */
```

# Lists

```reasonml
let x: list(shape) = [];

type simplePicture = { shape: list(shape),  backgroundColor: color};
```

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

## Smart Constructors

https://github.com/ostera/reason-design-patterns/blob/master/patterns/smart-constructors.md

```reasonml
type name = Name(string); /* Business Contraint: Length < 20 */

type makeName = string => option(name);
let smallerThan20 = s => String.length(s) < 20

let makeName: makeName = str => (smallerThan20(str)) ? Some(Name(str)) : None

/* Pretty:

let makeName: makeName = str => (str |> String.length < 20) ? Some(Name(str)) : None

*/

makeName("Sven");

makeName("Foo bar Foo bar Foo bar ");
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

## Model with Abstract Types;

type address;

type customer = {
  name,
  address,
};
```

## Generics

```reasonml
type numberOfPlayers = | NumberOfPlayers(int);
type duration = Minutes(int) | Hours(int);
type boardgame = {numberOfPlayers: int, duration: duration};

type cover = Leather | Cardboard;
type notebooks = {numberOfPages: int, cover: cover};

type id = int;

type product('productKind) = {
  productId: id,
  productSpecificData: 'productKind,
};

let myNotebook = {productId: 27, productSpecificData: {numberOfPages: 300, cover: Leather}};
let myBoardgame = {productId: 28, productSpecificData: {numberOfPlayers: 5, duration: Minutes(90)}};


let productIdOf = product => product.productId;

let numberOfPlayersOf = product => product.productSpecificData.numberOfPlayers;
```