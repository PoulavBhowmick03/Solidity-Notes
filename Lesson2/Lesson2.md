# Lesson 2

# ****Chapter 2: Mappings and Addresses****

## ****Addresses****

The Ethereum blockchain is made up of ***accounts***, which you can think of like bank accounts. An account has a balance of ***Ether*** (the currency used on the Ethereum blockchain), and you can send and receive Ether payments to other accounts, just like your bank account can wire transfer money to other bank accounts.

Each account has an `address`, which you can think of like a bank account number. It's a unique identifier that points to that account, and it looks like this:

`0x0cE446255506E92DF41614C46F1d6df9Cc969183`

## ****Mappings****

***Mappings*** are another way of storing organized data in Solidity.

Mapping in Solidity acts like a hash table or dictionary in any other language. These are used to store the data in the form of key-value pairs, a key can be any of the built-in data types but reference types are not allowed while the value can be of any type. Mappings are mostly used to associate the unique Ethereum address with the associated value type.

A hash table is **an implementation of an associative array, a list of key-value pairs that allow you to retrieve a value via a key**.

Defining a `mapping` looks like this:

```solidity
// For a financial app, storing a uint that holds the user's account balance:
mapping (address => uint) public accountBalance;
// Or could be used to store / lookup usernames based on userId
mapping (uint => string) userIdToName;
```

A mapping is essentially a key-value store for storing and looking up data. In the first example, the key is an `address` and the value is a `uint`, and in the second example the key is a `uint` and the value a `string`.

# ****Chapter 3: Msg.sender****

## **Solidity Global Variables**

Global variables in Solidity are predefined variables available in any function or contract. These variables reveal blockchain, contract, and transaction data. Some common global variables in Solidity:

| Variable | Type | Description |
| --- | --- | --- |
| msg.sender | address | The address of the account that sent the current transaction. |
| msg.value | uint | The amount of Ether sent with the current transaction. |
| block.coinbase | address | The address of the miner who mined the current block. |
| block.difficulty | uint | The difficulty of the current block. |
| block.gaslimit | uint | The maximum amount of gas that can be used in the current block. |
| block.number | uint | The number of the current block. |
| block.timestamp | uint | The timestamp of the current block. |
| now | uint | An alias for block.timestamp. |
| tx.origin | address | The address of the account that originally created the transaction (i.e., the sender of the first transaction in the call chain). |
| tx.gasprice | uint | The gas price (in Wei) of the current transaction. |

• Global variables’ values vary based on context. In a transaction, msg.sender refers to the sender’s address, but outside of a transaction, it refers to the contract’s address (such as in a constructor).

## **msg.sender**

In Solidity, there are certain global variables that are available to all functions. One of these is `msg.sender`, which refers to the `address` of the person (or smart contract) who called the current function.

# ****Chapter 4: Require****

In lesson 1, we made it so users can create new zombies by calling `createRandomZombie` and entering a name. However, if users could keep calling this function to create unlimited zombies in their army, the game wouldn't be very fun.

Let's make it so each player can only call this function once. That way new players will call it when they first start the game in order to create the initial zombie in their army.

How can we make it so this function can only be called once per player?

For that we use `require`. `require` makes it so that the function will throw an error and stop executing if some condition is not true:

```solidity

function sayHiToVitalik(string memory _name) public returns (string memory) {
  // Compares if _name equals "Vitalik". Throws an error and exits if not true.
  // (Side note: Solidity doesn't have native string comparison, so we
  // compare their keccak256 hashes to see if the strings are equal)
  require(keccak256(abi.encodePacked(_name)) == keccak256(abi.encodePacked("Vitalik")));
  // If it's true, proceed with the function:
  return "Hi!";
}
```

If you call this function with `sayHiToVitalik("Vitalik")`, it will return "Hi!". If you call it with any other input, it will throw an error and not execute.

Thus `require` is quite useful for verifying certain conditions that must be true before running a function.

# ****Chapter 5: Inheritance****

One feature of Solidity that makes this more manageable is contract ***inheritance***:

```solidity
contract Doge {
function catchphrase() public returns (string memory) {
return "So Wow CryptoDoge";
}
}

contract BabyDoge is Doge {
function anotherCatchphrase() public returns (string memory) {
return "Such Moon BabyDoge";
}
}
```

`BabyDoge` ***inherits*** from `Doge`. That means if you compile and deploy `BabyDoge`, it will have access to both `catchphrase()` and `anotherCatchphrase()` (and any other public functions we may define on `Doge`).

This can be used for logical inheritance (such as with a subclass, a `Cat` is an `Animal`). But it can also be used simply for organizing your code by grouping similar logic together into different contracts.

# ****Chapter 6: Import****

When you have multiple files and you want to import one file into another, Solidity uses the `import` keyword:

```
import "./someothercontract.sol";

contract newContract is SomeOtherContract {

}
```

# ****Chapter 7: Storage vs Memory (Data location)****

***Storage*** refers to variables stored permanently on the blockchain. ***Memory*** variables are temporary, and are erased between external function calls to your contract. Think of it like your computer's hard disk vs RAM.

### Most of the time you don't need to use these keywords because Solidity handles them by default. State variables (variables declared outside of functions) are by default `storage` and written permanently to the blockchain, while variables declared inside functions are `memory` and will disappear when the function call ends.

# ****Chapter 8: Zombie DNA****

# ****Chapter 9: More on Function Visibility****

**The code in our previous lesson has a mistake!**

If you try compiling it, the compiler will throw an error.

The issue is we tried calling the `_createZombie` function from within `ZombieFeeding`, but `_createZombie` is a `private` function inside `ZombieFactory`. This means none of the contracts that inherit from `ZombieFactory` can access it.

## **Internal and External**

In addition to `public` and `private`, Solidity has two more types of visibility for functions: `internal` and `external`.

`internal` is the same as `private`, except that it's also accessible to contracts that inherit from this contract. **(Hey, that sounds like what we want here!)**.

`external` is similar to `public`, except that these functions can ONLY be called outside the contract — they can't be called by other functions inside that contract. We'll talk about why you might want to use `external` vs `public` later.

For declaring `internal` or `external` functions, the syntax is the same as `private` and `public`:

```solidity

contract Sandwich {
  uint private sandwichesEaten = 0;

  function eat() internal {
    sandwichesEaten++;
  }
}

contract BLT is Sandwich {
  uint private baconSandwichesEaten = 0;

  function eatWithBacon() public returns (string memory) {
    baconSandwichesEaten++;
    // We can call this here because it's internal
    eat();
  }
}
```

# ****Chapter 10: What Do Zombies Eat?****

## ****Interacting with other contracts****

For our contract to talk to another contract on the blockchain that we don't own, first we need to define an ***interface***.

```solidity
contract LuckyNumber {
  mapping(address => uint) numbers;

  function setNum(uint _num) public {
    numbers[msg.sender] = _num;
  }

  function getNum(address _myAddress) public view returns (uint) {
    return numbers[_myAddress];
  }
}
```

Now let's say we had an external contract that wanted to read the data in this contract using the `getNum` function.

First we'd have to define an ***interface*** of the `LuckyNumber` contract:

```solidity
contract NumberInterface {
  function getNum(address _myAddress) public view returns (uint);
}

```

Notice that this looks like defining a contract, with a few differences. 

For one, we're only declaring the functions we want to interact with — in this case `getNum` — and we don't mention any of the other functions or state variables.

Secondly, we're not defining the function bodies. Instead of curly braces (`{` and `}`), we're simply ending the function declaration with a semi-colon (`;`).

# ****Chapter 12: Handling Multiple Return Values****

This `getKitty` function is the first example we've seen that returns multiple values. Let's look at how to handle them:
