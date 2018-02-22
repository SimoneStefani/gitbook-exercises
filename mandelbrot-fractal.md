## Introduction

In this exercise you will implement a Mandelbrot set generator, or rather an image generator. You should do some reading so that you understand the basics of what the Mandelbrot set is and why it can generate some beautiful images; this text only contains a minimal explanation.

## Mandelbrot

The Mandelbrot set is defined as the set of complex numbers $$c$$ for which the sequence $$z_n$$ does not approach infinity. The value $$z_n$$ is defined as follows:

$$
\begin{eqnarray*}
    z_0 &= &0 \\ 
    z_{n+1} & = &z_n^2 + c
\end{eqnarray*}
$$

If you remember how to do the square of a complex numbers you know everything there is to know to start:

$$
\begin{eqnarray*}
    (x + yi)^2 = x^2 - y^2 + 2xyi
\end{eqnarray*}
$$

How do we know if a complex number $$(x + yi)$$ belongs to the Mandelbrot set? We could of course start to compute $$z_n$$ for higher values and see if we approach infinity but that would of course take a very long time (to say the least). 

An observation saves us from spending the rest of our lives computing $$z_n$$: *if $$|z_n| > 2$$ then there is no turning back, $$z_n$$ will only increase in size*. The absolute value of a complex number is of course:

$$
\begin{eqnarray*}
    |(x + yi)| = \sqrt{x^2 + y^2}
\end{eqnarray*}
$$

We still do not want to compute forever; if the number actually does belong to the Mandelbrot set we will of course never hit the threshold. Therefore, we set an upper limit $$n$$ that will be the maximum *depth* of our computation.

So given a maximum value of $$n$$, we can for any complex number $$c$$ say if it *definitely does not belong to* or if it *could possibly belong to* the Mandelbrot set. In the case where we know for sure that the number does not belong to the set we also have a value $$i$$ which was the point where $$|z_i| > 2$$. This value $$i$$ is the color we need to generate a beautiful Mandelbrot image.

### Complex Numbers
Since we are going to work with complex numbers we might as well start by implementing a module to handle these. Let's make it simple and represent a complex number as a tuple with its real and imaginary values. Create a module `Cmplx` that exports the following functions:

- `new(r, i)`: returns the complex number with real value $$r$$ and imaginary $$i$$
- `add(a, b)`: adds two complex numbers
- `sqr(a)`: squares a complex number
- `abs(a)`: the absolute value of $$a$$

You might want to use the `sqrt/1` function exported from the Erlang `:math` module when calculating the absolute value. You call Erlang modules like any module but the Erlang modules all have atoms as names.

``` elixir
> :math.sqrt(42)
```

The Complex module implements an abstract data type; the internals of how complex numbers are represented should not be visible outside of the module. We of course know, but we should not make use of this knowledge.

### The Brot Module

For no reason at all we will call our first module `Brot`, it will implement the computation of the $$i$$ value given a complex value $$c$$. We must of course give it a maximum iteration *depth* or we risk getting stuck in an infinite computation.

Implement a function `mandelbrot/2` that, given the complex number $$c$$ and the maximum number of iterations $$m$$, return the value $$i$$ at which $$|z_i| > 2$$ or 0 if it does not for any $$i < m$$ i.e. it should always return a value in the range $$0..(m-1)$$.

``` elixir
def mandelbrot(c, m) do
  z0 = Cmplx.new(..., ...)
  i = 0
  test(i, z0, c, m)
end
```

The `test/4` function should of course test if we have reached the maximum iteration, in which case it returns zero, or if the absolute value of `z` is greater than 2, in which case it returns `i`. Make sure that you use the functions exported from the `Cmplx` module.

Do some test to see that the function works (here I'm writing the complex numbers directly knowing that we represent them as tuples, this is of course cheating but very convenient).

- `Brot.mandelbrot(Cmplx.new(0.8, 0), 30)`
- `Brot.mandelbrot(Cmplx.new(0.5, 0), 30)`
- `Brot.mandelbrot(Cmplx.new(0.3, 0), 30)`
- `Brot.mandelbrot(Cmplx.new(0.27, 0), 30)`
- `Brot.mandelbrot(Cmplx.new(0.26, 0), 30)`
- `Brot.mandelbrot(Cmplx.new(0.255, 0), 30)`

What is happening? Which values could possibly belong to the Mandelbrot set - how sure are you? Do some more testing, why stop at thirty iterations? Try fifty!

### The Printer

Before carrying on we should make sure that we can generate an image. You should have the file `PPM` module that will write the final image to a file. Make sure that you can use this module and that you know where files are located when created.

The API to the module is:

`write(name, image)`: where the name is the name (possibly full path name) of the file and the image is a list of rows where each row is a list of tuples `{:rgb, r, g, b}` (each value being in the range 0..255).

So once we know that it is working we can carry on to produce some images.