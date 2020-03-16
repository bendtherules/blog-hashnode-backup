## My experience with Typescript annotations

# TLDR: 

**Pros** — Great to start off with. Less commitment, more immediate reward.  
**Cons** — Annotations feel underpowered — verbose and repetitive, different constructs (than .ts, closer to jsdoc), imports are hard, type assertion and generics don’t work that well, smaller community. Overall, much closer to .d.ts files than a full-blown .ts replacement

---

Typescript allows you to add types to JavaScript files using jsdoc-style comment annotations. I have tried using it in my personal projects and to some extent, at work. This post is not about how to use it, but rather my opinions on what worked, what didn’t work and whether you should consider using it.

I tried to find success or failure stories with annotations before starting off, but couldn’t find much. This is just my effort in documenting things I liked and felt were missing — going beyond “just use annotations if you want types in Javascript”.
And to some extent, expanding on a [long comment](https://medium.com/@bendtherules/i-hope-you-have-tried-using-it-in-decent-sized-projects-and-not-just-talking-about-a-theoretical-753990cc4598) I left on a medium article.

Note — I use the term _annotation_ here to refer to typescript annotations within js. Similarly, _ts_ usually refers to typescript in .ts file and _Typescript_ refers to the general language.

# A bit of context

**TLDR** : Moving from JS to TS (even for some files) needs more thought and commitment (higher risk, higher long-term reward). Adding TS annotations to JS is non-obtrusive and encourages experimentation (lower risk, decent reward).


![Typescript in .ts](https://cdn.hashnode.com/res/hashnode/image/upload/v1584343472690/X0sl6YAof.png)
**Typescript in .ts**

![Typescript annotations in .js](https://cdn.hashnode.com/res/hashnode/image/upload/v1584343583728/nRqSC1F92.png)
**Typescript annotations in .js**

Typescript powers intelligence for both Javascript and Typescript within VS Code. Now, Typescript is a superset of JS — meaning that existing js files are valid ts, but that wouldn’t necessarily give us better type information. It is the extra type hints that TS allows you to add — using additional language syntax — is what provides deeper type information.
This extra language syntax is not part of the Javascript syntax and hence, would typically need moving to a .ts file, some changes in the build pipeline and tooling.

This brings the question of “Typescript vs Javascript” where you have to choose one or the other. It really hinders experimentation and comes with a lot of baggage — needs long-term thought, commitment from the whole team, developer re-skilling and proper rollback strategy (not as easy as it sounds). All of this points to more senior-level decisions and higher capital investment without adequate immediate payoff.


![Type hints in vscode while using ts annotations in .js](https://cdn.hashnode.com/res/hashnode/image/upload/v1584344157203/_QPp_ZAna.png)
**Type hints in vscode while using ts annotations in .js**

Typescript annotations are additional comments within existing JS files — which would immediately start producing richer type hints in VS Code. It works better with an additional config file. These changes are mostly non-obtrusive — so no .ts files, no build change, no new dependency to add. I see it as a perception problem — a friendly-looking diff which is non-destructive, merely adds a few comments and a config file is more likely to get a buy-in from the team, rather than one which touches a lot of files, introduces a new file extension or changes something as critical as the build system.

P.S. Typescript does infer types (without any extra hint) for both JS and TS, but when used alone — it’s not very useful in any non-trivial codebase.

# Good stuff

**TLDR** : Pros are as advertised — the fact that it actually works, interoperability with other js/ts files, good VSCode support and uniform interface (for JS and TS) at api and config level.

As we discussed above, it solves a huge risk-reward problem and makes it very easy to get started with it without any major change. They lack some of the features that moving to a complete .ts file would provide, but it's easier to do so when it’s already adding some value.


![Importing types from a js/ts file (using annotation)](https://cdn.hashnode.com/res/hashnode/image/upload/v1584350676264/6ul_wxzLF.png)
** Importing types from a js/ts file (using annotation) **

**JS-TS Interoperability** — Annotations are not limited to the scope of a single file. You can import stuff from other JS/TS files and add typing to your own exports using annotations.

**Uniform API interface** — Typescript can be used both as a tool (executable) or as a library (TS compiler api). Both of them supports these annotations using a simple flag `allowJS`. This makes it very easy for other downstream libraries or toolings like [typedoc](https://typedoc.org/), to support both these options without extra code.

**VS Code** has good support for TS annotations including type hint, rename symbol, path autocomplete, syntax highlighting inside annotation, etc.

# But…

When I started using annotations on a existing javascript codebase, some flaws started to come up.
In a nutshell, it felt — verbose and repetitive, lagging in features, less safe, and most importantly, simply not powerful enough being a line comment. It feels like writing inline styles instead of a nice CSS file.

# **Verbose and repetitive** —

![](https://miro.medium.com/max/60/1*vCjjJL1_7kg2kNSCGKLG9g.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/2496/1*vCjjJL1_7kg2kNSCGKLG9g.png" width="1248" height="658" role="presentation"/></noscript>

Repeating inline![](https://miro.medium.com/max/60/1*yUgYfqP084mhEHvBQtCfig.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/2968/1*yUgYfqP084mhEHvBQtCfig.png" width="1484" height="766" role="presentation"/></noscript>

Using typedef

Most of the annotation constructs are line comments which describe the typing of symbols defined in the following line. Line comments make it very convenient to just copy paste repeating types. There are block constructs to define it in one place (within the file or in another file), but these are often new constructs added exclusively to annotations and not available in general Typescript — making it a new learning process (even if you know TS).

This is often complicated by the fact that some block-level constructs might be missing or in active development and the docs are not always updated. Effectively, if you find something missing - be prepared to either “ignore” it (use some lesser appropriate construct) or go on a wild goose chase for hours.

I usually find it easier to **define all common typings in a actual .TS file** and import them within js files for reuse.

# Importing types is hard —

Ok, maybe this shouldn’t be a separate point. It’s actually just an extension of the previous “line comment” bashing, but seriously — you don’t know how bad inline imports feel unless you have seen it.

![](https://miro.medium.com/max/60/1*riiApBgDSOoezHIvx5D1eQ.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/2428/1*riiApBgDSOoezHIvx5D1eQ.png" width="1214" height="766" role="presentation"/></noscript>

Importing with annotations

Think about it, you have to add a new import line for
- every imported file
- every imported variable (even from same file) separately
- at every usage site (whenever variable of imported type is created)

Imagine importing ten variables like this from multiple files in a deeply nested folder structure and using them in different lines!

Now, using typedefs — you can save some keystrokes if you are reusing the same imported variables, but it still leaves a lot to be desired. There is no multi-variable import using the same typedef statement and none of the fancy import statements that we are so comfortable with.
There are some [discussions](https://github.com/Microsoft/TypeScript/issues/22160) around this, but AFAIK there is still no good solution to this.

# Expression-level annotation —

_What comes to your mind first when you think about typing?_
For me, it’s — Adding type definition to most of the variables.

And annotations sound very well-suited for it. After all, if all we need is a way to define the types of variables, then we can just define its type at the definition site (using annotations).

But there are multiple useful constructs in .ts which allows us to play with type, often at a expression level. Think of type assertions and generics.

![](https://miro.medium.com/max/60/1*SdqrU14D7XZNROn3VchdaQ.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/2460/1*SdqrU14D7XZNROn3VchdaQ.png" width="1230" height="262" role="presentation"/></noscript>

Type assertion example

**Type assertions** allow us to change the type of any smaller expression (including variables or properties) only within the context of that statement. This is not directly possible with annotations as they can only describe the return value of a statement (sort of).

Now this creates an interesting problem in conjunction with type inference. Say you are sure about the type of a expression (ex. a variable used in the statement), but want Typescript to infer the return type of the whole statement.

![](https://miro.medium.com/max/60/1*77p44F_OeTuGnA2ZMrLiRw.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/2732/1*77p44F_OeTuGnA2ZMrLiRw.png" width="1366" height="514" role="presentation"/></noscript>

Type assertion + inference with .ts![](https://miro.medium.com/max/60/1*Mrqn2qrins0pU4Z8sLYBXg.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/1820/1*Mrqn2qrins0pU4Z8sLYBXg.png" width="910" height="442" role="presentation"/></noscript>

Can’t use type assertion in .js

This is fine in .ts, just assert the type of that variable and rest is inferred correctly. But with annotations, you can only describe the return value of the whole statement — so you have to manually infer the whole return type based on all parts of the statement, not just the asserted variable. In other words, you can only describe the return type of a function call, not the type of arguments.
This is very error-prone and will easily break when any of the constituents change.

![](https://miro.medium.com/max/60/1*IzIG1BjOHTSKXHEEwymwYg.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/1452/1*IzIG1BjOHTSKXHEEwymwYg.png" width="726" height="370" role="presentation"/></noscript>

Explicit generics example![](https://miro.medium.com/max/60/1*hL06nnIXbGZo27cKYG8yrw.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/1348/1*hL06nnIXbGZo27cKYG8yrw.png" width="674" height="442" role="presentation"/></noscript>

Implicit generics in action

**Generics,** on the other hand, is a way to pass types as input to a function call. They are usually passed explicitly, but can be understood implicitly by TS in some cases. Based on this input types, the function usually decided about some characteristics of the output. Ex — In the queue example, all getters and setters on the returned object (i.e. push/pop on `queue`) will only allow number types.

![](https://miro.medium.com/max/60/1*I1rVxCtzCIPi2w207uG5uw.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/2228/1*I1rVxCtzCIPi2w207uG5uw.png" width="1114" height="550" role="presentation"/></noscript>

Generics works well with ts![](https://miro.medium.com/max/60/1*kEtNTAWA-XruAOZOgF8zxQ.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/1588/1*kEtNTAWA-XruAOZOgF8zxQ.png" width="794" height="514" role="presentation"/></noscript>

But troublesome with js

So, we face the same problem here where we want to pass in some extra types as input to a function call and expect the output type to be decided by the function itself. Annotations don’t really allow us to pass in types inline — we have to manually figure out what the function is supposed to return (given the input) and set that type on the output value.
In the above example, notice how we are setting type as `Promise<number[]>` in js instead of just `number[]`, like we can with ts. This has same downsides as the type assertion problem.

# Learning new generics for complex libraries —

Javascript libraries with higher level of indirection support generics to tie up the different layers and provide a type-safe interface. These indirections work well due to the highly dynamic nature of Javascript, but requires very flexible type signature and complex generics to make them type-safe.
Because of its multiple type signatures, if you pass a wrong generic (explicitly) as input — Typescript can easily misinterpret it as one of the other forms and show a huge type diff explaining why your current types don’t fit in. It can take some time to understand the real problem.

In fact, I find myself learning a few more things about the library in these situations — to debug the problem in hand and also to learn more exhaustively about it. Some libraries provide good explanation of its typings and generics, sometimes you have to read through a article and in worst case, just go through the type definition files and try to match it with its docs.

This applies to Typescript as a whole, not just annotations.

![](https://miro.medium.com/max/56/1*AFraSdlsxpadGIT1S-eV1g.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/3136/1*AFraSdlsxpadGIT1S-eV1g.png" width="1568" height="1666" role="presentation"/></noscript>

Look how complex the type signature for react-redux `connect` is!

Say, for example `connect` in react-redux — think of it as a complex function, which accepts a function (mapStateToProps) and a plain object (ownProps of the component) as input. It merges the return object from the function call (which reads implicitly from a global store) with the plain object and returns a new one.
Now, to define the type of the output, it needs access to type of its ownProps and redux state — through generics. But that’s not all — you can also pass `mapDispatchToProps`, which will add dispatch methods to props and `mergeProps` function, which lets you decide the merge strategy between ownProps and this redux introduced props. This is where your generics can get insanely complicated and wrong — if you don’t understand it in details.

Luckily redux has good docs right within its type definition files and also on docs page, but without those it can be hard to go through the raw type definitions and understand what `InferableComponentEnhancerWithProps` possibly means.

Type definition of some libraries consists of many generics and lot of manipulations on them — which almost feels like a mini-language of its own. Without good docs or guides, it can be hard to understand all the trickery within a new library.

# Refactoring types adds overhead —

Refactoring code itself is hard. With a type system, you’ll also have to make sure that these contracts are moved around and match up correctly.
This is a general drawback with Typescript, not specific to annotations.

For the code you want to move around, you have to figure out all the dependencies and dependents, sever the existing connections and connect them back in a different way. During refactoring types, many type contracts have to be made explicit — which were implicit (and could be skipped) earlier due to type inference.
If these implicit contracts have to be made explicit manually, then it adds a lot of overhead. For most refactorings, the priority is to get the code working functionally. But till types are not refactored correctly, they will keep giving warnings which feels like a drag.
In vscode, in-built refactorings like “extract to function” adds this extra types automatically. But if you want to refactor something else, say extract a smaller React component out of a bigger component — then you will have to manually add types for props and state for new component and maybe also remove some state from the bigger component. This is not unlike keeping your proptypes updated as your component changes over time.
This might feel like nitpicking, but the extra overhead often acts as a justification to delay refactoring. Or types are somehow glued together to finish the job.

![](https://miro.medium.com/max/60/1*d-cKLgC55H1oEn14D-3z7w.png?q=20)

<noscript><img class="s t u ho ai" src="https://miro.medium.com/max/4256/1*d-cKLgC55H1oEn14D-3z7w.png" width="2128" height="1020" role="presentation"/></noscript>

Refactoring types in react components

On the other hand, types actually do help in making refactorings more predictable and confident. It is easier to find the linked definitions and usages using types. And after refactoring is completed, it gives some confidence about the end result.

# Smaller community —

Typescript has a decent sized community, but annotations are much less popular. It is one of those things which you feel doesn’t matter, until you face a problem.

You will often need to search for things, but might not find the answers easily. The information is all over the place and often not updated — some on release notes, some on wiki, basarat’s gitbook, chain of github issues. It's not like you won’t find any information on the internet, but it's frustrating at times when you are looking for something specific. Not many success stories about it, not many tutorials that go beyond the surface, not many guides on how to do xyz with annotations.

Might sound odd, but it’s sometimes actually hard to even phrase the questions about annotations on google (because of all the context, TypeScript +annotation +your problem). You’ll probably anyway end up finding the TS solution first. Sometimes the TS solution is translatable to annotations, but sometimes it has to be done with a different construct.

# Conclusion

Now, I understand it’s easy to criticize a technology without even trying to understand why some decisions were made. That doesn’t negate any of the problems, it just makes things more understandable.

Most of the downsides are related to it being a line comment and not integrated with the language (js) — which is exactly what makes it useful in the first place. Imagine pointing to various parts of one line (js) from another line (annotations) — preferably without repeating (duplicating) the js in the annotations. It is a hard problem to solve. There are also other considerations like jsdoc compatibility, existing conventions, community feedback and coming up with a acceptable new construct.
Jsdoc is an existing solution for types in comments. Typescript tries to be compatible with it by supporting jsdoc constructs and their values (like `@params`) and favouring jsdoc constructs instead of adding new ones (when an equivalent exists). This creates some challenge when they have to decide between choosing a more typescript way vs jsdoc way of doing things.

Because of multiple possible solutions, community feedback and compatibility with existing things — decisions are often deferred till they become critical. It seems like typescript features have a deadline, but annotation equivalents are left hanging if there are conflicting opinions or often considered as a second thought.

In short, annotations still feel like a backwards citizen in the typescript land and more suitable for documenting external contracts, rather than a full-blown .ts replacement. They feel closer to type definition files (.d.ts) than typescript (.ts) files.