---
layout: post
title: Calculating Planets' Volumes with Numpy
image: "/posts/milkyway.jpg"
tags: [Python, Numpy]
---

In this post I'm going to run through a function in Python that can quickly complete calculations on numbers in an array

Let's get into it!

---

Let's start be importing numpy and defining an array of the radius of the solar system planets (in km) as radii:

```ruby
import numpy as np

radii = np.array([2439.7, 6051.8, 6371, 3389.7, 69911, 58232, 25362, 24622])
```

Now, let's test the equation that will be used on one number (r):

```ruby
r = 10
volume = 4/3 * np.pi * r ** 3
print(volume)

>>> 4188.790204786391
```

After checking that the function is evaluating correctly, we can calcualte the volume of the planets:

```ruby
volumes = 4/3 * np.pi * radii ** 3
print(volumes)

>>> [6.08272087e+10 9.28415346e+11 1.08320692e+12 1.63144486e+11
     1.43128181e+15 8.27129915e+14 6.83343557e+13 6.25257040e+13]
```

if we had the radius of a million planets, we could calculate their volumes as quick as typing the below function:

```ruby
radii = np.random.randint(1,1000,1000000)

volumes = 4/3 * np.pi * radii ** 3
print(volumes)

>>> [2.69228478e+09 8.86803480e+08 1.56345757e+06 ... 3.40057454e+08
     1.91232096e+09 5.42675431e+08] 
```
This calculation could have been completed using other means, like a for loop, but that would take so much time and computing power.

***Numpy*** is a very powerful tool to conduct calcualtions. Since a single numpy array, can only contain a single data type, unlike lists or tuples which can contain many data types, the calculation is completed very quickly in fractions of a second.

