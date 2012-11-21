# Prelude

> Style is what separates the good from the great. <br/>
> -- Bozhidar Batsov

One thing has always bothered me as Ruby developer - Python developers
have a great programming style reference
([PEP-8](http://www.python.org/dev/peps/pep-0008/)) and we never got
an official guide, documenting Ruby coding style and best
practices. And I do believe that style matters. I also believe that
such fine fellows, like us Ruby developers, should be quite capable to
produce this coveted document.

This guide started its life as our internal company Ruby coding guidelines
(written by yours truly). At some point I decided that the work I was
doing might be interesting to members of the Ruby community in general
and that the world had little need for another internal company
guideline. But the world could certainly benefit from a
community-driven and community-sanctioned set of practices, idioms and
style prescriptions for Ruby programming.

Since the inception of the guide I've received a lot of feedback from
members of the exceptional Ruby community around the world. Thanks for
all the suggestions and the support! Together we can make a resource
beneficial to each and every Ruby developer out there.

By the way, if you're into Rails you might want to check out the
complementary
[Ruby on Rails 3 Style Guide](https://github.com/bbatsov/rails-style-guide).

# The Ruby Style Guide

This Ruby style guide recommends best practices so that real-world Ruby
programmers can write code that can be maintained by other real-world Ruby
programmers. A style guide that reflects real-world usage gets used, and a
style guide that holds to an ideal that has been rejected by the people it is
supposed to help risks not getting used at all &ndash; no matter how good it is.

The guide is separated into several sections of related rules. I've
tried to add the rationale behind the rules (if it's omitted I've
assumed that is pretty obvious).

I didn't come up with all the rules out of nowhere - they are mostly
based on my extensive career as a professional software engineer,
feedback and suggestions from members of the Ruby community and
various highly regarded Ruby programming resources, such as
["Programming Ruby 1.9"](http://pragprog.com/book/ruby3/programming-ruby-1-9)
and ["The Ruby Programming Language"](http://www.amazon.com/Ruby-Programming-Language-David-Flanagan/dp/0596516177).

The guide is still a work in progress - some rules are lacking
examples, some rules don't have examples that illustrate them clearly
enough. In due time these issues will be addressed - just keep them in
mind for now.

You can generate a PDF or an HTML copy of this guide using
[Transmuter](https://github.com/TechnoGate/transmuter).

Translations of the guide are available in the following languages:

* [Chinese Simplified](https://github.com/JuanitoFatas/ruby-style-guide/blob/master/README-zhCN.md)
* [Chinese Traditional](https://github.com/JuanitoFatas/ruby-style-guide/blob/master/README-zhTW.md)

## Table of Contents

* [Source Code Layout](#source-code-layout)
* [Syntax](#syntax)
* [Naming](#naming)
* [Comments](#comments)
* [Annotations](#annotations)
* [Classes](#classes)
* [Exceptions](#exceptions)
* [Collections](#collections)
* [Strings](#strings)
* [Regular Expressions](#regular-expressions)
* [Percent Literals](#percent-literals)
* [Metaprogramming](#metaprogramming)
* [Misc](#misc)

## Source Code Layout

> Nearly everybody is convinced that every style but their own is
> ugly and unreadable. Leave out the "but their own" and they're
> probably right... <br/>
> -- Jerry Coffin (on indentation)

* Use `UTF-8` as the source file encoding.
* Always add the file-encoding directive to the top of each ruby file in your project: `# encoding: UTF-8`
* Use two **spaces** per indentation level. No hard tabs.

    ```Ruby
    # good
    def some_method
      do_something
    end

    # bad - four spaces
    def some_method
        do_something
    end
    ```

* Use Unix-style line endings. (*BSD/Solaris/Linux/OSX users are covered by default,
  Windows users have to be extra careful.)
    * If you're using Git you might want to add the following
    configuration setting to protect your project from Windows line
    endings creeping in:

        $ git config --global core.autocrlf true

* Use spaces around operators, after commas, colons and semicolons, around `{`
  and before `}`. Whitespace might be (mostly) irrelevant to the Ruby
  interpreter, but its proper use is the key to writing easily
  readable code.

    ```Ruby
    sum = 1 + 2
    a, b = 1, 2
    1 > 2 ? true : false; puts 'Hi'
    [1, 2, 3].each { |e| puts e }
    ```

    The only exception is when using the exponent operator:

    ```Ruby
    # bad
    e = M * c ** 2

    # good
    e = M * c**2
    ```

* No spaces after `(`, `[` or before `]`, `)`

    ```Ruby
    some(arg).other

    items[1] = "Test"

    def do_bleh(thing1, thing2)
      # body ommitted
    end
    ```

  **EXCEPTION**: Array literals maye be defined with or without leading/trailing spaces

    ```Ruby
    # Both OK
    [1, 2, 3].length
    [ 1, 2, 3 ].length
    ```

* Don't use case statements in place of if/elsif/else conditionals.

    ```Ruby
    # VERY BAD, no argument to `case'
    case
    when song.name == 'Misty'
      puts 'Not again!'
    when song.duration > 120
      puts 'Too long!'
    when Time.now.hour > 21
      puts "It's too late"
    else
      song.play
    end

    # GOOD, the subject changes, so use an if.
    if song.name == 'Misty'
      puts 'Not again!'
    elsif song.duration > 120
      puts 'Too long!'
    elsif Time.now.hour > 21
      puts "It's too late"
    else
      song.play
    end
    ```

* Indent `when` as deep as `case`. I know that many would disagree
  with this one, but it's the style established in both the "The Ruby
  Programming Language" and "Programming Ruby".

    ```Ruby
    case person
    when Admin
      puts "Hola!"
    when User
      puts "Amigo!"
    when Guest
      puts "Good to see you friend."
    else
      raise VillianError.new(person)
    end

    kind = case year
           when 1850..1889 then 'Blues'
           when 1890..1909 then 'Ragtime'
           when 1910..1929 then 'New Orleans Jazz'
           when 1930..1939 then 'Swing'
           when 1940..1950 then 'Bebop'
           else 'Jazz'
           end
    ```

* Use empty lines between `def`s and to break up a method into logical
  paragraphs.

    ```Ruby
    def some_method
      data = initialize(options)

      data.manipulate!

      data.result
    end

    def some_method
      result
    end
    ```

* If a method calls argumemt list goes over 80ish characters (use your judgement), break
up the arguments onto multiple lines.

    ```Ruby
    # BAD. line is too long
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com', from: 'us@example.com', subject: 'Important message', body: source.text)
    end

    # good
    def send_mail(source)
      Mailer.deliver(
        to: 'bob@example.com',
        from: 'us@example.com',
        subject: 'Important message',
        body: source.text
      )
    end

    # goodish (arguments aligned)
    def send_mail(source)
      Mailer.deliver(to: 'bob@example.com',
                     from: 'us@example.com',
                     subject: 'Important message',
                     body: source.text)
    end
    ```

* Add underscores to big numeric literals to improve their readability.

    ```Ruby
    # bad - how many 0s are there?
    num = 1000000

    # good - much easier to parse for the human brain
    num = 1_000_000
    ```

* Use RDoc and its conventions for API documentation.  Don't put an
  empty line between the comment block and the `def`.
* Keep lines fewer than 80 characters.
* Avoid trailing whitespace.

## Syntax

* Use `def` with parentheses when there are arguments. Omit the
  parentheses when the method doesn't accept any arguments.

     ```Ruby
     def some_method
       # body omitted
     end

     def some_method_with_arguments(arg1, arg2)
       # body omitted
     end
     ```

* Only use `for` in ERB templates. `for` is implemented in terms of `each` (so
  you're adding a level of indirection), but with a twist - `for`
  doesn't introduce a new scope (unlike `each`) and variables defined
  in its block will be visible outside it.

    ```Ruby
    arr = [1, 2, 3]

    # bad
    for elem in arr do
      puts elem
    end

    # good
    arr.each { |elem| puts elem }
    ```

* Never use `then` for multi-line `if/unless`.

    ```Ruby
    # bad
    if some_condition then
      # body omitted
    end

    # good
    if some_condition
      # body omitted
    end
    ```

* Favor the ternary operator(`?:`) over `if/then/else/end` constructs.
  It's more common and obviously more concise.

    ```Ruby
    # bad
    result = if some_condition then something else something_else end

    # good
    result = some_condition ? something : something_else
    ```

* Use one expression per branch in a ternary operator. This
  also means that ternary operators must not be nested. Prefer
  `if/else` constructs in these cases.

    ```Ruby
    # bad
    some_condition ? (nested_condition ? nested_something : nested_something_else) : something_else

    # good
    if some_condition
      nested_condition ? nested_something : nested_something_else
    else
      something_else
    end
    ```

* Never use `if x: ...` - it is removed in Ruby 1.9. Use
  the ternary operator instead.

    ```Ruby
    # bad
    result = if some_condition: something else something_else end

    # good
    result = some_condition ? something : something_else
    ```

* Never use `if x; ...`. Use the ternary operator instead.

* Use `when x then ...` for one-line cases. The alternative syntax
  `when x: ...` is removed in Ruby 1.9.

* Never use `when x; ...`. See the previous rule.

* Use `&&/||` for boolean expressions, `and/or` for control flow.  (Rule
  of thumb: If you have to use outer parentheses, you are using the
  wrong operators.)

    ```Ruby
    # boolean expression
    if some_condition && some_other_condition
      do_something
    end

    # control flow
    document.saved? or document.save!
    ```

* Avoid multi-line `?:` (the ternary operator); use `if/unless` instead.

* Favor modifier `if/unless` usage when you have a single-line
  body.

    ```Ruby
    # good
    do_something if some_condition

    # meh
    if some_condition
      do_something
    end
    ```

* Favor `unless` over `if` for negative conditions (or control
  flow `or`).

    ```Ruby
    # bad
    do_something if !some_condition

    # good
    do_something unless some_condition
    ```

* Never use `unless` with `else`. Rewrite these with the positive case first.

    ```Ruby
    # bad
    unless success?
      puts 'failure'
    else
      puts 'success'
    end

    # good
    if success?
      puts 'success'
    else
      puts 'failure'
    end
    ```

* Don't use parentheses around the condition of an `if/unless/while`,
  unless the condition contains an assignment (see "Using the return
  value of `=`" below).

    ```Ruby
    # bad
    if (x > 10)
      # body omitted
    end

    # good
    if x > 10
      # body omitted
    end

    # ok
    if (x = self.next_value)
      # body omitted
    end
    ```

* Omit parentheses around parameters for methods that are part of an
  internal DSL (e.g. Rake, Rails, RSpec), methods that are with
  "keyword" status in Ruby (e.g. `attr_reader`, `puts`) and attribute
  access methods. Use parentheses around the arguments of all other
  method invocations.

    ```Ruby
    class Person
      attr_reader :name, :age

      # omitted
    end

    temperance = Person.new('Temperance', 30)
    temperance.name

    puts temperance.age

    x = Math.sin(y)
    array.delete(e)
    ```

* Prefer `{...}` over `do...end` for single-line blocks.  Avoid using
  `{...}` for multi-line blocks (multiline chaining is always
  ugly). Always use `do...end` for "control flow" and "method
  definitions" (e.g. in Rakefiles and certain DSLs).  Avoid `do...end`
  when chaining.

    ```Ruby
    names = ['Bozhidar', 'Steve', 'Sarah']

    # good
    names.each { |name| puts name }

    # bad
    names.each do |name|
      puts name
    end

    # good
    names.select { |name| name.start_with?('S') }.map { |name| name.upcase }

    # bad
    names.select do |name|
      name.start_with?('S')
    end.map { |name| name.upcase }
    ```

    Some will argue that multiline chaining would look OK with the use of {...}, but they should
    ask themselves - is this code really readable and can't the blocks contents be extracted into
    nifty methods?

* Avoid `return` where not required for flow of control.

    ```Ruby
    # bad
    def some_method(some_arr)
      return some_arr.size
    end

    # good
    def some_method(some_arr)
      some_arr.size
    end
    ```

* Avoid `self` where not required. (It is only required when calling a self write accessor.)

    ```Ruby
    # bad
    def ready?
      if self.last_reviewed_at > self.last_updated_at
        self.worker.update(self.content, self.options)
        self.status = :in_progress
      end
      self.status == :verified
    end

    # good
    def ready?
      if last_reviewed_at > last_updated_at
        worker.update(content, options)
        self.status = :in_progress
      end
      status == :verified
    end
    ```

* As a corollary, avoid shadowing methods with local variables unless they are both equivalent.

    ```Ruby
    class Foo
      attr_accessor :options

      # ok
      def initialize(options)
        self.options = options
        # both options and self.options are equivalent here
      end

      # bad
      def do_something(options = {})
        unless options[:when] == :later
          output(self.options[:message])
        end
      end

      # good
      def do_something(params = {})
        unless params[:when] == :later
          output(options[:message])
        end
      end
    end
    ```

* Use spaces around the `=` operator when assigning default values to method parameters:

    ```Ruby
    # bad
    def some_method(arg1=:default, arg2=nil, arg3=[])
      # do something...
    end

    # good
    def some_method(arg1 = :default, arg2 = nil, arg3 = [])
      # do something...
    end
    ```

    While several Ruby books suggest the first style, the second is much more prominent
    in practice (and arguably a bit more readable).

* Avoid line continuation (\\) where not required. In practice, avoid using
  line continuations at all.

    ```Ruby
    # bad
    result = 1 - \
             2

    # good (but still ugly as hell)
    result = 1 \
             - 2
    ```

* Using the return value of `=` (an assignment) is ok, but surround the
  assignment with parentheses.

    ```Ruby
    # good - shows intended use of assignment
    if (v = array.grep(/foo/)) ...

    # bad
    if v = array.grep(/foo/) ...

    # also good - shows intended use of assignment and has correct precedence.
    if (v = self.next_value) == 'hello' ...
    ```

* Initialize all instance variables that should have initialization defaults inside the constructor. Don't defer initialization to accessors with `||=` if you know that the attribute will be used. If it's possible that it'll go unused, go ahead and defer it since the fastest code is the code that never runs.

    ```Ruby
    # BAD
    class Sack
      def items
        @items ||= []
      end
    end

    # GOOD
    class Sack
      attr_reader :items

      def initialize
        @items = []
      end
    end
    ```

* Use `||=` freely to initialize variables.

    ```Ruby
    # set name to Bozhidar, only if it's nil or false
    name ||= 'Bozhidar'
    ```

* Don't use `||=` to initialize boolean variables. (Consider what
would happen if the current value happened to be `false`.)

    ```Ruby
    # bad - would set enabled to true even if it was false
    enabled ||= true

    # good
    enabled = true if enabled.nil?
    ```

* You should know the common Ruby global variables like `$0` and `$/`. Avoid `$1-9`.

* Never put a space between a method name and the opening parenthesis.

    ```Ruby
    # bad
    f (3 + 2) + 1

    # good
    f(3 + 2) + 1
    ```

* If the first argument to a method begins with an open parenthesis,
  always use parentheses in the method invocation. For example:
  
    ```Ruby
    # BAD
    add (3 + 2) + 1
    
    # GOOD
    add((3 + 2) + 1)
    
    # GOOD
    add( (3 + 2) + 1 )
    ```

* The new hash literal syntax is preferred in Ruby 1.9 when your hash keys are symbols.

    ```Ruby
    # bad
    hash = { :one => 1, :two => 2 }

    # good
    hash = { one: 1, two: 2 }
    ```

* The new lambda literal syntax is preferred in Ruby 1.9.

    ```Ruby
    # BAD
    lambda = lambda { |a, b| a + b }
    
    # GOOD
    lambda = ->(a, b) { a + b }
    ```

* Always use `#call` to execute a lambda:

    ```Ruby
    add = lambda { |a, b| a + b }
    
    # BAD
    add.(1, 2)
    
    # BAD
    add[1, 2]
    
    # ACCEPTABLE (use your judgement in context)
    add.call 1, 2
    
    # GOOD
    add.call(1, 2)
    ```

* Use `_` for unused block parameters.

    ```Ruby
    # bad
    result = hash.map { |k, v| v + 1 }

    # good
    result = hash.map { |_, v| v + 1 }
    ```

## Naming

> The only real difficulties in programming are cache invalidation and
> naming things. <br/>
> -- Phil Karlton

* Use `snake_case` for methods and variables.
* Use `CamelCase` for classes and modules.  (Keep acronyms like HTTP,
  RFC, XML uppercase.)
* Use `SCREAMING_SNAKE_CASE` for other constants.
* The names of predicate methods (methods that return a boolean value)
  should end in a question mark.
  (i.e. `Array#empty?`).
  
* The names of potentially "dangerous" methods (i.e. methods that modify `self` or the
  arguments, `exit!` (doesn't run the finalizers like `exit` does), etc.) should end with an exclamation mark if
  there exists a safe version of that *dangerous* method.
* Examples of *dangerous* methods would be methods that mutate collections, modify the file-system, or perform operations like `require` that can't be easily undone.
* Don't include non-bang "safe" methods just to have them unless that makes sense in the context of your API.

* When using `reduce` with short blocks, name the arguments `|a, e|`
  (accumulator, element).
* When defining binary operators, name the argument `other`.

    ```Ruby
    def +(other)
      # body omitted
    end
    ```

* Use `map` over `collect`, `detect` over `find`, `select` over
  `find_all`, `reduce` over `inject` and `size` over `length`.

## Comments

> Good code is its own best documentation. As you're about to add a
> comment, ask yourself, "How can I improve the code so that this
> comment isn't needed?" Improve the code and then document it to make
> it even clearer. <br/>
> -- Steve McConnell

* Write concise code
* Comments longer than a word are capitalized and use punctuation. Use [one
  space](http://en.wikipedia.org/wiki/Sentence_spacing) after periods.
* Avoid superfluous comments.

    ```Ruby
    # bad
    counter += 1 # increments counter by one
    ```

* Keep existing comments up-to-date. An outdated comment is worse than no comment
at all.

* Avoid writing comments to explain bad code. Refactor the code to
  make it self-explanatory. (Do or do not - there is no try. --Yoda)
  
* Keep in mind that there's no such thing as code that's so readable you'll understand
  your intent a year from now.
  
* Also keep in mind that you should never have more comments than code. Large detailed
  usage examples, development guidelines and architecture discussions belong on a Wiki,
  not in your code. That's just begging the comments to be carelessly obsoleted.

## Annotations

* The only Annotation you should ever use is `# TODO: $explanation...`. The point of it is to find-in-project. If it's a FIX, include a brief note of the problem, and the solution you envision (if it occurs to you).
* The annotation keyword is followed by a colon and a space, then a note
  describing the problem.
* If multiple lines are required to describe the problem, subsequent
  lines should be indented two spaces after the `#`.
* Feel free to use sub-classification of annotations as long as they always start with `TODO`.

    ```Ruby
    def bar
      # TODO: BUG: This has crashed occasionally since v3.2.1. It may
      #   be related to the BarBazUtil upgrade.
      baz(:quux)
    end
    ```

* In cases where the problem is so obvious that any documentation would
  be redundant, annotations may be left at the end of the offending line
  with no note. This usage should be the exception and not the rule.

    ```Ruby
    def bar
      sleep 100 # TODO: OPTIMIZE
    end
    ```

### Suggestions for sub-classification:
* Use `TODO` to note missing features or functionality that should be
  added at a later date.
* Use `FIXME` to note broken code that needs to be fixed.
* Use `OPTIMIZE` to note slow or inefficient code that may cause
  performance problems.
* Use `HACK` to note code smells where questionable coding practices
  were used and should be refactored away.
* Use `REVIEW` to note anything that should be looked at to confirm it
  is working as intended. For example: `REVIEW: Are we sure this is how the
  client does X currently?`
* Use other custom annotation keywords if it feels appropriate, but be
  sure to document them in your project's `README` or similar.

## Classes

* When designing class hierarchies make sure that they conform to the
  [Liskov Substitution Principle](http://en.wikipedia.org/wiki/Liskov_substitution_principle).
* Try to make your classes as
  [SOLID](http://en.wikipedia.org/wiki/SOLID_(object-oriented_design\))
  as possible.
* Always supply a proper `to_s` method for classes that represent
  domain objects.

    ```Ruby
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      def to_s
        "#@first_name #@last_name"
      end
    end
    ```

* Use the `attr` family of functions to define trivial accessors or
mutators.

    ```Ruby
    # bad
    class Person
      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end

      def first_name
        @first_name
      end

      def last_name
        @last_name
      end
    end

    # good
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end
    end
    ```

* Never inherit from `Struct.new`.  Limit usage of Struct and OpenStruct instances to the local scope.  Do this *only* for "throwaway" objects.  Consider writing a discrete class instead.

    ```Ruby
    # BAD
    class Person < Struct.new(:first_name, :last_name)
    end

    # GOOD
    Person = Struct.new(:first_name, :last_name)

    # GOOD
    class Person
      attr_reader :first_name, :last_name

      def initialize(first_name, last_name)
        @first_name = first_name
        @last_name = last_name
      end
    end
    ````

* Consider adding factory methods to provide additional sensible ways
to create instances of a particular class.

    ```Ruby
    class Person
      def self.create(options_hash)
        # body omitted
      end
    end
    ```

* Prefer inheritance over [duck-typing](http://en.wikipedia.org/wiki/Duck_typing). It's self documenting and enables you to guide usage with `ArgumentError` checks.

    ```Ruby
    # BAD
    class Duck
      def speak
        puts 'Quack! Quack'
      end
    end

    class Dog
      def speak
        puts 'Bau! Bau!'
      end
    end
    
    # GOOD
    class Animal
      # abstract method
      def speak
        raise NotImplementedError.new
      end
    end
    
    # extend superclass
    class Duck < Animal
      def speak
        puts 'Quack! Quack'
      end
    end
    
    # extend superclass
    class Dog < Animal
      def speak
        puts 'Bau! Bau!'
      end
    end
    
    class Pets
      include Enumerable
      
      def initialize
        @pets = Set.new
      end
      
      def <<(pet)
        raise ArgumentError.new("Expected +pet+ to be an Animal but was #{pet.class.name.inspect}") unless pet.is_a?(Animal)
        @pets << pet
      end
      
      def each
        @pets.each { |pet| yield pet }
      end
    end
    ```

* Prefer class instance-variables over class-variables.

    ```Ruby
    class Parent
      @@class_var = 'parent'

      def self.print_class_var
        puts @@class_var
      end
    end

    class Child < Parent
      @@class_var = 'child'
    end

    Parent.print_class_var # => will print "child"
    ```

    As you can see all the classes in a class hierarchy actually share one
    class variable. Class instance variables should usually be preferred
    over class variables.

* Assign proper visibility levels to methods (`private`)
in accordance with their intended usage. Don't go off leaving
everything `public` (which is the default). After all we're coding
in *Ruby* now, not in *Python*.
* Never use `protected` in Ruby. It doesn't mean what it does in every other language (`private` is actually the equivalent to `protected` in other languages), it'll just confuse.
* Indent the `public` and `private` methods as much the
  method definitions they apply to. Leave one blank line above them.

    ```Ruby
    class SomeClass
      def public_method
        # ...
      end

      private
      def private_method
        # ...
      end
    end
    ```

* Use `def self.method` to define singleton methods. This makes the code
  easier to refactor since the class name is not repeated. Don't use the "clever" hack of `class << self`.

    ```Ruby
    class TestClass
      # BAD
      def TestClass.some_method
        # body omitted
      end

      # BAD
      class << self
        def first_method
          # body omitted
        end
      
        def second_method_etc
          # body omitted
        end
      end
      
      # GOOD
      def self.some_other_method
        # body omitted
      end
    end
    ```

## Exceptions

* Signal exceptions using the `raise` keyword.

    ```Ruby
    begin
      # BAD
      fail 'Oops';
      
      # GOOD
      raise FancyPantsError.new
    rescue => error
      raise if error.message != 'Oops'
    end
    ```
* Strive to always Type your exceptions so downstream users can handle them appropriately.

    ```ruby
    # BAD
    raise "The gem #{gem.name} wasn't found!"
    
    # GOOD
    class MissingGemError < StandardError
      def initialize(gem)
        super "The gem #{gem.name} wasn't found!"
      end
    end
    
    raise MissingGemError.new(gem)
    ```

* Never return from an `ensure` block. If you explicitly return from a
  method inside an `ensure` block, the return will take precedence over
  any exception being raised, and the method will return as if no
  exception had been raised at all. In effect, the exception will be
  silently thrown away.

    ```Ruby
    def foo
      begin
        fail
      ensure
        return 'very bad idea'
      end
    end
    ```

* Use *implicit begin blocks* when possible.

    ```Ruby
    # bad
    def foo
      begin
        # main logic goes here
      rescue
        # failure handling goes here
      end
    end

    # good
    def foo
      # main logic goes here
    rescue
      # failure handling goes here
    end
    ```

* Mitigate the proliferation of `begin` blocks by using
  *contingency methods* (a term coined by Avdi Grimm).

    ```Ruby
    # bad
    begin
      something_that_might_fail
    rescue IOError
      # handle IOError
    end

    begin
      something_else_that_might_fail
    rescue IOError
      # handle IOError
    end

    # good
    def with_io_error_handling
       yield
    rescue IOError
      # handle IOError
    end

    with_io_error_handling { something_that_might_fail }

    with_io_error_handling { something_else_that_might_fail }
    ```

* As a general rule, don't suppress exceptions.

    ```Ruby
    # bad
    begin
      # an exception occurs here
    rescue SomeError
      # the rescue clause does absolutely nothing
    end

    # bad
    do_something rescue nil
    ```
    
* Avoid using `rescue` in its modifier form except in `parse` cases.

    ```Ruby
    # BAD - this catches all StandardError exceptions
    do_something rescue nil
    
    # OK - Most of the stdlib classes that implement a #parse method
    #      don't also implement a silent #try_parse method.
    @published_at = Time::parse(value) rescue nil
    ```

* Don't use exceptions for flow of control.

    ```Ruby
    # bad
    begin
      n / d
    rescue ZeroDivisionError
      puts 'Cannot divide by 0!'
    end

    # good
    if d.zero?
      puts 'Cannot divide by 0!'
    else
      n / d
    end
    ```

* Avoid rescuing the `Exception` class.  This will trap signals and calls to
  `exit`, requiring you to `kill -9` the process.

    ```Ruby
    # bad
    begin
      # calls to exit and kill signals will be caught (except kill -9)
      exit
    rescue Exception
      puts "you didn't really want to exit, right?"
      # exception handling
    end

    # good
    begin
      # a blind rescue rescues from StandardError, not Exception as many
      # programmers assume.
    rescue => e
      # exception handling
    end

    # also good
    begin
      # an exception occurs here

    rescue StandardError => e
      # exception handling
    end

    ```

* Put more specific exceptions higher up the rescue chain, otherwise
  they'll never be rescued from.

    ```Ruby
    # bad
    begin
      # some code
    rescue Exception => e
      # some handling
    rescue StandardError => e
      # some handling
    end

    # good
    begin
      # some code
    rescue StandardError => e
      # some handling
    rescue Exception => e
      # some handling
    end
    ```

* Release external resources obtained by your program in an ensure
block.

    ```Ruby
    f = File.open('testfile')
    begin
      # .. process
    rescue
      # .. handle error
    ensure
      f.close unless f.nil?
    end
    ```

* Favor the use of exceptions for the standard library over
introducing new exception classes.

## Collections

* Prefer literal array and hash creation notation (unless you need to
pass parameters to their constructors, that is).

    ```Ruby
    # bad
    arr = Array.new
    hash = Hash.new

    # good
    arr = []
    hash = {}
    ```

* Avoid percent-expressions (`%w`).

    ```Ruby
    # BAD
    STATES = %w(draft open closed)
    
    # GOOD
    STATES = ['draft', 'open', 'closed']
    ```

* Avoid the creation of huge gaps in arrays.

    ```Ruby
    arr = []
    arr[100] = 1 # now you have an array with lots of nils
    ```

* Use `Set` instead of `Array` when dealing with unique elements. `Set`
  implements a collection of unordered values with no duplicates. This
  is a hybrid of `Array`'s intuitive inter-operation facilities and
  `Hash`'s fast lookup.
* Prefer Strings instead of Symbols as hash keys unless you've carefully considered the potential of bloating your Symbol table, which is never Garbage Collected.

* Avoid the use of mutable objects as hash keys.
* The new hash literal syntax is preferred in Ruby 1.9 when your hash keys are symbols.

    ```Ruby
    # BAD
    hash = { :one => 1, :two => 2, :three => 3 }

    # GOOD
    hash = { one: 1, two: 2, three: 3 }
    ```

* Avoid `Hash#fetch`. The value isn't assigned to the key, so it can hide performance issues if your default is expensive to compute.

* Never modify a collection while traversing it.

## Strings

* Prefer string interpolation instead of string concatenation:

    ```Ruby
    # bad
    email_with_name = user.name + ' <' + user.email + '>'

    # good
    email_with_name = "#{user.name} <#{user.email}>"
    ```

* Consider padding string interpolation code with space. It more clearly sets the
  code apart from the string. Use your best judgement.

    ```Ruby
    "#{ user.last_name }, #{ user.first_name }"
    ```

* Always prefer double-quoted strings.

    ```Ruby
    # BAD
    name = 'Bozhidar'

    # GOOD
    name = "Bozhidar"
    ```

* Avoid using `String#+` or `String#<<` when you need to construct large data chunks. Use a `StringIO` instead.

    ```Ruby
    # BAD
    html = ''
    html << '<h1>Page title</h1>'

    paragraphs.each do |paragraph|
      html << "<p>#{paragraph}</p>"
    end
    
    # GOOD - Faster, less GC thrashing since you aren't constantly
    #        expanding the size of the String.
    html = StringIO.new
    html << '<h1>Page title</h1>'
    
    paragraphs.each do |paragraph|
      html << "<p>#{paragraph}</p>"
    end
    ```

## Regular Expressions

* Don't use regular expressions if you just need plain text search in string:
  `string['text']`
* For simple constructions you can use regexp directly through string index.

    ```Ruby
    match = string[/regexp/]             # get content of matched regexp
    first_group = string[/text(grp)/, 1] # get content of captured group
    string[/text (grp)/, 1] = 'replace'  # string => 'text replace'
    ```

* Use non-capturing groups when you don't use captured result of parentheses.

    ```Ruby
    /(first|second)/   # bad
    /(?:first|second)/ # good
    ```

* Avoid using $1-9 as it can be hard to track what they contain. Named groups
  can be used instead.

    ```Ruby
    # bad
    /(regexp)/ =~ string
    ...
    process $1

    # good
    /(?<meaningful_var>regexp)/ =~ string
    ...
    process meaningful_var
    ```

* Character classes have only few special characters you should care about:
  `^`, `-`, `\`, `]`, so don't escape `.` or brackets in `[]`.

* Be careful with `^` and `$` as they match start/end of line, not string endings.
  If you want to match the whole string use: `\A` and `\z` (not to be
  confused with `\Z` which is the equivalent of `/\n?\z/`).

    ```Ruby
    string = "some injection\nusername"
    string[/^username$/]   # matches
    string[/\Ausername\z/] # don't match
    ```

* Use `x` modifier for complex regexps. This makes them more readable and you
  can add some useful comments. Just be careful as spaces are ignored.

    ```Ruby
    regexp = %r{
      start         # some text
      \s            # white space char
      (group)       # first group
      (?:alt1|alt2) # some alternation
      end
    }x
    ```

* For complex replacements `sub`/`gsub` can be used with block or hash.

## Percent Literals

* Avoid `%w`.

    ```Ruby
    STATES = %w(draft open closed)
    ```

* Avoid `%r`.

    ```Ruby
    # BAD
    %r(\s+)

    # BAD
    %r(^/(.*)$)
    # should be /^\/(.*)$/

    # GOOD
    /^\/blog\/2011\/(.*)$/
    ```

* Avoid `%q`, `%Q`, `%x`, `%s`, and `%W`.

## Metaprogramming

* Avoid needless metaprogramming.

## Misc

* Write `ruby -w` safe code.
* Avoid hashes as optional parameters. Does the method do too much?
* Avoid methods longer than 10 LOC (lines of code). Ideally, most methods will be shorter than
  5 LOC. Empty lines do not contribute to the relevant LOC.
* Avoid parameter lists longer than three or four parameters.
* Use class instance variables instead of global variables.

    ```Ruby
    #bad
    $foo_bar = 1

    #good
    class Foo
      class << self
        attr_accessor :bar
      end
    end

    Foo.bar = 1
    ```

* Avoid `alias` when `alias_method` will do.
* Use `OptionParser` for parsing command line options.
* Code in a functional way, avoiding mutation in all but the cases where you absolutely cannot.
* Do not mutate arguments unless that is the purpose of the method.
* Avoid more than three levels of block nesting.
* Be consistent. In an ideal world, be consistent with these guidelines.
* Use common sense.

# Contributing

Nothing written in this guide is set in stone. It's my desire to work
together with everyone interested in Ruby coding style, so that we could
ultimately create a resource that will be beneficial to the entire Ruby
community.

Feel free to open tickets or send pull requests with improvements. Thanks in
advance for your help!

# Spread the Word

A community-driven style guide is of little use to a community that
doesn't know about its existence. Tweet about the guide, share it with
your friends and colleagues. Every comment, suggestion or opinion we
get makes the guide just a little bit better. And we want to have the
best possible guide, don't we?
