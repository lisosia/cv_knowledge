# QUBO問題

## QUBOとは

Quadratic Unconstrained Binary Optimization（QUBO）問題とは、 $ q_i \in \{0, 1\} $ の場合に $ f(x) = \sum_{i,j} H_{i,j} q_i q_j $ を最小化する問題。

$ q_i \in \{0, 1\} $ の代わりに $ q_i \in \{-1, 1\} $ を使った定式化はイジングモデルと言うが、これもQUBOと等価。

MaxCut問題や巡回セールスマン問題はQUBOに帰着できる。

QUBOを単純に総当たりで解くと計算量は $ 2^N $ となり $ N $ が大きくなると指数的に計算量が増大する。

## シミュレーテッドアニーリング法（SA） / 焼きなまし法

QUBO問題の近似解を得るヒューリスティックとしてアニーリング法がある。山登り法のように単純に目的関数が小さくなるように変数を動かすのではなく、温度に比例したノイズを乗せることで、局所最適解に陥ることなく、大局解を発見できる可能性が高くなる。

QUBO問題を対象としてアニーリングを行うハードウェアが開発されている。CMOSを用いたものや、D-WAVE社の量子ビットを用いたものがある。

D-WAVE社のアニーリングマシンは量子ビットを使ってアニーリングを行う。局所解にトラップされないようにするのがアニーリング法の肝だが、量子アニーリングマシンでは、トンネル効果がこれを行うと期待されている。しかし、他のアニーリングマシンに比べて良い解が得られるかは未知数。

## QUBO問題に対してアニーリング法を用いることは有効か

実問題をQUBOに落とし込むときに、オーバーヘッドが大きい問題（問題サイズに比べてたくさんのQUBO変数が必要になる問題）では有効ではなさそう。

たとえば、巡回セールスマン問題には向いてなさそう（[1]）。

問題によっては有効かも。MaxCut問題を素直に定式化するとQUBOになる。

## 量子アニーリングマシンは有効か

D-WAVE社が実質唯一のベンダー。D-WAVE社のアニーリングマシンは現状、他のCMOSアニーラやSimulated Annealingにくらべて、

- 高速ではない
- ビット数は5000で少ない
- 高価

という状況で、現状量子を使う利点はない（[2]）。

また、よく言う量子超越性は、量子アニーリングマシンとは無関係なので注意（[3]）。これは量子ゲート方式の（汎用）量子コンピュータにのみ当てはまるもの。

## 参考文献

[1] [「量子」と組合せ最適化に関する怪しい言説 ―とある研究者の小言―](https://tasusu.hatenablog.com/entry/2021/07/03/131243)

[2] [Is there quantum advantage to be had with a D-Wave computer in 2020?](https://quantumcomputing.stackexchange.com/a/14630)

[3] [\[コラム\]なぜD-Waveは量子ゲート方式に行かざるを得なかったのか](https://blueqat.com/yuichiro_minato2/ac036704-f855-4a22-9ec3-98a237f34dbd)
