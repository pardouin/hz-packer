# HZ-Packer
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




