Have you ever tried to covert a string with emojis to bytes?


```python
text = 'Hi 🙋🏽‍♂️'

print(bytes(text, 'utf-8'))
```

    b'Hi \xf0\x9f\x99\x8b\xf0\x9f\x8f\xbd\xe2\x80\x8d\xe2\x99\x82\xef\xb8\x8f'


And wonder TH are the `\x` thingies and how do they even translates back to emojis?

Diving deep, you will find it takes five unicode characters to represent this single emoji.


```python
emoji = '🙋🏽‍♂️'
print("Length: ", len(emoji))
for c in emoji:
    code = ord(c)
    print(code, hex(code), c)
```

    Length:  5
    128587 0x1f64b 🙋
    127997 0x1f3fd 🏽
    8205 0x200d ‍
    9794 0x2642 ♂
    65039 0xfe0f ️


First we have the base emoji: person raising hand 🙋 `128587` or `0x1f64b` in hexadecimal. Often represented as `U+1F64B`

Then we have the skin tone modifier: light skin tone `127997`

If you add these two, you will get a gender neutral person with this skin tone raising hand.


```python
print(chr(128587) + chr(127997))
```

    🙋🏽


The second last one represents a male symbol, and the last one is called Variation Selector-16 (VS16), which indicates the previous character should be displayed as an emoji, rather than a symbol.

There are many characters can be displayed either as symbol or emoji.



```python
heart_symbol= '♥'
print(heart_symbol)
print(heart_symbol + chr(65039))
```

    ♥
    ♥️


Note: if the system or the renderer doesn't support that emoji, it might be rendered as a symbol. In that case, copy the output and paste it into a different text editor or browser to see the difference.

So, the first two and lat two represents two separate emojis. The middle character is called a Zero Width Joiner (ZWJ), which is used to combine two or more emojis.


```python
woman = '👩'
laptop = '💻'
print(woman + chr(8205) + laptop)
```

    👩‍💻


Of course, the system or font has to support these combinations, otherwise it will be rendered just separate.


```python
man = '👨'
toilet = '🚽'

print(man + chr(8205) + toilet)
```

    👨‍🚽


Now let's see how each unicode characters in general can be represented as bytes in the `utf-8` encoding.

Let's consider the character character `U+1F64B`.

First we have to determine how many bytes will be required to store this, according to the range of the character. We could store this number itself with just three bytes, but then there would be no way to decode it back to one single character. So this patter is used instead: ([why this?](https://en.wikipedia.org/wiki/UTF-8#Description))

`11110xxx 10xxxxxx 10xxxxxx 10xxxxxx`

Now lets put the bits of the number `0x1f64b` in this pattern. Lets first convert it to binary.



```python

print(bin(0x1F64B))
```

    0b11111011001001011


The pattern has 21 bits, so let's add 0s at start to match

`000011111011001001011`
`000 011111 011001 001011`

putting in the pattern: `11110000 10011111 10011001 10001011`

Now, what are the hexadecimal of these bytes?


```python
print(list(map(hex, [0b11110000, 0b10011111, 0b10011001, 0b10001011])))
```

    ['0xf0', '0x9f', '0x99', '0x8b']


Notice that these matches with the first 4 thingies we wanted to understand. Now you know what they mean.
