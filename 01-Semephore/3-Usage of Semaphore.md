### Usage

https://semaphore.appliedzkp.org/
demo: https://semaphore.appliedzkp.org/
paper: https://docs.zkproof.org/pages/standards/accepted-workshop3/proposal-semaphore.pdf


当使用者注册了他们的 identity，Semaphore 会产生一组 EdDSA 公私钥对 $pubKey/privKey$，并且将 $publickey$ 和**两个随机秘密值**哈希之后送去给合约，储存在 Merkle Tree 中（就是加入一个 Group）。这个 Hash 就是 identity Commitment，而两个随机秘密值则为 `identityNullifier` 和 `identityTrapdoor` 

1. `Trapdoor`:  private, known only by user
2. `Nullifier`:  private, known only by user
3. `Commitment`:  public

- `Trapdoor`: 3469645641351166901963573195925..31427130
- `Nullifier`: 14981737431400894267254289547...65621677
- `Commitment`: 3702884716619962522790556....3891772
<img src="http://imagesoda.oss-cn-beijing.aliyuncs.com/Sodaoo/2023-07-07-013240.png" width="70%" />
只要公开该 merkle tree 的 root，其他使用者即可知道此使用者是这个 group 的成员

ZKP 的部分则是在验证公私钥对以及验证公钥是某个 merkle tree 的 leaf。用户能够在 Semaphore 提供的前端（client 端、链下）产生他们在某个 group 中的 proof。将 proof 送到合约中时就可以验证这个 proof 是否 valid。


**Groups:**


Semaphore groups are binary incremental Merkle trees in which each leaf contains an identity commitment for a user. Groups can be abstracted to represent events, polls, or organizations.  ( Semaphore groups 是二进制递增的默克尔树，其中每个叶子包含用户的身份承诺。可以将这些组抽象为表示事件、投票或组织。)


**Proofs**

Semaphore members can anonymously [prove](https://semaphore.appliedzkp.org/docs/guides/proofs) that they are part of a group and that they are generating their own **signals**. 
Signals could be anonymous votes, leaks, reviews, or feedback. (Semaphore members 可以匿名证明他们是一个团体的一部分，并且他们正在产生自己的信号。这些信号可以是匿名投票、泄漏、评论或反馈 )

<img src="http://imagesoda.oss-cn-beijing.aliyuncs.com/Sodaoo/2023-07-06-145528.png" width="70%" />