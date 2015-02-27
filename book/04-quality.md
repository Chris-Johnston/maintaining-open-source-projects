# Maintaining Quality

As a maintainer you spend a significant amount of your open source time doing
code review. There’s two types of feedback you provide:

* High level: about software design, design patterns, anti-patterns,
    architecture, suggestion of alternative implementations.
* Low level: details like matching coding style with the surroundings of the
    file or project, indentation, and naming conventions.

A high level plan is key for the success of the project. Weak foundations can
complicate updating or extending the software, which as time goes by can stall
development altogether.

It might seem that style feedback is less relevant, as an unnecessary comma or a
misspelled name won’t break any feature, but small discrepancies give the
impression of individual developers working without purpose rather than a team
working together towards a common goal. Lack of attention to detail conveys an
attitude that might permeate other aspects like readability, dependency
management, and testing. Inconsistent style is a superficial but notable sign
that people don’t care much about quality.

Kees Keizer and colleagues from the University of Groningen [conducted
experiments] showing that if people observe a certain social norm or legitimate
rule was violated, they are more likely to violate other norms or rules, which
causes disorder to spread. This effect is also described as “broken windows
theory”: one broken window in a building is enough for increasing the chances
that more will appear.

[conducted experiments]:
http://www.sciencemag.org/content/322/5908/1681.abstract

Bram Moolenaar, author and maintainer of the popular text editor called Vim,
suggests maintainers to set a precedent:

> Write nice code. Use white space properly, use good names for methods, add
> comments to explain anything that isn’t obvious, etc. If you write ugly code,
> anyone who intends to fix a bug or add a feature will not enjoy his work and
> likely avoid it next time.

One reason it’s good to take care of code quality at every level is that good
software quality keeps maintenance costs low. Poor application structural
quality in business applications results in cost and schedule overruns and
creates waste in the form of rework. Jussi Koskinen in [_Software Maintenance
Costs_] cites studies that show the relative cost of maintaining software and
managing its evolution represents more than 50% of its total cost.

Let’s see how we can control these costs.

[_Software Maintenance Costs_]:
https://web.archive.org/web/20120313070806/http://users.jyu.fi/~koskinen/smcosts.htm


## Adopt a style guide

Programming style is a set of rules used while writing code. A style guide
establishes and enforces style to improve communication, helping programmers
read and understand source code. It ensures consistency within a file and across
multiple files, and enforces best practices. When a group of people adhere to
the same guidelines all the files feel familiar for everyone.

You may write the coding standard for your project or adopt an existing one, and
follow it. Any guideline will be useful, as any one of them will take a myriad
of little decisions for you and your team so you don’t need to think about them
too. It doesn’t matter what decisions these are, the point is to not have to
discuss them frequently. Code can either conform or not with the guideline,
there’s little room for opinions and bike-shed type of discussions. This frees
time for us to focus on the deeper concepts of a given changeset, the high level
overview of the code in review.

Here is a list of three sample style guides and coding standards:

* [Most popular Ruby style guide](https://github.com/bbatsov/ruby-style-guide)
* [Symfony’s PHP coding standard](http://symfony.com/doc/current/contributing/code/standards.html)
* [thoughtbot style guides for different languages and
    frameworks](https://github.com/thoughtbot/guides/tree/master/style#style)

Because these standards are shaped through public discussions in pull requests
they decide not only on superficial style but also foster best practices
whenever possible. For example, in Ruby it is better to avoid rescuing the
general `Exception` class, as it traps OS signals to exit a process, requiring
to do a non catchable `KILL` to end execution of the script, and thoughtbot
guidelines advice against that practice. By following a popular guideline we
avoid rough edges even when we are not aware they could exist.

If you work on your own side projects though, you might be better off throwing
away any conventions and ignore everything anyone has ever said. Because as
[Harrison Shoff answers in a critique to his guidelines], the lack of process is
what gets us to new discoveries. Not everything has to feel cookie cut,
particularly for explorations.

[Harrison Shoff answers in a critique to his guidelines]:
https://github.com/airbnb/javascript/issues/102#issuecomment-28157738

Style guides can help polish reliability, performance, security, maintainability
and size of your software. It is your choice to research the reasoning behind
each rule, and to check if they apply to you and your team. Even without the
need of learning all this context and becoming an erudite in software metrics
you can achieve healthy results. Follow the rules that were discussed and shaped
by hundreds of people from your community, and break them when they don’t make
sense for you any longer.


## Use static analysis tools

The existence of a document that specifies process is not enough for people to
follow it. You might be surprised. Take a moment to sink that in. You will
continue receiving contributions in the most _creative_ styles you have ever
seen, and you might not be happiest about the originality contributors show.

If you receive a contribution with guidelines violations that you’d rather see
followed, you can do any of these four options:

1. Ignore the changes. Don’t merge, which is rude to the contributor(s). It also
   doesn’t help the project if the code changes are functionally useful.

2. Ignore the guidelines. Merge, which leads to sloppy looking code.

3. Merge and apply style changes yourself. This is time consuming and will
   clutter the git history with stylistic rather than functional changes.

4. Comment on every violation, working with the author until the changeset is
   good. This is both time consuming and it can get socially awkward.

Or you can do nothing, and let a robot do the job! A bot will comment on every
style violation, in virtually no time, with no emotions, consistently. Meet
static analysis tools.

For example Ruby has a tool called rubocop that exposes style violations.
Running rubocop in a project that wasn’t following any styleguide can be
daunting as there could be hundreds of violations.

Hound CI is a product that uses rubocop, and it comments in the lines of pull
requests that are changed and do not conform to the style guides. Hound is
consistently the first reviewer of any PR in the projects that set it up. Being
it a robot it avoids the potentially awkward situation of nitpicking style
violations over a contribution. People rarely get offended by a robot dog. It is
indeed waiting to criticize your style, but it does so always, with the same
speed, consistency and lack of passion each time. People can handle Hound items
ahead of your review, leaving code that reads as if it has been written by
anyone at your team.

Other useful static analysis tools are:

* For JavaScript code, Douglas Crockbford’s tricky to please [JSLint], or the
    configurable fork [JSHint].
* [RIPS] for PHP security analysis.
* [Brakeman] for Rails security analysis.
* [Flog] for Ruby code complexity.
* [Flay] for Ruby code duplication.
* [Wikipedia list of static analysis tools].

[JSLint]: http://jslint.com/
[JSHint]: http://jshint.com/
[RIPS]: http://rips-scanner.sourceforge.net/
[Brakeman]: http://brakemanscanner.org/
[Flog]: http://ruby.sadi.st/Flog.html
[Flay]: http://ruby.sadi.st/Flay.html
[Wikipedia list of static analysis tools]: https://en.wikipedia.org/wiki/List_of_tools_for_static_code_analysis

Let machines do what they are good at, and humans do what machines can’t do: you
can stop thinking about _how_ code is changing to focus better on _what_ is
changing.


## Request regression tests for every change

Testing helps verify whether, after introducing new behavior or updating the
project, the change has had the intended effect. Tests help us gain confidence
that the project:

* Meets its requirements
* Responds correctly to all kinds of input
* Performs its functions within an acceptable time frame
* Is usable
* Can be installed and run in its intended environments

Testing cannot establish that a product functions well under any condition, but
can determine that it does not function properly under specific conditions. A
failing test is the most specific description of an issue a project can get. The
issue is indeed encoded in running software. In an ideal world we would have a
failing test accompanying the natural language description of every issue. If an
issue has no accompanying failing test in describes malfunctioning of the
software, it should be a priority to get to a failing test before introducing
any change in the software.

These tests will in turn serve as regression tests. We get a regression if a
change that fixes an issue or enhances the project has unintended consequences
over other parts of it. Having tests covering each bug fix protect us from these
bugs reappearing, making the software more predictable and maintainable.


## Run tests on every commit

Running tests before committing to master helps avoid one developer’s work
breaking another developer’s copy. Continuous Integration originally described a
workflow in which every developer would run all unit tests in their local
environment and verify they passed before sharing her commits.

Nowadays build servers automatically run all tests after every commit, and
report results to the authors closing a feedback loop. In addition to automated
tests, CI environments can implement continuous processes of general quality
control. Such processes run additional static and dynamic tests, measure and
profile performance, extract and format documentation from the source code, and
facilitate manual QA processes. This continuous application of quality control
aims to improve visibility over the quality of software.

[Travis CI] is an open-source continuous integration service used to build and
test projects hosted on GitHub, and it’s free for open source projects. As other
similar services, Travis CI automatically detects when a commit has been pushed,
tries to build the project and run its tests. This includes commits to all
branches, not only to the master branch. When that process has completed, it
notifies the developer about the success or failure of the run.

Travis CI is configured by adding a YAML file named `.travis.yml` to the root
directory of the GitHub repository. It will test all combinations you specify
of:

* Runtimes to test against
* Environment variables you can use to configure scripts
* Allowed failures

[Travis CI]: https://travis-ci.com/

An example file for a ruby library might be:

```
language: ruby

rvm:
  - 2.0
  - 2.1
  - 2.2

env:
  - DB=mongodb
  - DB=redis
  - DB=mysql

gemfile:
  - Gemfile
  - Gemfile.rails32
  - Gemfile.rails41
  - Gemfile.rails42
```

This results in a 3×3×4 build matrix that will validate your project runs in the
many combinations of rails version, ORM and Ruby your users may have.

By automating as many quality controls as possible in a CI environment you will
make sure there is visibility into the project’s different quality measures,
helping your team and community take care of them.


## Choose your own priorities

When you start a new project you more or less consciously follow a set of values
and priorities. You have a belief, taste or decision on what is most important
for your project and community, and take care of those areas.

An incomplete list of values one might prioritize are:

* Reliability
* Security
* Usability
* Design
* Code quality
* Popularity
* Performance
* Cleverness

A prioritization of values you might like in your community can be:

* Collaboration
* Friendliness
* Experience
* Low barrier of entry
* Stability
* Numbers (lines of code, commits, etc)

<!--
As a maintainer, each decision you take and each interaction you have with the
community sets a tone. You might show happiness about certain things, anger or
indifference about others. Each issue you close, each Pull Request you merge,
each thread you participate in shows your values, and that will probably grow a
following of people who share them. See examples of how maintainers set the
priorities of their projects in these threads:

* TODO: A Rails thread speaking about conventions
* TODO: A reactive.js thread speaking about performance
* TODO: A GNU thread speaking about licenses
* TODO: A FreeBSD thread enforcing code quailty, correctness, simplicity or “elegance”
* TODO: An OpenBSD thread on security of different implementations
-->

A developer will like it if the object model is close to the real world domain
model and code is loosely coupled. A customer may think a product is good if it
can be understood and used in less than a minute. A product owner will find the
software healthy if it’s profitable. Different people see the same product
through their different lenses. What’s irrelevant to one project is essential to
another.

As the project’s maintainer, you’re in a unique position to define the values of
your project’s community. In this chapter we’ve mainly discussed values related
to code quality: adhering to a style guide, maintaining test coverage, using CI,
and so on. A project that doesn’t honor these values is less likely to succeed,
but a project certainly won’t succeed if no one wants to work on it! Make sure
that the values you choose to cultivate resonate with potential contributors and
help make the project something you can be proud of.