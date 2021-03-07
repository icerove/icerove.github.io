---
title: Analysis type on colon exp
date: 2021-03-02 21:24:33
updated: 2021-03-02 21:24:33
tags: 
- qly
- compiler
- programming language design
categories:
- qly
---

colon in quote and further unquote
```
'a:b => don't care in '
'a:f[b] => don't care too
'a:f[,b] => can happen in macro writing or code manipulation function, do we care at all???
```
When will you use a quote?
- to refer a symbol: `'a`
- to refer array of symbols: `'[a b c]`
- to refer to array of some symbol `'[a ,x b]`, where `x` is a var
- used in macros and code generation functions, such as `b[v[a 'some-type] 'f[foo [x:,a]]]`

In the first three cases, not care about type analysis in quote exp.
In the fourth case, it could be complex enough that require type analysis:
```
'f[foo [x:,b[
    v[a 3]
    func[a]
]]]
```

Therefor need always recurse in, both before colon and after colon part, without consider it as an error.
(If quote level is 0 and there is an orphane quote exp it is an error, but it will be caught by analyze-mexp-out)