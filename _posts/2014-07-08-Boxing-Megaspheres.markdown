---
layout: post
title: "Puzzle: Boxing Megaspheres"
date: 2014-07-08 11:30:00 EST
categories: puzzle
highlight_in_list: true
---

Suppose you have a box, measuring one meter by one meter by one meter. If I give you spheres that are 0.99 meters in diameter, how many spheres can you fit into the box?

Just one, obviously, so let's make things a bit more interesting.

**A Million Dimensions**

Instead of a 3d box, I give you a *megabox*: a [million dimensional hypercube](http://en.wikipedia.org/wiki/Unit_cube#Unit_hypercube) spanning one meter along each axis. I also provide a bunch of *megaspheres*, [million-dimensional hyperspheres](http://en.wikipedia.org/wiki/N-sphere) scaled to be 0.99 meters in diameter. When you place one of these spheres into the box, there's barely a centimeter of clearance along any given axis... but there's a whole lot of axies.

How many megaspheres can you fit into the megabox?

**Spacing**

Try to work out a good lower bound before reading the hints below. Or just write down a guess, and read on.

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

**Hint #1**

Yes, it's more than one.

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

**Hint #2**

What's the distance between opposite corners of the box?

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

**Hint #3**

There's a huge number of corners to push things against.

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

...

**More Than One**

Let's convince ourselves that we can *at the very least* put two megaspheres into the box.

The counter-intuitive thing you have to realize about this box is that, although side-to-side distance is quite cramped, there's *boatloads* of diagonal distance to work with. If the box goes from $(0,0,...,0)$ to $(1,1,...,1)$, i.e. it covers $[0,1]^{1000000}$, then the distance between two opposite corners is $\sqrt{1^2 + 1^2 + ... + 1^2} = \sqrt{1000000 \cdot 1^2} = 1000$. That's pretty surprising, I think, so let's try to use it by putting spheres along that long main diagonal.

A sphere is inside the box when all of the coordinates for its center are between $0.495$ and $0.505$. This limits how much of that long diagonal we can use, but there's still more than enough to work with. We'll place sphere #1 at $(0.495, 0.495, ..., 0.495)$ and sphere #2 at $(0.505, 0.505, ..., 0.505)$. So sphere #1 is on the all-zero-ward corner of the valid range, touching all the zero-ward sides of the box, and sphere #2 is the same except it's one-ward.

The distance between the two spheres is $\sqrt{(0.505-0.495)^2 + (0.505-0.495)^2 + ... + (0.505-0.495)^2}$, which simplifies to $\sqrt{1000000 \cdot 0.01^2} = \sqrt{100} = 10$. Because $10$ is more than $0.99$, the spheres are not touching. They're not even close to touching! We can easily fit eight more megaspheres on the same diagonal.

Let's move on to a more complicated solution.

**A Lower Bound**

Unfortunately, because [packing problems](http://en.wikipedia.org/wiki/Packing_problems) are notoriously difficult, we are not going to go for an exact solution. We're going to try to find a very rough lower bound, to get an idea of how large the answer is. To get that lower bound, we'll make two big simplifications to the problem.

The first simplification is to assume that all of the spheres are pushed up against the sides of the box. That is to say, we only place spheres centered on corners of the imaginary smaller box covering just $[0.495, 0.505]^{1000000}$.

This is a useful simplification because it moves the problem into the domain of [coding theory](http://en.wikipedia.org/wiki/Coding_theory). Each corner of the box is identified by a binary sequence like $010111...0101$, with the $i$'th bit set to $1$ if the corner is one-ward along the $i$'th dimension (i.e. its $i$'th coordinate is $0.505$), and otherwise set to $0$ because the corner is zero-ward ($i$'th coordinate is $0.495$).

The true distance between two corners is determined by how many of their bits differ, which is known as the [Hamming distance](http://en.wikipedia.org/wiki/Hamming_distance). Adjacent corners have a Hamming distance of 1, because they only differ by one bit, and this corresponds to an actual Euclidean distance of 0.01 between them. If $d$ bits differ between two corners, then the Euclidean distance between those corners is $\sqrt{d \cdot 0.01^2} = \frac{\sqrt{d}}{100}$.

Our megaspheres intersect if and only if their centers are less than a distance of $0.99$ apart. So if two corners each have a sphere placed on them, then those corners must have enough differing bits for $\frac{\sqrt{d}}{100} \geq 0.99$ to be satisfied. Simplifying, we find that the minimum Hamming distance between used corners is $d \geq 99^2 = 9801$.

So our goal is to find a large set of corners, subject to the constraint that all corners in the set must differ from each other in at least $9801$ bit positions.

Now comes the second simplification. Instead of finding an optimal set of points, we're going to use a pattern involving [polynomials over finite fields](http://en.wikipedia.org/wiki/Finite_field_arithmetic).

We start by partitioning the million bits used to specify each corner into groups of size $16$.

The bits in each group will encode a value from the (unique) finite field of size $2^{16}$, commonly called ${\mathbb{F}_{2^{16}}}$.
Unfortunately, explaining how addition and multiplication work in finite fields is beyond the scope of this post.
The main thing you have to know is that polynomials work almost exactly like they do over the reals. More specifically, what we care about is that when two polynomials of degree $b$ or less agree on $b+1$ or more points, then they must be the same polynomial. Conversely, if we start with two different polynomials of degree at most $b$ and sample $b+s$ points from each, then at least $s$ of those points must have differed.

Each corner specifies $\frac{1000000}{16} = 62500$ points (each being a group of $16$ bits), and we need at least $9801$ bit differences. So we set $s=9801$ and $b=52699$. This is actually very conservative, because we're counting only 1 bit difference per point difference, but a single point difference could be up to 16 bit differences. (If you're wondering why I'm not sidestepping that issue by using a smaller finite field, like ${\mathbb{F}_{2}}$, it's because the size of a finite field places a limit on the maximum degree of polynomials it can contain. We can only use the finite field with $2^{16}$ values because $2^{16} > \frac{1000000}{16}$. Also the size must be a prime power, but I digress.)

The way we will pick corners is by enumerating the polynomials of degree $52699$ (or less) in ${\mathbb{F}_{2^{16}}}$. There are $52700$ coefficients to pick (one for each power of $x$ from $x^0$ to $x^{52699}$), and each coefficient can be any of the $2^{16}$ values in the finite field, so there are $\left({2^{16}}\right)^{52700}$ such polynomials. For each polynomial $P(x)$ we will yield a corner whose $i$'th group of bits encodes $P(i)$, the polynomial's value at $i$, for each $i$ in range. We place a megasphere on each yielded corner, and that's it.

The spheres we just placed aren't touching, because the corners are generated in a way that guarantees they have a sufficiently large hamming distance, and the spheres are inside the box, because their centers fall in the valid range.

How many spheres did we just fit?... Approximately $10^{253828}$.

And our encoding is nowhere near optimal! The [Hamming bound](http://en.wikipedia.org/wiki/Hamming_bound) on a million bit code with a minimum distance of $9801$ is $\frac{2^{1000000}}{\sum_{k=0}^{4900} {1000000 \choose k}}$, or approximately $10^{287591}$, which is tens of thousands of orders of magnitude better.

And we limited ourselves to only using the corners! No matter what we do at the corners, there's still room for another sphere at the very center of the box (and plenty more along with it).

So do be awestruck at the sheer vastness of the number of megaspheres we can put into this box, but don't be surprised if the true solution has far more digits than the puny quarter million digit lower bound I just gave.

**Summary**

How many million-dimensional spheres can we pack into a slightly wider box? A lot.

**Update (2018)**

[There is a much better, simpler, lower bound for this problem.](https://news.ycombinator.com/item?id=16886810)

When you place a megasphere in the megabox, it is preventing you from placing other megapsheres nearby.
Specifically, if you place a megasphere centered at position $p$, this sphere makes it impossible to place a second megasphere centered at any point $q$ within 0.99 meters of $p$.
No other points are eliminated by placing a megasphere.
So, in other words, placing a sphere decreases the remaining 'available volume' by at most the volume of a sphere of radius 0.99 meters.

The volume of a million-dimensional hypersphere with a radius of 0.99 meters is $(0.99 \sqrt{\pi})^{10^6} / \Gamma(10^6/2 + 1) \approx 10^{-2388130}$.
We start with $0.01^{10^6}$ volume of available space to place sphere centers, and each placed megasphere reduces that space by at most $10^{-2388130}$.
Do the division, and you see that it must be possible to place at least $10^{388118}$ megaspheres in the megabox.

---

[Discuss on Reddit](http://www.reddit.com/r/math/comments/2a55h0/puzzle_boxing_megaspheres/)
