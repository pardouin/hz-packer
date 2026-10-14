# HZ Packer
https://pardouin.github.io/hz-packer/ is an online tool to pack python scripts as chinese characters with HZ-GB.

## About HZ-GB

HZ-GB represents chinese characters as pairs of ascii printable characters. The chinese sections are wrapped in `~{` and `~}`.
For example:
```python
bytes("China is 中国 or 中华人民共和国","hz")   >>>   b'China is ~{VP9z~} or ~{VP;*HKCq92:M9z~}'
```

There are 7445 chinese chars encoded this way so it doesn't cover all 8836 possible pairs of ascii chars in range 33..126.
The rules for a pair to exist can be summed up like this:

```
only printable ascii, no space or newline

banned prefix:   *+,-./xyz{|}~

banned suffix, for given prefix:
     &   ->   9:;<=>?@YZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~
     (   ->   ;<=>?@ABCDjklmnopqrstuvwxyz{|}~
     '   ->   BCDEFGHIJKLMNOPrstuvwxyz{|}~
     "   ->   !"#$%&'()*+,-./0cdop}~
     )   ->   !"#pqrstuvwxyz{|}~
     $   ->   tuvwxyz{|}~
     %   ->   wxyz{|}~
     W   ->   z{|}~
```

## Packing example

If you try to pack the following script:

```python
print(123456789)
```

it results in:

```python
exec(bytes('痱轭舁辈炒刀犯供','hz'))
```

which will basically do:

```python
exec(b'~{print(123456789)~}')
```

Which won't run. So you have to rewrite your script to fix this.
There's no way to make sense from the ending `~}` so you can just comment it out, and the opening `{` must be closed so for example this script would fix it in theory:

```python
print(123456789)}#   ->   ~{print(123456789)}#~}
```
(unary `~` is not defined for sets but it will evaluate the `print` before raising an error)

But the tool won't let you pack this because it creates impossible pairs, so for example this fix will do:

```python
[print(123456789),]};#   ->   ~{[print(123456789),]};#~}
```
which is finally packable AND runs.

## Comparison with the classic 2:1

The classic 2:1 unpacker costs 25 chars, so it's interesting if the script to pack is at least 52 bytes:

```
52/2 + 25 = 26 + 25 = 51
```

HZ unpacker costs 20 chars, but you need to tailor your script to both deal with the wrapper AND end up with only valid pairs, which will very rarely take less than 5 bytes to fix - it can take exceptionally less if your script was already shaped like a comprehension dict or set, for example. So a realistic lower bound would be a script of length 47 that requires 5 bytes to fix:

```
(47 + 5)/2 + 20 = 52/2 + 20 = 26 + 20 = 46
```

So in general HZ has more potential than the classic 2:1, but you get benefits from it only if your script can be fixed with at most 8~9 bytes, which is usually quite challenging - and the longer the script is, the more pairs to fix, so the window where HZ shines is not that wide.

Note: in Coconut, HZ unpacker cost 18 chars only, and Coconut `argv` is available without import through `os.sys`, leading to less rewriting, so sometimes HZ will save in Coconut while not saving in Python.

