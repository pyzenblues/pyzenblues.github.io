---
layout: post
title: "Monty Hall problem"
date: 2018-07-13 20:21:00 +0800
categories: blog
---

# Monty Hall problem

> Suppose you’re on a game show, and you’re given the choice of three doors. Behind one door is a car, behind the others, goats. You pick a door, say number 1, and the host, who knows what’s behind the doors, opens another door, say number 3, which has a goat. He says to you, “Do you want to pick door number 2?” Is it to your advantage to switch your choice of doors?

上面引述的這段話是經典的機率問題，出現在 [Let's Make a Deal](https://en.wikipedia.org/wiki/Let%27s_Make_a_Deal) 美國電視節目上。主持人會提供三道門讓玩家選擇，其中只有一扇門背後是車子，另外兩扇門你打開會看到山羊。在你選擇了某扇門之後，主持人會打開一扇後面是山羊的門，然後會再次給你選擇的機會，請問這時候你要換嗎？還是繼續堅持自己原本的選擇呢？到底換或不換哪邊猜中車子的機率比較大呢？

在思考機率這類問題，盡量不要依賴直覺，通常你的直覺只會幫倒忙。像作者原本憑直覺覺得換或不換機率都是 $\frac{1}{2}$，深入了解問題後才發現案情並不單純。通常 sample space 不大的情況下，我們可以把每個可能的 outcome 列出來，並把每個 outcome 的機率計算出來。最後釐清題意，例如問題可以是「車子在 door C 的機率是多少」，又或者「玩家換的話，贏的機率是多少」。暸解問題後，接下來就是定義符合這次 event 的 outcome 有哪些，把這些機率全部加起來，這樣就算大功告成了。

在我們找出 sample space 之前，為了讓案情更單純，我們先做一些假設：
- 車子出現在 $$\{A,B,C\}$$ 這三扇門之一的機率是一樣的
- 玩家的選擇是隨機的，跟車子在哪扇門沒有關係
- 玩家做出選擇後，主持人一定要隨機打開某扇背後是山羊的門，並給玩家再次選擇的機會

下面這張圖是列出假如車子在 door A 的所有 outcome，例如 $$\{A,C,B\}$$ 就是指車子在 door A、玩家選擇 door C、主持人只能選擇 door B。
$$\{A,A,B\}$$ 則是指車子在 door A、玩家選擇 door A、主持人隨機選了 door B。

<div class="mermaid">
graph LR
    subgraph level 0
    L0{car location}-- A -->L11{player's guess}
    L0-- B -->L12{player's guess}
    L0-- C -->L13{player's guess}
    end
    subgraph level 1
    L11-- A -->L21{host open door}
    L11-- B -->L22{host open door}
    L11-- C -->L23{host open door}
    end
    subgraph level 2
    L21-- B -->L31[host open B]
    L21-- C -->L32[host open C]
    L22-- C -->L33[host open C]
    L23-- B -->L34[host open B]
    end
</div>

這兩個 outcome 組成的 event 的機率又是多少呢？
$$
\begin{align*}
\Pr(\{(A,C,B),(A,A,B)\}) &= \frac{1}{3} \times \frac{1}{3} \times 1 + \frac{1}{3} \times \frac{1}{3} \times \frac{1}{2}\\
&= \frac{1}{6}
\end{align*}
$$

車子在 door B or C 的所有可能就請讀者自行腦補，作者這邊直接列出樣本空間。

$$
S =
\left\{
\begin{align*}
(A,A,B),(A,A,C),(A,B,C),(A,C,B),(B,A,C),(B,B,A),\\
(B,B,C),(B,C,A),(C,A,B),(C,B,A),(C,C,A),(C,C,B)
\end{align*}
\right\}
$$
and $$\Pr(S) = 1$$

回到原本的問題，在主持人打開一扇門，門後是山羊的狀況下，玩家如果更換選擇，贏的機率是多少呢？符合這個事件的 outcome 有
$$\{(A,B,C),(A,C,B),(B,A,C),(B,C,A),(C,A,B),(C,B,A)\}$$

計算每個 outcome 的機率再通通把它加起來，結果玩家更換選擇真的比較好，有沒有很神奇呢！

$$
\begin{align*}
\Pr(wins\:by\:switching) &= \Pr(\{(A,B,C)\}) + \Pr(\{(A,C,B)\}) + \Pr(\{(B,A,C)\}) +\\
&\quad\quad \Pr(\{(B,C,A)\}) + \Pr(\{(C,A,B)\}) + \Pr(\{(C,B,A)\})\\
&= \frac{1}{9} + \frac{1}{9} + \frac{1}{9} + \frac{1}{9} + \frac{1}{9} + \frac{1}{9}\\
&= \frac{2}{3}
\end{align*}
$$

筆記就先寫到這邊，希望讀者下次遇到機率問題不會再顏面抽搐、宛如身在地獄。話雖如此，要克服看到機率就頭痛的症狀恐怕很難，作者也還在治療中 : P
