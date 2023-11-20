## Programming errors

- bugs : // deploy a broken Michelson code, not using Ligo compiler for example ...

  - //const d : option<tez> = 1mutez - 2mutez; (https://github.com/InferenceAG/TezosSecurityBaselineChecking/blob/master/testcases/TC-004/mutezUnderflow.tz)
    => use Ligo
  - rounding issues : https://opentezos.com/smart-contracts/avoiding-flaws/#9-contract-failures-due-to-rounding-issues
  - still some bitwise operations are unsecure , like for Bitwise.shift_right or Bitwise.shift_left , it will raise Michelson error if oveflow is reached
    => solution : do lot of checks on the code or leave default behavior if the consequence is not important

  - Mistake of sender vs source
    => think twice about potential use case of your endpoint

- lib code change : //TODO example of devops issue
  => do more unit tests and CI reports

- private
  - any secret value can be read
    => don't store secret or encrypt it and reveal it later
- predictable information used for random :

  - use block timestamp. Can be predictable as it is in seconds and we know the block time more or less
  - use contract origination address : it is composed of hash of operation + origination index
    => solution :
    - ask independant participat to submit a random number. A bit painful as it require to do commit/reveal and a way to unlock a locked situation
    - good randomness Oracle. It is, in theory, possible to create a good off-chain random Oracle. Chainlink offers a randomness Oracle based on a verifiable random function (VRF), and may be one of the few, if not the only reasonably good available randomness Oracle but not available on Tezos

- leave the contract blocked on a state waiting for a user action. Ex : Shifumi game based on 10min timout to cliam a victory in case of opponent unfair behavior to not play
  => solution : Always have a way to unlock a situation, setting an admin control or timestamp based resolution
