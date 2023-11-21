## Programming errors

Programming errors in web3 are mistakes or bugs that occur when writing smart contracts.

1. Bugs

Writing Michelson code requires careful attention to detail and rigorous testing. If the code contains errors or inconsistencies, it may result in a failed transaction that consumes gas and storage fees. Therefore, it is advisable to use high-level languages that compile to Michelson, such as LIGO, and to verify the generated code before deploying it on the node.

On below example, Ligo is verifying that a the substraction is failing as it returns an optioal value.

```ligolang
option<tez> = 1mutez - 2mutez;
```

Run the code and watch the generated Michelson code

```bash
taq compile 1-bugs.jsligo
more ./artifacts/1-bugs.tz
```

After doing the division `SUB_MUTEZ`, a check will be done with `IF_NONE` instruction.
Ligo will not compile if you forget to manage the optional value and return th result directly

Run the code

```bash
taq simulate 1-bugs.tz --param 1-bugs.parameter.default_parameter.tz
```

Modify the michelson file **./artifacts/1-bugs.tz** to not check the diff and run again

```michelson
{ parameter (or (unit %reset) (or (mutez %decrement) (mutez %increment))) ;
  storage mutez ;
  code { UNPAIR ;
         IF_LEFT
           { DROP 2 ; PUSH mutez 0 }
           { IF_LEFT {  SWAP ; SUB  } { ADD } } ;
         NIL operation ;
         PAIR } }
```

```bash
taq simulate 1-bugs.tz --param 1-bugs.parameter.default_parameter.tz
```

```logs
Underflowing subtraction of 0 tez and 0.000001 tez
```

&rarr; **SOLUTION** : use Ligo compiler to prevent runtime errors

2. Rounding issues

https://opentezos.com/smart-contracts/avoiding-flaws/#9-contract-failures-due-to-rounding-issues

3. Unsecure bitwise operations

like for Bitwise.shift_right or Bitwise.shift_left , it will raise Michelson error if oveflow is reached

&rarr; solution : do lot of checks on the code or leave default behavior if the consequence is not important

4. Sender vs source confusion

&rarr; think twice about potential use case of your endpoint

5. Library updates

//TODO example of devops issue

&rarr; do more unit tests and CI reports

6. Private data

any secret value can be read

&rarr; don't store secret or encrypt it and reveal it later

7. Predictable information used as random seed

&rarr; solution :

- use block timestamp. Can be predictable as it is in seconds and we know the block time more or less
- use contract origination address : it is composed of hash of operation + origination index

&rarr; solution :

- ask independant participat to submit a random number. A bit painful as it require to do commit/reveal and a way to unlock a locked situation
- good randomness Oracle. It is, in theory, possible to create a good off-chain random Oracle. Chainlink offers a randomness Oracle based on a verifiable random function (VRF), and may be one of the few, if not the only reasonably good available randomness Oracle but not available on Tezos

8. Blocked state

leave the contract blocked on a state waiting for a user action. Ex : Shifumi game based on 10min timout to cliam a victory in case of opponent unfair behavior to not play

&rarr; solution : Always have a way to unlock a situation, setting an admin control or timestamp based resolution
