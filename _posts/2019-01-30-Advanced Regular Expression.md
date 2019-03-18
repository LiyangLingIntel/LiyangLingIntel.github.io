---
layout:     post
title:      "Advanced Regex"
subtitle:   "Tricks in Python Regular Expression"
date:       2019-01-30
author:     Lyon Ling
header-img: img/post-bg-pythonregex.jpg
catalog: true
tags:
    - Python
    - Tricks 
---

When I started to use python regex, the most commonly used are `.` `*` `?` `()` `[]` `\d` `\s` and etc. However, I have sometimes met problems like I cannot partial match strings or control the groups as I needd.

So, here I found some techniques which would be helpful.

### Expressions

1. `(?:A)`

   Cancel the group function of `()`. That means the content matched `A `would not form a group.

2. `(?<=B)A`

    Positive lookbehind assertion. This matches the expression `A` only if `B` is immediately to its left. This can only matched fixed length expressions.

3. `A(?=B)` 

   Lookahead assertion. This matches the expression `A` only if it is followed by `B`.

4. `A(?!B)` 

   Negative lookahead assertion. This matches the expression `A` only if it is not followed by `B`.

5. `(...)\1` 

   The number `1` corresponds to the first group to be matched. If we want to match more instances of the same expresion, simply use its number instead of writing out the whole expression again. We can use from `1` up to `99` such groups and their corresponding numbers.

6. `(?aiLmsux)` 

   Here, `a`, `i`, `L`, `m`, `s`, `u`, and `x` are flags:

   - `a` — Matches ASCII only
   - `i` — Ignore case
   - `L` — Locale dependent
   - `m` — Multi-line
   - `s` — Matches all
   - `u` — Matches unicode
   - `x` — Verbose

7. `(? )` 

   Inside parentheses like this, `?` acts as an extension notation. Its meaning depends on the character immediately to its right.

8. `(?PAB)` 

   Matches the expression `AB`, and it can be accessed with the group name.

### Functions 

1. `re.findall(A, B)` 

   Matches all instances of an expression `A` in a string `B` and returns them in a list.

2. `re.search(A, B)` 

   Matches the first instance of an expression `A` in a string `B`, and returns it as a re match object.

   `re.match(A, B)`

   Similar to  `re.search`, while `re.match` will stop and return `None`, if does not match pattern at the beginning of the string.

3. `re.split(A, B)` 

   Split a string B into a list using the delimiter `A`.

4. `re.sub(A, B, C)` 

   Replace `A` with `B` in the string `C`.

   `re.subn(A, B, C, D)`

   Same as `re.sub`, while `D` means the relace times, if not specified, it will replace all matched patterns.