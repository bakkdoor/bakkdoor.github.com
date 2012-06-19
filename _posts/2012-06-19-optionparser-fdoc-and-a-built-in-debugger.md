---
layout: post
title: "OptionParser, FDoc and a built-in debugger"
category:
tags: []
---
{% include JB/setup %}

## Debugger

I've been adding a bunch of new features to Fancy recently.  The most
exciting to me right now probably is the built-in debugger that comes
with Fancy. It's based on
[Rubinius' built-in command-line debugger](http://rubini.us/doc/en/tools/debugger/)
but obviously allows for Fancy-specific options and debugging support.

I started working on this after talking to
[Brian](http://brixen.io/) at
[Euruko 2012 in Amsterdam](http://www.euruko2012.org/) about Rubinius,
ideas for Nikita (which he talked about in his talk) and tool support
on top of Rubinius in general.

So if you want to debug some Fancy code, you can start up the
command-line debugger like this:

{% highlight bash %}
$ fancy -debug <source_file>
{% endhighlight %}

This will launch you directly into the debugger before starting
execution of the programm. If you don't pass a source file to execute
along, it will simply start debugging Fancy's REPL,
[ifancy](https://github.com/bakkdoor/fancy/blob/master/bin/ifancy),
which always gets started when you don't pass a source file to the
`fancy` executable.

From the debugger prompt you can now specify breakpoints by class and
method name, just as you'd do with Ruby and Rubinius' debugger. See
the
[official documentation page for Rubinius' command-line debugger](http://rubini.us/doc/en/tools/debugger/)
for general help on how to use it.

The main difference is that you can now set breakpoints on Fancy
classes & methods, evaluate Fancy code right from within the debugger
(and based on the environment you're debugging) etc. It's not perfect
but it gets the job done. I do want to write a nicer, graphical
debugger some time for Fancy using the available APIs Rubinius
provides and that are used in the reference command-line
debugger. Maybe once Nikita is ready and working, hooking it up with
Fancy should be pretty straight forward.

The necessary changes to get the debugger working with Fancy are
actually quite simple and short. The whole implementation is only 66
lines of Fancy code and is in Fancy's repository at
[lib/rbx/debugger.fy](https://github.com/bakkdoor/fancy/blob/master/lib/rbx/debugger.fy).

Apart from starting up the debugger explicitly beforehand, you can
also just set a breakpoint in your code explicitly and then run your
code normally, like so:

{% highlight fancy %}
require: "rbx/debugger"
def my_method {
  # do something
  # Pause execution and start up the debugger at this point:
  Rubinius Debugger start
  # do some more
}
{% endhighlight %}

For more information on available commands type `help` in the debugger prompt.


## OptionParser

Fancy now also comes with a built-in [OptionParser](https://github.com/bakkdoor/fancy/blob/master/lib/option_parser.fy) class that deals
with your standard command-line option parsing needs. It's basically
inspired by Ruby's `optparse`. It automatically provides a `--help`
option using the information you provided when setting up your
command-line options.

Here's an example:

{% highlight fancy %}
# Save this to options.fy
require: "option_parser"

o = OptionParser new: @{
  banner: "My crazy server options:"
  with: "-port [port]" doc: "Set the listening port" do: |port| {
    @port = port
  }
  with: "--auto-update" doc: "Auto update settings" do: {
    # do something really smart
  }
}

# you can pass any Array in here, but most people will just pass in ARGV:
o parse: ARGV
{% endhighlight %}

This will automatically provide the following `--help` option output:

{% highlight bash %}
$ fancy options.fy --help
My crazy server options:

  --auto-update  Auto update settings
  --help         Display this information
  -port [port]   Set the listening port
{% endhighlight %}

Options are sorted alphabetically in ascending order.


## FDoc

[FDoc](https://github.com/bakkdoor/fancy/blob/master/bin/fdoc) has
been a part of Fancy for quite some time now (~1.75 years) and
generates the JSON based output for the API documentation page for
Fancy available at: [api.fancy-lang.org](http://api.fancy-lang.org/).
Initially started and written by
[Victor Borja](https://twitter.com/vborja) back in 2010, it never
really was finished up to what he intented to do. And I also never
really fully got into how it all worked until recently when I decided
to start cleaning things up a bit and making it useful outside of just
generating documentation for Fancy's standard library itself.

I'm happy to say that as of now FDoc can be used to generate
documentation pages similar to
[api.fancy-lang.org](http://api.fancy-lang.org/) for any Fancy based
project. It will even provide those nice GitHub links to method
definitions, if you pass along the github repository user/reponame
combination. I also fixed some bugs that I found and now when you
click on the little octocat on the right side of a method
documentation section, it'll jump to GitHub and highlight the complete
code of the method you were looking at before. It's quite nice if you
want to see what the actual implementation of a method looks like.

Run `fdoc --help` to see all available options.


## What's next?

I've been working on a bunch of other stuff that makes day-to-day
development with Fancy easier and more fun. There's
[Fake](http://github.com/fancy-lang/fake), a simple Rake like
automation tool. I've started work on
[FPR](https://github.com/fancy-lang/fpr), Fancy's package registry
(still in the making), that will allow searching for and adding of new
Fancy packages available on GitHub. And of course a bunch of libraries
I added or other people have contributed to the community. It's still
quite small and growing, but as the language matures, I'm sure more
people will find interest in the language and want to help out.

I for myself am committed to this either way. :)

More updates coming soon.