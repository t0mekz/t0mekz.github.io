---
layout: post
title: Dependency Inversion Principle
image: /assets/images/posts/dependency/head.png
---
Source:  [Mostly based on Robert C. Martin article Engineering Notebook columns for The C++ Report.](https://drive.google.com/file/d/0BwhCYaYDn8EgMjdlMWIzNGUtZTQ0NC00ZjQ5LTkwYzQtZjRhMDRlNTQ3ZGMz/view)
## Definition
As whole SOLID principles, also Dependency Inversion Principle comes to the world thanks to the Uncle Bob (Robert C. Martin).
The most common principle definition what you can find in the Internet are those two points:
>* High-level modules should not depend on low-level modules. Both should depend on abstractions.
>* Abstractions should not depend on details. Details should depend on abstractions.

But what the hell that's even supposed to mean in real life?

## What bad quality code means?

The same as Uncle Bob did in his Engeneering Notebook, when he was describing DIP, let we also start from the very beginning and answer to the _simple_ question: What do we used to call as a _"bad code"_ or _bad design_?

![](https://cdn-images-1.medium.com/max/1200/1*J2mKSLBEp_jUbMtOWXTTjQ.png)

Here are 3 points of bad design code pointed by Uncle Bob:
>1. It is hard to change because every change affects too many other parts of the system. (Rigidity)
>2. When you make a change, unexpected parts of the system break. (Fragility)
>3. It is hard to reuse in another application because it cannot be disentangled from the current application. (Immobility)

We all know this kind of code from our experience! _At least I know, maybe you are the better develeper, good to you!_ Is that mean we are a terrible developers? Of course not! _I like to belive in that at least, lol_ 
Now when we know what does the bad design mean, we can start to think how we can avoid that in the future. I believe you know what is the answer for this need. 
__Dependency Inversion Principle!__ 
In fact yes and no. 
So maybe the whole __SOLID__?!  
_Yes!_ 
and 
_No!_ as well. 

What I want to expose to you here is, __there is no holy grail!__

Of course all those pricinples are the fundaments of good OOP, but there are whole lot more than that, so keep learning and start implementing this in real world scenarios, SOLID is a good point to start!

## What DIP is used for?

Let consider this following, simple code snippet:
```ruby
class CreatePayment
  def initialize(params)
    @params = params
  end

  def call
    payment = create_payment
    generate_logs(payment)
    # do some other stuff
  end
  
  private
  
  attr_reader :data
  
  def create_payment
    GreatPayments.new(params).create
  end
  
  def generate_logs(payment)
    FancyLogsGenerator.new(payment).generate
  end
end
```

As you should expect this snippet violates DIP and is also a good example of bad designed code, but where exactly is the problem? Till now, I would consider that snippet as a good code example.

Do you remember those three bad design points I mentioned above? 
_I haven't done this only to play with you in this silly game above._ 

Let start with the simplest one:
>3. It is hard to reuse in another application because it cannot be disentangled from the current application. (Immobility)

Are we able to copy paste this class to the whole new application and expect it to work properly? I don't think so. 
To make it possible we will have to add all coupled classes like `GreatPayments` and `FancyLogsGenerator`.

>1. It is hard to change because every change affects too many other parts of the system. (Rigidity)
>2. When you make a change, unexpected parts of the system break. (Fragility)

Those two points are quite similar, so let me to explain it together.

Consider that you have to use different payment providers depending on client who are making payment or you have found better/cheaper log provider.
All of these will cause you to make changes in the whole  `CreatePayment` class(!) and that can cause potential system breaks.

I know this example is quite simple and maybe it hard to see the real problem here, but trust me changing existing and working on production class for crucial action like creating payments it's not the most comfortable thing in the world. In real world situations classes are more complicated and there are a lot more things which can possible go wrong.
## DIP in action!
If you are interested how DIP looks in action, here's our fixed example:

```ruby
class CreatePayment
  def initialize(params, payments_provider, log_generator)
    @params = params
    @payments_provider = payments_provider
    @log_generator = log_generator
  end

  def call
    payment = create_payment
    generate_logs(payment)
    # do some other stuff
  end
  
  private
  
  attr_reader :data
  
  def create_payment
    payments_provider.create
  end
  
  def generate_logs(payment)
    log_generator.generate
  end
end

CreatePayment.new(params, GreatPayments.new(params), FancyLogsGenerator.new(payment))
```
Only by this two simple changes we made our code:
* Flexibility - if you want to use different payment provider, just inject it during a call, but remember to provide the same interface!
*  Stability - until you will provide the same interface to your dependencies, you don't have to make any changes in your high-level class, smaller chance to made a mistake.
* Mobility - you can easily copy-paste this class to your new application and by sure it will work, becuase there is any thightly coupled elements
* Easier testing - from now you don't have to mock your dependencies anymore, just provide them!
## Final thoughts

I hope I proved to you how important and useful DIP is. More than that, it is easy to use!

Probably you have heard before about other techniques and priciples like _Dependency Injection (DI)_ and _Inversion of Control (IoC)_. It can looks very similar to DIP and that can be confusing.

Martin Fowler in his article [DIP in the Wild](https://martinfowler.com/articles/dipInTheWild.html#YouMeanDependencyInversionRight) are explaining this a little bit wider, but for now let me quote just a 
most important piece:

>DI is about how one object acquires a dependency. When a dependency is provided externally, then the system is using DI. IoC is about who initiates the call. If your code initiates a call, it is not IoC, if the container/system/library calls back into code that you provided it, is it IoC.
>
>DIP, on the other hand, is about the level of the abstraction in the messages sent from your code to the thing it is calling. To be sure, using DI or IoC with DIP tends to be more expressive, powerful and domain-aligned, but they are about different dimensions, or forces, in an overall problem. 
>**DI is about wiring, IoC is about direction, and DIP is about shape.**

I will try explain IoC and DI more in my future articles.
