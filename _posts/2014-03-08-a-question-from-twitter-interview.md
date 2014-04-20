---
layout: post
title: Twitter的一道面试题
tags: algorithms
published: false
---

> [i-failed-a-twitter-interview](http://qandwhat.apps.runkite.com/i-failed-a-twitter-interview/)

``` ruby
def easy_volumes(walls)
  lm = rm = 0
  l, r = 0, walls.length - 1
  vols = 0

  while l < r
    lm = walls[l] if walls[l] > lm
    rm = walls[r] if walls[r] > rm
    if lm >= rm
      vols += rm - walls[r]
      r -= 1
    else
      vols += lm - walls[l]
      l += 1
    end
  end

  vols
end
```

精妙，没有一丝多余。
