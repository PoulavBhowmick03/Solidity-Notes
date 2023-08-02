# Contracts

A `contract` is the fundamental building block of Ethereum applications — all variables and functions belong to a contract

# Version Pragma

All solidity code start with version pragma

All solidity source code should start with a "version pragma" — a declaration of the version of the Solidity compiler this code should use. This is to prevent issues with future compiler versions potentially introducing changes that would break your code.

For the scope of this tutorial, we'll want to be able to compile our smart contracts with any compiler version in the range of 0.5.0 (inclusive) to 0.6.0 (exclusive). It looks like this: 

`pragma solidity >=0.5.0 <0.6.0;`.

### A smart contract with empty contract called `ZombieFactory`

```solidity
pragma solidity >=0.5.0 <0.6.0;

contract ZombieFactory {

}
```

# ****Chapter 3: State Variables & Integers****

***State variables*** are permanently stored in contract storage. This means they're written to the Ethereum blockchain. Think of them like writing to a DB. In other words, it is written to the Ethereum blockchain and is like writing to a database. State variables hold the state of a contract, and their values can be read and modified by functions within the contract.

## **Unsigned Integers: `uint`**

The `uint` data type is an unsigned integer, meaning **its value must be non-negative**. There's also an `int` data type for signed integers.

*Note: In Solidity, `uint` is actually an alias for `uint256`, a 256-bit unsigned integer. You can declare uints with less bits — `uint8`, `uint16`, `uint32`, etc..*

# ****Chapter 5: Structs****

Sometimes you need a more complex data type. For this, Solidity provides ***structs***:

```solidity
struct Person {
  uint age;
  string name;
}
```

Structs allow you to create more complicated data types that have multiple properties.

# ****Chapter 6: Arrays****

There are two types of arrays in Solidity: ***fixed*** arrays and ***dynamic*** arrays:

```solidity
// Array with a fixed length of 2 elements:
uint[2] fixedArray;
// another fixed Array, can contain 5 strings:
string[5] stringArray;
// a dynamic Array - has no fixed size, can keep growing:
uint[] dynamicArray;
```

You can also create an array of ***structs***. Using the previous chapter's `Person` struct:

```solidity
Person[] people; // dynamic Array, we can keep adding to it
```

## **Public Arrays**

You can declare an array as `public`, and Solidity will automatically create a `getter` method for it. The syntax looks like:

```solidity
Person[] public people;
```

In Solidity, a `getter` method is an automatically generated function that allows you to retrieve the value of a public state variable in a contract. When you declare an array or variable as public, Solidity automatically generates a getter method with the same name as the variable. The getter method is used to retrieve the current value of the variable from outside the contract.

# ****Chapter 7: Function Declarations****

A function declaration in solidity looks like the following:

```solidity
function eatHamburgers(string memory _name, uint _amount) public {

}
```

This is a function named `eatHamburgers` that takes 2 parameters: a `string` and a `uint`.

Note that we're specifying the function visibility as `public`. We're also providing instructions about where the `_name` variable should be stored- in `memory`.

This is required for all reference types such as arrays, structs, mappings, and strings.

What is a reference type you ask?

Well, there are two ways in which you can pass an argument to a Solidity function:

- By value, which means that the Solidity compiler creates a new copy of the parameter's value and passes it to your function. This allows your function to modify the value without worrying that the value of the initial parameter gets changed.
- By reference, which means that your function is called with a... reference to the original variable. Thus, if your function changes the value of the variable it receives, the value of the original variable gets changed.

### 1. Create a `public` function named `createZombie`. It should take two parameters: **_name** (a `string`), and **_dna** (a `uint`). Don't forget to pass the first argument by value by using the `memory` keyword

```solidity
pragma solidity >=0.5.0 <0.6.0;

contract ZombieFactory {

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;

    function createZombie(string memory _name , uint _dna) public {

    }
}
```

# ****Chapter 8: Working With Structs and Arrays****

# ****Chapter 9: Private / Public Functions****

it's good practice to mark your functions as `private` by default, and then only make `public` the functions you want to expose to the world.

Let's look at how to declare a private function:

```solidity
uint[] numbers;

function _addToArray(uint _number) private {
  numbers.push(_number);
}
```

We use the keyword `private` after the function name. And as with function parameters, it's convention to start private function names with an underscore (`_`).

# ****Chapter 10: More on Functions****

## **Return Values**

To return a value from a function, the declaration looks like this:

```solidity
string greeting = "What's up dog";

function sayHello() public returns (string memory) {
  return greeting;
}

//the function is marked as public, which means it can be called externally from outside the contract. The function also specifies that it returns a string type value.

//Inside the sayHello function, there is a single statement. It uses the return keyword to send the value of the greeting variable back as the output of the function.
```

Solidity also contains ***pure*** functions, which means you're not even accessing any data in the app. Consider the following:

```solidity
function _multiply(uint a, uint b) private pure returns (uint) {
  return a * b;
}
```

This function doesn't even read from the state of the app — its return value depends only on its function parameters. So in this case we would declare the function as ***pure***.

# ****Chapter 11: Keccak256 and Typecasting****

Ethereum has the hash function `keccak256` built in, which is a version of SHA3. A hash function basically maps an input into a random 256-bit hexadecimal number. A slight change in the input will cause a large change in the hash.

It's useful for many purposes in Ethereum, but for right now we're just going to use it for pseudo-random number generation

Also important, `keccak256` expects a single parameter of type `bytes`. This means that we have to "pack" any parameters before calling `keccak256`:

Example:

```solidity
//6e91ec6b618bb462a4a6ee5aa2cb0e9cf30f7a052bb467b0ba58b8748c00d2e5keccak256(abi.encodePacked("aaaab"));
//b1f078126895a1424524de5321b339ab00408010b7cf0e6ed451514981e58aa9keccak256(abi.encodePacked("aaaac"));
```

As you can see, the returned values are totally different despite only a 1 character change in the input.

## **Typecasting**

Sometimes you need to convert between data types. Take the following example:

```solidity
uint8 a = 5;
uint b = 6;
// throws an error because a * b returns a uint, not uint8:uint8 c = a * b;
// we have to typecast b as a uint8 to make it work:uint8 c = a * uint8(b);

```

In the above, `a * b` returns a `uint`, but we were trying to store it as a `uint8`, which could cause potential problems. By casting it as a `uint8`, it works and the compiler won't throw an error.

Let's fill in the body of our `_generateRandomDna` function! Here's what it should do:

1. The first line of code should take the `keccak256` hash of `abi.encodePacked(_str)` to generate a pseudo-random hexadecimal, typecast it as a `uint`, and finally store the result in a `uint` called `rand`.
2. We want our DNA to only be 16 digits long (remember our `dnaModulus`?). So the second line of code should `return` the above value modulus (`%`) `dnaModulus`.

```solidity
function _generateRandomDna(string memory _str) private view returns (uint) {
        uint rand = uint(keccak256(abi.encodePacked(_str)));
        return rand % dnaModulus
    }
```

# ****Chapter 13: Events****

***Events*** are a way for your contract to communicate that something happened on the blockchain to your app front-end, which can be 'listening' for certain events and take action when they happen.

```solidity
// declare the eventevent IntegersAdded(uint x, uint y, uint result);

function add(uint _x, uint _y) public returns (uint) {
  uint result = _x + _y;
// fire an event to let the app know the function was called:emit IntegersAdded(_x, _y, result);
  return result;
}

```

Your app front-end could then listen for the event. A JavaScript implementation would look something like:

```solidity
YourContract.IntegersAdded(function(error, result) {
// do something with result
})
```

# ****Chapter 14: Web3.js****

Ethereum has a JavaScript library called ***Web3.js***.

In a later lesson, we'll go over in depth how to deploy a contract and set up Web3.js. But for now let's just look at some sample code for how Web3.js would interact with our deployed contract.

Don't worry if this doesn't all make sense yet.

```solidity
// Here's how we would access our contract:var abi =/* abi generated by the compiler */var ZombieFactoryContract = web3.eth.contract(abi)
var contractAddress =/* our contract address on Ethereum after deploying */var ZombieFactory = ZombieFactoryContract.at(contractAddress)
// `ZombieFactory` has access to our contract's public functions and events// some sort of event listener to take the text input:
$("#ourButton").click(function(e) {
  var name = $("#nameInput").val()
// Call our contract's `createRandomZombie` function:
  ZombieFactory.createRandomZombie(name)
})

// Listen for the `NewZombie` event, and update the UIvar event = ZombieFactory.NewZombie(function(error, result) {
  if (error) return
  generateZombie(result.zombieId, result.name, result.dna)
})

// take the Zombie dna, and update our imagefunction generateZombie(id, name, dna) {
  let dnaStr = String(dna)
// pad DNA with leading zeroes if it's less than 16 characterswhile (dnaStr.length < 16)
    dnaStr = "0" + dnaStr

  let zombieDetails = {
// first 2 digits make up the head. We have 7 possible heads, so % 7// to get a number 0 - 6, then add 1 to make it 1 - 7. Then we have 7// image files named "head1.png" through "head7.png" we load based on// this number:
    headChoice: dnaStr.substring(0, 2) % 7 + 1,
// 2nd 2 digits make up the eyes, 11 variations:
    eyeChoice: dnaStr.substring(2, 4) % 11 + 1,
// 6 variations of shirts:
    shirtChoice: dnaStr.substring(4, 6) % 6 + 1,
// last 6 digits control color. Updated using CSS filter: hue-rotate// which has 360 degrees:
    skinColorChoice: parseInt(dnaStr.substring(6, 8) / 100 * 360),
    eyeColorChoice: parseInt(dnaStr.substring(8, 10) / 100 * 360),
    clothesColorChoice: parseInt(dnaStr.substring(10, 12) / 100 * 360),
    zombieName: name,
    zombieDescription: "A Level 1 CryptoZombie",
  }
  return zombieDetails
}

```

What our JavaScript then does is take the values generated in `zombieDetails` above, and use some browser-based JavaScript magic (we're using Vue.js) to swap out the images and apply CSS filters. You'll get all the code for this in a later lesson.

