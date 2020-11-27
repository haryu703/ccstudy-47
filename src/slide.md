---
marp: true
paginate: true
---

# Bitcoin Cash 2020-11-15 ハードフォーク

---
# 自己紹介
- haryu703

---

# 目次
1. 今回のハードフォークの内容
1. BCHとABC
1. ハードフォーク後の動向

---

# 1. 今回のハードフォークの内容
- ASERT
    新しい難易度調整アルゴリズムの導入
- Automatic Replay Protection
    今回で廃止

---
# 1.1. ASERTの概要
- 新しい難易度調整アルゴリズム
- 難易度のぶれを小さくし、ブロック生成時間を安定させる
- 正式な名前はaserti3-2d

---
# 1.2. 今までの難易度調整アルゴリズム
- 2017年11月のハードフォークで導入された
- cw-144と呼ばれる
- 過去144ブロックの生成間隔の移動平均などから難易度を決める
- 難易度が変化しやすい分、ぶれが激しい
- 難易度の低い間だけBCHをマイニングされることが問題（turbo blocks）
    - BCHだけマイニングしている人は損をする
    - ブロック生成間隔が不安定になる
    - 取引所の承認数が引き上げられる

---
# 1.3. ASERT
- $next\_target = old\_target * 2^{time\_delta - ideal\_block\_time * (height\_delta + 1) \over halflife}$
    - $old\_target$: `anchor block`のtarget
    - $time\_delta$: `evaluation block`と`anchor block`の親のtimestampの差
    - $ideal\_block\_time$: 目標のブロック生成間隔（600秒）
    - $height\_delta$: `evaluation block`と`anchor block`のblock heightの差
    - $halflife$: 定数のパラメータ、単位は秒。mainnetは172800（=2日）
    - $next\_target$: `evaluation block`の次のブロックのtarget
- `anchor block`はASERT有効化前の最後のブロック
- 計算は整数値のみで行う
- $2^n$の計算は3次の近似多項式を使う
- この式の定数などがaserti3-2dという名前の由来になっている

---
- ブロックが10分間隔で作られている場合
    $$
    \begin{aligned}
    next\_target &= old\_target * 2^{600 * (height\_delta + 1) - 600 * (height\_delta + 1) \over 172800} \\
    &= old\_target
    \end{aligned}
    $$

- ブロックが5分間隔で作られている場合
    $$
    \begin{aligned}
    next\_target &= old\_target * 2^{300 * (height\_delta + 1) - 600 * (height\_delta + 1) \over 172800} \\
    &= old\_target * 2^{-300 * (height\_delta + 1) \over 172800}
    \end{aligned}
    $$
    - 576ブロック（2日）でtargetは半分（難易度は倍）になる

- ブロックが60分間隔で作られている場合
    $$
    \begin{aligned}
    next\_target &= old\_target * 2^{3600 * (height\_delta + 1) - 600 * (height\_delta + 1) \over 172800} \\
    &= old\_target * 2^{3000 * (height\_delta + 1) \over 172800}
    \end{aligned}
    $$
    - 57.6ブロック（2日）でtargetは倍（難易度は半分）になる


---
# 1.4. Automatic Replay Protection
- 計画的なハードフォークでノードの更新を強制する仕組み
- ABC以外のノードではもともと導入されていなかった
- ハードフォークする日付が前回のハードフォーク時に決まる
- 前回のハードフォークでIFPの議論が不十分なまま実装された一因
- 今回のハードフォーク以降は仕様から外される

---
# 2. BCHとABC
1. [~5月] IFPの議論から、Bitcoin ABC(ABC)のIFPに反対する開発者がBitcoin Cash Node(BCHN)を立ち上げる
    - 参考: https://tech.coincheck.blog/entry/2020/05/26/114705
1. [7月頃] [ASERTが提案される](https://read.cash/@jtoomim/bch-upgrade-proposal-use-asert-as-the-new-daa-1d875696)
1. [7月頃] [ABC側でGrasbergという別の難易度調整アルゴリズムが提案される](https://read.cash/@deadalnix/announcing-the-grasberg-daa-88c61cee)
1. [~8月] [コミュニティの議論の中でASERTが優勢となる](https://read.cash/@sha256_88ebd526/bitcoin-cash-bch-november-2020-upgrade-statement-f7c03159)
    - ABC以外の主なノード実装はASERTを採用する

---

5. [8月] [ABCがASERTを受け入れるが、同時にIFPを強制するルールも追加する](https://medium.com/bitcoin-abc/bitcoin-abcs-plan-for-the-november-2020-upgrade-65fb84c4348f)
    - ABCがソフトフォークすることになる
1. [8月~] [大手のマイナーがBCHNを支持するシグナルを出し始める](https://news.bitcoin.com/hash-watch-bitcoin-cash-miners-begin-signaling-node-implementations/)
    - https://cash.coin.dance/blocks/historical
1. [8月~] [BCHとABCのハードフォーク後の先物取引を始める取引所が出てくる](https://coinflex.com/ja/blog/coinflex-launches-governance-futures-bch-vs-bch-abc/)
1. [11月] [ABCがBCHNのネットワークに従う実装もサポートし始める (Bitcoin ABC BCHN Edition)](https://blog.bitcoinabc.org/2020/11/06/bitcoin-abc-will-support-both-bcha-and-bchn-after-the-chain-split/)

---
# 3. ハードフォーク後の動向
- 661648ブロックでフォーク
- ハッシュレートはBCH:ABCが95:5ほどの割合
- [難易度の遷移](https://blockchair.com/bitcoin-cash/charts/difficulty?interval=1m&compare=bitcoin-abc)
- BCHN
    - 大きな影響はなくBCHを継承
- ABC
    - ハードフォーク後24時間で7ブロックしか掘られなかった
        - すでに難易度調整により正常になっている
    - mempoolが頻繁に詰まっている
    - [ABCを煽るようなメッセージのついた空ブロックが掘られる](https://news.bitcoin.com/slow-and-empty-blocks-with-a-mysterious-message-abcs-new-chain-off-to-a-rocky-start/)
        - ハッシュレートが低いのでマイナーに遊ばれてる

---
# おわりに
- 今回の分裂はコミュニティ主導のプロジェクトをコミュニティが守るという点で成功
- BCHNを中心としたBCHコミュニティの尽力あっての成功
- コミュニティの分裂という痛みは今回もあった
