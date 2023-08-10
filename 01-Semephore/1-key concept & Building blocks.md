[Reference: Slides from Wei jie Koh](https://www.youtube.com/watch?v=y5uV9eRb3-w)

**Problems:**
 - Privacy dApps are hard to build
 - zk- SNARKs require specialised knowledge and skills 
 - Trusted setups are impractical(unable) for many

**Solution :** 
- **A generic enough circuit** can cover some use cases
- **One trusted setup** for said circuit saves everyone trouble 

, so it enables some use cases like 
 - mixes anonymous **login** and 
 - anonymous **voting**

**A Semaphore User Can :**
- **Register** their identity in a smart contract, and then:
- **Broadcast** a signal
	- a. **Anonymously prove** that their identity is in the set of registered identities(Group)
		- Merkle Proof 
	- b. **Publicly signal** an arbitrary string to an external nullifier once per identity and external nullifier
		- prevent double-spending

It allows a user to broadcast their support of an arbitrary string, without revealing who they are to anyone, besides being approved to do so. Semaphore is meant to be used a base layer for signaling-based applications - mixers, anonymous DAOs, anonymous journalism, etc.

Semaphore 可以让用户在智能合约上注册一个 `Idendity`（身份）: 
1. 可以匿名的证明他们的身份存在某一个群体 `Group` 之中
2. 公开地储存（Broadcast）任意字符串（Signal）在合约之中，这类似于 nonce 的 External Nullifier 存在，如果在 External Nullifier 的情况下，有对同一个字串做 double-signalling 那就是非法的

实际上有 nullfier 和 path index 就已经足够证明了，External Nullifier 的作用类似 `nounce`
, 在 External Nullifier 不同的情况下，可以生成不同的 nullifier hash 用于不同的应用


**How Semaphore works**
 - Identity registration
	- The contract inserts an **identity commitment** into an on-chain Merkle tree
 - Signal broadcasting
	 - The contract verifies a zk-SNARK proof
	 - The contract, which maintains a set of *external nullifiers*, prevents double-signalling to an external nullifier by the **same identity commitment**

Semaphore consists of a `smart contract` and `zero-knowledge proof` components which work in tandem(串联). 
 - The `smart contract` handles state, permissions, and proof verification **on-chain**. 
 - The `zero-knowledge` components work **off-chain** to allow the user to **generate proofs**, which allow the smart contract to update its state if these proofs are valid.  (Semaphore 智能合约在链上处理状态、权限和证明验证。zk 组件在链下工作，允许用户生成证明，如果这些证明有效，则允许链上智能合约更新其状态 )

### key concept & Building blocks

#### TornadoCash

See `TornadoCash`

#### Merkle Proof

See `Merkle Proof` in `TornadoCash`

#### Circom Tips

Recap the `assign`(赋值) & `constraints`(约束)  in Circom

```rust
//poseidon.inputs[0] is a component component, there is no value before, how to constrain equality?
// - A constraint between `poseidon.inputs[0]` and `externalNullifier` ensures that they have the same value. If one of the values ​​changes during subsequent constraints or calculations, it violates the constraint, rendering the entire constraint system unsolvable.

//poseidon.inputs[0] 是个组件 component, 之前也没有值呀，怎么约束相等呢？
//  - `poseidon.inputs[0]`和`externalNullifier`之间的约束确保它们具有相同的值。如果其中一个值在后续的约束或计算中发生变化，那么它会违反此约束，从而使整个约束系统无解。
component poseidon = Poseidon(2);
poseidon.inputs[0] <== externalNullifier;
```


#### External Nullifiers

`externalNullifier`: the value that prevents double-signaling;

The application designer can exploit the external nullifier to limit a user’s action to a particular topic only once, or even [rate limit](https://github.com/ChihChengLiang/semaphore_auth/issues/23) the user action three times, or once every 5 minutes.

```python (question)
// OneOfUs
external_nullifier = HASH("Is the typhon going to hit Osaka?")
```

In the OneOfUs example, the external nullifier is just the hash of the question. Once the user answers the question 1 publishes the nullifier hash, they can’t answer the question 1 again because that would publish a duplicated nullifier hash. But the user can still answer another question, say question 2 since the nullifier hash will be different and the Semaphore contract hasn’t seen it before.

<img src="http://imagesoda.oss-cn-beijing.aliyuncs.com/Sodaoo/2023-07-24-122920.png" width="70%" />

> The signal here can be an abstract expression, which can be specifically expressed as: `a vote`, or a `withdrawal` (这里的 signal 可以是一个抽象的表达，具体可以表示为：一次投票，或者一次提款)

#### Privacy outcomes

From an attacker’s perspective:
1. There is a set of registered identity `commitments`    
2. There is a set of broadcasted `signals` ,  **But I don’t know which signals belong to which commitment**!
3. The amount of privacy you get depends on the size of the anonymity set (类似 TornadoCash, 匿名集越大, 越没法关联起 Deposit 和 Withdraw , 隐私性越强 )
