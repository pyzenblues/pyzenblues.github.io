---
layout: post
title: "Iterators and Generators"
date: 2018-05-14 16:27:00 +0800
categories: blog
---

# Iterators and Generators

```python
for element in iterable:
    pass  # do something with element
```

在 Python 如果要迭代某個容器，上面的程式碼是很常見的做法，只要這個物件是 iterable 都可以這樣處理，例如 list set dict 等，或者讀檔也有類似的寫法。那要怎樣才能產生 iterator 呢？我們可以透過 `iter(iterable)` 得到 iterator，再用 `next(iterator)` 依序查訪容器裡每個元素，直到 iterator 拋出 StopIteration exception，Python 則提供 for-loop 語法簡化這個流程。

generator 長得很像 iterator，實作的方法是寫一個 function，一般的 function 如果需要回傳值可以用 return 回傳結果給 caller，但 generator 用 `yield` 來回傳結果。下面提供一個經典範例：[\[1\]](https://docs.python.org/3.5/reference/expressions.html#yield-expressions)

```python
def fibonacci(n):
    a, b = 0, 1
    for i in range(n):
        yield a
        a, b = b, a + b

[x for x in fibonacci(10)]  # print [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

generator 執行流程長得很像 `coroutine`，可以有多個 entry point，再透過 generator method 來控制執行流程。[\[2\]](https://docs.python.org/3.5/reference/expressions.html#generator-iterator-methods)

由於 generator 太過強大，導致筆者常常寫出佈滿臭蟲的 generator，不過用得好的話，也可以讓我們的程式碼看起來更簡潔。話不多說，再來看看一個簡單的範例：

```python
g1 = (i for i in range(1, 10))
g2 = (i * pow(10, i) for i in g1)
g3 = (pow((1 + 1 / i), i) for i in g2)
[e for e in g3]  # Euler's number
```

上面的 `(generator expressions)` 是一行版的 generator，並沒有用到 yield 語法。一層一層把 generator 傳下去，資料可以在每一層做相對應的處理。[\[3\]](https://docs.python.org/3.5/reference/expressions.html#generator-expressions)

讀者應該可以漸漸感受到 generator 強大的魔力，接下來我們要開始用可怕的 `yield` 語法。免責聲明，下面兩個範例純屬示範 XD

## 大老二發牌

```python
import itertools, random, collections


def coroutine(func):
    """
    用來 activate generator
    """

    def decorated(*args, **kwargs):
        g = func(*args, **kwargs)
        next(g)
        return g
    return decorated

def dealer(players):
    """
    撲克牌大老二，每張牌用 (number, color) 來表示
    """

    poker = [card for card in itertools.product(range(1, 13 + 1), range(4))]
    random.shuffle(poker)

    for i, card in enumerate(poker):
        players[i % len(players)].send(card)

    for p in players:
        p.close()

@coroutine
def player(name=None):
    player = dict(name=name, hand=[])

    try:
        while True:
            card = (yield)
            player['hand'].append(card)
    finally:
        sort_card().send(player)
        print(player)

@coroutine
def sort_card():
    player = (yield)
    player['hand'] = sorted(player['hand'], key=lambda t: (14, 3) if t == (2, 3) else t)
    yield 'end'


players = [player() for i in range(4)]
dealer(players)
```

## 十點半發牌

```python
def dealer(players):
    """
    撲克牌十點半
    """

    poker = [card for card in itertools.product(range(1, 13 + 1), range(4))]
    poker = [*filter(lambda card: card[1] in (0, 3), poker)]  # 濾掉紅色的牌
    random.shuffle(poker)

    players.append(player(name='dealer', dealer=True))  # 莊家

    compare_pipe = compare_with_dealer()

    while len(players) > 0:
        p = players[0]
        players.rotate()

        card = poker.pop()
        feedback = p.send(card)
        if feedback is False:
            p.send(compare_pipe)
            players.remove(p)

    compare_pipe.close()

@coroutine
def player(name=None, dealer=False):
    player_record = dict(name=name, hand=[], dealer=dealer)

    while True:
        draw = sum(0.5 if point > 10 else point for point, color in player_record['hand']) < 10.5 - 3
        if draw:
            card = (yield draw)
            player_record['hand'].append(card)
        else:
            compare_pipe = (yield draw)
            compare_pipe.send(player_record)

@coroutine
def compare_with_dealer():
    player_records = []

    try:
        while True:
            player_record = (yield)
            player_record['score'] = sum(0.5 if point > 10 else point for point, color in player_record['hand'])
            if player_record['dealer']:
                dealer_record = player_record
            else:
                player_records.append(player_record)
    finally:
        for player_record in player_records:
            if dealer_record['score'] > 10.5 > player_record['score'] or 10.5 >= player_record['score'] > dealer_record['score']:
                print('winner is {name}'.format(**player_record))


dealer(collections.deque([player(name=i) for i in range(4)]))
```

打完收工（咦？），這兩個範例只是為了測試 `generator.send` 而想出來的練習，平常應該不會想要跟 `next(generator)` 一起使用。`generator.send` 除了可以傳值給 generator，也可以接收 generator 回傳的 feedback，但請小心使用。當 caller 用 `generator.close` 結束 generator 時，generator 裡面的 try ... finally 語法可以在結束前執行一段程式碼。

以上程式碼皆在 Python 3.5.3 跑過，筆記先寫到這邊，希望各位讀者使用 generator 愉快 : )
