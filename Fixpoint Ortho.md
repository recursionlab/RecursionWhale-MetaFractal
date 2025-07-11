
MULTIPLE TITLES:
Function Application Needs to Grow a Spine Already
Fixpoint Ortho
 Fixpoint Constructions in Focused Orthogonality Models of
 Optimal Fixed Point Combinator
 Towards third generation HOTT
 Thinking Recursively, Rethinking Corecursively
Local Higher-Order Fixpoint Iteration
 Towards third generation HOTT
 Part 3: Univalent universes
(Co)recursion in Logic Programming: Lazy vs Eager∗
AUNIFIEDFRAMEWORKFORGENERALIZED
 MULTICATEGORIES
 Superrecursive Features of Interactive 
 Y IS A LEAST FIXED POINT COMBINATOR



---


Thunderseethe's DevlogPostsMaking a Language
Function Application Needs to Grow a Spine Already
 March 31, 2025  13-minute read
 Programming Languages• Type Inference
Function application can be found nestled into the heart of basically every functional language. At the risk of hyperbole, I would even say every programming language. Unlike languages inheriting from the C family, function application in functional languages makes use of currying  . Currying lets us eschew multi argument functions in favor of a bunch of single argument functions that return more functions. Function application (and currying) herald all the way back to the dawn of the lambda calculus:

data LambdaCalc 
  = Var String
  | App LambdaCalc LambdaCalc -- <-- our boi
  | Fun String LambdaCalc
Many languages have been built atop the sacrosanct application: one argument applied to one function. Every time we see it, it inspires the same feelings in all of us:

Weak
Feckless
Inept INVERTEB–
What’s that? People don’t feel that strongly about AST nodes? They care way more about keyword length? Alright, fair enough. Personally I’m partial to fun. A keyword length of 3 is true, just, and it’s literally fun.

A Quick Look at Currying 
Curried function application is a long-standing tradition in functional languages. So it might come as a surprise that not currying your function, taking multiple arguments at a time, allows you to infer better types then just considering one application at a time.

Before we understand why, let’s take a step back and talk about currying. Currying is a feature that turns multi argument functions into a series of single argument functions. Each function taking one parameter at a time and returning a new function that takes the next parameter. Instead of a single application f(x, y), we have two applications (f x) y. f x returns a new function that takes our y. This chain of applications (_ x) y is called the application’s spine. f is called the head of the application.

For how common currying is in functional programming, it is quite divisive. Some people swear by currying as elevating programming above mundane concerns such as function arity. Others say it makes it impossible to reason about programs. You can never tell when a function does real work, rather than just returning a new function immediately.

At a glance, we might guess that f and g in f (g x) are functions of one parameter. If we peak at f’s definition, f x y = x y, we discover that actually both f and g take two parameters. In a language without currying, we’d have to write f (g x) as (\y -> f (\y -> g x y) y) making parameter count exceedingly obvious.

On the upside of currying, it can improve readability by removing noise. Writing map (update tallyValue) tallies cuts right to the heart of matters and doesn’t require any superfluous names. This is subjective of course, but in my opinion currying can help highlight what’s important by removing that which is not.

More objectively, it simplifies things in the static analysis of a language. When we encounter an application f x, we check if f is a function. If it is, we’re good. If it’s not, we immediately have an error. Compiler passes like typechecking and inlining benefit from this simplicity.

This scales pleasantly to applying multiple arguments. When we encounter (f x) y, we recurse to check f x and then check that y is the right argument for f x. Our application node, App LambdaCalc LambdaCalc, is correct by construction. If we see an application, we know we have one argument and one function.

With multiple arguments we have to worry about do we have the right number of arguments and are the right types at the right positions in the function call. It’s not the end of the world, but certainly more involved to achieve the same end. Multiple arguments also force some verbosity onto us. If we want to pass a function with some arguments already applied, we must use a closure. Instead of saying map (f 4) ys, we’re forced to say the more verbose map (\y -> f 4 y) ys.

Classic tradeoffs apply here, of course, smart people will look at map (\y -> f 4 y) ys and say that its good to require the closure because it makes the runtime characteristics of the program more obvious. Because we have to use a closure, it’s apparent to everyone reading that a closure is being allocated. (f 4) is still allocating (not a closure but a partial application or Pap), but that’s no longer immediately apparent. We have to go look at f to figure out that it needs more arguments and isn’t returning a value immediately.

While they’re not wrong, I harbor a fondness for currying. I miss it whenever I find myself using Rust. When I return to Haskell, I delight in indulging currying to construct pointless  programs.

A Point Against Currying 
I’m here today, however, to place another point in the pile against currying. Much as it pains me to do so. I’ve noticed a trend in recent research that favors passing multiple arguments at a time. Typechecking multiple arguments to a function gives us enough information to infer polytypes, sometimes. Sounds great, but what’s a polytype?

When we talk about inferring types we distinguish between two types of type: monotypes and polytypes. Monotypes are free to contain any number of type variables, but they cannot introduce them (aka bind them). These are some of your favorite types like Int, a -> a, Map k (Int, v), etc. Polytypes are monotypes but free to introduce any number of type variables. A classic example of a polytype is the type of id, which takes any type a and returns a value of that type:

id :: forall a. a -> a
id x = x
The forall a there introduces our type variable a and makes forall a. a -> a a polytype. Haskell allows omitting the forall and just writing a -> a, but this is still a polytype. Haskell just allows syntax sugar to avoid spelling out the forall. We’ll avoid that syntax sugar, so our monotypes and polytypes are clearly distinguishable.

Polytypes, however, can put the forall in more interesting places as well:

forall a . a -> (a, forall b . b -> b)
[forall x . x -> x]
forall k . Map k (forall v . (Show v) => Maybe v)
These are also all polytypes. Except, unlike id, we’ve historically been unable to infer these polytypes.

At the end of inferring a top level declaration, like id, we have a monotype such as a -> a. We find all the free variables in this monotype and bind them to create our polytype forall a. a -> a. In a sense, this is inferring a polytype. You provide a top level definition, and type inference returns its polytype.

The important distinction, however, is that this process never requires solving a type variable with a polytype. At the end of inference we’ve only solved type variables with monotypes. There’s quite a lot that goes into type inference that I’m brushing over here, if you’re curious about the details see this post . For our purposes today, it suffices to know that it’s not possible to infer types with arbitrary foralls such as forall a . a -> (forall b. b -> (a, b)).

This  is  not  for  lack  of  trying  . Many attempts have been made to allow for inference of arbitrary polytypes. Even with the approach we’re talking about today we can’t infer all polytypes, just some of them. Inferring all polytypes requires annotations to disambiguate where foralls should appear.

A recent line of research, however, shows that if you make use of multiple arguments during inference you can infer polytypes without annotations:

A quick look at impredicativity 
Interestingly enough, the idea appears to be independently applied in another paper:

Sound and Complete Bidirectional Typechecking for Higher-Rank Polymorphism with Existentials and Indexed Types 
It’s always neat to see when disparate work converges on the same solution out of a set of shared constraints. There’s also a great talk that covers the idea:

Type inference for application spines 
At a high level, by examining the arguments to a function we can sometimes glean enough information to infer a polytype. The advantage of this approach, over previous attempts, is its lightweight and doesn’t require annotations. If we don’t gather enough information, we’re free to bail out and do inference normally with monotypes.

Turns Out It’s About Type Inference 
Traditionally, type checking an application starts by inferring a function type. Commonly this will be a function type with two variables a -> b. Followed by checking our argument has type a. Our new quick look approach proceeds in the opposite direction. We collect all the types of our arguments and then use them to check our function type. If our argument types provide enough information we can check our function has a polytype, rather than a monotype.

Some, admittedly contrived, examples from A quick look at impredicativity  will help us grasp the idea. Consider a list of id functions (don’t ask why I need a list of id functions I assure you its important business logic!):

ids :: [forall a . a -> a]
ids = [id, id, id]
Let’s say we want to add a new id to the list with id : ids. Technically this is a function call with two parameters (:) id ids. Where (:) has type:

(:) :: forall p . p -> [p] -> [p]
x : xs = -- the implementation doesn't matter
With normal inference, we have to give id : ids the type [a -> a]. We’re only allowed to solve p to a monotype, so we have to pick a -> a. But we’ve lost important information, notably that the ids in our list work for different types. They don’t all have to be applied to the same type a.

By examining both our arguments, id and ids, we can determine a more specific type for id : ids. We can deconstruct our application into its head (:), and it’s spine [id, ids]. We can match up the argument types of (:), p and [p], with the types of our argument spines, forall a . a -> a and [forall a . a -> a].

Our first match, p and forall a . a -> a, proves unfruitful. Quick look  details the technical reasons why, but we can’t solve our naked type variable p to a polytype forall a . a -> a. Doing so makes typechecking undecidable, and people generally aren’t willing to wait that long for their types. Fortunately, our vertebrate application provides a second match to consider: [p] and [forall a . a -> a]. With this pairing we can determine that p must be forall a . a -> a.

The reason for this is a little subtle. Dressing p in [p] makes it unambiguous where the forall must appear. In contrast to our first pair p and forall a . a -> a, where the forall has two valid placements. If we only had our first argument (:) id, our term would have two valid typings:

forall a . [a -> a] -> [a -> a]
[forall a . a -> a] -> [forall a . a -> a]
Our poor type inference doesn’t have enough information to tell which type to choose. But once we see [forall a . a -> a], we can be certain only the second type applies.

Multi argument application is instrumental to allowing this enhanced inference to take place on more terms. Our approach relies on two critical pieces of information provided by our application spine:

A polytype for our application head, forall p . p -> [p] -> [p] in the case of (:).
The type of each argument to match against our head’s polytype.
We might suspect that we can determine these two things without application spines. When we consider ((:) id) ids we can give (:) id the type forall f . [f] -> [f] and then match it against ids’s type [forall a . a -> a]. It’s not valid, however, to infer forall f . [f] -> [f] for (:) id. (:) id is an application node, it has to have a monotype. We only get away with giving a polytype to (:) because it’s a bound variable.

Bound variables can have polytypes because variables don’t require any inference. Again, this runs against intricacies in type inference implementation. Type inference works out a type for each bound variable in scope and saves it in an environment. All we have to do when we see (:) is lookup its type in that environment.

We might also wonder if we really need a polytype for our application head. If (:) has the type p -> [p] -> [p] it looks like our approach would work just as well. Our p looks the same but is distinct from the p in forall p . p -> [p] -> [p]. The forall ensures us that p doesn’t show up anywhere else in the type we’re currently inferring. Without that guarantee, it’s not safe to solve our type variables to polytypes. Similar to solving a naked type variable, solving an unbound variable to a polytype will make type checking undecidable.

This leads us to an important caveat where this approach does not apply. If the head of our application spine is a more complicated expression than a variable, we can’t apply this tactic. For example if instead of (:) id ids, we had (\ x y -> (:) x y) id ids that would stop us in our tracks. (\x y -> x y) has to be given a monotype, not a polytype.

I worry opportunities to apply quick look won’t arise in practice. We need our applications be headed by a variable and contain enough arguments to unambiguously determine a polytype. Thankfully, not a lot of people write code like (\ x y -> (:) x y) id ids in practice.

Most applications have the shape we need, some number of applications with a variable at the head. Some number of arguments can be one (it can even be zero), consider head ids. Our single argument ids is enough to determine that the type of head should be [forall a . a -> a] -> (forall a . a -> a). If our application doesn’t have that shape, we ship it off to normal inference to receive its monotype. Because we can always fall back to normal inference, there isn’t a lot to be lost by trying this and seeing if it works.

I don’t know if this is enough for me to forsake currying entirely. I viewed the trusty App LambdaCalc LambdaCalc node as kind of the default for functional languages (in no small part due to Haskell being my formative functional language). I’m rethinking my view now that application spines are beneficial not only practically but also theoretically. Maybe functional languages should rethink application moving forward and consider outright multi argument applications ala f(x, y, z). SML already has the convention that multiple parameters should be passed in a tuple rather than use currying: f (x, y, z)

Okay that’s a little too far, I love currying too much for that. But at least consider explicit currying. That makes the multi argument structure immediately obvious. The advantages of the application spine can’t be ignored.

 Get in Touch
 thunderseethe
 










---



















---




 Fixpoint Constructions in Focused Orthogonality Models of
 Linear Logic
 Marcelo Fiorea,1,3 Zeinab Galalb,1,2,4 Farzad Jafarrahmanib,2,5
 a University of Cambridge
 b Sorbonne University
 Abstract
 Orthogonality is a notion based on the duality between programs and their environments used to determine when they can
 be safely combined. For instance, it is a powerful tool to establish termination properties in classical formal systems. It was
 given a general treatment with the concept of orthogonality category, of which numerous models of linear logic are instances,
 by Hyland and Schalk. This paper considers the subclass of focused orthogonalities.
 We develop a theory of fixpoint constructions in focused orthogonality categories. Central results are lifting theorems for
 initial algebras and final coalgebras. These crucially hinge on the insight that focused orthogonality categories are relational
 f
 ibrations. The theory provides an axiomatic categorical framework for models of linear logic with least and greatest fixpoints
 of types.
 We further investigate domain-theoretic settings, showing how to lift bifree algebras, used to solve mixed-variance recursive
 type equations, to focused orthogonality categories.
 Keywords: Orthogonality; linear logic; categorical models; fixpoint constructions; inductive, coinductive, and recursive
 types.
 Introduction
 Linear logic with fixpoints
 Propositional linear logic lacks datatypes with iteration or recursion principles. This is usually remedied
 by extending it to the second order, thus defining a logical system in which Girard’s System F [19] can
 be embedded. Even if such a system is very expressive in terms of computable functions, its algorithmic
 expressiveness is poor: for instance, it is not possible to write a term
 that computes the predecessor function in one (or a uniformly bounded) number of reduction steps.
 Girard first considered an extension of linear logic with fixpoints of formulas in an unpublished note [17].
 However, the first comprehensive proof-theoretic investigation of such a system was given by Baelde [2]
 1 Research partially supported by EPSRC grant EP/V002309/1.
 2 Research partially supported by the RealiSe Emergence project.
 3 Email: marcelo.fiore@cl.cam.ac.uk
 4 Email: zeinab.galal@lip6.fr
 5 Email: farzad.jafarrahmani@lip6.fr
 Published November 21, 2023
 10.46298/entics.12302
 Proceedings Available Online at
 https://doi.org/10.46298/entics.proceedings.mfps39
 ©M. Fiore, Z. Galal,
 & F. Jafarrahman
 cb Creative Commons
8–2
 Fixpoints constructions in focused orthogonality models of linear logic
 who introduced and studied µMALL, an extension of multiplicative additive linear logic with induction and
 coinduction principles, with motivations coming from proof-search and system verification. Linear logic ex
ponentials were not considered in µMALL; they could be somewhat encoded with inductive and coinductive
 types though without their denotational interpretation satisfying the required Seely isomorphisms.
 Ehrhard and Jafarrahmani [9] introduced a system µLL extending µMALL with exponentials and studied
 it from the Curry-Howard-Lambek perspective. Their notion of categorical model of µLL is an extension
 of the standard notion of Seely category for classical linear logic with suitable initial algebras and final
 coalgebras. Specifically, they presented a totality model of µLL that is an instance of a general categorical
 construction developed in this paper. In the totality model, least and greatest fixpoints are calculated
 by lifting initial algebras and final coalgebras from the relational model. Here, by viewing it as a special
 case of a focused orthogonality construction, we are able to develop a general methodology for constructing
 models of linear logic with fixpoints.
 Orthogonality and glueing for models of linear logic
 Logical relations [38,31,37] are by now a standard tool in the theory of programming languages to certify
 program properties that cannot be obtained by naive induction arguments. The basic idea is to associate
 to each type a predicate that is preserved by the operations on the type. Such predicates depend on the
 program property that one is interested in proving (termination, type safety, parametricity, etc.) and their
 use provides a powerful proof method.
 Orthogonality methods originate from the semantics of linear logic and are particularly well-suited for
 languages modelling classical negation [15]. The general principle is to restrict attention to pairs of terms
 and contexts in a pole ‚ Ď Terms ˆ Contexts that contains correct computations. For a set of terms
 T ĎTerms, one can then consider the set of all contexts TK Ď Contexts that yield a correct computation
 when combined with any term in T. Dually, for a set of contexts C Ď Contexts, one can consider the
 set of all terms CK Ď Terms that yield a correct computation when combined with any context in C.
 These constructions yield a duality between subsets of terms and subsets of contexts, and one associates
 to each type a subset of terms T that is equal to its double dual TKK. Such dualities between terms and
 environments (or player and opponent) form the basis of game semantics [22] and of Krivine’s classical
 realizability [25].
 Logical relations have a categorical abstraction given by Artin-Wraith glueing [43], while orthogonality
 constructions are obtained via Hyland-Schalk double glueing [23]. Here, we will be particularly interested
 in a well-behaved subclass of orthogonality categories arising from poles and referred to as focused orthogo
nality categories. These, we will recast as relational fibrations and therefrom develop a general categorical
 theory that lifts initial algebras and final coalgebras to focused orthogonality categories, and therefore
 provides models of linear logic with least and greatest fixpoints.
 Structure of the paper
 ‚ We start by recalling the notion of orthogonality category by Hyland and Schalk in Section 1.
 ‚ In Section 2, we develop a theory of fixpoint constructions for relational fibrations by lifting initial
 algebras and final coalgebras to the Grothendieck category of a relational fibration.
 ‚ We show in Section 3 that focused orthogonality models are instances of relational fibrations. This
 provides us with a general categorical construction to obtain models of linear logic with least and greatest
 f
 ixpoints. A variety of examples is considered in Section 4.
 ‚ Finally, in Section 5, we show how to lift bifree algebras to focused orthogonality categories in an
 axiomatic domain-theoretic setting.
 1 Preliminaries on orthogonality categories
 From a categorical viewpoint, logical relations can be presented using glueing constructions, also called
 Artin-Wraith glueing, sconing, or Freyd covering [1,43,14]. These allow the lifting of monoidal (or cartesian)
Fiore, Galal, Jafarrahmani
 8–3
 closed structure to glueing categories. Orthogonality methods fit into the more general framework of double
glueing constructions by Hyland, Schalk, and Tan [39,23] which is tailored to ‹-autonomous categories. The
 general idea is to associate two predicates with each type: one for the type and another one for its dual.
 For a ‹-autonomous category C with monoidal units 1 and K, an orthogonality relation K is a family
 of subsets
 Kc Ď Cp1,cqˆCpc,Kq
 indexed by objects c P C and verifying some compatibility conditions with respect to the linear logic
 structure [23]. For a subset X Ď Cp1,cq, its orthogonal XK Ď Cpc,Kq is given by XK :“ ty : c Ñ K |
 @x P X.x Kc yu with the idea that XK contains the environments (or counter-terms) that yield a correct
 computation (with respect to the chosen orthogonality relation) when combined with any term in X. Dually,
 for a subset Y Ď Cpc,Kq, its orthogonal Y K Ď Cp1,cq is given by YK :“ tx : 1 Ñ c | @y P Y.x Kc yu. We
 restrict attention subsets of global elements that are double orthogonally closed and define the orthogonality
 category induced by tKcucPC to have objects given by pairs pc,Xq with X “ XKK Ď Cp1,cq and morphisms
 pc, Xq Ñ pd,Yq given by morphisms f : c Ñ d in C such that:
 @x PX.fx PY and @yPYK.yf PXK .
 (1)
 Provided that some conditions on C and the orthogonality relation tKcucPC hold, if C is a model of
 classical linear logic then so is the induced orthogonality category, and the forgetful functor preserves the
 linear logic structure strictly [23]. In this paper, we will restrict to the special case where the orthogonality
 relation arises from a distinguished subset ‚ Ď Cp1,Kq, referred to as a pole, as follows:
 ‚c :“ tpx,yq P Cp1,cqˆCpc,Kq | yx P‚u .
 Such orthogonality relations are called focused and for them the two conditions in (1) above are equiv
alent [23]. This property will crucially allow us to subsume focused orthogonality categories within a
 f
 ibrational setting and, from the theory of fixpoint constructions for relational fibrations of the following
 section, we will obtain models of linear logic with least and greatest fixpoints.
 2 Fixpoint constructions in relational fibrations
 This section develops a general method to lift initial algebras and final coalgebras form the base category
 of a relational fibration to its total category.
 2.1 Relational fibrations
 We start by recalling some basic properties of relational fibrations.
 Definition 2.1 A C-indexed poset is a contravariant functor from a category C to the category Poset of
 posets and monotone functions between them.
 For an indexed poset R : Cop Ñ Poset, a morphism f : c Ñ d in C, and S P Rpdq, it is customary to
 write f˚pSq for RpfqpSq P Rpcq. For R P Rpcq, we moreover write f : R Ą S for R ď f˚pSq.
 Definition 2.2 The Grothendieck category GCpRq of a C-indexed poset R : Cop Ñ Poset has objects
 given by pairs tc|Ru with c P C and R P Rpcq, and morphisms f : tc|Ru Ñ td|Su given by morphisms
 f : c Ñd in C such that f : R ĄS in Rpcq. Identities and composition are given as in C.
 The forgetful functor U : GCpRq Ñ C is a Grothendieck fibration with partially-ordered fibers. Note
 that, for every c P C, R ď R1 in Rpcq if and only if idc : tc|Ru Ñ tc|R1u in GCpRq.
 We refer to U as the relational fibration of the C-indexed poset R. The cartesian lifting of f : c Ñ d in
 C with respect to td|Su P GCpRq is the morphism f : tc|f˚Su Ñ td|Su in GCpRq.
8–4
 Fixpoints constructions in focused orthogonality models of linear logic
 Definition 2.3 For a C-indexed poset R, we say that an endofunctor F on GCpRq is a lifting of an
 endofunctor F on C whenever the following diagram commutes
 GCpRq
 U
 C
 F
 GCpRq
 F
 C
 U
 We let F be a lifting of F as above for the rest of the section.
 For tc|Ru P GCpRq, we write FcpRq for the element in RpFcq given by F tc|Ru; in other words, we
 let F tc|Ru “ tFc | FcpRqu. Therefore, for all f : tc|Ru Ñ td|Su in GCpRq, since Fpfq “ Fpfq, we
 have that FcpRq ď pFfq˚
 `FdpSq˘ in RpFcq. This has the following direct consequences.
 Lemma 2.4 (i) For all c P C, the function Fc : Rpcq Ñ RpFcq is monotone.
 (ii) For all f : c Ñ d in C and S P Rpdq, Fc
 `f˚pSq˘ ď pFfq˚
 `FdpSq˘ in RpFcq.
 2.2 Initial-algebra lifting theorem
 By Lemma 2.4(i), every coalgebra γ : c Ñ Fc induces the monotone operator
 Rpcq Fc
 ÝÑRpFcq γ˚
 ÝÑRpcq .
 A lifting of the F-coalgebra γ to an F-coalgebra amounts to providing a post fixpoint of γ˚ ˝ Fc; that is,
 an R PRpcq such that R ď γ˚
 `FcpRq˘. On the other hand, a lifting of an F-algebra δ : Fd Ñ d amounts
 to providing an S P Rpdq such that FdpSq ď δ˚pSq.
 We now consider homomorphisms from a coalgebra γ : c Ñ Fc to an algebra δ : Fd Ñ d as given by
 morphisms f : c Ñ d such that the following diagram commutes:
 Fc Ff
 γ
 c
 Fd
 δ
 f
 d
 Lemma 2.5 For a coalgebra γ : c Ñ Fc, the least pre-fixpoint ∇γ P Rpcq of the monotone operator γ˚˝Fc,
 whenever it exists, provides a lifting of γ such that for all liftings S P Rpdq of an algebra δ : Fd Ñ d, every
 homomorphism c Ñ d from γ to δ lifts to a morphism tc|∇γ u Ñ td|Su.
 Proof We have γ : tc|∇γu Ñ tFc | Fcp∇γqu because ∇γ is a fixpoint of γ˚ ˝Fc.
 Let δ : Fd Ñd and S PRpdq be such that FdpSq ď δ˚pSq and let f : c Ñ d be an homomorphism from
 γ to δ.
 By Lemma 2.4(ii) and the assumption on S, we have
 Fc
 `f˚pSq˘ ď pFfq˚
 `FdpSq˘ ď pFfq˚
 `δ˚pSq˘
 and hence
 pγ˚ ˝ Fcq`f˚pSq˘ ď `δ ˝pFfq˝γ˘˚pSq “ f˚pSq ;
 that is, f˚pSq is a pre-fixpoint of γ˚ ˝ Fc. Therefore, ∇γ ď f˚pSq as required.
 l
 Theorem 2.6 For an initial F-algebra α : Fa Ñ a, if the monotone operator pα´1q˚ ˝Fa on Rpaq has a
 least pre-fixpoint ∇α then α : tFa | Fap∇αqu Ñ ta|∇αu is an initial F-algebra.
Fiore, Galal, Jafarrahmani
 8–5
 Proof For every F-algebra δ : tFd | FdpSqu Ñ td|Su the unique homomorphism upδq : a Ñ d from
 α : Fa Ña to δ :FdÑd is a homomorphism from α´1 : a Ñ Fa to δ : Fd Ñ d and, by Lemma 2.5, it is
 also the unique homomorphism from α : tFa | Fap∇αqu Ñ ta|∇αu to δ : tFd | FdpSqu Ñ td|Su.
 l
 Let α : Fa Ñ a be an initial algebra and, for an algebra δ : Fd Ñ d, let upδq : a Ñ d be the
 unique homomorphism from α to δ. In the situation of the theorem, initial algebras α satisfy the following
 property:
 for every algebra δ : Fd Ñ d and S P Rpdq, if δ : FdpSq Ą S then upδq : ∇α Ą S .
 This provides an abstract general form of induction principle. Indeed, in particular, one has:
 for every S P Rpaq, if FapSq ď α˚pSq then ∇α ď S .
 As advocated by Hermida and Jacobs [21, Definition 3.1], the standard induction principle is recovered
 when ∇α is the greatest element Ja of Rpaq, in which case one has:
 for every algebra δ : Fd Ñ d and S P Rpdq, if δ : FdpSq Ą S then upδq˚pSq “ Ja
 and, in particular, that:
 for every S P Rpaq, if FapSq ď α˚pSq then S “ Ja .
 An ipo (inductive poset) is a poset with a least element and joins of directed subsets. Such posets are
 particularly relevant to us here because of Pataraia’s constructive theorem that every monotone endofunc
tion on an ipo has a least pre-fixpoint [30].
 Definition 2.7 A C-indexed ipo is a C-indexed poset R such that Rpcq is an ipo for all c P C.
 Corollary 2.8 For every C-indexed ipo R, every endofunctor F on C, and every endofunctor F on GCpRq
 lifting it, initial F-algebras lift to initial F-algebras.
 2.3 Final-coalgebra lifting theorem
 Definition 2.9 A C-indexed poset R is said to have existential quantification whenever, for all f : a Ñ b
 in C, the monotone function f˚ : Rpbq Ñ Rpaq has a left adjoint, for which we write f! : Rpaq Ñ Rpbq.
 Lemma 2.10 For a C-indexed poset R with existential quantification, let F be an endofunctor on C and
 F be an endofunctor on GCpRq lifting it.
 For a coalgebra δ : d Ñ Fd, the greatest post-fixpoint ∆δ P Rpdq of the monotone operator δ˚ ˝ Fd,
 whenever it exists, provides a lifting of δ such that for all liftings R P Rpcq of a coalgebra γ : c Ñ Fc, every
 homomorphism c Ñ d from γ to δ lifts to a morphism tc|Ru Ñ td|∆δu.
 Proof We have δ : td|∆δu Ñ tFd | Fdp∆δqu because ∆δ is a fixpoint of δ˚ ˝Fd.
 Let γ : c Ñ Fc and R P Rpcq be such that R ď γ˚
 `FcpRq˘ and let f : c Ñ d be an homomorphism
 from γ to δ.
 We prove R ď f˚p∆δq by equivalently showing f!pRq ď ∆δ establishing that f!pRq is a post-fixpoint of
 δ˚ ˝Fd. Indeed,
 R ďγ˚
 `FcpRq˘
 , by assumption
 ď γ˚pFcpf˚pf!pRqqqq
 , as f! % f˚
 ď γ˚ppFfq˚pFdpf!pRqqqq , by Lemma 2.4(ii)
 “ f˚pδ˚pFdpf!pRqqqq
 and therefore f!pRq ď δ˚pFdpf!pRqqq.
 , as f : pc,γq Ñ pd,δq
 l
 Definition 2.11 A co-ipo is a poset whose opposite is an ipo. A C-indexed co-ipo is a C-indexed poset
 Rsuch that Rpcq is a co-ipo for all c P C.
8–6
 Fixpoints constructions in focused orthogonality models of linear logic
 By the dual of Pataria’s theorem [30], that monotone endofunctions on co-ipos have greatest post
f
 ixpoints, we obtain the following.
 Corollary 2.12 For every C-indexed co-ipo with existential quantification R, every endofunctor F on C,
 and every endofunctor F on GCpRq lifting it, final F-coalgebras lift to final F-coalgebras.
 We note that the above may be also established under slightly weaker hypothesis than existential
 quantification.
 Lemma 2.13 For a C-indexed poset R, let F be an endofunctor on C and F be an endofunctor on GCpRq
 lifting it. For a coalgebra δ : d Ñ Fd, such that Rpdq is a co-ipo let ∆δ P Rpdq be the greatest post-fixpoint
 of the monotone operator δ˚ ˝ Fd.
 For a coalgebra γ : tc|Ru Ñ tFc|FcpRqu and a homomorphism f : c Ñ d from γ to δ, if tS P Rpdq |
 Rďf˚pSqu is a sub co-ipo of Rpdq then f lifts to a morphism tc|Ru Ñ td|∆δu.
 Proof It suffices to show that tS P Rpdq | R ď f˚pSqu is invariant under δ˚ ˝Fd. Indeed, if R ď f˚pSq,
 then FcpRq ď Fc
 `f˚pSq˘ ď pFfq˚
 `FdpSq˘ and R ď γ˚
 `FcpRq˘ ď γ˚
 `pFfq˚pFdpSqq˘ “ f˚
 `δ˚
 `FdpSq˘˘.l
 3 Focused orthogonality fibrationally
 Westudy focused orthogonality categories representing them in terms of Grothendieck categories of indexed
 complete lattices with existential quantification. This, together with the study of the previous section,
 provides an axiomatic theory of fixpoint constructions in focused orthogonality models of linear logic.
 3.1 Focused orthogonality categories
 We expand upon the exposition of focused orthogonality given in Section 1. A pole in a category C is a
 subset ‚ Ď Cps,tq for a pair of distinguished objects s and t. To obtain a model of intuitionistic linear
 logic one takes s to be the monoidal unit 1, while in the classical setting one further takes t to be its dual
 K. The focused orthogonality induced by a pole ‚ is the family of relations t‚c Ď Cps,cq ˆ Cpc,tqucPC
 given by
 x ‚c u ðñ pu˝xqP‚ .
 The defining property of focused orthogonalities is being reciprocal [20]; in the sense that, for all morphisms
 x : s Ñc, f : c Ñd, and u:dÑt in C,
 pf ˝xq ‚d u ðñ x‚c pu˝fq .
 This plays a crucial role in the following section.
 For a subset X Ď Cps,cq, its orthogonal XK Ď Cpc,tq is given as below on the left
 XK :“tu: cÑt|@xPX.xKuu , UK :“tx:sÑc|@uPU.xKuu
 (2)
 while, dually, for a subset U Ď Cpc,tq, its orthogonal UK Ď Cps,cq is given as above on the right.
 As it is standard, these definitions induce a Galois connection between Cpcq :“ `P`Cps,cq˘,Ď ˘ and
 C˝pcq :“ `P`Cpc,tq˘,Ě˘. The fixpoints of the associated closure operator on Cpcq, referred to as double
 orthogonally closed subsets, form complete lattices:
 Dpcq :“ tX Ď Cps,cq | X “ XKKu .
 (3)
 Definition 3.1 The focused orthogonality category O‚pCq of a category C with a pole ‚ Ď Cps,tq has
 objects given by pairs pc,Xq with c P C and X P Dpcq, and morphisms f : pc,Xq Ñ pd,Yq given by
 morphisms f : c Ñ d in C such that
 @px : s Ñcq P X.pf ˝xq P Y .
 (4)
Fiore, Galal, Jafarrahmani
 8–7
 3.2 Focused orthogonality categories are relational fibrations
 We need to introduce some notation.
 (i) For a morphism f : c Ñ d in a category C, we respectively write
 f˝ :“ Cps,fq : Cps,cq Ñ Cps,dq
 and
 ˝f :“ Cpf,tq : Cpd,tq Ñ Cpc,tq
 for the operations of post and pre composition with f.
 (ii) For a function h : A Ñ B between sets, we write h˚ : PpBq Ñ PpAq for the inverse image function
 and h! : PpAq Ñ PpBq for its left adjoint. In elementary terms,
 h˚pTq :“ ta P A | hpaq P T u and h!pSq :“ tb P B | Da P S.hpaq “ bu .
 For the rest of the section, let f : c Ñ d be a morphism in a category C with a pole ‚ Ď Cps,tq. Since,
 for x : s Ñ c in C and V ĎCpd,tq,
 x P pf˝q˚pV Kq ðñ @v P V.pf ˝xq K v and xP`p˝fq!pVq˘K ðñ @v PV.x Kpv˝fq
 we have
 `p˝fq!pV q˘K “ pf˝q˚pV Kq for all V Ď Cpd,tq
 and obtain the commutative diagram on the left below that recasts reciprocity as a lifting property by
 duality:
 C˝pdq
 p´qK
 Cpdq
 p˝fq!
 pf˝q˚
 C˝pcq
 p´q
 Cpcq
 pf˝q˚
 Cpdq
 K
 p´qK
 C˝pdq
 p˝fq!
 Cpcq
 p´q
 C˝pcq
 K
 (5)
 Then, as pf˝q˚ : Cpdq Ñ Cpcq lifts along the right adjoints p´qK, it also lifts along the forgetful functors
 from their induced categories of algebras; that is, in this case, it restricts to double orthogonally closed
 subsets. We spell out the details.
 In (5), the diagram on the left has as mate the diagram on the right; that is,
 `
 pf˝q˚pY q˘K Ď p˝fq!pY Kq for all Y Ď Cps,dq .
 Pasting these two diagrams, we obtain a distributive law:
 pf˝q˚
 Cpdq
 p´qKK
 C˝pdq
 pf˝q˚
 Cpcq
 p´q
 C˝pcq
 KK
 that is, `pf˝q˚pY q˘KK Ď pf˝q˚pY KKq for all Y Ď Cps,cq. Thus, pf˝q˚ : Cpdq Ñ Cpcq restricts to a
 meet-preserving monotone function Dpdq Ñ Dpcq between complete lattices.
 The above development results in a representation theorem for focused orthogonality categories in terms
 of Grothendieck categories of indexed complete lattices with existential quantification.
 Definition 3.2 A C-indexed complete lattice is a C-indexed poset R such that Rpcq is a complete lattice
 for all c P C.
 ùñ
 ùñ
8–8
 Fixpoints constructions in focused orthogonality models of linear logic
 Theorem 3.3 Every category C with a pole ‚ induces a C-indexed complete lattice with existential quan
tification D whose Grothendieck category GCpDq is isomorphic to the focused orthogonality category O‚pCq.
 Proof Consider the indexed family tDpcqucPC of double orthogonally closed subsets (3) with action
 Dpfq :“ pf˝q˚ : Dpdq Ñ Dpcq for all f : c Ñ d in C. Note that the condition X Ď DpfqpYq is equivalent
 to the statement (4).
 l
 Corollary 3.4 Let C be a category with a pole ‚. For every endofunctor F on C and every endofunctor
 F on O‚pCq lifting it, F has an initial algebra (resp. final coalgebra) if and only if so does F.
 4 Models of linear logic with fixpoints
 4.1 Categorical models
 We provide an alternative presentation of the notion of categorical model of classical linear logic with fix
points given by Ehrhard and Jafarrahmani [9, Definition 7]. Our approach is adaptable to the intuitionistic
 setting which we also include.
 We restrict attention to the specification of linear logic types; we omit the specification of the logical
 system, the categorical models of which are well known. The treatment of fixpoint operators requires the
 consideration of types with variance in contexts of type variables with variance. To this end, we introduce
 judgements for types of the form Γ $ τ : v where v ranges over the set of sorts V :“ t`,´u, Γ ranges
 over V-sorted contexts, and τ ranges over types. The sorts are used to indicate the intended variance, with
 ` (positive) standing for covariance and ´ (negative) standing for contravariance; as such the dual sort v
 of a sort v is given by ` “ ´ and ´ “ `.
 The core judgement rules of the types of linear logic are:
 Γ $x:v
 px : v in Γq
 Γ $τ1 : v Γ$τ2 :v
 Γ $τ1oτ2 : v
 Γ $o:`
 `
 o in tb,‘,&u˘
 In classical linear logic, these are extended with:
 Γ $τ1 : v Γ$τ2 :v
 Γ $τ1`τ2 : v
 `
 o in t1,0,Ju˘
 Γ $τ :v
 Γ $!τ :v
 Γ $τ :v
 Γ $?τ :v
 Γ $τ :v
 Γ $τK :v
 Γ $K:`
 while in intuitionistic linear logic they are extended with:
 Γ $τ1 : v Γ$τ2 :v
 Γ $τ1 ⊸τ2 :v
 A model of classical linear logic (resp. intuitionistic linear logic) is a ‹-autonomous (resp. symmetric
 monoidal closed bicartesian) category with a linear exponential comonad [34,32,29]. For both classical and
 intuitionistic models L , every judgement x1 : v1,...,xn : vn $ τ : v has a standard interpretation functor
 Lv1 ˆ¨¨¨ˆLvn Ñ Lv, where L` :“ L and L´ :“ Lop. The class of interpretation functors induced
 by judgements forms a V-sorted concrete clone L on L.
 We recall the notion of parameterised fixpoint (see, for instance, Fiore [11, Chapter 6]). A functor
 F : D ˆC ÑC is said to have parameterised initial algebras (resp. final coalgebras) whenever, for all
 d P D, the endofunctor Fpd,´q on C has an initial algebra (resp. final coalgebra), say µFpdq `resp. νFpdq˘,
 in which case the structure canonically extends to a functor µF : D Ñ C (resp. νF : D Ñ C).
 A V-sorted concrete clone of functors F “ tFσ,v Ď rCσ,CvsuσPV‹,vPV, where Cps1,...,snq :“ Cs1 ˆ ¨¨¨ ˆ
 Csn, is said to have parameterised fixpoints whenever every F P Fps1,...,sn,vq,v has parameterised initial
 algebras and final coalgebras, and their induced functors µF and νF are in Fps1,...,snq,v.
Fiore, Galal, Jafarrahmani
 8–9
 A model of linear logic with fixpoints is a model of linear logic L on which there is a V-sorted concrete
 clone of functors with parameterised fixpoints containing the V-sorted clone L on L. This notion of model
 is in line with the general notion of model for second-order equational presentations [12] and allows for
 a canonical interpretation of the extension of the linear logic typing judgements with rules for least and
 greatest fixpoints as follows:
 Γ,x : v $ τ : v
 Γ $φx.τ :v
 4.2 Focused orthogonality models
 pφ in tµ,νuq
 Theorem 4.1 (Hyland and Schalk [23]) For a model of classical linear logic L with a distributive law
 Lp1,´q Ñ Lp1,!´q and a pole ‚Ď Lp1,Kq between the monoidal units, the induced focused orthogonality
 category O‚pLq is a model of classical linear logic and the forgetful functor to L preserves the structure
 strictly.
 There is an analogous theorem for models of intuitionistic linear logic for which the reader is referred
 to Hyland and Schalk [23].
 The following result is a consequence of the theorem above and Corollary 3.4.
 Theorem 4.2 Under the hypothesis of Theorem 4.1, if L is a model of linear logic with fixpoints then so
 is O‚pLq and the forgetful functor to L preserves the structure strictly.
 4.3 Examples
 A variety of models of linear logic are instances of focused orthogonality constructions. We examine
 examples to which Theorem 4.2 applies and thereby yield models with fixpoints. As not all orthogonality
 models of linear logic are instances of focused orthogonality constructions, we also discuss the cases of
 coherence and finiteness spaces to which our results may be applied, even though these models do not arise
 from focused orthogonalities in the relational model.
 Example 4.3 `Phase spaces [18]˘ Consider a commutative monoid pM,e,¨q and a subset ‚ Ď M. For
 a subset X Ď M, its orthogonal is defined as XK :“ ty P M | @x P M,x ¨ y P‚u. Subsets satisfying
 X “XKK are called facts and provide a complete provability semantics for linear logic. The commutative
 monoid M can be considered as a compact closed category M with a single object ‚ (being both 1 and
 K). The pole ‚Ď Mp‚,‚q corresponds to a subset of M and one can reformulate the phase model within
 the setting of focused orthogonality (except for the exponential structure which is defined differently).
 Therefore, one can interpret least and greatest fixpoints of multiplicative and additive linear logic types in
 phase semantics to provide a Tarskian sound model of µMALL [5].
 The category Rel of sets and relations between them is one of the most basic models of linear logic,
 with many other models arising as refinements of it. Being compact closed, it is a degenerate model. The
 monoidal units are singletons and there are only two non-trivial focused orthogonalities on Rel given by
 the poles t∅u and ttiduu.
 Example 4.4 The model of non-uniform totality spaces studied by Ehrhard and Jafarrahmani [9] corre
sponds to the focused orthogonality category Rel‚ induced by the pole ‚“ ttiduu. Explicitly, for a set
 A and a subset X Ď Relp1,Aq– PpAq, one has:
 XK “tyPPpAq|@xPX,y˝x“idu“ty PPpAq|@xPX,xXy ‰∅u .
 The induced category is a model of µLL that provides a normalization theorem for proofs.
 One can generalize the relational model by considering the category of weighted relations (or matrices)
 RelS over a continuous semiring S [27,26]. Standard examples are the Boolean semiring ptt,fu,_,^,f,tq,
8–10
 Fixpoints constructions in focused orthogonality models of linear logic
 the completed natural numbers N “ pN Y t8u,`,¨,0,1q, and the completed non-negative reals R` “
 pR` Yt8u,`,¨,0,1q. Objects of RelS are sets and a morphism from A to B is a function f : AˆB Ñ S
 (also called an S-matrix). The composite of f : A ˆ B Ñ S and g : B ˆC Ñ S is the function AˆC Ñ S
 given by pg ˝fqpa,cq :“ ř
 bPB fpa,bq ¨ gpb,cq. Since RelSp1,Kq– S, a pole consists of a subset of S.
 Example 4.5 The model of probabilistic coherence spaces PCoh by Danos and Ehrhard [4] can be ob
tained as a focused orthogonality category on RelR` 
with pole r0,1s Ď R`. The associated coKleisli
 category provides a fully abstract model of probabilistic PCF [10] with a fixpoint operator for terms that
 may be obtained as an application of our results in Section 5 (see Example 5.9).
 Hamano [20] considered a continuous extension of PCoh based on the category of measurable spaces
 and s-finite transition kernels. Even if not providing monoidal closed structure, his construction involves
 a focused orthogonality. Indeed, taking the same pole ‚:“ r0,1s Ď R`, for a measurable space pA,Σq, a
 measure µ viewed as a morphism p1,P1q Ñ pA,Σq, and a measurable function f viewed as a morphism
 pA,Σq Ñ p1,P1q, one has µ ‚pA,Σq f iff ş
 Af dµ ď 1.
 Example 4.6 Orthogonality can be also used to relate models. For instance, by considering the qualitative
 linear logic model ScottL, whose objects are preorders and morphisms are ideal binary relations, the
 category of preorders and projections introduced by Ehrhard [8] can be obtained as a subcategory of the
 focused orthogonality model pScottLˆRelq‚ with pole ‚:“ tpid,idqu. A reflexive object in this setting,
 obtained by lifting reflexive objects from ScottL and Rel, allows the translation of normalization theorems
 between idempotent and non-idempotent typing systems [7].
 Example 4.7 Coherence spaces, first introduced by Girard [16] to give a denotational semantics for Sys
tem F, led to the discovery of linear logic through the linear decomposition of stable functions. The category
 of coherence spaces Coh can be obtained as an orthogonality construction on Rel: two subsets of a set
 are orthogonal whenever their intersection has cardinality at most one.
 The orthogonality for coherence spaces in Rel is not focused; however, in Coh one can consider the
 refinement of coherence spaces with totality that corresponds to the focused orthogonality category Coh‚
 with pole ‚:“ ttiduu Ď Cohp1,Kq.
 Example 4.8 The model of (differential) linear logic of finiteness spaces by Ehrhard [6] is based on an
 orthogonality in Rel that captures finite computations: two subsets of a set are orthogonal whenever their
 intersection is finite. This is however not focused and thus one cannot directly apply the results developed
 in the paper. Tasson and Vaux [40] studied conditions for lifting endofunctors on Rel to Fin and showed
 how to calculate least fixpoints for a subclass of linear logic formulas. It remains to be seen whether
 f
 initeness spaces provide a model for µLL. Instead, one can extend the model by considering a weighted
 version of finiteness spaces over RelN with pole ‚:“ N Ď N, obtaining a focused model of linear logic with
 f
 ixpoints.
 5 Domain-theoretic models
 After Freyd [13], the category-theoretic solution of recursive type equations, where one is interested in
 f
 ixpoints of recursive types with mixed variance, is based on the notion of algebraic compactness asserting
 the coincidence of initial algebras and final coalgebras. In domain-theoretic models, this in turn arises from
 the limit/colimit coincidence in the order-enriched setting [33,42,36].
 We write Cpo for the category of cpos (ω-chain complete partial orders) and continuous functions
 between them.
 Theorem 5.1 (Fiore [11, Chapter 7]) Let a kind be a Cpo-category with ep-zero (viz. a zero object
 such that every morphism with it as source is an embedding) and colimits of ω-chains of embeddings.
 Every kind is Cpo-algebraically compact; that is, every Cpo-endofunctor on it has a bifree algebra
 (viz. an initial algebra whose inverse is a final coalgebra).
 In domain-theoretic models of linear logic, fixpoint operators arise naturally and are typically charater
ized by the axiom of uniformity.
Fiore, Galal, Jafarrahmani
 8–11
 Definition 5.2 (i) A fixpoint operator on a category D with a terminal object J is a family of functions
 p´q: : Dpd,dq Ñ DpJ,dq indexed by the objects d of D such that f: “ f ˝f: for all endomorphisms
 f on d.
 (ii) Let C and D be categories with terminal object, and let J : C Ñ D be a bijective-on-objects functor
 preserving terminal objects. A fixpoint operator p´q: on D is said to be J-uniform if for every
 h : c Ñd in C and f :cÑc,g :dÑd in D,
 Jphq ˝ f “ g˝Jphq implies Jphq˝f: “ g: .
 The following is a consequence of the study of fixpoint operators by Simpson and Plotkin [35].
 Corollary 5.3 For a kind D equipped with a Cpo-comonad S on it, the coKleisli category DS has a unique
 J-uniform fixpoint operator for J : D Ñ DS the cofree functor of the adjoint resolution of S.
 The above results apply, for instance, to the relational model and the coherence space model (for the
 quantitative and qualitative comonads) of linear logic. We now proceed to show that further examples
 arise from Grothendieck categories in general and from focused orthogonality in particular.
 The following definition and theorem are instances of Section 5 of Cattani, Fiore, and Winskel [3] where
 the categorified scenario is considered. We will write Cppo for the full subcategory of Cpo consisting
 of the pointed cpos (that is, those with bottom element) and let CppoK be the subcategory of Cppo
 consisting of the strict (that is, bottom-element preserving) functions.
 Definition 5.4 An admissible D-indexed poset for a CppoK-category D is a Poset-functor R : Dop Ñ
 Poset such that the poset Rpdqop is a cppo for all d P D and the monotone function Dpc,dq Ñ PosetpRpdqop,Rpcqopq :
 f ÞÑ pf˚qop is strict continuous for all c,d P D.
 Theorem 5.5 For a kind D and an admissible D-indexed poset R, the Grothendieck category GDpRq is a
 kind and the forgetful functor to D preserves the structure strictly.
 Proof (outline) Since pŽ
 n fnq˚ “ Ź
 npfnq˚ : Rpdq Ñ Rpcqforeveryω-chainf inDpc,dq, theGrothendieck
 category GDpRq Cpo-enriches. Since, the reindexing pKc,dq˚ : Rpdq Ñ Rpcq along the bottom element
 Kc,d P Dpc,dq is constantly the top element of Rpcq, the ep-zero of GDpRq consists of the ep-zero K of D
 paired with the top element of RpKq. The colimiting cone xen : pdn,Rnq Ñ pd,Rqyn of an ω-chain of embed
dings xpdn,Rnq Ñ pdn`1,Rn`1qyn in GDpRq consists of the colimiting cone of embeddings xen : dn Ñ dyn
 of the ω-chain of embeddings xdn Ñ dn`1yn in D with R :“ Ź
 nppnq˚pRnq P Rpdq for pn the projection of
 en.
 Finally, we investigate focused orthogonality in this domain-theoretic setting.
 Definition 5.6 An admissible pole for a CppoK-category D is a pole that is a sub-cppo of Dps,tq.
 l
 Lemma 5.7 For aCppoK-category with an admissible pole, the indexed poset of double orthogonally closed
 sets is admissible.
 Corollary 5.8 For a kind D with an admissible pole ‚, the focused orthogonality category O‚pDq is a
 kind and the forgetful functor to D preserves the structure strictly.
 Example 5.9 Theweighted relational model RelR` 
(see Example 4.5) is a kind and the pole ‚ :“ r0,1s Ď
 R` is admissible. Corollaries 5.8 and 5.3 provide then a uniform fixpoint operator on the coKleisli category
 of probabilistic coherence spaces which allows us to recover the fixpoint operator for terms of [4].
 On the other hand, totality models are tools to provide a denotational account of normalization and
 therefore do not have fixpoint operators. In particular, note that for the totality models presented in
 Section 4.3 the underlying orthogonality construction is done with the singleton pole ␣tidu( which does
 not contain the empty relation and is therefore not admissible.
8–12
 Fixpoints constructions in focused orthogonality models of linear logic
 Conclusion
 Recasting focused orthogonality constructions within a relational fibration framework, we have developed a
 categorical theory to construct new models of linear logic with fixpoints by means of lifting initial algebras
 and final coalgebras from the base model to the focused orthogonality one. Our method is widely applicable:
 it allows to re-explain the totality model of µLL studied by Ehrhard and Jafarrahmani [9] and opens the
 way for refining a variety of other models besides the relational one. In connection to this, Tsukada and
 Asada [41] provided a unified framework based on module theory to make the linear algebraic aspect of
 models of linear logic explicit. In particular, they considered models of intuitionistic linear logic based on
 categories of R-modules and linear maps for R a Σ-semiring. It would be interesting to investigate fixpoint
 constructions in these models and thereafter consider refinements of them using our theory for focused
 orthogonalities. The same applies to their discussion of models of classical linear logic.
 Our lifting theorems further extend from relational fibrations to categorical fibrations. In future work,
 we aim to use these results to obtain a theory of fixpoint constructions for general glueing and double
glueing models. Since double glueing constructions have been extensively used to study full completeness
 by refining models to contain only morphisms that are the interpretation of proof terms [28], we aim to
 also use our results to construct fully complete models of linear logic with fixpoints.
 While we have considered fixpoint operators in the induced cartesian closed category of domain-theoretic
 models of linear logic, we also aim to explore lifting theorems for traces [24] in the linear base model. Many
 orthogonality and (double) glueing constructions are indeed done on a compact closed category (which
 has a canonical trace) and the refinement induced by the orthogonality usually eliminates this degeneracy.
 Understanding whether one can lift this canonical trace to orthogonality or double-glued categories would
 provide a new method for constructing traced categories for intuitionistic models.



 ---




















-
 Combining fixpoint and differentiation theory
 Zeinab Galal
 University of Bologna
 Italy
 Inria Sophia Antipolis
 France
 ABSTRACT
 arXiv:2407.12691v1  [math.CT]  17 Jul 2024
 Interactions between derivatives and fixpoints have many impor
tant applications in both computer science and mathematics. In
 this paper, we provide a categorical framework to combine fix
pointswithderivativesbystudying Cartesiandifferential categories
 with a fixpoint operator. We introduce an additional axiom relat
ing the derivative of a fixpoint with the fixpoint of the derivative.
 Weshowhowthestandard examplesofCartesian differential cate
gories where we can compute fixpoints provide canonical models
 of this notion. We also consider when the fixpoint operator is a
 Conway operator, or when the underlying category is closed. As
 an application, we show how this framework is a suitable setting
 to formalize the Newton-Raphson optimizationforfast approxima
tion of fixpoints and extend it to higher order languages.
 CCS CONCEPTS
 • Theory of computation → Categorical semantics; • Mathe
matics of computing → Differential calculus.
 KEYWORDS
 Categorical semantics, Cartesian differential categories, fixpoint
 operators
 ACMReference Format:
 Zeinab Galal and Jean-Simon Pacaud Lemay. 2024.Combining fixpoint and
 differentiation theory. In 39th Annual ACM/IEEE Symposium on Logic in
 Computer Science (LICS ’24), July 8–11, 2024, Tallinn, Estonia. ACM, New
 York, NY, USA, 16 pages. https://doi.org/10.1145/3661814.3662108
 1 INTRODUCTION
 Fixpoint theory has many applications for establishing the exis
tence anduniquenessofsolutionsforsystems ofdifferential and in
tegral equations. Dually, using the information provided by deriva
tives to approximate more efficiently fixpoints is a key tool in nu
merical analysis, data-flowanalysis, automaticdifferentiation, enu
merative combinatorics,formallanguagetheory,etc.Awell-known
 example is the Newton-Raphson method for fast approximation
 of roots or fixpoints of differentiable functions. Starting from an
 initial point, the main idea is to compute the next iteration by us
ing the tangent line. It is a powerful technique as the convergence
 Permission to make digital or hard copies of all or part of this work for personal or
 classroom use is granted without fee provided that copies are not made or distributed
 for profit or commercial advantage and that copies bear this notice and the full cita
tion on the first page. Copyrights for components of this work owned by others than
 the author(s) must be honored. Abstracting with credit is permitted. To copy other
wise, or republish, to post on servers or to redistribute to lists, requires prior specific
 permission and/or a fee. Request permissions from permissions@acm.org.
 LICS ’24, July 8–11, 2024, Tallinn, Estonia
 ©2024 Copyright held by the owner/author(s). Publication rights licensed to ACM.
 ACMISBN979-8-4007-0660-8/24/07...$15.00
 https://doi.org/10.1145/3661814.3662108
 Jean-Simon Pacaud Lemay
 Macquarie University
 Australia
 rate is quadratic: at each iteration, the number of accurately com
puted digits doubles. Many other standard optimization methods
 are based on generalizations or variants of the Newton-Raphson
 method.
 From the viewpoint of programming languages, fixpoints are a
 key feature as they allow for the presence of loops and defining
 programs via recursion or iteration. Incorporating recursive fea
tures into differential programming languages has seen increasing
 interest in recent years [1, 17, 51] and there is already a rich litera
ture on implicit automatic differentiation to perform automatic dif
ferentiation efficiently on functions defined implicitly by fixpoint
 equations [3, 5, 33].
 While the categorical semantics of differentiation and fixpoints
 are now well-established lines of research, one aspect that has not
 yet beenstudied in full detail is the interaction between them from
 a semantical viewpoint. The objective of this paper is to develop a
 denotational framework to combine fixpoints with derivatives by
 axiomatizing the notion of Cartesian differential categories with a
 f
 ixpoint operator. This axiomatization provides a guideline to in
troducing differentials to-calculi with fixpoint operators such as
 PCF [54] and the-Y-calculus [60].
 Cartesiandifferential categories (Definition 2.1)wereintroduced
 by Blute, Cockett, and Seely in [8], and provide the categorical
 foundations of multivariable differential calculus. Cartesian closed
 differential categories (Section 6) provide the categorical seman
tics of the differential-calculus, introduced by Ehrhard and Reg
nier [9, 20, 50]. Cartesian (closed) differential categories have been
 quite successful in formalizing various notions related to differen
tiation, and have also found numerous applications in both mathe
matics and computer science. Moreover, Cartesian differential cat
egories belong to a larger story of categorical foundations of dif
ferentiation, which include differential categories [7], coherent dif
ferential categories [21], reverse differential categories [13], differ
ential restriction categories [12], and tangent categories [11].
 In this paper, we show how to combine the differential opera
tor in a Cartesian differential category with various categorical no
tions of fixpoint operators [6, 35, 36, 38, 57] such as parametrized
 f
 ixpoint operators (Definition 3.2) and Conway operators (Defini
tion 4.2). As such, to this end, we introduce Cartesian differential
 f
 ixpoint categories (Definition 3.3), which are Cartesian differential
 categories equipped with a parametrized fixpoint operator which
 satisfies whatwe call the differential-fixpointrule (6) describing the
 derivative of the parametrized fixpoint.
 To help understand the differential-fixpoint rule, let us provide
 some intuition using ordinary calculus. Consider the smooth func
tion : R×R →Rdefinedas ( , ) :=1− 2 .Thenthefunction
 : R →Rdefined as ( ) := 1
 1+ 2 
is a solution of the fixpoint or
 1
LICS ’24, July 8–11, 2024, Tallinn, Estonia
 Zeinab Galal and Jean-Simon Pacaud Lemay
 implicit equation
 If wedifferentiate bothsides, using thechainruleontheright-hand
 side, we obtain:
 ′
 Provided that 
′
 ( ) = ( , ( ))+ ( , ( )) · ′( )
 ( , ( )) ≠1,wemayrewrite (2) as:
 −1 
( ) = 1− ( , ( ))
 ( , ( )) = −2
 (1 + 2)2
 (2)
 relating the derivative of with the derivative of its fixpoint. This
 is the basis of many iteration schemes to compute or approximate
 the derivative of a fixpoint such as the Newton-Raphson method.
 If wecombinetheequations(1) and(2), we obtainthatthefunction
 T( ) : R×R→R×RdefinedasT( )( , ) = ( ( ), ′( )· )isa
 f
 ixpoint for the function T( ) : R×R×R×R → R×Rdefinedas:
 T( )( , , , ) = ( ( , ), ( , )· + ( , )· ). (3)
 ThisToperationiscalledthetangentbundlefunctor (Definition2.2),
 and can be defined in any Cartesian differential category (it corre
spondstothetangent bundleinalgebraicgeometryandtotheJaco
bian vector productin forwarddifferentiation). Therefore, comput
ing the derivative of the fixpoint of is equivalent to computing
 the fixpoint of T( ), and this is the property we axiomatize for
 Cartesian differential categories.
 Outline: Section 2 is a background section where we review
 Cartesian differential categories. In Section 3, we study the com
patibility relation between parametrized fixpointoperatorsand dif
ferential combinators, and introduceCartesian differential fixpoint
 categories. In Section 4, we consider the case of Conway operators,
 and prove an equivalence between various axioms relating deriva
tives and fixpoints. In Section 5, we study the relation between fix
points and linearity. In Section 6, we consider the Cartesian closed
 setting. In Section7, we provide manyexamples ofCartesian differ
ential fixpoint categories. In Section 8, we provide an application
 byextending theNewton-Raphsonoptimizationmethodtoourset
ting. We concludein Section9 witha discussion aboutfuture work.
 Related works: In [19, Theorem 5.29], Ehrhard proves a com
patibility relation in a cpo-enriched setting between the least fix
point operator (in the coKleisli category) and the tangent bundle
 functor of a Scott coherent differential category. In [59], Sprunger
 and Katsumata construct Cartesian differential categories with de
layed trace operators, which are related to trace operators but no
 longer assume the fixpoint axiom.
 Conventions: We assume the reader is familiar with basic no
tions of category theory such as categories, functors, products, etc.
 Anarbitrary category will be denoted by C, with arbitrary objects
 denoted by capital letters , , , , etc. and maps by minuscule
 letters , ,ℎ, etc. Hom-sets are denoted as C( , ), arbitrary maps
 as : → , identity mapsas 1 : → ,andweusethestan
dard notation ◦ and convention for composition (unlike in other
 Cartesian differential papers which use diagrammatic order).
 2 CARTESIANDIFFERENTIAL CATEGORIES
 ( ) = ( , ( )).
 (1)
 In this background section, we review Cartesian differential cate
gories. For a more in-depth introduction, we refer the reader to [8,
 14, 32, 46, 48].
 The underlying structure of a Cartesian differential category is
 that of a Cartesian left additive category, which can be described
 as a category with finite products which is skew-enriched over the
 category of commutative monoids [32]. More explicitly, a left ad
ditive category [8, Definition 1.1.1] is a category C such that each
 hom-set C( , ) is a commutative monoid (written additively), so
 wehave zero maps 0 and can take the sum of maps + ,however
 only pre-composition preserves the additive structure, that is,
 ( + )◦ = ◦ + ◦ and 0◦ =0.
 Maps whichdopreserve the additive structure, that is,
 ◦ ( + )= ◦ + ◦ and ◦0=0,
 are called additive maps [8, Definition 1.1.1]. Then a Cartesian
 left additive category is a left additive category with finite prod
ucts which is compatible with the additive structure. For a cate
gory C with finite products, we denote the product by ×, the pro
jection maps as 
: 
1 ×... × → ,thepairingoperationby
 −, . . . , −, the canonical diagonal map by Δ = 1 ,1 : →
 × and the terminal object by ⊤. Then a Cartesian left ad
ditive category [8, Definition 1.2.1] is a left additive category C
 which has finite productsand such that all the projection maps are
 additive.
 ACartesian differential category is a Cartesian left additive cate
gory equipped with a differential combinator, which is an operator
 which sends maps to their derivative. In this paper, it is impor
tant to note that we follow the now more widely used convention
 used for Cartesian differential categories which flips the conven
tion used in [8], so that the linear argument of the derivative is in
 the second argument rather than in the first.
 Definition 2.1. A Cartesian differential category [8, Definition
 2.1.1] is a Cartesian left additive category C equipped with a dif
ferential combinator D, which is a family of functions indexed
 by pairs of objects in C:
 : 
D: C( , ) →C( × , )
 →
 D[ ] : × →
 such that the seven following axioms hold:
 [CD.1] D[0] = 0 and for all , : → ,
 D[ + ]=D[ ]+D[ ]
 [CD.2] for all : → and , , : → ,
 D[ ] ◦ ,0 =0 and
 D[ ] ◦ , + =(D[ ]◦ , )+(D[ ]◦ , )
 [CD.3] D[1 ] = 2 and for ∈ {1,2},
 D[ ] = ◦ 2 : ( 1× 2)×( 1× 2)→
 [CD.4] for all : → ,
 D[ 1,..., ] = D[ 1],...,D[ ]
 [CD.5] for all : → , : → ,
 D[ ◦ ]=D[ ]◦ ◦ 1,D[ ]
 2
Combining fixpoint and differentiation theory
 LICS ’24, July 8–11, 2024, Tallinn, Estonia
 [CD.6] for all : → and , , : → ,
 D[D[ ]] ◦ , ,0, =D[ ]◦ ,
 [CD.7] for all : → and , , : → ,
 D[D[ ]] ◦ , , ,0 =D[D[ ]]◦ , , ,0
 For a map : → , themapD[ ] : × → iscalledthe
 derivative of .
 The axioms of a differential combinator are analogues of the ba
sic properties of the total derivative from multivariable differential
 calculus. The axiomssay that:(1) thederivative of a sumis the sum
 of the derivatives, (2) derivatives are additive in their second argu
ment, (3) thederivative of identity maps and projectionsare projec
tions, (4) the derivative of a pairing is the pairing of the derivatives,
 (5) the chain rule for the derivative of a composition,(6) the deriva
tive is linear in its second argument, and lastly (7) is the symmetry
 of the partial derivatives. The term linear refers to the canonical
 notion of linearity in a Cartesian differential category, which we
 discuss in Section 5. There is a sound and complete term calculus
 for Cartesian differential categories [8, Section 4], which is useful
 for intuition and proofs. So we write:
 D[ ]( , ) := d ( )
 d ( )·
 In particular, the chain rule axiom is expressed as:
 d ( ( ))
 d
 ( ) · = d ( )
 d ( ( ))· d ( )
 d ( )·
 Arguably, the canonical example of a Cartesian differential cat
egory is the category of real smooth functions, whose differen
tial combinator is given by the standard differentiation of smooth
 functions [14, Example 2.10]. In Section 6, we will discuss Carte
sian closed differential categories, which are particularlyimportant
 since they provide the categorical semantics of the differential
calculus [9]– though closed structure does not play a crucial role
 for the story of this paper. In Section 7, we provide other examples
 of Cartesian differential categories. For more examples of Carte
sian differential categories, we refer the reader to see [14, 32].
 In a Cartesian differential category, the differential combinator
 induces a functor called the tangent bundle functor. This functor
 plays an important role in the story of this paper since, as we
 will see in Section 3, it fits very naturally with fixpoint operators.
 The name comes from the fact that every Cartesian differential
 category is a tangent category [11, Proposition 4.7], and so this
 functor corresponds to the classical tangent bundle functor for Eu
clidean spaces.
 Definition 2.2. For a Cartesian differential category C, the tan
gent bundle functor [46, Proposition 1] is the endofunctor T :
 C → Cdefined on objects as T( ) = × and on maps as
 T( ) = ◦ 1,D[ ] ,whichinthetermcalculusis expressed as:
 T( )( , ) = ( ), d ( )
 d ( )·
 Properties that the tangent bundlefunctorsatisfies can befound
 in [46, Lemma2].Itisworthwhiletopointoutthatthetangent bun
dle preserves composition if and only if the chain rule of the differ
ential combinator holds. In fact, the chain rule can be reformulated
 in terms of the tangent functor bundle as: D[ ◦ ] = D[ ] ◦T( ).
 3 COMBININGFIXPOINTSAND
 DERIVATIVES
 In this section, we combinedifferential combinatorsand parametri
zed fixpoint operators by studying their compatibility in an arbi
trary Cartesian differential category, and then introducethe notion
 of aCartesian differential fixpointcategory. For a morein-depthin
troduction to categorical interpretations of fixpoint operators, we
 refer the reader to [6, 35, 36, 57].
 Let us begin by explaining why for a Cartesian differential cat
egory, one must consider a parametrized fixpoint operator rather
 thansimplyabasic fixpointoperator.Recallthat inacategory with
 a terminal object, a point of an object is a map from the termi
nal object to , so : ⊤ → . Then a fixpoint operator [57,
 Definition 2.1] is an operator fix which for every endomorphsim
 : 
→ associates a point fix( ) : ⊤ → such
 ◦ fix( ) = fix( )
 meaning that fix( ) is a fixpoint of . Unfortunately, this kind of
 f
 ixpoint operator is in a certain sense incompatible with differen
tial combinators. This is because, in a Cartesian differential cate
gory, the derivative of a point : ⊤ → is always zero, D[ ] = 0.
 Therefore:
 L
 3.1. In a Cartesian differential category with a fixpoint
 operator, for every map : → ,D[fix( )] = 0.
 For a Cartesian differential category, one must instead consider
 parametrized fixpoint operators, which axiomatize the notion of
 f
 ixpoints for maps in context. For a map of type × → ,
 the parameter is viewed as representing the context of the term,
 then taking the parametrized fixpoint gives a map of type → .
 Parametrized fixpoint operators are axiomatized by two axioms:
 the fixpoint axiom and by naturality in the context argument.
 Definition 3.2. For a category C with finite products, a parame
trized fixpoint operator [57, Definition 2.2] is a family of func
tions fix indexed by pairs of objects in C,
 : 
f
 ix :C( × , )→C( , )
 × →
 f
 ix ( ) : →
 such that the following axioms hold:
 1. Parametrized fixpoint axiom: for all maps : × → :
 ◦ 1 ,fix ( ) =fix ( )
 2. Naturality axiom: for all maps : → and : × → :
 f
 ix ( )◦ =fix ( ◦( ×1 ))
 For a map : × → ,themapfix ( ) : → iscalledthe
 parametrized fixpoint of .
 Since we will be working with term calculus notation for Carte
sian differential categories, we shall use the following term calcu
lus notation to write the parametrized fixpoint operator:
 f
 ix ( )( ) = . ( , )
 where the variable is bound. The notation here is for an arbi
trary fixpoint operatorand not necessarily the least fixpoint opera
tor. For example, the parametrized fixpoint axiom, which says that
 3
LICS ’24, July 8–11, 2024, Tallinn, Estonia
 Zeinab Galal and Jean-Simon Pacaud Lemay
 f
 ix ( )isafixpointof in context , is expressed as:
 ( , . ( , )) = . ( , ).
 (4)
 Awell-known example of a category with a parametrized fixpoint
 operator is the category of Scott domains and Scott continuous
 functions [35, Example 7.1.2], whose parametrized fixpoint opera
tor is given by the standard Kleene iteration formula. In Section 6,
 wewilldiscuss parametrized fixpoint operatorsin Cartesian closed
 categories. In Section7,weprovide otherexamplesofparametrized
 f
 ixpoint operators. For more examples of parametrizedfixpoint op
erators, we refer the reader to see [35, 36].
 So how should a differential combinator and parametrized fix
point operator interact? In particular, what should the derivative
 of aparametrizedfixpointbe?Consider thefollowingcomputation
 using the parametrized fixpoint axiom and the chain rule:
 d . ( , )
 d
 = d ( , )
 ( ) · = d ( , . ( , ))
 d
 d( , ) ( , . ( , )) ·( , d . ( , )
 d
 From (4) and (5), we see that the pair
 (
 . ( , ), d . ( , )
 d
 ( ) · )
 ( ) ·
 ( ) · )
 (5)
 is a parametrizedfixpointofT( )( , , , )inthe and variables,
 that is:
 T( )( , . ( , ), , d . ( , )
 d
 = ( . ( , ), d . ( , )
 d
 ( ) · )
 ( ) · )
 Thus, our compatibility relation between a parametrized fixpoint
 operator and differential combinator is asking that the derivative
 of the parametrized fixpoint be equal to the second component of
 the nested fixpoint:
 ( , ).( ( , ), d ( , )
 d( , ) ( , ) ·( , )).
 In other words, the derivative of the parametrized fixpoint is the
 second componentoftheparametrizedfixpointofthetangent bun
dle, up to a rearranging of variables. To make certain that this in
deed makes sense, let us check that the types work correctly. So
 starting with a map : × → , taking its parametrized fix
point gives us fix ( ) : → , and so its derivative is of type
 D[fix ( )] : × → .Ontheotherhand,applyingthetangent
 bundle functor gives us T( ) : × × × → × .Forthisto
 be of the correct type to apply the parametrized fixpoint operator,
 we must swap the middle two terms. So let
 = 1, 3, 2, 4 : × × × → × × ×
 be the canonical isomorphism which swaps the middle two terms.
 Then pre-composing by gives us T( ) ◦ : × × × →
 × .Sowemay take its parametrized fixpoint to get a map of
 type fix ×
 × (T( ) ◦ ) : × → × .Lastly,wepost-compose
 by the second projection to finally get
 2 ◦fix ×
 × (T( )◦ ) : × → .
 Whenthesetwomapsareequal(Figure1),wecallthisthedifferential
f
 ixpoint rule and use it as our axiom for the definition of a Carte
sian differential fixpoint category.
 Definition 3.3. A parametrized fixpoint operator for a Cartesian
 differential category satisfies the differential-fixpoint rule if for
 every map : × → ,thefollowingequalityholds:
 D[fix ( )] = 2◦fixT
 T (T( )◦ )
 which in the term calculus is expressed as follows:
 d . ( , )
 d
 ( ) · = 2( ( , ).( ( , ), d ( , )
 (6)
 d( , ) ( , ) ·( , )))
 ACartesian differential fixpoint category is a Cartesian differ
ential category with a parametrized fixpoint operator which satis
f
 ies the differential-fixpoint rule.
 Wecanalsoaskhowthetangentbundlefunctorandtheparame
trized fixpointoperatorinteract. Fromthedifferential-fixpoint rule,
 by definition we have that:
 T(fix ( )) = fix ( )◦ 1, 2◦fixT
 T (T( )◦ )
 However, since the first component of fixT
 T (T( ) ◦ ) is not nec
essarily fix ( ) ◦ 1, we may not have that T(fix ( )) is equal
 to fixT
 T (T( ) ◦ ). When these two are equal, we call this the
 tangent-fixpoint rule.
 Definition 3.4. A parametrized fixpoint operator for a Cartesian
 differential category satisfies the tangent-fixpoint rule if for ev
ery map : × → ,thefollowingequalityholds:
 T(fix ( )) = fixT
 T (T( ) ◦ )
 which in the term calculus is expressed as follows:
 (
 . ( , ), d . ( , )
 d
 = ( , ).( ( , ), d ( , )
 ( ) · )
 d( , ) ( , ) ·( , ))
 (7)
 It means that the Cartesian functor (T, ) is a morphism of cate
gories with parametrized fixpoint operator (Definition 1.3 in [6] in
 the coCartesian setting).
 It is straightforward to see that the tangent-fixpoint rule implies
 the differential-fixpoint rule:
 L
 3.5. A parametrized fixpoint operator which satisfies the
 tangent-fixpoint rule also satisfies the differential-fixpoint rule.
 P
 . By definition, note that 2 ◦ T( ) = D( ). Therefore,
 post-composing both sides of (7) by the second projection 2 re
sults precisely in (6).
 □
 Therefore, a Cartesian differential category with a parametrized
 f
 ixpointoperatorwhichsatisfiesthetangent-fixpointruleisaCarte
sian differential fixpoint category. While we do not have an ex
ample of a Cartesian differential category with fixpoint operator
 wherethedifferential-fixpointruleholdsbutnotthetangent-fixpoint
 rule, there is no reason to assume that the converse is necessar
ily true. The reason for choosing the differential-fixpoint rule over
 4
Combining fixpoint and differentiation theory
 LICS ’24, July 8–11, 2024, Tallinn, Estonia
 × −→
 × −→
 f
 ix( )
 −−−−−→ 
× D[fix( )]
 −−−−−−−−→ 
the tangent-fixpoint rule as an axiom for Cartesian differential fix
point categories comes from the fact that it is the differential com
binator which is central in the definition of Cartesian differential
 categories rather than the tangent bundle functor. When asking
 for compatibility regarding a structure or property on a Cartesian
 differential category, it is more natural to ask what the deriva
tives of the necessary structuralorpropertymaps are. Thetangent
f
 ixpointruleismorenaturalwhenconsidering aCartesiandifferen
tial category instead as a tangent category [11], and willbethe cen
tral axiomfor thenotionofatangent categorywithaparametrized
 f
 ixpoint operator (which is future work, see Section 9). That being
 said, we show that for Conway fixpoint operators these two rules
 are equivalent.
 × × × T( )
 −−−−→ ×
 =
 × × × −→ × × × T( )
 × fix(T( )◦ ))
 −−−−−−−−−−−→ ×
 Figure 1: Differential-fixpoint rule
 −−−−→ ×
 2
 −−→ 
2. Bekič’s axiom: for all maps : × × → and : × ×
 → thefollowing equality holds:
 f
 ix × ( , )=
 , fix ( ◦(1 × ×1 )◦(Δ ×1 ))
 where := fix ( ◦((1 × ×fix × ( ))◦Δ × )) : → . In
 the term calculus, it is expressed as:
 ( , )( ( , , ), ( , , )) =
 (
 4 CONWAYOPERATORSANDDERIVATIVES
 Fixpoint operators are closely related to the notion of trace opera
tors. In particular,to give a trace operatorfor productscorresponds
 to providing aspecialkind ofparametrized fixpointoperatorcalled
 a Conway operator. In this section, we study the compatibility be
tween differential combinators and Conway operators (and trace
 operators), and introduce the notion of a traced Cartesian differen
tial category. For a more in-depth introduction to Conway opera
tors and trace operators, we refer the reader to [35, 36, 38].
 . ( , , . ( , , )), . ( , . ( , , . ( , , )), ))
 While the right-hand side of Bekič’s axiom’s expression seems
 more complex, it is extremely useful in practice. The idea is that
 Bekič’s axiom can be understood as a form of Gaussian elimina
tion: solving a system of nested fixpoint equations with multiple
 variables is reduced tosolving fixpointequationswith onevariable
 at a time. As such, Bekič’s axiom will be key in the proofs of this
 section. Most known fixpoint operators are Conway but it is pos
sible to construct fixpoint operators that satisfy all of the axioms
 but one of the axioms in Definitions 3.2 and 4.2 [23].
 Definition 4.1. For a category C with finite products, a trace
 operator [38, Section 2] is a family of functions Tr indexed by
 triples of objects in C,
 Tr , :C( × , × )→C( , ) : × → × 
Tr , ( ) : →
 which satisfy the axioms found in [35, Definition 2.1].
 Asmentioned, fora category withfinite products,giving atrace
 operator is equivalent to giving a special kind of parametrized fix
point operator called a Conway operator. We provide here the ax
iomatization of a Conway operator as found in [35], in terms of
 dinaturality and Bekič’s axiom. Equivalent axiomatizations can be
 found in [36, 57].
 Definition 4.2. For a category C with finite products, a Conway
 operator [35, Theorem 3.1] is a parametrized fixpoint operatorfix
 which also satisfies:
 1. Dinaturalityaxiom: for all maps : × → and : →
 f
 ix ( ◦ )= ◦fix ( ◦(1 × ))
 which in the term calculus is expressed as:
 .
 ( ( , )) = ( . ( , ( )))
 For a category with finite products, there is a bijective corre
spondencebetweentraceoperatorsandConwayoperators[35,The
orem 3.1]. To go from a Conway operator to a trace operator, first
 recall that by the universal property of the product, a map of type
 : 
× → × isatupleofmaps = 1, 2 where 1 =
 1 ◦ : × → and 2= 2◦ : × → .Thenstarting
 with a Conway operatorfix, define the trace operator Tr on a map
 = 1, 2 : × → × asthefollowingcomposite:
 Tr , ( )( ) = 1( 1 ,fix ( 2) )
 which in the term calculus is expressed as:
 Tr , ( 1, 2 )( ) = 1( , . 2( , )).
 On the other hand, starting from a trace operator Tr, define the
 Conway operator fix on a map : × → asfollows:
 f
 ix ( ) =Tr , ( , )
 As such, a category with finite products and a Conway operatoris
 a traced (Cartesian) monoidal category [38]. So we define:
 Definition 4.3. A traced Cartesian differential category is
 a Cartesian differential fixpoint category whose parametrized fix
point operator is a Conway operator.
 We wish to show that for a Conway operator, the differential
f
 ixpoint rule is equivalent to the tangent-fixpoint rule. To do so, it
 will be useful to consider yet another equivalent rule that even
 5
LICS ’24, July 8–11, 2024, Tallinn, Estonia
 Zeinab Galal and Jean-Simon Pacaud Lemay
 more precisely describes the derivative of the parametrized fix
point. Notice that when weappliedthechainruleto d . ( , )
 d
 ( )·
 above, we in fact showed that it was a parametrized fixed point
 for
 d ( , )
 d( , ) ( , . ( , )) ·( ,−).
 Wemaythen ask that it instead be the parametrized fixpoint.
 Definition 4.4. A parametrized fixpoint operator for a Cartesian
 differential categorysatisfies thestrongdifferential-fixpointrule
 if for every map : × → ,thefollowingequalityholds:
 D[fix ( )] =fix × (D[ ]◦ 1,fix ( )◦ 1, 2, 3 ) (8)
 which in the term calculus is expressed as follows:
 d . ( , )
 d
 ( ) · = . d ( , )
 d( , ) ( , . ( , )) ·( , )
 Weshouldexplainwhywechosethedifferential-fixpointruleas
 the axiom for Cartesian differential fixpoint categories instead of
 the strong differential-fixpoint rule. While the strong differential
f
 ixpoint rule only involves the differential combinator, notice that
 f
 ix ( ) appears on both sides of (8). Thus, while both the differ
ential combinator and the tangent bundle functor are used in the
 differential-fixpoint rule, it is slightly more natural. With all that
 said, we will show that for a Conway operator, thanks to Bekič’s
 axiom, all three of these rules are equivalent.
 P
 4.5. For a Conway operator fix of a Cartesian dif
ferential category, the following are equivalent:
 (i) fix satisfies the differential-fixpoint rule;
 (ii) fix satisfies the tangent-fixpoint rule;
 (iii) fix satisfies the strong differential-fixpoint rule.
 P
 . The key to this proof is Bekič’s axiom. Before we try to
 prove the equivalence of these three rules, let us first compute a
 useful identity using the term calculus. Assuming we have a Con
way operator (and not assuming any of the three rules), consider
 any map : × → .Tosimplify using Bekič’s axiom, let
 = ( , ), and define
 ℎ( , , ) = ( , ) and ( , , )= d ( , )
 d( , ) ( , ) ·( , ).
 Then using Bekič’s axiom, we compute that:
 ( , ).(ℎ( , , ), ( , , ))
 = ( .ℎ( , , . ( , , )), . ( , .ℎ( , , . ( , , )), ))
 So we have the equality:
 ( , ).( ( , ), d ( , )
 d( , ) ( , ) ·( , ))
 = ( . ( , ), . d ( , )
 d( , ) ( , . ( , )) ·( , ))
 (9)
 Using this identity, we can now prove the desired equivalence. We
 will prove ( ) ⇒ ( ) ⇒ ( ) ⇒ ( ). Now ( ) ⇒ ( ) is just
 Lemma3.5. For ( ) ⇒ ( ),using(9), wecaneasily expand outthe
 differential-fixpoint rule as:
 d . ( , )
 d
 ( ) ·
 = 2( ( , ).( ( , ), d ( , )
 d( , ) ( , ) ·( , )))
 = 2( . ( , ), . d ( , )
 d( , ) ( , . ( , )) ·( , ))
 = . d ( , )
 d( , ) ( , . ( , )) ·( , )
 So the strong differential-fixpoint rule holds. For ( ) ⇒ ( ), ap
plying the strong differential-fixpoint rule to (9) gives us that:
 ( , ).( ( , ), d ( , )
 d( , ) ( , ) ·( , ))
 = ( . ( , ), . d ( , )
 d( , ) ( , . ( , )) ·( , ))
 = ( , ).( ( , ), d . ( , )
 d
 ( ) · )
 So the tangent-fixpoint rule holds. So we conclude that for a Con
way operator, the three rules are equivalent.
 □
 Therefore, for a traced Cartesian differential category, its Con
way operator satisfies the differential-fixpoint rule, the tangent
f
 ixpoint rule, and the strong differential-fixpoint rule. Since a Con
wayoperator is equivalent to a trace operator, there is yet another
 equivalent rule involving the trace operator. Unfortunately, there
 does not appear to be a nice formula for describing the derivative
 of the trace. Instead, the trace operator behaves quite nicely with
 the tangent bundle functor. Now for a map : × → × ,we
 have that
 T(Tr , ( )) : × → × .
 On the other hand, first applying the tangent functor gives T ( ) :
 × × × → × × × .Beforewecantakeitstrace,wemust
 post- and pre-compose by to get ◦T( ) ◦ : × × × →
 × × × .Thentakingthetracewefinally get amap of type
 Tr ×
 × , × 
( ◦T( )◦ ) : × → × .
 Asking that these maps be equal is equivalent to the other rules.
 P
 4.6. A Cartesian differential category with a Con
way operator is a traced Cartesian differential category if and only
 if for the induced trace operator Tr and for every map : × →
 × ,thefollowing equality holds:
 T(Tr , ( )) = TrT
 T ,T ( ◦T( ) ◦ )
 (10)
 whichmeansthat ( , ) isatracedmonoidalfunctor ([38]) where the
 monoidal product is cartesian.
 P
 . For the ⇒ direction, let = 1, 2 . Let us first put
 ◦T( )◦ intoapairsothatwecantakeits trace. By [46, Lemma
 2.9(iv)], recall that T(ℎ, ) = ◦ T(ℎ),T( ) . From this it is
 straightforward to compute that
 ◦ T( )◦ = T( 1)◦ ,T( 2)◦ .
 6
Combining fixpoint and differentiation theory
 LICS ’24, July 8–11, 2024, Tallinn, Estonia
 Then, using the tangent-fixpoint rule, we compute that:
 TrT
 T ,T ( ◦T( ) ◦ ) = TrT
 T ,T (T( 1) ◦ ,T( 2) ◦ )
 = T( 1) ◦ ◦ 1 × ,fixT
 T (T( 2)◦ )
 = T( 1) ◦ ◦ T(1 ),T(fix ( 2))
 = T( 1) ◦T( 1 ,fix ( 1) )
 = T( 1 ◦ 1 ,fix ( 2) )
 = T(Tr , ( 1, 2 )) = T(Tr , ( ))
 So (10) holds as desired. For the ⇐ direction, we will show that
 (10) implies the tangent-fixpoint rule. So for : × → , we
 get:
 T(fix ( )) = T(Tr , ( , ))
 = TrT
 T ,T ( ◦T( , )◦ )
 = TrT
 T ,T (T( ),T( ) ◦ )
 = TrT
 T ,T (T( ) ◦ ,T( )◦ )
 = fixT
 T (T( ) ◦ )
 So the tangent-fixpoint rule holds. Then by Proposition 4.5, it fol
lows that we have a traced Cartesian differential category.
 5 FIXPOINTSANDLINEARITY
 Animportantnotion ina Cartesian differential category is the con
cept of linearity. In particular, linear maps are those whose deriva
tives are simply themselves evaluated in thesecond argument. The
 term linear is justified since in many models, linear in the Carte
sian differential category sense corresponds to being linear in the
 classical algebraic sense.
 Definition 5.1. In a Cartesian differential category, a map 
→ is linear [8, Definition 2.1.1] if D[ ] = ◦ 2, which
 in the term calculus is expressed as:
 d ( )
 d ( )· = ( )
 For a Cartesian differential category C, its subcategory of linear
 maps will be denoted as LIN[C].
 Properties of linear maps can be found in [14, Lemma 2.6], such
 as the fact that they are closed under composition, sum, and prod
uct structure. In particular, every linear map is also additive [14,
 Lemma 2.6.(i)]. From this, it follows that LIN[C] has finite biprod
ucts [8, Corollary 2.2.3]. It is important to note that although linear
 maps and additive maps often coincide, in an arbitrary Cartesian
 differential category not every additive map is necessarily linear.
 Wenowconsider how linearity interacts with parametrized fix
point operators. The first natural thing to ask is that the linear
 maps bethe strict maps. Indeed, an important concept for fixpoint
 operators is the notion of uniformity, which is used to character
ize fixpoint operators uniquely without relying on order-theoretic
 arguments. The uniformity axiom is then relative to a subcategory
 of strict maps.
 Definition5.2. InacategoryCwithfiniteproductsandequipped
 with a parametrized fixpoint operator fix, a map ℎ : 
strict [36, Definition 4.4] if for every 
: 
→ is
 × → and :
 × → , iftheequality on the left holds, then the equality on
 the right holds:
 ℎ ◦ = ◦(1 ×ℎ) ⇒ ℎ◦fix ( )=fix ( ).
 which in the term calculus is expressed as:
 ℎ ( ( , )) = ( ,ℎ( )) ⇒ ℎ( . ( , ))= . ( , ).
 Definition 5.3. Let C and S be categories with finite products,
 and J : S → C be a bijective-on-objects and strict product pre
serving functor. Then a parametrized fixpoint operator on C is J
uniform [57, Definition 2.8] if for every map ℎ in S, J(ℎ) is strict
 in C.
 Therefore, S is understood as a suitable subcategory of strict
 mapsofC.Forcategoriesofdomain-like structures,thestrict maps
 are usually the ones which preserve bottom elements. In categor
ical models of Linear Logic with fixpoints, the strict maps of in
terests are the maps of the base monoidal category. For Cartesian
 differential categories, it is natural to ask that the linear maps be
 strict. This can also be expressed in terms of the uniformity axiom
 by considering the canonical inclusion functor J : LIN[C] → C.
 Therefore, we may ask that the parametrized fixpoint operator be
 J-uniform, which we call being linearly uniform. Explicitly:
 □
 :
 Definition 5.4. A parametrized fixpoint operator on a Cartesian
 differential category is linearly uniform if every linear map is
 strict.
 In Section 7.5, we will use the uniformity axiom to make use
 of a theorem by Plotkin and Simpson [57] so that we can prove
 the necessary compatibility results between differential combina
tors and parametrized fixpoint operator in coKleisli categories of
 comonads satisfying some conditions on bifree algebras.
 Linearity also behaves quite nicely with Conway operators. An
 importantfirstobservationisthatforaConwayoperator,theparametrized
 f
 ixpoint of a linear map is again linear.
 L
 : 
P
 5.5. In a traced Cartesian differential category, if a map
 × → islinear,thenfix ( ) : → isalsolinear.
 . Weprove this using the term calculus. Using the strong
 differential-fixpoint rule and the fact that is linear, we compute:
 d . ( , )
 d
 ( )· = . d ( , )
 d( , ) ( , . ( , ))·( , ) = . ( , )
 and we conclude that fix ( ) is also linear.
 □
 Therefore, if C is a traced Cartesian differential category with
 Conwayoperatorfix,thenfixisalsoaConwayoperatoronLIN[C].
 This in turn means that LIN[C] also inherits the trace operator.
 However, for a category with finite biproducts, to give a trace op
erator is equivalent to giving a repetition operator [56, Proposition
 6.11]. So for a traced Cartesian differential category, its Conway
 operator induces a repetition operator on its subcategory of linear
 maps.
 Definition 5.6. For a category C with finite biproducts, a repeti
tion operator [56, Proposition 6.11] is a family of functions (−)∗
 indexed by objects in C,
 : 
(−)∗ : C( , ) →C( , )
 →
 ∗ : →
 7
LICS ’24, July 8–11, 2024, Tallinn, Estonia
 Zeinab Galal and Jean-Simon Pacaud Lemay
 such that the following equalities hold for all , : → :
 1. Fixpoint axiom: ∗ = 1 + ◦ ∗
 2. Addition axiom: ( + )∗ = ( ∗ ◦ )∗ ◦ ∗
 3. Dinaturality axiom: ( ◦ )∗ ◦ = ◦ ( ◦ )∗
 C
 5.7. For a traced Cartesian differential category C,
 LIN[C] has a repetition operator defined as ∗ = fix ( 1 + ◦ 2),
 which in the term calculus is expressed as follows:
 ∗
 ( ) = .( + ( )).
 (11)
 On the other hand, we can also consider linearity in a certain
 argument.Forthestoryofthispaper,weareparticularlyinterested
 in maps which are linear in their second argument.
 Definition 5.8. In a Cartesian differential category, a map : ×
 → islinearin its second argument [14, Definition 4.5] (or
 simply linear in when there is no confusion) if
 D[ ] ◦ , ,0, = ◦ ,
 which in the term calculus is expressed as:
 d ( , )
 d( , ) ( , ) ·(0, ) = ( , ).
 In particular, [CD.6] is precisely the statement that for any map
 : 
→ , its derivative D[ ] : × → is linear in its
 second argument .Similarly,foramap : 1 × ... ×
 → ,we
 can also define what it means to be linear in its-th argument
 [32, Definition 2.6]. Now, taking the parametrized fixpoint in a
 linear argument results in zero. This makes sense since a map :
 × → whichislinearin isalsoadditivein .In particular,
 ( ,0) = 0. Thus 0 is a parametrized fixpoint. It is important to
 note that : × → beinglinear in isquitedifferent from
 being linear. Thus the following lemma does not clash with the
 previous one.
 L
 : 
P
 5.9. In a traced Cartesian differential category, if a map
 × → islinearin thenfix ( ) =0.
 . We use the term calculus again. The key axiom here is
 [CD.2], which recall says that evaluating a derivative at zero in its
 second argument is zero. Therefore, using the strong differential
f
 ixpoint rule, we compute that . ( , ) is equal to
 .
 d ( , )
 d( , ) ( , . ( , )) ·(0, ) = d . ( , )
 d
 6 CLOSEDSETTING
 ( ) · 0 = 0 □
 Let us now consider the case when the underlying category is
 Cartesian closed. This is an important case since Cartesian closed
 differential categories (alsosometimescalleddifferential-categories)
 provide the categorical semantics of the differential-calculus. For
 a more in-depth introduction to Cartesian closed differential cate
gories and the differential-calculus, we refer the reader to [9, 10,
 14, 20, 50].
 For a Cartesian closed category, we denote the internal hom by
 ⇒ ,theevaluationmapbyeval , : ( ⇒ )× → ,andfor
 a map : × → ,wedenoteitscurryingby ( ) : → ⇒ ,
 which recall is the unique map such that eval , ◦( ( ) × 1 ) = .
 Asexplained in [10, Lemma 4.10],there are twoequivalent ways of
 expressing compatibility between the closed structure and the dif
ferential combinator: one in terms of the Curry operator and one
 in terms of the evaluation map. In terms of the latter, a Cartesian
 closed differential category [10, Section 4.6] is a Cartesian dif
ferential category which is Cartesian closed such that every evalu
ation map is linear in their internal hom argument (which by our
 convention means they are linear in their first argument). Another
 equivalent way of axiomatizing Cartesian closed differential cate
gories is via an axiom which says that the derivative of a Curry is
 the Curryofthepartial derivative [14, Definition 6.2]. Every model
 of the differential-calculus induces a Cartesian closed differential
 category [10, Theorem 4.3],and conversely, every Cartesian closed
 differential category induces a model of the differential-calculus
 [9, Theorem 4.12].
 On the other hand, in a Cartesian closed category, fixpoint op
erators can be internalized with the notion of fixpoint combinator:
 Definition 6.1. For a Cartesian closed category C, a fixpoint
 combinator is a family of maps {Y : ⇒ → } indexed
 by the objects of C, satisfying the following axiom for all :
 Y =eval◦ 1⇒ ,Y .
 The equivalence between parametrized fixpoint operators and
 f
 ixpoint combinators in the Cartesian closed setting works as fol
lows: starting with a parametrized fixpoint operator fix, for every
 object , define the map Y : ⇒ → astheparametrized
 f
 ixpoint of the evaluation map eval , : ( ⇒ ) × → ,that
 is,
 Y :=fix ⇒ (eval , ).
 For the other direction, for every : × → ,its parametrized
 f
 ixpoint can be expressed in terms of Y as:
 f
 ix ( ) =Y ◦ ( ).
 (12)
 Fromthisidentity, we can express the differential-fixpoint ruleand
 the others in terms of Y. In particular, we highlight that (14) is the
 analogue of the equation Ehrhard computes in the coKleisli cate
goryofacoherent differential categorywhichisScott[19,Theorem
 5.29].
 P
 6.2. In a Cartesian closed differential category with
 a parametrized fixpoint fix,
 (i) fix satisfies the differential-fixpoint rule if and only if the fol
lowing equality holds:
 D[Y] = 2◦YT ◦ T(eval , )◦ (13)
 (ii) fix satisfies the tangent-fixpointrule if and only if the following
 equality holds:
 T(Y) =YT ◦ T(eval , )◦ (14)
 (iii) fix satisfies the strong differential-fixpointrule if and only if the
 following equality holds:
 D[Y]=fix ◦ (D[eval] ◦ 1,Y ◦ 1, 2, 3 ) (15)
 P
 . Let us prove (i). For the ⇒ direction, by setting =
 eval , in (6), and then using (12), we get that:
 D[Y] =D[fix ⇒ (eval , )]
 = 2◦fixT
 T⇒T T(eval , )◦
 = 2◦YT ◦ T(eval , )◦
 8
Combining fixpoint and differentiation theory
 LICS ’24, July 8–11, 2024, Tallinn, Estonia
 For the ⇐ direction, first recall that ( ) ◦ℎ = ( ◦ (ℎ × 1)). We
 also have thatTisastrongCartesian monoidalfunctor[46,Lemma
 2.9(v)]
 ◦ (T( ) ×T( )) = T( × )◦ .
 From these identities, it follows that
 (T( ) ◦ ) ◦T( ) = (T( ◦( ×1))◦ ).
 Using this and (12), we compute that:
 D[fix ( )] =D[Y ◦ ( )]
 = D[Y]◦T( ( ))
 = 2◦YT ◦ T(eval , )◦ ◦T( ( ))
 = 2◦YT ◦ T eval , ◦( ( )×1) ◦
 = 2◦YT ◦ (T( )◦ )
 = 2◦fixT
 T (T( )◦ )
 So the differential-fixpoint rule holds as desired. One can prove (ii)
 and (iii) using similar computations.
 7 EXAMPLES
 7.1 Categories with finite biproducts
 □
 Any category C with finite biproducts is a Cartesian differential
 categorywherethedifferential combinatorisgiven bypre-composition
 with the second projection: D[ ] = ◦ 2 [14, Example 2.10]. If C
 comes equipped with a parametrized fixpoint operator such that
 the projection maps are strict (Definition 5.2), then the differential
f
 ixpoint rule holds, and so C is a Cartesian differential fixpoint cat
egory. On the other hand, any ConwayoperatoronCcaneasily be
 seen to satisfy the strong differential-fixpoint rule. Therefore, any
 category with finite biproducts which is equipped with a Conway
 operator (or equivalently a trace operator or a repetition operator)
 is a traced Cartesian differential category.
 7.2 Categories of (weighted) relations
 Thecategory Rel whoseobjects are sets and morphismsare binary
 relations between them is one of the most basic models of linear
 logic, with many other models arising as refinements of it. The
 operation mapping a set to the set of finite multisets over ,
 !
 := { : →N| hasfinitesupport}
 can be equippedwith a comonad structureon Rel. The induced co
Kleisli category Rel! is a Cartesian differential category [9, Section
 5.1] and also has a canonical Conway operator [34, Proposition 3].
 Binary relations over sets can be generalized to weighted rela
tions over a continuous semi-ring [43]. A continuous semi-ring is a
 semi-ring (S, ≤,0,+, 1, ·) which is also a cpo, whose zero element 0
 is the bottom, and both the addition and multiplication are contin
uous. For a continuous semi-ring S, the category RelS has objects
 sets and maps from to are functions : × → S. The
 composite of : × → Sand : × → Sisthefunction
 ◦ : × →Sgivenby
 ( ◦ )( , ) =
 ( , ) · ( , )
 ∈
 where the sum on the right is well defined by the cpo structure of
 S. Thefinite multiset construction also induces a comonad on RelS
 and the corresponding co-Kleisli category is a Cartesian differen
tial category. The derivative of a weighted relation : ! × → S
 is D[ ] : ! ×! × →S:
 ( , , ) → ( + , ) if =[ ]forsome ∈
 0
 otherwise
 The co-Kleisli category RelS
 ! also has a Conway operator obtained
 via Kleene iteration. For a weighted relation : ! × ! × →
 S, its least fixpoint is fix : ! × → S inductively defined as
 f
 ix := ∈Nfix ,wherefor ∈! ,wedefine
 f
 ix0 ( ) =0 fix +1 ( )= ( ,fix ( ))
 with composition in this expression being taken in the co-Kleisli
 category. The fixpoint operator defined above satisfies the univer
sal property of being the unique parametrized fixpoint operator
 that is uniform with respect to linear morphisms, which here cor
respond precisely to the morphisms of RelS. This uniform Con
way operator and the differential combinator verify the (strong)
 differential-fixpoint rule, and therefore RelS
 ! is a traced Cartesian
 closed differential category, and moreover the Conway operator is
 linearly uniform. This is truebecause thisexample is aninstance of
 a more general statement concerning fixpoint operators obtained
 via bifree algebras of comonads [57]– which we discuss in Section
 7.5 below. When taking the Boolean semi-ring B = {t,f}, we get
 back the relational model, RelB
 ! = Rel!.
 7.3 Formal Power Series
 For a continuous semi-ring S, we may consider the full subcate
gory of RelS
 ! whose objects are the finite sets. This subcategory of
 f
 inite sets is again a traced Cartesian differential category and is
 equivalent to the Lawvere theory of formal power series over S.
 Weobtain atraced Cartesian differential category of formal power
 series over any continuous semi-ring. Even though this is a sub
category of the previous example, since formal power series over
 continuous semi-rings are used in many areas of computerscience,
 it is worthwhile to spell out some details.
 For a continuous semi-ring S, let PWS be the category whose
 objects are the natural numbers ∈ Nand whereamap : →
 is an-tuple of power series in-variables:
 = ( 1( 1,..., ),..., ( 1,..., )).
 Since coefficients areover acontinuoussemi-ring, thecomposition
 of power series is well-defined. Now for ∈ N, define the set as
 {1, . . ., }.ThenthefullsubcategoryRelS
 ! withobjectsrestrictedto
 f
 inite sets of the form is isomorphic to PWS. Indeed, a weighted
 relation : ! × →SinRelS
 ! corresponds to the tuple of power
 series = ( 1( ),..., ( )) where for 1 ≤ ≤ :
 ( 1,. . ., ) =
 ( 1,. . ., , ) · 1
 1 ...
 ( 1,...,
 ) ∈S
 Therefore, it follows that PWS is a traced Cartesian differential
 category. In particular, the least fixpoint in the weighted relational
 model corresponds to the least fixpoint for power series over con
tinuous semi-rings first described in [55]. On the other hand, for
 a map : → 1,which is just a power series in variables
 9
LICS’24,July8–11,2024,Tallinn,Estonia ZeinabGalalandJean-SimonPacaudLemay
 ( 1,..., ),applyingthedifferentialcombinatortoitresultsin
 thesumofitspartialderivatives:
 D[ ]( , )=
 =1
 ( ) ·
 ThiscorrespondstothenotationD | ( ) inpaperssuchas[25].
 By[CD.4],foratuple =( 1( ),..., ( )), itsderivativeis
 D[ ]=(D[ 1]( , ),...,D[ ]( , )).
 7.4 Quantale-enrichedprofunctors
 Anotherpossiblegeneralizationofthenotionofrelationsisgiven
 bythenotionofquantaleenrichedprofunctors[39,61].Aquantale
 Qisacompletelatticethatissymmetricmonoidalclosedsuchthat
 thetensordistributesoverarbitrarysuprema.Quantalesareidem
potentsemi-ringswithjoin∨asadditivestructureandtensor⊗as
 multiplicativestructure. Insteadofhavingsetsasobjects,wecan
 considerricherstructuresgivenbythenotionofquantaleenriched
 categories:foraquantaleQ,aQ-categoryAconsistsofasetofob
jectsOb(A)andforallobjects , anobjectA( , ) inQtogether
 withcompositionA( , )⊗A( , )≤A( , )andidentityinequal
ities1≤A( , ) inQ.Forexample,aQ-categoryfortheLawvere
 quantaleR+∪{∞}correspondstothenotionofgeneralizedmetric
 space[44].
 ForQ-categoriesA,B,aQ-profunctor :A− −→B(alsocalled
 distributorsorbimodules)fromAtoBisafunction :Ob(A)×
 Ob(B)→Qwithbiactioninequalitiesforall , ′∈Ob(A), , ′ ∈
 Ob(B):
 A( , ′)⊗ ( , )⊗B( ′ , )≤ ( ′ , ′).
 ThereisananalogousfreeexponentialconstructionmappingaQ
categoryAtotheQ-category!Awhosesetofobjects is theset
 offinitesequences = 1,..., ofobjectsofAandfor =
 1,..., and = 1,..., ,thehom-objectisgivenby
 !A( , )=
   
   
 : 1≤≤
 A( , ) if =
 ⊥ otherwise
 Theinducedco-Kleislicategoryhasadifferentialcombinator,where
 thederivativeofaQ-profunctor : !A− −→Bgeneralizestheonein
 thediscretecaseforweightedrelations:
 ( 1,..., , ′
 1,..., ′ , )→ ( 1,..., , ′
 1 , ) if =1
 ⊥ otherwise
 Theinducedco-Kleislicategoryalsohasafixpointoperatormap
pingaQ-profunctor : !A⊗!X− −→Xtofix : !A− −→Xobtained
 byKleeneiterationas∨∈Nfix withfix0 =⊥and
 fix+1 ( , )=
 = 1,..., ∈!X
 0,..., ∈!A
 ( 0, , )⊗
 1≤≤
 fix ( , )
 SowegetaCartesiandifferentialfixpointcategory,whichisagain
 aninstanceofthebifreealgebrasstoryinthenextsection.
 7.5 Fixpointsfrombifreealgebras
 Recall thatforanendofunctor :C→C,abifree-algebra[57,
 Section5]isaninitial-algebra( , : → ) suchthat the
 inverseof isafinal-coalgebra ( , −1 : → ).Aresult
 bySimpsonandPlotkinallowsonetoconstructaparametrized
 fixpointoperatorintheco-Kleislicategoryofacomonadwhose
 underlyingendofunctorhassuitablebifreealgebras[57].Wepro
ceedtoshowthatiftheco-KleislicategoryisalsoaCartesiandif
ferentialcategory, thenit isalsoaCartesiandifferentialfixpoint
 category.
 T 7.1. [57,Proposition6.5&Theorem3]LetCbeacat
egorywithfiniteproductsequippedwithacomonad ( , , ). Let
 J :C→C bethefreefunctorinducedbythecomonadicadjunction.
 (1) If forallobjects inC, theendofunctors ( ×−)havea
 bifreealgebra,thenC hasauniqueuniform(withrespectto
 J)parametrizedfixpointoperator.
 (2) If forallobjects inC, theendofunctors ( × ( ×−))
 and ( ×−×−)haveabifreealgebra,thenC hasaunique
 uniform(withrespecttoJ)Conwayfixpointoperator.
 Wegiveasketchofhowtheparametrizedfixpointoperatoris
 constructedviabifreealgebras.Assumethatforall inC,theend
ofunctor ( ×−)hasabifreealgebra : ( ×Φ )→Φ which
 wecanviewasamap ×Φ →Φ intheco-KleislicategoryC .
 Asexplainedin[57,Section6],forall inC,thereexistsaunique
 map : →Φ inC suchthat
 ◦(1 × )◦Δ = .
 Moreover,forall : × → inC ,thereexistsauniquemap
 :Φ → inCsuchthat
 J( )◦ = ◦J(1 × ).
 Then,foramap : × → inC , itsparametrizedfixpointis
 definedasthefollowingcomposite:
 fix ( ) := −−→Φ J( ) −−−−−→ .
 NowsupposethatC isalsoaCartesiandifferentialcategory,such
 that forallmaps inC, J( ) islinear inC .Wewillnowshow
 thattheparametrizedfixpointoperatordefinedabovesatisfiesthe
 tangent-fixpointrule.
 Todoso, thekeytothisproofisthatif islinear, thenT( )=
 × [46, Lemma2.(iii)]. Inparticular, forall inC,wehave
 thatT(J( )) = J( )×J( ).Now, tohelpwithnotation, define
 :Φ × →Φ ×Φ as := T( )◦ , that is, theuniquemap
 suchthat
 J( )◦ × =T( )◦ ◦(1 × ×J( )).
 Then, for : × → ∈C ,wecomputethat (noteJalso
 preservesproductsstrictly):
 T( )◦ ◦(1 × ×J(( × )◦ ))
 =T( )◦ ◦(1 ×1 ×J( )×J( ))◦(1 × ×J( ))
 =T( )◦(1 ×J( )×1 ×J( ))◦ ◦(1 × ×J( ))
 =T( )◦(J(1 × )×J(1 × ))◦ ◦(1 × ×J( ))
 =T( )◦T(J(1 × ))◦ ◦(1 × ×J( ))
 =T(J( ))◦T( )◦ ◦(1 × ×J( ))
 =(J( )×J( ))◦J( )◦ ×
 =J(( × )◦ )◦ ×
 10
Combining fixpoint and differentiation theory
 LICS ’24, July 8–11, 2024, Tallinn, Estonia
 Byuniqueness, it follows that T( )◦ = ( × )◦ .Dually,since
 −1
 × is afinal coalgebra, there is a unique map : Φ × Φ →
 Φ × inCsuchthat
 J( ) ◦T( )◦ = × ◦(1 × ×J( ))
 and also such that J( ) ◦ T( ) = × , which by uniqueness im
plies that ◦ = 1Φ ×Φ . Using these identities, we can finally
 compute that:
 f
 ixT
 T (T( ) ◦ ) = J( T( )◦ ) ◦ × 
= J(( × )◦ )◦J( )◦T( )
 = (J( )×J( )) ◦J( )◦J( )◦T( )
 = (J( )×J( )) ◦T( )
 = T(J( ) ◦ ) =T(fix ( ))
 We conclude that the tangent-fixpoint rule holds and that there
fore C isaCartesian differential fixpoint category. This construc
tion subsumes boththe exampleofweighted relations (Section 7.2)
 and the example of quantale profunctors (Section 7.4) since the
 free exponential comonadin bothexamples has therequiredbifree
 algebras obtained via enrichment arguments. Both categories of
 weighted relations and Q-profunctors are cpo-algebraically com
pact[28–30,58]:itmeans thatevery endofunctorthatiscpo-enriched
 has a bifree algebra and one can verify that the endofunctor of the
 free exponential comonad in both cases is a cpo-enriched functor.
 For the weighted relational model, Laird computes these bifree al
gebras explicitly using nested finite multisets in [42].
 7.6 Fixpoint operators from fixpoint objects
 Astandard constructionto obtain fixpointoperatorsin a Cartesian
 closed category is via cpo-enrichment. A category C is-cpo en
riched if each hom-set C is an-cpo and composition C( , ) ×
 C( , ) →C( , ) isScott-continuous. If in addition, the evalua
tion and pairing are monotonous, each hom-set C( , ) has a bot
tomelement 0andforall ,wehave0◦ = 0andeval◦0, = 0,it
 was shown byBerry[4] that thecategory has a least parametrized
 f
 ixpoint operator.For each ,wecan constructafixpointcombina
tor morphism Y : ⇒ → asthesupremumY = Y
 where
 Y0=0 and Y+1=eval , ◦ 1⇒ ,Y .
 Explicitly, the parametrized fixpoint operator is defined as in (12).
 Now, if C is a Cartesian differential category and the cpo bottom
 elements 0 coincide with the zero of the additive structure, then
 we can show that this parametrized fixpoint operator satisfies the
 tangent-fixpoint rule, or equivalently Y satisfies (14). We omit
 the proof as it follows the same reasoning as done by Ehrhard in
 [20, Theorem 5.29] in coherent differential categories with a Scott
 f
 ixpoint [19]. The known models of (coherent) PCF typically have
 reflexive objects (retractions ( ⇒ ) ⊳ ) which provide models
 of untyped-calculs and are particular cases of fixpoint objects.
 WeprovebelowthatthetangentfixpointaxiomholdsforCartesian
 closed categories where fixpoint objects induce a fixpoint operator
 by Lawvere’s theorem [45].
 T
 7.2. [45, Section 1] In a Cartesian closed category C, if
 there is a morphism : → ⇒ suchthatfor every morphism
 : 
→ ⇒ , thereexists a morphism : → suchthat
 can be factored as = ◦ , then every morphism : × → 
has a parametrized fixpoint fix ( ) : → .
 The construction works as follows: for : × → ,we first
 define the map : × → as
 := ◦(1 ×eval) ◦ (1 × ×1 )◦(1 ×Δ ).
 Now let : → beamapfactoring ( ) : → ⇒ as
 ( ) = ◦ .Thentheparametrized fixpoint is defined as follows:
 f
 ix ( ) = −→ Δ −−−→ × ×
 1
 −−−−−→ ⇒ × eval ,
 −−−−−−→ 
Toprovethedifferential-fixpointrule,weneedtoimposestronger
 conditions. So we assume that for every object , there is a map
 : 
→ ⇒ suchthat forevery map : → ⇒ ,
 there is a unique morphism : → such that = ◦ .
 This stronger assumption holds automatically for the retraction
 case with reflexive objects. We also need the choice of fixpoint ob
jects to be compatiblewith the differential structure, so we assume
 that for all ,
 T =( 1⇒1T )◦T( ).
 Moreover, since 1 ◦ 1 ,0 = 1 ,wehave that
 T( ) =( 1 ,0⇒1T )◦ T .
 Now for a map : × → ,define :T ×T →T asthe
 following composite:
 := T ◦ ◦(1T ×eval)◦ (1T × T ×1T )◦(1T ×ΔT ).
 Nowlet : T →T betheuniquemapsuchthat T ◦ = ( ).
 Note that T(Δ ) = ◦ΔT soweobtain:
 f
 ixT
 T (T( ) ◦ ) = evalT ,T ◦ ( T ×1T ) ◦ΔT ◦
 Ontheotherhand, towork outT(fix ( )), weusesomeidentities
 that hold in a Cartesian closed tangent category [31, Section 5.2],
 so in particular hold in a Cartesian closed differential category. We
 f
 irst have that
 T(eval , ) ◦ = evalT ,T ◦ (( 1 ⇒1T )×1T )
 which allows us to first compute that T( ) ◦ is equal to
 T( ◦(1 ×eval) ◦ (1 × ×1 )◦(1 ×Δ ))◦
 = T( ) ◦ ◦(1T ×eval)◦ (1T × T ×1T )◦(1T ×ΔT ) =
 Therefore, is the unique morphism suchthat T ◦ = (T( )◦
 ). Moreover, for a map : × → ,wehavethat
 T( ( )) = ( 1 ,0⇒1T )◦ (T( )◦ )
 Wethen compute that:
 T ◦T( )= ( 1 ⇒1T )◦T( )◦T( )
 = ( 1 ⇒1T )◦T( ( ))
 = ( 1 ⇒1T )◦( 1 ,0⇒1T )◦ (T( )◦ )
 = (T( )◦ )
 11
LICS ’24, July 8–11, 2024, Tallinn, Estonia
 Zeinab Galal and Jean-Simon Pacaud Lemay
 By uniqueness, it then follows that T( ) = , so we may finally
 compute that:
 T(fix ( )) = T(eval) ◦T( ×1 )◦T(Δ )◦T( )
 = T(eval) ◦ T( ×1 ) ◦ ◦ΔT( ) ◦
 = T(eval) ◦ ◦ (T( ) ×1T )◦ΔT ◦
 = eval ◦ (( 1 ⇒ 1T )×1T )◦(T( )×1T )◦ΔT ◦
 = eval ◦ ( T ×1T )◦ΔT ◦
 = fix (T( )◦ )
 So the tangent-fixpoint rule holds as desired. Note that the unique
ness requirement can be weakened if we assume that the factoring
 morphisms for 
and T( ) are chosen in a uniform way.
 8 NEWTON-RAPHSONITERATIONSCHEME
 Newton-Raphson iteration has been extended to Kleene algebras
 or more generally power series over semi-rings [25, 26, 37, 41]
 where systems of equations in this setting can represent context
free grammars, data-flow equations, authorization problems, dat
alog queries etc. Newton iteration always converges for power
 series over-continuous semi-rings and if we restrict to idem
potent semi-rings, then it was shown to converge after a finite
 number of steps [25]. Similar ideas were also developed for enu
merative combinatorics to compute efficiently large combinatorial
 structures that are defined via fixpointequations[16,52,53].In the
 combinatorial setting, the series always have positive coefficients
 corresponding to the number of structures of a given size and it
 also leads to a Newton iteration scheme that is always convergent.
 Since combinatorial species are first order terms of a Cartesian
 differential fixpoint (bi)category [27] and power series over a con
tinuoussemi-ring areasubcategoryoftheweightedrelationalmodel,
 weweremotivatedtodevelop ageneral Newton-Raphson approxi
mation scheme for Cartesian differential fixpoint categories which
 we present below. While the following construction works for the
 more general setting of Cartesian differential categories, we chose
 to present it in the Cartesian closed framework to emphasize the
 viewpoint of optimization procedures as higher order operators.
 8.1 Newtonapproximants
 WestartbyrecallingthestandardNewton-Raphsoniterationmethod
 to findorapproximatefixpointsforreal-valued functions. Fora dif
ferentiable function : R → R verifying ′( ) ≠ 1 for all ∈ R,
 we consider a sequence of Newton approximants { } ∈ with
 0 ∈ Ran initial chosen value and for each , +1 := N( )( )
 where N( ) : R →Risdefinedas
 N( ) : → + 1
 1 − ′( ) ·( ( )− ).
 The sequence { } ∈ maynotconverge or may not converge to
 a fixpoint of but under suitable conditions on , it converges to
 a fixpoint and the convergence rateis quadratic. Note first that the
 quotient
 1
 1− ′( ) = 1+ ′( ) + ( ′( ))2 +... is a solution of the
 f
 ixpoint equation
 1
 1 − ′( ) =1+ ′( )· 1
 1 − ′( )
 whichcorrespondsexactlytotherepetitionoperatorfixpointequa
tion 5.6. The general idea of Newton-Raphson iteration in our set
ting is to accelerate the computation of the non-linear fixpoint
 operator in the Cartesian (closed) category combining derivatives
 withtheinducedlinear repetitionoperatorinthecategoryLIN[C] (11).
 While weremain in a setting where everything is non-negative,
 we want to perform truncated subtractions: for example, in the
 semiring ofnon-negative extendedrealsR∞
 ≥0 = ([0,∞], ≥,×,1,+,0),
 the truncatedsubtraction operation ⊖ correspondstomin{0, −
 }. For a general Cartesian closed differential fixpoint category C,
 weknowthatitisenriched over commutativemonoidsand wecan
 equipeachhom-setC( , )withthenaturalorderrelationinduced
 by addition:
 ∀ , : → , ≤ :⇔ ∃ℎ: → , +ℎ= .
 Weassumethatforevery inC( , ),themaps +(−) and (−)+
 have right adjoints for the ≥ ordering. Explicitly, for each , ,ℎ in
 C( , ), there exist maps ⊖ and ⊖ℎ suchthat
 ℎ ≥ ⊖ ⇔ +ℎ≥ ⇔ ≥ ⊖ℎ.
 It implies the following identities for all , ,ℎ in C( , ) and for
 every additive map : → :
 ⊖ ( +ℎ) = ( ⊖ )⊖ℎ
 (16)
 ≥ ( + )⊖
 (17)
 ≤ ( ⊖ )+ andif ≥ , then =( ⊖ )+ (18)
 ⊖ ≤( ⊖ℎ)+(ℎ⊖ )
 (19)
 ◦ ( ⊖ )≥( ◦ )⊖( ◦ )
 (20)
 Since for : → inC,D[ ] : × → islinearinitssecond
 argument, it implies that for any point ⊤ −→ ,
 D [ ] :=D[ ]◦( ×1 ) : →
 is a morphism in LIN[C]. Therefore, (D [ ])∗ : → verifies
 the fixpoint equation
 (D [ ])∗ · = +D [ ] ·((D [ ])∗ · ).
 WecannowdefineamapN : ⇒ → ⇒ correspondingto
 Newton-Raphson iteration as follows:
 → .( +(D [ ])∗ ·( ( )⊖ ))
 To define an analogue of Newton iteration in the parametrized
 Cartesian case, we recall first the notion of partial derivative. For
 a map : × → ,wecanobtainitspartialderivative [14, Def
inition 6.2] from the total derivative D[ ] : × × × → by
 precomposing with 0 morphisms:
 D1[ ] := D[ ] ◦ (1 ×1 × 1 ,0 ) : × × →
 D2[ ] := D[ ] ◦ (1 ×1 × 0,1 ) : × × →
 In term calculus, we write the partial derivatives as:
 d ( , )
 d
 ( ) · = d ( , )
 d( , ) ( , )·( ,0)
 d ( , )
 d
 ( ) · = d ( , )
 d( , ) ( , )·(0, )
 We can also define higher order derivatives by taking the partial
 derivative of the total derivative. For : → ,wedefinethe-th
 derivative as ( ) := (D1) [ ] : × → ,itisnon-linearinits
 12
Combiningfixpointanddifferentiationtheory LICS’24,July8–11,2024,Tallinn,Estonia
 firstargumentandmulti-linearinitslast arguments.Intheterm
 calculus,wewritethisas:
 ( ) ( ,( ,..., ))= d ( )
 d ( )·( ,..., )
 IntheparametrizedCartesiancase,wedefineanoperator
 N:C( × , )→C( × , )
 mappingamorphism : × → tothemorphism
 ( , )→ + d ( , )
 d ( )
 ∗
 ·( ( , )⊖ )
 ToperformNewtoniteration,weassumethatCisaTaylorcate
gory[18,19,22,40],whichmeansthateachmap : → is
 equaltoitsTaylorexpansion:
 ( + )= 1
 !
 d ( )
 d ( )·( ,..., ) (21)
 orequivalentlyfor ≤
 ( )= 1
 !
 d ( )
 d ( )·( ⊖ ,..., ⊖ ) (22)
 AssumethatthefixpointcombinatorY: ⇒ → inCis
 obtainedbyScottiterationasthesupremumY= Ywith
 Y0=0 and Y+1=eval , ◦ 1⇒ ,Y
 whereCisassumedtobe-cpoenriched.Weassumeherethat
 the-cpoenrichment iscompatiblewiththeadditivestructure:0
 isthebottomelementandadditioncommuteswithsupremaof
chains.
 Sincethefixpointoperatorweconsideristheleastfixpointop
erator,itimpliesthattheinducedlinearrepetitionoperator(Corol
lary5.7)satisfiesthefollowinginductionaxiom[24]forallℎ, , ∈
 LIN[C]( , ):
 ℎ+ ◦ ≤ ⇒ ∗◦ℎ≤ . (23)
 Wecandeduceitfromtheexplicitcomputationof ∗=fix ( 1+
 ◦ 2)(Corollary5.7)whichisequalto ∗ with
 ∗0 :=0 and ∗ +1 :=1 + ◦ ∗ .
 WedefineafamilyofmorphismZ : ⇒ → corresponding
 totheNewtonapproximants:
 Z0=0 and Z+1=eval◦N,Z .
 L 8.1. Forall ,thefollowinginequalitieshold:
 Y≤Z ≤Y and Z ≤eval◦ 1,Z ≤Z+1
 P . Forafixed : → ,tosimplifythenotation,wewrite
 :=Z( ), :=Y( ),and :=Y( ),
 •Wehave 0 =0≤ (0)andweassumethat forsome ,
 ≤ ( ).Since +1isequalto
 N( )( )= +(D [ ])∗ ·( ( )⊖ )
 weapplytheTaylor formula(21)bysetting := and
 :=(D [ ])∗ ·( ( )⊖ ),andobtain
 ( +1)= ( + )≥ ( )+D [ ] · = +1
 wherethelastinequalityisobtainedfromunfoldingtherep
etitionoperatorintheexpressionof andusingtheinduc
tionhypothesistoobtainthat ( )=( ( )⊖ )+ by
 (18).
 •Usingagainthefixpointaxiomfortherepetitionoperator
 andthefactthat ≤ ( ),weobtainthat +1isequalto
 ( )+(D [ ])·(D [ ])∗ ·( ( ))⊖ )≥ ( ).
 •Weshowthatforall , ≤ .Thebasecaseistrivial,and
 ifweassumethat ≤ forsome ,thenwehave:
 +1= ( )≤ ( )≤ +1
 wherethelasttwoinequalitieswereprovedabove.
 •Thebasecase 0=0≤ istrivial,wewanttoshowthat
 ifforsome , ≤ , then +1≤ .Weshowthatforall
 ∈N,
 +(D [ ])∗ ·( ( ))⊖ )≤ +1( )≤
 whichwillimplythedesiredresultbytakingthesupremum
 over .For =0, theinequalityfollowsfrom ( )≥ .
 Fortheinductivestep,weusetheTaylorformulatoobtain
 +2( )≥ ( +(D [ ])∗ ·( ( ))⊖ ))
 ≥ ( )+(D [ ]) ·(D [ ])∗ ·( ( ))⊖ ))
 = +(D [ ])∗ +1 ·( ( ))⊖ ) □
 AsacorollaryofLemma8.1,weobtainthattheNewtonchain
 convergesandapproximatestheleastfixpointfrombelowasde
sired.
 8.2 Taylormetricandconvergencerate
 TomeasuretheconvergencerateofNewtoniteration,weusea
 metricinducedbytheTaylorexpansionwhichcanbedefinedin
 anyTaylorcategory[47,49].Wedefineafamilyofoperatorson
 homsetsM :C( , )→C( , )mappingamorphism : →
 toits-thTaylormonomialbyevaluatingthe-thderivativeat0:
 M( )( ) := d ( )
 d (0)·( ,..., )
 Thisfamilyofoperatorsinducesapseudo-metric(theseparation
 axiomdoesnotnecessarilyhold)oneachhomset : C( , )×
 C( , )−→C( , )givenby
 ( , )−→ 2− where =inf{ ∈N|M( )≠M( )}
 0 ifforall ∈N,M( )=M( )
 similartotheArnold-Nivatmetricfor-termswithtruncations[2].
 InaCartesiandifferentialcategory, ( , )=2− alsocorresponds
 totheintuition ( )− ( ) = ( ) inanalysisandweusethis
 canonicaldistanceforconvergenceanalysisofoptimizationinour
 setting.ThestrongertriangleinequalityholdsfortheTaylorpseudo
metric:
 ( ,ℎ)≤max{ ( , ), ( ,ℎ)}.
 IfCisaTaylorcategory[18,19,22,40],thentheseparationaxiom
 holdsandweobtainanultra-metric[47,49].
 FortheconvergencerateofNewton’smethod,weassumefur
therthatfor : × → , (0,0)=0andthatthesecondpartial
 13
LICS ’24, July 8–11, 2024, Tallinn, Estonia
 Zeinab Galal and Jean-Simon Pacaud Lemay
 derivative of is nilpotent in its linear argument, meaning that
 there exists ≥ 1 such that for all , , :
 d ( , )
 d
 ( ) · =0.
 similarly to [52, 53] where they require the Jacobian matrix to be
 nilpotent. For an additive map ℎ : → , if ℎ is nilpotent, then
 for all : → ,wehave
 ≤ ℎ∗ ◦ ( ⊖(ℎ◦ )).
 (24)
 To simplify the notation, we write again := fix( ) : → ,
 := Z( ) : → , +1 :=Z+1( ) : → and
 (D [ ]( )) · := d ( , )
 d
 (
 ( )) · .
 Using (16), we obtain that ( ) ⊖ +1( ) is equal to
 ( ( )⊖ ( ))⊖ D [ ]( ) ∗ ·( ( , ( ))⊖ ( ))
 and by (24), it is less than or equal to
 (D [ ]( ))∗ · ( ( )⊖ ( ))⊖(D [ ]( ))·( ( )⊖ ( ))
 ⊖ (D [ ]( ))∗ ·( ( , ( ))⊖ ( ))
 which, by (20) and (16), is in turn is less than or equal to
 (D [ ]( ))∗ · ((( ( )⊖ ( )) ⊖ ( ( , ( ))⊖ ( )))
 ⊖(D [ ]( )) · ( ( )⊖ ( )))
 using (16) and (19), it is less than or equal to
 (D [ ]( ))∗ · ( ( ) ⊖ ( ( , ( ))
 +(D [ ]( )) ·( ( )⊖ ( ))))
 Lastly, since ( ) = ( , ( )) and ≤ ,wecanapplytheTaylor
 formula (22) and obtain that:
 ( ) = ( , ( ))+(D [ ]( ))·( ( )⊖ ( ))
 1
 !
 +
 (
 ≥2
 d ( , )
 d
 ( )) · ( ( ) ⊖ ( ))
 Using (17), we conclude that ( )⊖ +1( ) is less than orequal to
 (D [ ]( ))∗ ·
 1
 !
 ≥2
 (
 d ( , )
 d
 ( )) · ( ( ) ⊖ ( ))
 Wenowstate the main theorem of this section:
 T
 8.2.
 (1) The family of Newton approximants {Z} ∈ is an-chain
 and its supremum Z := 
Z verifies Z = Y and for all
 , Z ≤Y,whichmeansthattheNewton approximants con
verge to the least fixpoint from below.
 (2) If we consider the canonical distance induced by the Taylor
 monomials, for : × → ,assuming that (0,0) = 0
 and that the second partial derivative of is nilpotent in its
 linear argument, then the convergence rate is quadratic1, for
 all :
 (Z+1( ),Y( )) ≤ ( (Z( ),Y( )))2.
 1
 In the works on Newton’s method for power series over-continuous semi-rings
 (e.g.[41]), the terminology of exponential convergence is sometimes used as the num
ber of accurately computed monomials of the power series doubles at each iteration.
 This is equivalent to quadratic convergence for the Taylor metric.
 9 CONCLUSIONANDFUTUREWORK
 In this paper, we provided a categorical framework for combining
 the theory of differentiation and the theory of fixpoints. We intro
duced the notion of Cartesian differential fixpoint categories (Sec
tion 3), and studied the case of Conway operators (Section 4), the
 relation between linearity and fixpoints (Section 5), and the closed
 setting (Section 6). We showed how many well-known examples
 are Cartesian differential fixpoint categories, such as weighted re
lations (Section 7.2), quantales profunctors (Section 7.4), models
 induced by bifree algebras (Section 7.5), and those induced by fix
pointobjects(Section7.6). WealsoshowedhowtheNewton-Raphson
 scheme can be applied in our framework (Section 8). We conclude
 withabrief discussion on potentialfuture workand directions that
 build on the story of this paper.
 Since the tangent bundle functor played such a crucial role, the
 f
 irst natural direction to take is to study tangent categories [11]
 withfixpointoperators,wherethedefiningaxiomwillbethetangent
f
 ixpoint rule (10). Moreover, (10) should also be considered as an
 axiom for tangent categories with trace operators in the general
 monoidal setting.
 There are also cofree Cartesian differential categories which are
 built via the Faà di Bruno construction [15, 32]. We aim to study
 parametrized fixpoint operators in these cofree models or under
stand how the Faà di Bruno construction can be used to build new
 examples of Cartesian differential fixpoint categories.
 Cartesian reverse differential categories [13] provide the cate
gorical foundations of reverse differentiation, an important tool
 for automatic differentiation. Since fixpoints are also an important
 tool for automatic differentiation [51], it would be worthwhile to
 understand the compatibility relation between parametrized fix
point operators and reverse differential combinators.
 Many optimization schemes are based on refinements of the
 Newton-Raphson method. We would like to extend our construc
tion to other iterative methods and study their application to ap
proximate solutions of differential equations in our setting.
 There are also many cases where we can only obtain fixpoints
 defined on a restricted domain which are used when we are in
terested in local minima or maxima and not global solutions. We
 therefore aim to develop a theory of fixpoints for differential re
striction category [12] which would allow us to capture local im
plicit function theorems that are more used in practice
















---






















---
he Optimal Fixed Point Combinator
 Arthur Chargueraud
 INRIA
 arthur.chargueraud@inria.fr
 Abstract. In this paper, we develop a general theory of xed point
 combinators, in higher-order logic equipped with Hilberts epsilon oper
ator. This combinator allows for a direct and e ective formalization of
 corecursive values, recursive and corecursive functions, as well as func
tions mixing recursion and corecursion. It supports higher-order recur
sion, nested recursion, and o ers a proper treatment of partial functions
 in the sense that domains need not be hardwired in the de nition of func
tionals. Our work, which has been entirely implemented in Coq, uni es
 and generalizes existing results on contraction conditions and complete
 ordered families of equivalences, and relies on the theory of optimal xed
 points for the treatment of partial functions. It provides a practical way
 to formalize circular de nitions in higher-order logic.
 1 Introduction
 1.1 Motivation: partial corecursive functions
 To the best of our knowledge, there exists, until now, no general approach to
 formalizing partial corecursive functions in a simple and satisfying manner. Con
sider for example the lter function on innite streams. Given a predicate P of
 type A bool (or A Prop), the lter function f takes a stream s and returns
 a stream made of the elements of s that satisfy P. The lter function is partial
 because it produces a well-de ned stream only when its argument s contains
 innitely many items satisfying the predicate P.
 One way to constructively formalize the de nition of lter in a logic of total
 functions is to have f take as extra argument a proof that its argument contains
 innitely many items satisfying the predicate P. In this approach, studied by
 Bertot [4], the new lter function does not have the type streamA streamA,
 but instead admits a dependent type. Unfortunately, working with dependent
 types is often associated with numerous technical di culties, so we would rather
 nd a solution that does not make such a heavy use of dependent types.
 A di erent, non-constructive approach to formalizing the lter function was
 proposed by Matthews [18]. To apply his technique, the lter function rst needs
 to be turned into a total function, by testing explicitly whether the argument be
longs to the domain. Let neverP s be a predicate that holds when the stream s
 does not contain any item satisfying P. The body of the lter function can be de
scribed through a functional F, as follows. Throughout the paper, the operator ::
denotes the consing operation on innite streams.
 F fs 
let x :: s= s in if (neverP s) then arbitrary else
 if (P s) then x :: (f s) else f s
 The lter function f can then be de ned as Fix1F , where Fix1 is a combinator
 that picks, using Hilberts epsilon operator, the unique xed point of its argument
 when it exists, and otherwise returns an arbitrary value. Here, the functional F
 can be proved to admit a unique xed point using a xed point theorem based
 on contraction conditions, devised by Matthews [18]. It follows that f satis es
 the xed point equation f s = F f s for any stream s.
 The main downside of the approach described above is that the domain of the
 function needs to be hardwired in its de nition. As argued in detail by Krauss
 [13] for the case of recursive functions, this requirement is unsatisfactory. First,
 it requires to modify the code of the functional, which is inelegant and may cause
 di culties when extracting executable code. Second, it overspeci es the output
 of the function outside its domain. Third, it requires to know the domain of the
 function at the time of its de nition, which is not always practical (see [13]).
 The central matter of this paper is to construct a xed point combinator Fix
 that truly supports partial functions. For example, Fix can be directly applied
 to the functional that describes the original lter function, shown below.
 F fs 
let x :: s= s in if (P s) then x :: (f s) else f s
 1.2 Fixed point equations with non-unique solutions
 Most forms of circular de nitions can be captured by (or encoded as) an equation
 of the form a = F a. Yet, such a xed point equation does not necessarily admit
 a unique solution.
 One typical case is that of partial functions. In a logic of total functions, a
 partial function can be represented as a pair of a total function f of type A B
 and a domain D of type A Prop. The partial function (fD) is said to be a
 partial xed point of a functional F of type (A B) (A B) if the equation
 f x = Ffx holds for any x satisfying D. (We postpone to 3.3 the discussion
 of how circular de nitions for partial functions can be expressed as equations of
 the form a = F a.) A functional F typically admits several partial functions as
 xed point. Can one of them be considered the best xed point for F?
 The starting point of this paper is the observation that the exact answer to
 this question is given by the theory of the optimal xed point developed in 1975
 by MannaandShamir[17], which we have formalized in Coq. A fundamental idea
 in this theory is that the only genuine solutions of a xed point equation are the
 partial functions that are consistent with any other xed point (two functions are
 consistent if they agree on the intersection of their domain). Such xed points
 are said to be generally-consistent. The optimal xed point is de ned as the
 generally-consistent xed point with the largest domain. In a sense, the optimal
 xed pointis the most well-de ned solution that can be extracted from the xed
point equation without making arbitrary choices. Manna and Shamir [17] proved
 that any functional of type (A B) (A B) admits an optimal xed point.
 Another typical case where xed point equations do not not admit unique
 solutions occurs when working modulo equivalence relations. A value a is a xed
 point of F modulo ( ) if the equation x F x holds for any value x such that
 x a.Moreover, a xed point a is said to be the unique xed point modulo ( )
 of a functional F if any other xed point x of F is equivalent to a modulo ( ).
 In this case, even though the functional F does not admit a unique solution,
 it admits a unique equivalence class of solutions. Thus, any element from this
 class of equivalence can be considered as representing the meaning of the the
 circular de nition associated with F. The interest of the de nition of xed
 point modulo is that it allows de ning recursive functions on values compared
 modulo an equivalence relation without involving an explicit quotient structure.
 1.3 A generic xed point combinator
 In order to unify the various forms of circular de nitions, we introduce a generic
 xed point combinator. The basic idea is to pick the best xed point, for a
 customizable notion of best that depends on the kind of circular de nition
 being targeted. Our combinator, called Fix, takes as argument an equivalence
 relation , an order relation and a functional F. It then uses Hilberts epsilon
 operator to pick, among the set of all xed points modulo of the functional F,
 the greatest xed point with respect to 
(not be confused with the greatest
 xed point in the sense of domain theory).
 Fix( )( )F 
x [greatest ( ) ( xed point modulo ( ) F) x]
 Appropriate instantiations of the binary relations and produce a combinator
 for unique xed point and a combinator for optimal xed point (possibly modulo
 an equivalence relation).
 Now, in order to exploit properties about the value returned by Fix( )( )F,
 we need to prove that the functional F indeed admits a greatest xed point. For
 a very large scope of circular de nitions, the existence of greatest xed points
 can be derived from one very general theorem, which is developed in this paper.
 This theorem combines and generalizes several existing ideas: contraction condi
tions [12], inductive invariants [15] and complete ordered families of equivalence
 [18, 10]. Moreover, the corollaries used in the particular case of partial functions
 rely on the theory of optimal xed points [17] and involves a generalized version
 of a theorem developed in the context of maximal inductive xed points [15,14].
 The paper is organized as follows. First, we present all the ingredients that
 our paper builds upon. Second, we describe our generic xed point combinator
 and its specialized versions. We then present our xed point theorem and its
 corollaries. Finally, we investigate, without formal justi cation, the possibility
 for code extraction from circular de nitions based on the combinator Fix. Due
 to space limitations, several results can only be summarized. The details can be
 found in the long version of this paper [8].
2 Ingredients
 2.1 Contraction conditions for recursive functions
 Harrison [12] used contraction conditions in order to show the existence of a
 unique xed point for functionals describing recursive functions.
 De nition 1 (Contraction condition for recursive functions). Let F be
 a functional of type (A 
B) (A B),and < be a well-founded relation on
 values of type A. The contraction condition for F with respect to < states:
 xf1 f2 ( y <x f1y =f2y) Ff1x=Ff2x
 This contraction condition ensures the existence of a unique xed point for F as
 soon as the codomain of the recursive function, the type B, is inhabited.
 To understand why the contraction condition holds for a (simple) terminat
ing recursive function, consider the following functional F, which describes a
 function that computes the binary logarithm of its argument: F f x 
if x 
1 then 0 else 1 + f x
 2 . Let us prove that this functional is contractive. Given
 arbitrary x, f1 and f2 and the assumption y < x f1y = f2y , the proof
 obligation is:
 if x 1then 0 else 1+f1
 x
 2 = ifx 1then0else1+f2
 x
 2
 If x is less or equal to 1, then the goal is trivial. Otherwise, we need to show
 that f1 x
 2 and f2 x
 2 are equal. The only way to prove this equality is to use
 the assumption y < x f1y = f2y . So, we have to justify the fact that x
 2 is
 less than x, which is true because x is greater than one. The inequation x
 2 < x
 indeed captures the fact that the recursive call is made on a value smaller than
 the current argument x.
 Contraction conditions support reasoning on higher-order recursion. They
 can also be adapted to n-ary recursive functions and mutually-recursive func
tions, which can be encoded into simple functions using products and sums,
 respectively. The details of the encoding can be hidden through appropriate
 reformulations of the contraction condition and of the xed point theorem.
 Moreover, contraction conditions can be easily extended so as to support
 partial functions by restricting arguments to be in a given domain D. For a
 functional F contractive on a domain D, the xed point theorem guarantees the
 existence of a partial xed point f on that domain, satifying xDx 
f x =
 F fx. Notice that the use of this theorem requires one to provide the domain D
 before constructing the xed point f of F.
 2.2 Inductive invariants
 As Krstic and Matthews [15] point out, the contraction condition for recursive
 function fails to handle the case of nested recursion. Consider the nested zero
 function, described by the functional F f x 
if x = 0 then 0 else f(f(x 1)).
Trying to prove F contractive leads to the proof obligation f1(f1(x 1)) =
 f2(f2(x 1)). The hypothesis of the contraction condition can be used to prove
 f1(x 1) equal to f2(x 1), because x 1 is smaller than x. However, we have
 no assumption at all on the value of f1(x 1), so we cannot prove the equality
 f1(f1(x 1)) = f2(f1(x 1)).
 To address this limitation, Krstic and Matthews [15] introduced the notion
 of inductive invariants and used it to weaken the contraction condition, thereby
 obtaining a stronger xed point theorem able to handle nested recursion.
 De nition 2 (Inductive invariants). A binary relation S of type A B 
Prop is an inductive invariant for a functional F of type (A 
B) (A B)
 if there exists a well-founded relation < such that
 xf ( y<xSy(fy)) Sx(Ffx)
 The rst observation to be made is that if S is an inductive invariant for F,
 then any xed point f of F admits S as post-condition, in the sense that S x(f x)
 holds for any x. Formally, the restricted contraction condition for a functional F,
 with respect to an inductive invariant S, is similar to the contraction condition
 except that it includes an extra hypothesis about the function f1. This condition
 guarantees the existence and uniqueness of a xed point.
 De nition 3 (Restricted contraction condition for recursive functions).
 xf1 f2 ( y <x f1y =f2y) ( y Sy(f1y)) Ff1x=Ff2x
 By instantiating S as the predicate xr(r = 0) , one can prove that the nested
 zero function admits a unique xed point and always returns zero.
 2.3 Complete ordered families of equivalences
 The contraction conditions described so far can only deal with recursion, for
 the basic reason that recursive calls must be applied to smaller values with re
spect to a well-founded relation. In order to deal with corecursive functions,
 Matthews [18] introduced a di erent form of contraction conditions stated in
 terms of families of converging equivalence relations. Di Gianantonio and Micu
lan [10] slightly simpli ed this structure, calling it complete ordered families of
 equivalence, abbreviated as c.o.f.e. . We follow their presentation.
 The contraction condition for a functional F of type (A A) A is stated
 in terms of a family of equivalence relations over values of type A, written i ,
 indexed with values of an ordered type I. This family needs to be complete in the
 sense that all coherent sequences converge to some limit. Note: the de nitions of
 coherence and of completeness can be skipped upon rst reading.
 De nition 4 (Ordered families of equivalence). The structure (AI
 i
 )
 is an ordered family of equivalences when is a well-founded transitive relation
 over the type I and i is an equivalence relation over the type A for any i of
 type I. Thereafter, the intersection of all the relations i is written .
De nition 5 (Coherent sequences). A sequence ui of values of type A in
dexed by elements of type I is said to be coherent if for any indices i and j such
 that i j the values ui and uj are equivalent at level i, that is, ui 
i uj. More
 generally, the sequence ui is said to be coherent on the domain K, for a predicate
 K of type I Prop, when the property ui 
i uj holds for any i and j satisfying
 K and such that i j holds.
 De nition 6 (Completeness for an ordered family of equivalences). An
 ordered family of equivalences (AI
 i
 ) is said to be complete if, for any
 downward-closed domain K (i.e., such that i j and Kj imply Ki) and for
 any sequence ui coherent on the domain K, the sequence ui admits a limit l on
 the domain K, in the sense that ui 
i l holds for any i satisfying K.
 A basic example of c.o.f.e. is the one associated with streams. In this case,
 I is the set of natural numbers ordered with <. The relation i relates any two
 streams that agree up to their i-th element. The intersection 
of the family
 of relations ( i )i N corresponds to stream bisimilarity. This construction of a
 c.o.f.e. for sterams can be easily generalized to coinductive trees.
 Complete ordered families of equivalences are used to state the following
 su cient condition for the existence of a unique xed point for F modulo .
 De nition 7 (Contraction condition for c.o.f.e.s). The functional F is
 contractive w.r.t. a complete ordered family of equivalences (AI
 i
 xyi ( j i x j y) Fx i Fy
 ) when
 In the particular case of streams, the contraction condition expresses the fact
 that if x and y are two streams that agree up to the index i 1, then F x and
 F y agree up to the index i. More generally, the contraction condition asserts
 that, given any two values x and y, the functional F is such that F x and F y
 are always closer to one another than x and y are, for an appropriate distance.
 Di Gianantonio and Miculan [11] have described a general theory, expressed
 in categories of sheaves, in which complete ordered families of equivalences are
 simply particular cases of sheaves on well-founded topologies. Their theory also
 covers the case of well-founded recursion, described by functionals of type x:
 A( y y <x B) B.However, di Gianantonio and Miculan do not cover
 the important case of nested calls, nor do they explain how the contraction
 condition for recursive functions described by functionals of type (A 
B) 
(A B) ts their model.
 2.4 Optimal xed point
 The combinator Fix1 for unique xed points [18] described in the introduction
 does not work for partial functions because the associated xed point equation
 typically admits several partial xed points. One idea, put forward by Krstic
and Matthews [15] and investigated in more details by Krstic in [14], is that
 there is always a best domain for any functional describing a terminating
 recursive function, and that, on this domain, there exists a unique xed point.
 The formalization of this idea relies on the notion of inductive xed point.
 De nition 8 (Inductive xed point). f is an inductive xed point of a
 functional F on a domain D if there exists a well-founded relation < such that:
 g xDx ( y<xDy fy=gy) fx=Fgx
 Interestingly, an inductive xed point on a given domain is always the unique
 xed point on that domain. Moreover, any functional admits a maximal inductive
 xed point, which is the inductive xed point with the largest domain. This
 theorem, which does not appear to have ever been mechanized, may suggest
 the de nition of a maximal inductive xed point combinator. Such a combinator
 would be useful for terminating functions. However, it would not accommodate
 corecursive functions.
 In this paper, we invoke an older and much more general theorem in order
 to formalize the notion of best xed point. The theorem, due to Manna and
 Shamir [17], asserts the existence of an optimal xed point for any functional de
scribing a partial function. While it was initially designed for recursive programs,
 the theorem turns out to apply to a much larger class of circular de nitions.
 Several de nitions need to be introduced before we can state this theorem.
 A partial function f, written with an overline, is represented as a pair (fD)
 of a total function f of type A 
B and of a domain D of type A Prop.
 We write A , B the type of partial functions from A to B. Moreover, we
 write dom( f) the right projection of f and write f (without an overline) the
 left projection of f. Two partial functions f and f are said to be equivalent,
 written f ,= f , if they have the same domain and are extensionally equal on
 that domain. Moreover, two partial functions f and f are said to be consistent
 if they agree on the intersection of their domains. Finally, a partial function f
 extends a partial function f, written f 
f , if the domain of f is included in
 the domain of f and if f and f are extensionally equal on the domain of f.
 Note that the relation de nes a partial order (modulo ,=) on the set of partial
 functions. The next two de nitions formalize the notion of optimal xed point.
 De nition 9 (Generally-consistent xed points). Let f be a xed point
 modulo ,= (the equivalence between partial functions) of a functional F of type
 (A , B) (A, B). The xed point f is said to be a generally consistent,
 written generally consistentF f, if any other xed point f of F modulo ,= is
 consistent with f.
 In other words, a generally-consistent xed point f of a functional F is such
 that, for any other xed point f of F, the equation f(x) = f(x) holds for any x
 that belongs both to the domain of f and that of f . The contrapositive of this
 statement asserts that the domain of a generally-consistent xed point cannot
 include any point x whose image is not uniquely determined by the xed point
equation for F. Thus, as argued by Manna and Shamir [17], generally-consistent
 xed points are the only genuine solutions of any circular function de nition.
 De nition 10 (Optimal xed point). A partial function f of type A , B
 is the optimal xed point of a functional F of type (A , B) 
(A , B) if
 it is the greatest generally-consistent xed point of F, with respect to the partial
 order 
on the set of partial functions.
 In short, the optimal xed point f of a functional F is the generally-consistent
 xed point of F with the largest domain. This means that every other generally
consistent xed point of F is a restriction of f to a smaller domain.
 Theorem 1 (Optimal xed point theorem). For any functional F of type
 (A , B) (A, B),where B is inhabited, F admits an optimal xed point.
 The optimal xed point theorem appears to have had relatively little impact as a
 theory of circular program de nitions, probably because optimal xed points are
 not computable in general. Yet, as a foundation for a theory of circular logical
 de nitions, we nd the optimal xed point theorem to be the tool of choice.
 2.5 Contributions of this paper
 1. By spotting the interest of optimal xed points for logical circular de ni
tions and by conducting the rst formal development of the optimal xed
 point theorem, we obtain a proper treatment of partiality for recursive and
 corecursive functions in higher-order type theory.
 2. Using invariants to generalize existing results on complete ordered fami
lies of equivalences, we provide the rst general method for justifying the
 well-de niteness of nested corecursive functions. The use of invariants also
 supports reasoning on certain forms of corecursive values that could not be
 formalized with previously-existing contraction conditions.
 3. By showing that contraction conditions for recursive functions can be ob
tained as a particular instance of contraction conditions for complete ordered
 families of equivalences, even when nested calls are involved, we are able to
 o er a uni ed presentation of a number of xed point theorems based on
 contraction conditions.
 3 The greatest xed point combinator
 3.1 De nition of the greatest xed point combinator
 The combinator Fix takes as argument an equivalence relation and a partial
 order , both de ned on values of an inhabited type A. It then takes a functional
 F oftype A Aandreturnsthegreatest xed point of F modulo withrespect
 to , if it exists. Its de nition relies on the predicate greatest 
P x , which
 asserts that x satis es P and that x is greater than any other value satisfying P,
 with respect to .
De nition 11 (The greatest xed point combinator).
 Fix( )( )F 
x [greatest ( ) ( xed point modulo ( ) F) x]
 The application of the epsilon operator requires a proof that the type A is
 inhabited. We encapsulate this proof using an inductive data type Inhabited, of
 sort Type 
Prop. (Note that proofs of type InhabitedA need not be manipulated
 explicitly, thanks to the use of Coqs typeclass facility.) Thus, Fix has type:
 A(InhabitedA) 
(A A Prop) (A A Prop) (A A) A
 3.2 Instantiation as a unique xed point combinator
 The unique xed point combinator Fix1, useful for circular de nitions that do
 not involve partial functions, can be de ned in terms of Fix. To that end, it
 su ces to instantiate both and as the equality between values of type A.
 De nition 12 (Another unique xed point combinator).
 FixValF 
Fix(=)(=)F
 FixVal is provably equivalent to the de nition x( y y = F y 
y =x).
 More generally, we can construct a combinator for unique xed point modulo
 an equivalence relation , simply by instantiating both and as .
 De nition 13 (Combinator for unique xed point modulo).
 FixValMod( )F 
Fix( )( )F
 3.3 Instantiation as an optimal xed point combinator
 We now construct a combinator that returns the optimal xed point of a func
tional F of type (A 
B) 
(A 
B). First, we need to transform F as a
 functional between partial functions, of type (A , B) 
(A , B), so as to
 be able to invoke the theory of optimal xed points. Second, we need to nd a
 suitable instantiation of the relation 
to ensure that the greatest xed point
 with respect to is exactly the optimal xed point. We start with the rst task.
 B) 
(A 
De nition 14 (Partialization of a functional). A functional F of type
 (A 
B) can be viewed as a functional of type (A , B) 
(A , B), i.e. as a functional on partial functions, by applying the following
 partialization operator: partialize F 
(fD) (F fD).
 De nition 15 (Partial xed points). Given a functional F of type (A 
B) (A B),wesay that f is a partial xed point of F if and only if it is a
 xed point of the functional partializeF modulo ,=.
Our next step is to de ne a relation F over the set of xed points of
 partialize F so that the greatest element of F is exactly the optimal xed
 point of F. On the one hand, the optimal xed point is a generally-consistent
 xed point of partializeF , moreover it is the greatest with respect to . On
 the other hand, the combinator Fix produces a xed point f of partializeF
 which is the greatest with respect to the relation F, meaning that any other
 xed point f satis es f
 F f. To ensure that f is the optimal xed point, we
 need to ensure (1) that f is generally consistent, meaning that it is consistent
 with any other xed point and (2) that f extends any other generally-consistent
 xed point. These two requirements give birth to the following de nition of F.
 De nition 16 (Partial order selecting the optimal xed point).
 f
 F f 
consistent f f
 Given a functional F of type (A 
(generally consistentF f
 B) 
(A 
f
 f)
 B), the value returned
 by Fix(,=)( F)(partializeF) is a function of type A , B. Since we are not
 interested in the domain of the resulting function but only in its support, of type
 A B,weretain only the rst projection.
 De nition 17 (The optimal xed point combinator).
 FixFunF 
1( Fix(,=)( F)(partializeF))
 Thefollowing theorem relates the de nition of FixFun with that of the optimal
 xed point, thereby justifying that FixFun indeed picks an optimal xed point.
 Theorem 2 (Correctness of the optimal xed point combinator). Given
 a functional F of type (A 
B) (A B)and a partial function f of type
 A, B, the following two propositions are equivalent:
 1. greatest ( ) (generally consistentF) f
 2. greatest ( F) ( xed point modulo (,=) (partializeF)) f
 This ends our construction of the optimal xed point combinator. The con
struction can be easily generalized to the case where values from the codomain B
 are compared with respect to an arbitrary equivalence relation 
rather than
 with respect to Leibniz equality. This construction results in a strictly more gen
eral combinator, called FixFunMod, which is parameterized by the relation .
 4 The general xed point theorem and its corollaries
 4.1 A general contraction theorem for c.o.f.e.s
 Our xed point theorem for c.o.f.e.s strengthens the result obtained in [18]
 and later re ned in [10], adding, in particular, support for nested calls. Our
 contraction condition generalizes the contraction condition for c.o.f.e.s with an
 invariant, in a somewhat similar way as in the restricted contraction condition.
i
 De nition 18 (Contraction condition). Given a c.o.f.e. (AI
 ), a func
tional F of type A A is said to be contractive with respect to an invariant Q
 of type I 
A Prop when
 xyi ( j i x j y Qjx Qjy) Fx i Fy Qi(Fx)
 Our xed point theorem asserts that a given functional admits a unique
 xed point as soon as it is contractive with respect to a continuous invariant.
 The notion of continuity that we introduce for this purpose is de ned as follows.
 De nition 19 (Continuity of an invariant). Given a c.o.f.e. (AI
 i
 ),
 an invariant Q is said to be continuous if the following implication holds for any
 downward-closed domain K, for any sequence (ui)i:I and for any limit l.
 ( iKi ui 
i l) ( iKi Qi(ui)) ( iKi Qil)
 i
 Theorem 3 (Fixed point theorem for c.o.f.e.s). If (AI
 ) is a c.o.f.e.
 and if F is a functional of type A A contractive with respect to a continuous
 invariant Q in this c.o.f.e., then F admits a unique xed point x modulo .
 Moreover, this xed point x is such that the invariant Qix holds for any i.
 The proof of this theorem is fairly involved. The xed point is constructed as
 a limit of a sequence, de ned by well-founded induction on . Each element
 of this sequence is itself de ned in terms of a limit of the previous elements in
 the sequence. Moreover, the convergence of all those limits depend on the fact
 that the i-th value of the sequence satis es the invariant at level i, that is, the
 predicate Qi. The details of the proof are described in the long version [8].
 4.2 Fixed point theorem for corecursive values
 When F is a contractive functional modulo , it admits a unique xed point
 modulo 
(by Theorem 3), thus FixValMod( )F satis es the xed point
 equation for F.
 Theorem 4 (Fixed point theorem for FixValMod).
 x =FixValMod( )F
 (AI
 i
 ) is a c.o.f.e.
 is equal to i:I 
i
 F is contractive w.r.t. Q
 Q is continuous
 x Fx
 iQix
 Compared with previous work, the use of an invariant in the contraction
 condition makes it strictly more expressive. For the sake of presentation, we
 consider a simple example. The circular de nition associated with the functional
 Fs 1::( lter( a)s) is correct only if a 1. When this is the case, we can
 prove F contractive. It su ces to de ne the invariant Q in such a way that
 Qis implies that the i rst elements of s are greater than or equal to a.
4.3 Fixed point theorem for recursive functions
 The goal of this section is to build a c.o.f.e. that can be used to prove that a
 functional F of type (A 
B) (A B)describing a terminating recursive
 function on a domain D admits a unique xed point of type A B. This rela
tively simple construction, which allows to unify the various forms of contraction
 conditions, does not seem to have appeared previously in the literature.
 Theorem 5 (c.o.f.e. for recursive functions). Let be an equivalence re
lation of type A 
A Prop, let < be a well-founded relation of type A 
A Prop, and let D be a domain of type A Prop. Then, the structure
 (A BA<+ x)isacomplete ordered family of equivalences, where (x)x:A is
 a family of equivalence relations on values of type A 
B de ned as follows:
 f1 
x f2 
y < xDy f1y f2y
 Above, <+ is the transitive closure of < and < its re exive-transitive closure.
 In this particular c.o.f.e., the contraction condition can be reformulated in
 a way which, in practice, is equivalent to the conjunction of the propositions
 S is an inductive invariant for F and F satis es the restricted contraction
 condition with respect to S (De nition 2 and De nition 3).
 Theorem 6 (Contraction condition for recursive functions). Let D be a
 domain of type A Prop and let S be a post-condition of type A B Prop
 compatible with , in the sense that if Sxy1 holds and if y1 y2 then
 Sxy2 also holds. Then, in the c.o.f.e. for recursive functions, a functional F
 is contractive w.r.t. the invariant x f Dx Sx(fx) as soon as F satis es
 xf1 f2Dx ( y<xDy f1y f2y Sy(f1y))
 F f1x Ff2x Sx(Ff1x)
 A corollary, not shown here, to the general xed point theorem (Theorem 3)
 can be stated for this reformulated contraction condition. The conclusion of this
 corrolary asserts the existence of a partial xed point f modulo on the domain
 D. Moreover, this xed point f satis es the post-condition xDx Sx(f x).
 The next key theorem in our development establishes that the partial xed
 point (fD) is a generally-consistent xed point of the functional partializeF .
 Theproof of this theorem is quite technical. It reuses and generalizes several ideas
 coming from the proof that inductive xed points are generally-consistent [14].
 Combining the existence of a generally-consistent xed point f for F on the
 domain D with the existence of an optimal xed point for F, we deduce that the
 domain of the optimal xed point of F contains D. It follows that the optimal
 xed point for F satis es the xed point equation on the domain D.
 Theorem 7 (Speci cation of FixFunMod for recursive functions).
 f =FixFunMod( )F
 is an equivalence
 < is well-founded
 S is compatible with 
F is contractive on D w.r.t. < and S modulo 
xDx fx Ffx
 xDx Sx(fx)
4.4 Fixedpointtheoremformixedrecursive-corecursivefunctions
 Duetospacelimitations,weskipthedescriptionofthec.o.f.e.forsimplecorecur
sivefunctionsanddirectlyfocusonthestrictlymoregeneralc.o.f.e.forfunctions
 mixingrecursionandcorecursion.Comparedwiththeconstructionproposedby
 Matthews[18],wehaveaddedsupportforpartialfunctionsandfornestedcalls.
 LetA Bbe the typeof the functiontobe constructedand letDbe
 thedomainonwhichwewant toprove the functionwell-de ned.Thevalues
 of the input typeAarecomparedwithrespect tosomewell-foundedrelation,
 written<.ThevaluesofthecoinductiveoutputtypeBarecomparedusingan
 existingc.o.f.e.(BI i ).Thefollowingresultexplainshowtocombine<and
 inordertoconstructac.o.f.e. forthefunctionspaceA B.
 Theorem8(c.o.f.e. formixed recursive-corecursive functions). The
 structure (A BI A< (ix)
 ) is a c.o.f.e., where< is the lexicographi
cal orderassociatedwiththepairof relations( <+)andwhere((ix)
 )(ix):I A
 isafamilyofequivalencerelationsonvaluesof typeA Bsuchthat
 f1
 (ix)
 f2 (jy) (ix) Dy f1y j f2y
 Theassociatedcontractionconditionandthe xedpointtheoremfollow.
 Theorem9(Contractioncondition for corecursive functions). LetD
 be adomainof typeA Prop. Let S be an indexedpost-conditionof type
 I A B Prop,compatiblewith i inthesensethat if Sixy1 holdsand
 if j i y1
 j y2 holds then Sixy2 holds.Then, inthec.o.f.e. formixed
 recursive-corecursivefunctions,afunctionalFiscontractivew.r.t.theinvariant
 (ix)f Dx Six(fx) assoonasFsatis esthecondition:
 ixf1f2Dx ( (jy)<(ix)Dy f1y j f2y Sjy(f1y) Sjy(f2y))
 Ff1x i Ff2x Six(Ff1x)
 Theorem10(Speci cationof FixFunModformixedfunctions).
 f=FixFunMod( )F
 <isawell-foundedrelation
 (BI i ) isac.o.f.e.
 isequal to i:I
 i
 F iscontractiveonDw.r.t.S
 Siscompatiblewith i
 xDx fx Ffx
 ixDx Six(fx)
 Let us apply this theoremto the lter function. Let f be the function
 FixFunMod( )F,whereF is the functional de ned in 1.1and stands for
stream bisimilarity. The domain D characterizes streams that contain innitely
 many elements satisfying the predicate P. Two streams from the domain are
 compared as follows: s < s holds if the index of the rst element satisfying P
 in s is less than the index of the rst element satisfying P in s. No invariant
 is needed here, so we de ne S such that Siss always holds. Let us prove F
 contractive, as in [18]. Assume the argument s decomposes as x :: s. There are
 two cases. If x satis es P, then the goal is x :: (f1s) i x :: (f2s). This fact
 is a consequence of the assumption f1 s i 1 f2s, which we can invoke because
 (i 
1s) is lexicographically smaller than (i s). If x does not satisfy P, the
 goal is f1 s i f2s. This fact also follows from the hypothesis of the contraction
 condition, because (i s) is lexicographically smaller than the pair (i s). Note
 that this relation holds only because the argument s belongs to the domain D.
 In conclusion, the equation f s Ff s holds for any stream s in the domain D.
 5 Code Extraction
 Given a formal development carried out in higher-order logic, one can extract
 a purely functional program by retaining only the computational content and
 erasing all the proof-speci c elements. The extracted code enjoys a partial cor
rectness property with respect to the original development. Note that termi
nation is usually not guaranteed: even a Caml program extracted from a Coq
 development can diverge [3,8]. Our de nition of Fix relies on Hilberts epsilon
 operator, a non-constructive axiom that does not admit an executable counter
part. Nevertheless, it is still possible to translate the constant Fix into a native
 let-rec construct from the target programming language.
 Our experiments suggest that this extraction leads to e cient and correct
 programs, with respect to partial correctness. However, a formal justi cation of
 our approach is not attempted in this paper. The theory of code extraction is
 already far from trivial (see, e.g. [16]) and there exists, as far as we know, no
 theory able to account for the correctness of code extraction in the presence
 of user-de ned extraction for particular constants. Thus, we leave the proof of
 correctness as a challenge to code extraction experts, and simply explain how to
 set up the extraction process in practice.
 In Haskell, where evaluation is lazy by default, the extraction of the constant
 Fix is very simple: it su ces to translate Fix into F letx = F xinx . This
 value has type A(A A) A , which is appropriate given the type of Fix.
 The extraction towards OCaml code is slightly trickier: due to the explicit boxing
 of lazy values, we need to extract the combinator for corecursive values towards
 a di erent constant than that used to extract functions. See [8] for details.
 6 Other related work
 The most closely related work has already been covered throughout 2. In this
 section, we brie y mention other approaches to circular de nitions. (A detailed
 list of papers dealing with recursive function de nitions can be found in [13].)
The package TFL developed by Slind [20] supports the de nition of total re
cursive functions for which a well-founded termination relation can be exhibited.
 Building on Slinds ideas, Krauss [13] developed the function package, which sup
ports a very large class of partial recursive functions. It relies on the generation
 of an inductive de nition that captures exactly the domain of the recursive func
tion. Contrary to our work, this approach does not support code generation for
 partial functions (except tail-recursive ones) and does not support corecursion.
 The technique of recursion on an ad-hoc predicate, which consists in de ning
 a function by structural induction on an inductive predicate that describes its
 domain, was suggested by Dubois and Donzeau-Gouge [9] and developed by
 Bove and Capretta [7]. Later, Barthe et al. [2] used it in the implementation
 of a tool for Coq. Besides the fact that it relies heavily on programming with
 dependent types, one major limitation of this approach is that the treatment of
 nested recursion requires the logic to support inductive-recursive de nitions, an
 advanced feature absent from many theorem provers.
 Another possibility for de ning terminating recursive functions is to work
 directly with a general recursion combinator [19], using dependently-typed func
tionals. Balaa and Bertot [1] proved a xed point theorem in terms of a con
traction condition for functions of type x : A ( y : ARyx By) Bx ,
 where R is some well-founded relation. More recently, Sozeau [21] implemented
 facilities for manipulating subset types in Coq, including a xed point combina
tor for functionals of type x : A y : y : ARyx B( 1y)
 Bx
 x : A(Bx). This approach supports higher-order and nested recursion, but
 only if the inductive invariant of the function appears explicitly in its type.
 As mentioned in the introduction, Bertot [4] has investigated the formal
ization of the lter function in constructive type theory. This work was later
 generalized to support more general forms of mixed recursive-corecursive func
tions [5]. More recently, Bertot and Komendantskaya [6] experimented reasoning
 about non-guarded corecursive de nitions by exploiting the correspondence be
tween streams and functions over natural numbers.
 7 Future work
 In the future, we would like to implement a generator for automatically deriv
ing corollaries to the general xed point theorem, covering each possible function
 arity and providing versions with and without domains and invariants. Proving
 such corollaries by hand on a per-need basis is generally manageable, but having
 a generator would certainly be much more convenient.





















 ---

















 ---



















 Towards third generation HOTT
 Part 1: Basic syntax
 Michael Shulman
 University of San Diego
 joint work with Thorsten Altenkirch and Ambrus Kaposi
 CMU HoTT Seminar
 April 28, 2022
Outline
 1 Background
 2 Identity types
 3 Function extensionality
 4 Univalence
 5 From univalence to homotopy theory
First generation: Book HoTT
 Cf. AwodeyWarren, Voevodsky, The HoTT Book
 1 Based on Intensional Martin-Lof Type Theory
 2 Identity types characterized by path induction
 3 Univalence is an axiom
 Advantages:
 Easy to do in Coq/Agda: assume univalence and away you go.
 Has models in all higher toposes.
 Disadvantages:
 Not computational (UA axiom is stuck)
 Many laws are not de nitional:
 IdA B((ab) (a b)) IdA(aa) IdB(bb)
 apf g(p) apf(apg(p))
 tr x A(x) B(x)(p (ab)) (trA(pa) trB(pb))
Second generation: Cubical type theories
 Cf. Bezem-Coquand-Huber, Cohen-Coquand-Huber-Mortberg,
 Angiuli-Brunerie-Coquand-Favonia-Harper-Licata
 1 Paths de ned as maps out of an interval exo-type I
 2 Cubical Kan operations asserted explicitly in syntax
 3 Univalence proved from glue types
 Advantages:
 Satis es canonicity and normalization
 Many equalities become de nitional
 Implemented in Cubical Agda, cooltt, ...
 Disadvantages:
 Not yet known to have models in higher toposes...
 . . . but it probably does (cf. ACCRS, cubical model for spaces).
 . . . ?
Whats not to like about cubical type theories?
 Martin-Lof J-elim is conceptually fundamental to equality . In
 Book HoTT, this simple rule automatically yields higher structure.
 Slogan for Book HoTT
 Homotopy is implicitly present in the foundations of mathematics.
 A nice story to tell philosophers.
 Accessible to students.
 In cubical type theory, identity is de ned using a homotopy-theoretic
 idea (paths), and higher structure is put in by hand (Kan ops).
 Fine if you already know you want to do homotopy theory.
 Doesnt have the same philosophical import.
 Not as accessible to students.
That interval
 The interval I is not an ordinary type, but appears in contexts.
 Complicates the meta-theory
 Harder to explain
 Harder to implement
 Termination-checking of boundaries
 Display of boundaries to the user
 Higher-order uni cation
 Were still learning how to implement cubical type theories.
 But its also worth exploring di erent approaches.
A problem shared by Book HoTT and CTT
 Chapter 2 of the Book characterizes lots of identity types:
 IdA B((ab) (a b)) IdA(aa) IdB(bb)
 IdA B(f g) 
(x:A)IdB(f (x) g(x))
 IdU(AB) Equiv(AB)
 In Book HoTT, these are all only equivalences.
 In cubical type theory, most of them are isomorphisms...
 . . . except for IdU, which is still only an equivalence!
 This limits the everyday usability of univalence. Given an
 equivalence f : A 
B : g, if we pass it through univalence we cant
 recover f or g de nitionally, only up to homotopy.
Towards H.O.T.T.
 I will describe work in progress towards a theory called
 Higher Observational Type Theory
 with the following properties:
 It admits models in all higher toposes, including spaces.
 Univalence by de nition (+ other Id characterizations).
 Homotopy theory is emergent rather than explicit;
 all rules have a convincing philosophical justi cation.
 Computation is a reasonable hope (no obvious stuck terms).
 Plan for the three talks:
 1 Basic syntax of H.O.T.T.
 2 Symmetries and semicartesian cubes
 3 Semantics of univalent universes
Outline
 1 Background
 2 Identity types
 3 Function extensionality
 4 Univalence
 5 From univalence to homotopy theory
Bishops conception of set
 Errett Bishop wrote that
 A set is de ned by describing exactly what must be done
 in order to construct an element of the set and what must
 be done in order to show that two elements are equal.
 MLTT follows this principle if equal refers to de nitional equality,
 giving introduction and rules:
 a : A
 b : B
 (ab) : A B
 1s 
1t : A
 2s 
2t : B
 s 
t : A B
 The elimination and rules are determined by harmony with the
 introduction rules.
Lower Observational Type Theory
 (Lower) Observational Type Theory (Altenkirch-McBride) applies
 the same principle to propositional equality types Eq.
 a : A
 b : B
 (ab) : A B
 p : EqA( 1s 1t)
 q : EqB( 2s 2t)
 (p =q) : EqA B(s t)
 But this theory is non-univalent by construction, with primitive UIP:
 p : EqA(x y)
 q : EqA(x y)
 irr(pq) : EqEqA(xy)(pq) 
Can we formulate a univalent version?
Notation and terminology
 In a univalent context, we refer to identity types, with formation rule
 A : U a:A b:A
 IdA(ab) : U
 The elements of an identity type are identi cations.
 Green highlights indicate rules of H.O.T.T.
 We omit unchanging ambient contexts .
Computing identity types
 The OTT rule
 p : EqA( 1s 1t)
 q : EqB( 2s 2t)
 (p =q) : EqA B(s t)
 says that EqA B behaves like EqA EqB. In a higher context, this
 would require in nitely many such rules for IdIdA B
 , etc. Instead, we
 make it a computation rule:
 IdA B(s t) IdA( 1s 1t) IdB( 2s 2t)
 Then we can just apply the same rule multiple times:
 IdIdA B(st)(pq) IdIdA( 1s 1t) IdB( 2s 2t)(pq)
 IdIdA( 1s 1t)( 1p 1q) IdIdB( 2s 2t)( 2p 2q)
Respect for equality
 Heres Bishop again (paraphrased):
 An operation f from A into B is called a function if we
 have f (a) = f(a) whenever aa A and a = a.
 For de nitional equality, MLTT has congruence rules for each
 primitive term former:
 s 
t : A B
 1s 
1t : A
 2s 
2t : B
 Lower OTT similarly asserts congruence terms:
 p : EqA B(s t)
 =
 1p : EqA( 1s 1t)
 =
 2p : EqB( 2s 2t)
 By structural induction, all terms respect both equalities.
Respect for higher equality
 We instead assert a general congruence term:
 x : A f :B p: IdA(aa)
 apxf (p) : IdB(f [a x] f [a x])
 In apxf (p), the variable x is bound in the term f .
 (For now, B doesnt depend on A; well come back to that later.)
 We give it computation rules on standard term-formers:
 apx(ab)(p) (apxa(p) apxb(p))
 apx 1s(p) 
1 apxs(p)
 apx 2s(p) 
2 apxs(p)
 These are well-typed by the previous rule IdA B IdA IdB.
 And for higher equalities, we can apply them multiple times.
Re exivity
 Everything is the same as itself, de nitionally and observationally:
 a : A
 a a:A
 Similarly, we assert re exivity terms:
 a : A
 a : EqA(aa)
 a : A
 re a : IdA(aa)
 with computation rules on standard term-formers:
 re (ab) 
(re a re b)
 re
 1s 
1re s
 re
 2s 
2re s
 Again, these rules are well-typed because IdA B IdA IdB.
ap on neutrals and redexes
 Note that apxf(p) computes on all terms that f could be, even
 those not headed by a constructor. The rules are consistent with
 computations occurring inside f ; for instance,
 apx 1(ab)(p) 
1 apx(ab)(p) 
1 (apxa(p) apxb(p)) apxa(p)
 can also be obtained by reducing 1(ab) a in the bound term.
 We complete the picture with rules for variables:
 apxx(p) p
 apxy(p) re y (if y is a variable= x)
 Then an ap term is never a normal form: it can always reduce.
 Think of it as a higher-dimensional explicit substitution f p x .
 modulo some detail well come back to later...
Functorial laws for ap
 Since ap always reduces, we can deduce by induction on terms the
 following admissible equalities:
 apxf (re a) re f[a x]
 apyg(apxf (p)) apxg[f y](p)
 apxt(p) re t (if x does not appear in t)
Outline
 1 Background
 2 Identity types
 3 Function extensionality
 4 Univalence
 5 From univalence to homotopy theory
Towards identity of functions
 The obvious rule for equality of functions is function extensionality:
 IdB C(f g) 
(y:B)IdC(f (y) g(y)) ?
 But this is trouble for ap on application. Given x : A f : B C
 and x : A b:B while p : IdA(aa), we want to compute
 apxfb(p) : IdC((f b)[a x] (f b)[a x])
 IdC(f [a x](b[a x]) f [a x](b[a x]))
 to something involving
 apxf (p) : IdB C(f[a x] g[a x])
 (y:B)IdC(f [a x](y) f [a x](y)) ?
 and
 apxb(p) : IdB(b[a x] b[a x])
 We need an equality in C between f[a x] and f[a x] applied to
 di erent inputs b[a x] and b[a x], but this apxf (p) cant give that.
Identity of functions
 A better rule is (still ignoring dependence of B on A)
 IdB C(f g) 
(u:B) (v:B) (q:IdB(uv))IdC(f (u) g(v))
 Once we have singleton contractibility, this will be equivalent to the
 nave version. But it also gives us
 apxf (p) : (u:B) (v:B) (q:IdB(uv))IdC(f [a x](u) f [a x](v))
 apxb(p) : IdB(b[a x] b[a x])
 so we can compute
 apxfb(p) : IdC(f [a x](b[a x]) f [a x](b[a x]))
 apxfb(p) apxf(p) b[a x] b[a x] apxb(p)
Identity of abstractions
 Let x : Ay : B t :C, hence x :A yt :B C. Given
 p : IdA(a0 a1), we can form apx( yt)(p), which has type
 IdB C(( yt)[a0 x] ( yt)[a1 x])
 (u:B) (v:B) (q:IdB(uv))IdC(t[a0 x u y] t[a1 x v y])
 How do we compute this?
Identity of abstractions
 Let x : Ay : B t :C, hence x :A yt :B C. Given
 p : IdA(a0 a1), we can form apx( yt)(p), which has type
 IdB C(( yt)[a0 x] ( yt)[a1 x])
 (u:B) (v:B) (q:IdB(uv))IdC(t[a0 x u y] t[a1 x v y])
 How do we compute this? We want to ap the term t on both p
 and q simultaneously. So we introduce a multi-variable ap:
 x1 : A1
 p1 : IdA1
 (a1 b1)
 xn : An t :C
 pn : IdAn
 (an bn)
 apx1 xnt(p1
 apx( yt)(p) 
pn) : IdC(t[a] t[b ])
 u v qapxyt(pq)
 (Still ignoring dependence in A1
 AnC.)
Computing with multi-variable ap
 Multi-variable ap computes with all the same rules we had before.
 The variable rules are
 apx1 xnxi
 (p1
 pn) pi
 apx1 xny(p1
 pn) re y (if y is a variable 
x1
 In addition, we can identify re exivity with the 0-ary ap
 (no bound variables in the subscript):
 re a 
ap()a()
 xn )
 Then all the computation rules for re become special cases of
 those for n-ary ap.
Outline
 1 Background
 2 Identity types
 3 Function extensionality
 4 Univalence
 5 From univalence to homotopy theory
Towards de nitional univalence
 We want univalence to hold by de nition , meaning IdU(AB)
 consists of equivalences. But what is an equivalence?
 Chapter 4 of the Book discusses several possibilites:
 1 Maps with contractible bers (Voevodsky equivalences)
 2 Half-adjoint equivalences
 3 Bi-invertible maps
 But philosophically, these all have problems:
 None feels canonical : why choose one over another?
 None is (de nitionally) symmetrical in A and B.
 Some are hard to motivate without homotopy theory a priori.
What is de nitional univalence?
 The HoTT Book gave three properties of a type Equiv(AB) to be
 a good notion of equivalence :
 1 There is an embedding Equiv(AB) , (A B).
 2 QInv(AB) Equiv(AB) over A B.
 3 Equiv(AB) QInv(AB) over A B.
 Here QInv(AB) is the nave type of quasi-invertible maps :
 QInv(AB) 
(f :A B) (g:B A) Id(g f 1A) Id(f g 1B)
 Univalence ( idtoeqv : IdU(AB) 
Equiv(AB) is an equivalence )
 can be stated equivalently using any such Equiv (but not QInv).
 can be stated equivalently using any such Equiv (but not QInv).
What is de nitional univalence?
 The HoTT Book gave three properties of a type Equiv(AB) to be
 a good notion of equivalence :
 1 There is an embedding Equiv(AB) , (A B).
 2 QInv(AB) Equiv(AB) over A B.
 3 Equiv(AB) QInv(AB) over A B.
 Here QInv(AB) is the nave type of quasi-invertible maps :
 QInv(AB) 
(f :A B) (g:B A) Id(g f 1A) Id(f g 1B)
 Univalence ( idtoeqv : IdU(AB) 
Equiv(AB) is an equivalence )
 can be stated equivalently using any such Equiv (but not QInv).
 But as soon as univalence holds, IdU also satis es these properties!
 Can univalence ever hold non-de nitionally?
What isde nitionalunivalence, really?
 Concretede nitionsofEquiv(AB) includemapsf :A Band
 g:B Aasdata. Itsuseful torememberexactlywhattheseare,
 de nitionally, tocomputewiththem.
 De nition
 Univalenceholdsde nitionally(at level1) if, forsomede nition
 Equiv(AB): (f:A B) (g:B A) wehave
 Equiv(AB) IdU(AB) Equiv(AB)
 suchthat(f g 1) (f g 2). (Perhaps 1 2.)
 Canalsoconsiderhigher levels,extractinghomotopiesaswell.
 Evencurrentcubical typetheories(CCHM,ABCFHL,CubicalAgda)
 donotsatisfythis!Cantevenextract f de nitionally.
One-to-onecorrespondences
 The best Equivisthetypeofone-to-onecorrespondences:
 1-1-Corr(AB): (R:A B U) (a:A)isContr( (b:B)R(ab))
 (b:B)isContr( (a:A)R(ab))
 Remark
 AnR:A B Uisacorrespondence. It isone-to-oneifeach
 elementofAorBhasexactlyonecorrespondent intheother.
 (Prefernottocall ita relation unless itsproposition-valued.)
 De nitionallysymmetricinAandB.
 Adirectpropositions-as-typesversionofclassical bijective
 relation (andreducestoitwhenABaresets), soitseasyto
 motivatewithouthomotopytheory.
 InalargeruniversethanAB...butsois IdU(AB).
 Alsoworksreallywell...
1-1 correspondences vs equivalences
 If R : A B Uis1-1, the centers of contraction yield
 f : A Bandg :B A,which form an equivalence.
 Conversely, if f : A 
B is an equivalence with inverse g : B 
we make a 1-1 correspondence by Rf(ab) : IdB(b fa).
 (b:B)IdB(b fa) is contractible with center (f a re fa).
 (a:A)IdB(b fa) is contractible with center (gb b).
 If we re-extract an equivalence, we get f and g de nitionally.
 With a fancier de nition of Rf , we can even remember the
 homotopies b : IdB(b fgb) and a : IdA(agfa).
 A,
Computing IdU
 For (philosophical, syntactic, and semantic) reasons (later), instead
 of IdU(AB) 1-1-Corr(AB), we make IdU primitive, with intro,
 elim, and but no . (Like a coinductive type with one destructor.)
 R : 1-1-Corr(AB)
 R : IdU(AB)
 A2 : IdU(A0A1)
 A2 :1-1-Corr(A0A1)
 p : 1-1-Corr(AB)
 p
 p
 This rule is su cient for de nitional univalence.
 Equiv(AB) 1-1-Corr(AB) IdU(AB) 1-1-Corr(AB) Equiv(AB)
Outline
 1 Background
 2 Identity types
 3 Function extensionality
 4 Univalence
 5 From univalence to homotopy theory
Towards-groupoid structure
 Now every A : U needs some re A : 1-1-Corr(AA). The obvious
 choice for its underlying correspondence is IdA : A 
A U:
 1(re A ) IdA
 The other parts of re A then give us singleton contractibility!
 1 2(re A ) : (a:A)isContr( (b:A)IdA(ab))
 2 2(re A ) : (b:A)isContr( (a:A)IdA(ab))
 In particular, this yields composition operations: given p : IdA(ax)
 and q : IdA(ay), we have (x p) (y q) : (b:A)IdA(ab). But
 (b:A)IdA(ab) is contractible, so we get (in particular)
 p 1 q: IdA(x y)
Computing-groupoid structure
 Now re is supposed to compute on all terms. And for U, the
 constructors are type formers. So we must specify how to
 compute, e.g., re A B using re A and re B.
 In the rst component, this is just the computation of identity types:
 re A B
 ( 1re A B
 (IdA B
 (IdA IdB
 ( 1re A
 2re A B )
 )
 )
 1re B
 )
 We give rules for the other components that compute the-groupoid structure of each type former, e.g. in A B
 (pq) 1 (r s) (p 1 r q 1 s)
Dependent identity types
 We can also apply non-nullary ap to terms in U.
 If z : A B :U and p : IdA(x y), we have
 apzB(p) : IdU(B(x)B(y))
 1(apzB(p) ) : B(x) B(y) U
 This is how we de ne the dependent/heterogeneous identity type:
 Idp
 zB(uv) :
 1(apzB(p) )(uv)
Rules for dependent identity types
 Since ap also computes on all terms, we give rules like
 Idp
 zB C(uv) Idp
 zB( 1u 1v) Idp
 zC( 2u 2v)
 The rule ap(re ) re implies that heterogeneous identity types
 over re reduce to homogeneous ones:
 Idre a
 zB (uv) IdB[a z](uv)
 Similarly, the rule apconstant(p) 
re
 implies that dependent
 identity types in constant families also reduce to homogeneous ones:
 Idp
 zB(uv) IdB(uv)
 (if z doesnt appear in B)
 Finally, functoriality of ap gives
 Idapx f (p)
 zC (uv) Idp
 xC[f z](uv)
Transport
 Still with z : A B : U and p : IdA(x y), we also have
 2(apzB(p) ) proving Idp
 B : B(x) 
B(y) Uis one-to-one.
 In particular, we have transport: each u : B(x) corresponds to some
 tr p
 zB(u) : B(y), with liftp
 zB(u) : Idp
 zB(u trp
 zB(u)).
 We must give computation rules for 2ap, specifying how transport
 computes on type families:
 tr p
 zB C(u) (trp
 zB( 1u) trp
 zC( 2u))
 liftp
 zB C(u) (liftp
 zB( 1u) liftp
 zC( 2u))
Deriving path induction
 As in cubical type theory, singleton contractibility and transport
 together imply Martin-Lof identity elimination J.
 1 Given C : (xy:A)IdA(x y) U with u : C(aa re a) and
 e : IdA(ab), singleton contractibility yields:
 q : Id (y:A)IdA(ay)((a re a) (be))
 2 Currying C to Ca : 
(y:A)IdA(ay)
 U, we can transport:
 tr q
 Ca
 (u) : Ca((be))
 C(abe)
 Again as in cubical type theory, the rule for J holds only typally.
and 
With dependent Id, we can de ne Id and Id :
 Id (x:A)B(x)(s t) 
(p:IdA( 1s 1t))Idp
 B( 2s 2t)
 Id (x:A)B(x)(f g) 
(x:A) (y:A) (p:IdA(xy))Idp
 B(f (x) g(y))
 with corresponding rules for ap on their term formers,
 and generalizations to dependent Id and Id .
Dependentmulti-variableapandId
 Dependent Idalsomakessenseofdependencies inn-aryap,using
 theevidentderivednotionofn-aryId.
 Eachidenti cationisdependentontheprecedingones.
 x1:A1 x2:A2(x1) xn:An(x1 xn 1) t :C(x1 xn)
 p1 : IdA1
 (a1 b1) p2 : Idp1
 x1A2
 (a2 b2) pn : Idp1 pn 1
 x1 xn 1An
 (an bn)
 apx1 xn t(p1 pn): Idp1 pn
 x1 xnC(t[a] t[b])
 Idp1 pn
 x1 xnC(uv): 1(apx1 xnC(p1 pn) )(uv).
 Whenformalizingthis,wemayuseaprimitivenotionoftelescope
 (acontextdependentontheambientcontext)










Towards third generation HOTT
 Part 2: Symmetries and semicartesian cubes
 Michael Shulman
 University of San Diego
 joint work with Thorsten Altenkirch and Ambrus Kaposi
 CMU HoTT Seminar
 May 5, 2022
Up today
 Plan for the three talks:
 1 Basic syntax of H.O.T.T.
 2 Symmetries and semicartesian cubes
 3 Semantics of univalent universes
Outline
 1 Acalculus of telescopes
 2 Some problems revealed by cubes
 3 Symmetry solves all problems
 4 Semicartesian cubes
 5 Semantic identity types
Review
 Last week I described the Book version of H.O.T.T., starting
 with simple ideas, and introducing complexity only as necessary.
 By way of review, lets reformulate the resulting theory more
 concisely and cleanly.
 In particular, we eventually ended up with n-variable ap (and Id)
 that bind a nite list of variables:
 x1 : A1
 xn : An t :B 
apx1 xnt(p1
 pn) : IdB( )
 Such a context su x is also called a telescope.
 We now reify these into a telescope calculus .
Telescopes
 Telescopes are de ned inductively as nite lists of types:
 tel
 tel
 A : U
 (
 x : A) tel
 The elements of a telescope are substitutions:
 () : 
: 
A : U a:A[ ]
 ( a): ( x :A)
 These are de ned mutually with their action on terms (and types):
 a : A
 : 
a[ ] : A[ ]
Dependent Idandapwithtelescopes
 Nowwecande neidentitytelescopesfromidentitytypes:
 tel : :
 Id ( )tel Id(() ())
 Id( x:A)(( a) ( a)) : Id ( ) : Id A(aa)
 Thesearede nedmutuallywithn-aryId,whichdependsonthem:
 : Id ( ) A:U a:A[ ] a :A[ ]
 Id A(aa):U
 WewriteIdA(aa) Id()
 A(aa) inthenon-dependentcase.
 (LasttimeIde neddependent Idintermsofap;herewepostulateit
 separatelyandthenmakethemcoincidelater.)
ComputingId
 Aswesawlasttime, Idcomputesonall typeformers:
 Id A B(s t) Id A( 1s 1t) Id B( 2s 2t)
 Id
 (x:A)B(s t) (q:Id A( 1s 1t))Id q
 ( x:A)B( 2s 2t)
 IdA B(f g) (u:A) (v:A) (q:Id A(uv))Id B(fugv)
 Id
 (x:A)B(f g) (u:A) (v:A) (q:Id A(uv))Id q
 ( x:A)B(fugv)
Id is a 1-1 correspondence
 All identity types are 1-1 correspondences:
 : Id ( ) 
corr A(a) : isContr
 A : U a:A[ ]
 (a:A[ ])Id A(aa)
 : Id ( ) 
A : U a :A[ ]
 corr A(a) : isContr (a:A[ ])Id A(aa)
 The centers of contraction constitute transport:
 : Id ( ) 
A : U a:A[ ]
 tr A(a) : A[ ]
 lift
 A
 (a) : Id A(a tr A(a))
 These witnesses compute on type formers:
 corr A B(a) 
hence also
 tr A B(a) 
,
 , etc.
Computingap
 AtermcanbeappliedtoIdofanytelescopeitdependson:
 : Id ( ) t :B
 ap t( ): Id B(t[ ] t[ ])
 Thishigher-dimensionalexplicitsubstitutioncomputesonall terms:
 ap (st)( ) (ap s( ) ap t( )
 ap 1s( ) 1ap s( ) ap 2s( ) 2ap s( )
 ap fb( ) ap f(p) b[a x] b[a x] ap b( )
 ap ( yt)( ) u v qap yt( q)
 Wede nere exivityasthe0-aryap: re a ap a().
Univalence
 IdU(AB) contains as a retract the type of 1-1 correspondences:
 1-1-Corr(AB) :
 1-1-Corr(AB) 
(R:A B U) (a:A)isContr( (b:B)R(ab))
 (b:B)isContr( (a:A)R(ab))
 IdU(AB) 1-1-Corr(AB)
 p p
 We identify dependent Id with ap into the universe:
 Id B(bb) 1(ap B( ) )(bb)
 corr B(bb) 
1 2(ap B( ) )(bb)
 corr B(bb) 
2 2(ap B( ) )(bb)
 (Last time, we de ned the LHS as the RHS. Separating them is more
 natural for Tarski universes, and permits types not lying in any universe.)
That asterisk: Neutral re exivities
 I claimed that ap is never a normal form, but theres one exception:
 When y is a variable, re y is neutral (hence normal).
 Since re is nullary ap, the rule that would apply is
 apx1 xny(p1
 pn) re y (if y is a variable 
x1
 where n = 0, but this just reduces re y ap()y() to itself!
 xn )
 This includes other terms that obviously must also be neutral:
 apxf(x)(p) 
re f (a0 a1 p) for a variable f : A B.
 IdA(a0 a1) ( 1re A)(a0 a1) for a variable A : U.
 Similarly, re re x
 , re re re x 
, etc., are also neutral.
Outline
 1 Acalculus of telescopes
 2 Some problems revealed by cubes
 3 Symmetry solves all problems
 4 Semicartesian cubes
 5 Semantic identity types
Squares and cubes
 H.O.T.T. is not a cubical type theory : there are no explicit cubes
 in the syntax. But like any other type theory with dependent identity
 types (including Book HoTT!), it has an emergent notion of cube:
 a02 : IdA(a00 a01)
 a12 : IdA(a10 a11)
 a21 : IdA(a01 a11)
 a22 : Ida02a12
 a20 : IdA(a00 a10)
 xy IdA(xy)(a20 a21)
 a12
 a10 a11
 a20
 a22
 a21
 a00 a01
 a02
 Similarly, IdIdIdA 
is a type of 3-dimensional cubes, etc.
 Very important point
 The roles of a02 a12 and a20 a21 are asymmetrical!
Cubical horn- llers
 Given a02 a12 a20, we have llers of left-to-right cubical horns:
 tr a02a12
 xy IdA(xy)(a20) : IdA(a01 a11)
 lifta02a12
 xy IdA(xy)(a20) : Ida02a12
 xy IdA(xy)(a20 tra02a12
 a10
 a20
 a12
 lifta02 a12
 xy IdA(xy)(a20)
 a00 a01
 a02
 xy IdA(xy)(a20))
 a11
 tr a02 a12
 xy IdA(xy)(a20)
 Similarly, tr and lift ll right-to-left cubical horns.
 And trIdIdA
 , etc. ll higher-dimensional left-right horns.
 Problem #1
 We dont seem to have top-to-bottom or bottom-to-top llers.
Degeneratecubes
 Givena2: IdA(a0 a1), therearetwodegeneratesquares:
 re a2
 : IdIdA(a0a1)(a2 a2) Idre a0 re a1
 xy IdA(xy)(a2 a2)
 apxre x
 (a2): Ida2
 xIdA(xx)(re a0
 re a1
 ) Ida2a2
 xy IdA(xy)(re a0
 re a1
 )
 a1 a1
 a0 a0
 re a1
 re a2
 re a0
 a2 a2
 a0 a1
 a0 a1
 a2
 apx re x(a2)
 a2
 re a0 re a1
Degeneratecubes
 Givena2: IdA(a0 a1), therearetwodegeneratesquares:
 a1 a1
 a0 a0
 re a1
 re a2
 re a0
 a2 a2
 a0 a1
 a0 a1
 a2
 apx re x(a2)
 a2
 re a0 re a1
 Problem#2
 Fora:A, thetwodoubly-degeneratesquares
 a a
 a a
 re a
 re re a
 re a
 re a re a
 a a
 a a
 re a
 apx re x(re a)
 re a
 re a re a
 seemtobede nitionallyunrelated.
Stuckdegeneraciesbreakcanonicity
 Problem#3
 Ourrulessofarcomputere a2
 basedonthestructureofa2,but
 apxre x
 (a2) isstuck,evenifa2 isveryconcrete.
 re x doesntreducewhenx isavariable.
 apdoesnt inspect its identi cationargument.
Stuckdegeneraciesbreakcanonicity
 Problem#3
 Ourrulessofarcomputere a2
 basedonthestructureofa2,but
 apxre x
 (a2) isstuck,evenifa2 isveryconcrete.
 re x doesntreducewhenx isavariable.
 apdoesnt inspect its identi cationargument.
 Abitnonobviously, thisalsobreakscanonicityforN.
 Intuitivehomotopy-theoreticreason
 ForatypeA:U, thesquareapxre x
 (re A) inUisessentiallya
 self-homotopyof theidentityequivalenceofA, i.e. (a:A)IdA(aa).
 TakingA=S1wegetastuckloopinIdS1(basebase),henceinZ.
 (Theresalsoanexplicitargumentusingtwouniverses insteadofS1.)
Outline
 1 Acalculus of telescopes
 2 Some problems revealed by cubes
 3 Symmetry solves all problems
 4 Semicartesian cubes
 5 Semantic identity types
Symmetry
 To solve these problems, we introduce a symmetry operation that
 transposes squares:
 a12
 a10 a11
 a20
 a22
 a00 a01
 a02
 a22 : Ida02a12
 a21
 a01 a11
 a21
 a02
 symA(a22)
 a12
 a00 a10
 a20
 xy IdA(xy)(a20 a21)
 symA(a22) : Ida20a21
 xy IdA(xy)(a02 a12)
TheotherKanoperations
 Nowwecan llothercubicalhorns, solvingproblem#1:
 a10 a11
 a00 a01
 a20
 a02
 a21
 a01 a11
 a00 a10
 a21
 a02
 a20
 a01 a11
 a00 a10
 a21
 lifta20 a21
 xy IdA(xy)(a02)
 a20
 a02 tr a20 a21
 xy IdA(xy)(a02)
 a10 a11
 a00 a01
 tr a20 a21
 xy IdA(xy)(a02)
 symA(lifta20 a21
 xy IdA(xy)(a02))
 a02
 a20 a21
Computing symmetry
 To solve problem #3, we de ne
 apxre x
 (a2) 
symA(re a2
 ).
 This computes based on a2...if sym also computes!
Computing symmetry
 To solve problem #3, we de ne
 apxre x
 (a2) 
symA(re a2
 ).
 This computes based on a2...if sym also computes!
 For the most part, computing symmetry is straightforward, e.g.:
 Ids02s12
 uv IdA B(uv)(s20 s21)
 Ids02s12
 uv IdA( 1u 1v) IdB( 2u 2v)(s20 s21)
 Ids02s12
 uv IdA( 1u 1v)( 1s20 1s21) Ids02s12
 Id 1s02 1s12
 uv IdB( 2u 2v)( 2s20 2s21)
 xwIdA(xw)( 1s20 1s21) Id 2s02 2s12
 yz IdB(yz)( 2s20 2s21)
 So we can de ne
 symA B((pq)) (symA(p) symB(q))
Dependentsymmetry
 Togeneralizethisto-types,weneeddependentsymmetryovera
 squareinatelescope(dontworrytoomuchaboutthesyntax):
 22: Id02 12
 Id ( )( 20 21) a22: Id02 12 22a02a12
 uv Id A(uv)
 (a20 a21)
 sym22
 A(a22): Id20 21sym( 22)a20a21
 uv Id A(uv)
 (a02 a12)
 Thenwecande ne
 sym22
 (x:A)B((pq)) (sym22
 A(p) sym22p
 ( x:A)B(q))
Symmetry for functions
 Idf02 f12
 f gIdA B(f g)(f20 f21) Idf02 f12
 f g (x0:A) (x1:A) (x2:IdA(x0 x1))IdB(fx0gx1)(f20 f21)
 (x00:A) (x01:A) (x02:IdA(x00x01))
 (x10:A) (x11:A) (x12:IdA(x10x11))
 (x20:IdA(x00x10)) (x21:IdA(x01x11)) (x22:Idx02 x12
 Idf02x02 f12x12
 uv IdB(uv)(f20x20 f21x21)
 So f22 : Idf02 f12
 xy IdA(xy)(x20x21))
 f gIdA B(f g)(f20 f21) is a function from squares in A, with
 arbitrary boundary, to squares in B with speci ed boundary.
 Thus we de ne symA B by transposing both input and output:
 symA B(f22)(x00 x10 x20 x01 x11 x21 x02 x12 x22)
 sym(f22(x00 x01 x02 x10 x11 x12 x20 x21 sym(x22)))
 Symmetry for-types is similar, using dependent symmetry.
Rules for symmetry
 Some obvious rules for symmetry are that it should be an involution:
 symA(symA(a22)) a22
 and it should commute with iterated ap on squares:
 symB(apapf 
(a22)) apapf 
(symA(a22))
 The nullary case of the latter is sym(re re a
 ) re re a
 .
 This solves problem #2:
 apxre x
 (re a) 
sym(re re a
 ) 
re re a
Higher-dimensional symmetry
 For n-dimensional cubes (i.e. n-fold iterated Id-types):
 We would expect symmetries to permute all n dimensions.
 The symmetric group Sn should act on n-cubes.
 We have transpositions of adjacent dimensions, from our sym.
 (E.g. symIdA 
: IdIdIdA 
IdIdIdA 
and apsymA 
: IdIdIdA 
Fortunately, Sn is generated by adjacent transpositions!
 Sn = 1 n 1
 k k =1
 j 
k = k j (j+1<k)
 k k+1 k = k+1 k k+1
 IdIdIdA 
.)
 The rst two relations follow from the equations on the last slide.
 To obtain the third, we assert
 symIdA
 (apsymA
 (symIdA
 (a222))) 
apsymA
 (symIdA
 (apsymA
 (a222)))
Outline
 1 Acalculus of telescopes
 2 Some problems revealed by cubes
 3 Symmetry solves all problems
 4 Semicartesian cubes
 5 Semantic identity types
Towards computation by gluing
 Symmetry computes the previously stuck term apxre x
 (a2).
 But how do we know there arent other stuck terms?
 Obviously, by proving canonicity/normalization.
 We havent done this yet, but the rst step (from a modern
 perspective) is constructing a set-based semantic model to be the
 codomain for Artin gluing.
Identity contexts
 Question
 What categorical structure corresponds to our identity types?
 The objects of a category C correspond to syntactic contexts.
 The fundamental operation on contexts takes to
 ID :
 : 
: 
: Id (
 )
 which factors the diagonal (i.e. is a path object):
 re
 ID
 ( :
 :
 This operation is functorial (via ap).
 ) = 
We have natural symmetries IDID = IDID , yielding an
 Sn-action on n-fold identity contexts..
Cubicalactions
 Thus,anID-structureonC isthesameas
 Afunctor ID:C C
 Nat. trans. r :1C IDands t : ID 1Cwithsr=tr=11C
 Natural symmetries ID ID=ID IDsatisfyingSn relations.
 De nition
 Let opbethemonoidalcategoryfreelygeneratedbyanobjectI,
 morphismsr :1 Iands t :I 1withsr=tr=11,where1is
 theunit,andsymmetriesI I=I IsatisfyingSn relations.
 ThenanID-structureonC isalsoequivalently
 Amonoidal functor op [CC]
 andthereforealsoequivalently
 Acoherentaction op C C.
The semicartesian cube category
 is a semicartesian monoidal category: symmetric monoidal
 and its unit 1 is terminal. Projections, but no diagonals.
 It is also the semicartesian monoidal category freely generated
 by an object I and morphisms s t : 1 I.
 We call the semicartesian cube category.
 This is the category used by:
 BernardyCoquandMoulin, for internal parametricity
 (actually they used a unary version, this would be the binary one)
 BezemCoquandHuber, for the original cubical model
 CavalloHarper, for the parametricity direction of parametric
 cubical type theory
Enrichment
 The presheaf category = Set op inherits a Day convolution
 monoidal structure (also semicartesian):
 k
 (X Y)n =
 Xk Y (nk )
 We write n for the representable ( I n). Note 0 is terminal.
 Theorem
 An action . : op C C is the same as an enrichment of C over
 that has powers by representables (write n X I n .X).
 Map(AB)n := C(A n B)
 (XMap(A n B))= (X nMap(AB))-enriched categories are the natural home for H.O.T.T. semantics.
Cubical objects
 Of course, 
is enriched over itself.
 Similarly, any category E op of cubical objects is-enriched, with
 powers and copowers if E is complete and cocomplete:
 k
 (A X)n =
 (A X)n =
 k
 (Ak 
(Xk)A
 ( m X)n=Xn m
 (nk )) X
 (kn )
 Map(XY)n = E op(X n Y)
More about the cube category
 Up to equivalence:
 The objects of are nite sets.
 A morphism 
(mn) is a function : n m
 that is injective on the preimage of m.
 The monoidal structure m n is disjoint union.
 Sometimes use a skeletal version with objects n = 01
 +
 n 1 ,
 but often the non-skeletal version with all nite sets is better.
 The coface k
 (n k n) is the identity on n k and
 sends k to .
 The codegeneracy k 
(nn k ) is the inclusion.
 The endomorphism monoid (nn) is the symmetric group Sn.
The magic of semicartesian cubes
 The monoidal structure of is almost cartesian; only the
 injectivity requirement spoils it. If it were cartesian we would have
 (nk )= (nk) (n ) ?
 Instead, we have
 (nk )=
 (n
 : (nk)
 (k) )
 Removing (k) from the second domain ensures the copaired
 function k 
n 
+ is still injective on the preimage of n.
 But in some ways this is even better!
Copowers by representables
 For A 
and X E op, we have
 k
 (A X)n =
 ( m X)n=
 k
 (Ak 
(nk )) X
 ( (km) (nk )) X
 =
 =
 =
 =
 (nm ) X
 (nm) (n (m) ) X
 (n
 (nm)
 Xn (m)
 (m) ) X
 (nm)
Semicartesian cylinders
 Taking m = 1, we get
 ( 1 X)n= 
A morphism 
Xn (1)
 (n1)
 (n1) is a function 1 n
 +, so either:
 some k n, in which case n (1) = n k , or
 + or , in which case n (1) = n. Thus:
 ( 1 X)n=Xn+Xn+
 Xn k
 k n
 An n-cube in 1 X is either an n-cube in the left-hand copy of X,
 an n-cube in the right-hand copy of X, or an (n 1)-cube in X
 stretched out in some dimension along the cylinder.
 There is almost no other cube category for which this holds.
Outline
 1 Acalculus of telescopes
 2 Some problems revealed by cubes
 3 Symmetry solves all problems
 4 Semicartesian cubes
 5 Semantic identity types
Semantic identity types
 In a-enriched category with representable powers, we also need:
 1 Coherence theorems.
 2 Transport and lifting ( brancy ).
 next time
 next time
 3 Categorical computation rules for Id, up to isomorphism.
Semantic identity types
 In a-enriched category with representable powers, we also need:
 1 Coherence theorems.
 2 Transport and lifting ( brancy ).
 next time
 next time
 3 Categorical computation rules for Id, up to isomorphism.
 Its tempting to think that, at least in , we can just de ne IdA B,
 IdA B, etc., to be whatever we want. But we cant: IdX must be
 de ned as 1 X. What we can de ne is the individual sets of
 n-cubes in a particular X 
. But:
 It can be non-obvious how these lead to a categorical
 characterization of the entire cubical set IdX.
 For type formers like A B, A B, we dont even have this
 much choice: they are determined by their universal properties.
 The computation rules for Id are non-trivial theorems about E op.
Identity types of products
 Note x : Ay : A IdA(x y) : U is represented semantically by the
 projection from the representable power 1 A A A.
 Since ( 1 
) is a right adjoint, it preserves products:
 1 
=
 (A B) ( 1 A) ( 1 B)
 (A B) (A B) (A A) (B B)
 =
 Syntactically, this gives
 IdA B(uv) = IdA( 1u 1v) IdB( 2u 2v)
 Same idea works for-types. A coherence theorem will improve = to =.
Up next
 Plan for the three talks:
 1 Basic syntax of H.O.T.T.
 2 Symmetries and semicartesian cubes
 3 Univalent universes



  A synthetic theory of-categories in homotopy type
 theory
 joint with Michael Shulman
 Octoberfest, Carnegie Mellon University
Motivation
 Why do I study category theory?
 — Ifind category theoretic arguments to be aesthetically appealing.
 What draws me to homotopy type theory?
 — Ifind homotopy type theoretic arguments to be aesthetically
 appealing.
Plan
 1. Homotopy type theory
 2. A type theory for synthetic 
3. Segal types and Rezk types
 4. The synthetic theory of-categories-categories
 Main takeaway: the dependent Yoneda lemma is a directed analogue of
 path induction in HoTT.
1
 Homotopy type theory
Types, terms, and type constructors
 Homotopy type theory has:
 • types A, B, …
 • terms x A, y B
 • dependent types x A ⊢ Bx type, x y A ⊢ Bx y type
 Type constructors build new types and terms from given ones:
 • products A B, coproducts A B, function types A B,
 • dependent sums ∑
 xABx , dependent products ∏
 xABx , and
 identity types x y A ⊢ x A y.
 Propositions as types:
 A B AandB
 A B AorB
 ∑
 ∏
 A B AimpliesB
 xA Bx
 xA Bx
 xBx
 xBx
 x Ay xequalsy
Identity types
 Formation and introduction rules for identity types
 x y A
 x Aytype
 x A
 reflx x A x
 ∑
 Semantics
 xreflx
 xyA x A y
 A A A
 ∆
 Hence ∑
 xyAx A y is interpreted as the path space of A and a term
 p x Aymay be thought of as a path from x to y in A.
Path induction
 The identity type family is freely generated by the terms reflx x A x.
 Path induction: If Bx y p is a type family dependent on x y A and
 p x Ay, then there is a function
 )
 path-ind 
(∏
 xA
 Bxx reflx
 ∏
 xyA
 ∏
 px Ay
 Bx y p
 Thus, to prove Bx y p it suffices to assume y is x and p is reflx.
 The-groupoid structure of A with
 • terms x A as objects
 • paths p x A yas 1-morphisms
 • paths of paths h p x Ay q as 2-morphisms, 
arises automatically from the path induction principle.
2
 A type theory for synthetic
 ( ,1)-categories
The intended model
 Set∆op ∆op
 bisimplicial sets
 Reedy 
types
 Segal
 types with
 composition
 Rezk
 types with
 composition
 & univalence
 Theorem (Shulman). Homotopy type theory is modeled by the
 category of Reedy fibrant bisimplicial sets.
 Theorem (Rezk). 
complete Segal spaces.-categories are modeled by Rezk spaces aka
Shapes in the theory of the directed interval
 Our types may depend on other types and also on shapes 2n,
 polytopes embedded in a directed cube, defined in a language
 ⊤
 and
 satisfying intuitionistic logic and strict interval axioms.
 ∆n 
∆
 Because ϕ 
t
 t
 t
 tn
 t
 2n tn 
2 t t
 t
 2 t t
 t
 ∆
 e.g.
 t t
 t
 t
 t
 t
 t
 implies ϕ, there are shape inclusions 
∆ 2
 t
 t
 t
 ∆ ∆.
Extension types
 shape inclusion: 
implies , i.e., so that 
t 
2n ϕ and
 .
 Formation rule for extension types
 shape
 A type
 ⟨ 
A a
 t 
2n 
so that ϕ
 ⟩
 a
 A term f 
⟨ 
A
 f 
⟩
 defines
 A so that f t
 a 
type
 a t for t 
A
 The simplicial type theory allows us to prove equivalences between
 extension types along composites or products of shape inclusions.
3
 Segal types and Rezk types
Hom types
 Formation rule for extension types
 shape
 ⊢ Atype
 ⟨ 
A a
 ⟩
 a 
type
 The hom type for A depends on two terms in A:
 x y A⊢homA x y
 A
 ∆ ∆ shape Atype x y ∆ A
 xy
 homA x y
 ⟨ 
∆ A
 ∆
 ⟩
 type
 A term f homA x y defines an arrow in A from x to y.
 ⟨
 xy
 ⟩
Segal types have unique binary composites
 A type A is Segal iff every composable pair of arrows has a unique
 composite, i.e., for every f homA x y and g homA y z the type
 ⟨ A
 fg
 ⟩
 is contractible.
 ∆
 Prop. A Reedy fibrant bisimplicial set A is Segal if and only if
 A∆2 ↠A 2
 1 has contractible fibers.
 fg
 Notation. Let compgf 
⟨ A
 ∆
 ⟩
 denote the unique
 inhabitant and write g ◦ f homA x z for its inner face, the composite
 of f and g.
Identity arrows
 For any x A, the constant function defines a term
 xx
 idx 
tx homA xx
 ⟨ 
∆ A
 ∆
 which we denote by idx and call the identity arrow.
 For any f homA x y in a Segal type A, the term
 idx f
 s t f t
 ⟨ A
 ∆
 ⟩
 ⟩
 witnesses the unit axiom f f ◦ idx.
Associativity of composition
 Let A be a Segal type with arrows
 f 
Prop.
 homA x y g homA y z h homA zw
 h ◦ g◦f
 h ◦g ◦f.
 Proof: Consider the composable arrows in the Segal type ∆
 f
 f
 x z
 h◦g
 f
 g◦f
 g◦f
 g
 y w
 Composing defines a term in the type ∆
 A:
 y
 g
 z
 g
 ℓ
 h◦g
 h
 h
 ∆
 A which yields a
 term ℓ homA xw so that ℓ h◦ g◦f andℓ h◦g ◦f.
Isomorphisms
 An arrow f homA x y in a Segal type is an isomorphism if it has a
 two-sided inverse g homA y x . However, the type
 ∑
 g ◦f idx
 g homA yx
 f ◦ g idy
 has higher-dimensional structure and is not a proposition. Instead define
 isisof
 ∑
 g homA yx
 g ◦f idx
 ∑
 h homA yx
 For x y A, the type of isomorphisms from x to y is:
 ∑
 x Ay
 isisof
 fhomA xy
 f ◦ h idy
Rezk types
 By path induction, to define a map
 id-to-iso
 x Ay x Ay
 for all x y A it suffices to define
 id-to-isoreflx
 idx
 ASegal type A is Rezk if every isomorphism is an identity, i.e., if the map
 id-to-iso
 is an equivalence.
 x Ay x Ay
Discrete types
 Similarly by path induction define
 id-to-arr
 ∏
 xyA
 x Ay homA x y by id-to-arr reflx idx
 and call a type A discrete if id-to-arr is an equivalence.
 Prop. A type is discrete if and only if it is Rezk and all of its arrows are
 isomorphisms. Thus, if the Rezk types are 
discrete types are-groupoids.
 Proof:
 id-to-arr-categories, then the
 x Ay homA x y
 id-to-iso
 x Ay
4
 The synthetic theory of
 ( ,1)-categories
Covariant fibrations I
 A type family x A ⊢ Bx over a Segal type A is covariant if for every
 f 
homA x y andu Bx there is a unique lift of f with domain u., i.e., if
 ∑
 vBy
 homB f u v iscontractible
 Here
 homB f u v
 ⟩
 ⟨ B f
 uv
 ∆ ∆
 is the type of arrows in B from u to v over f.
 B f B
 where
 ⌟
 ∆ A
 f
 Notation. The codomain of the unique lift defines a term f u By .
 Prop. For u Bx , f homA x y , and g homA y z ,
 g f u g◦f u and idx u u
Covariant fibrations II
 A type family x A ⊢ Bx over a Segal type A is covariant if for every
 f 
homA x y and u Bx there is a unique lift of f with domain u.
 Prop. If x A ⊢ Bx is covariant then for each x A the fiber Bx is
 discrete. Thus covariant type families are fibered in-groupoids.
 Prop. Fix a A. The type family x A ⊢ homA ax is covariant.
 For u homA ax and f homA x y , the transport f u equals the
 composite f ◦ u as terms in homA a y ., i.e., f u
 f ◦ u.
The Yoneda lemma
 Let x A ⊢Bx be a covariant family over a Segal type and fix a A.
 Yoneda lemma. The maps
 ev-id 
and
 yon 
ϕϕa ida
 )
 (∏
 xA
 homA ax Bx
 u x f f u Ba
 (∏
 Ba
 )
 homA ax Bx
 xA
 are inverse equivalences.
 Proof: The transport operation for covariant families is functorial in A
 and fiberwise maps between covariant families are automatically natural.
 Note. A representable isomorphism ϕ ∏
 xA homA ax
 homA bx
 induces an identity ev-idϕ b A a if the Segal type A is Rezk.
The dependent Yoneda lemma
 From a type-theoretic perspective, the Yoneda lemma is a
 “directed” version of the “transport” operation for identity types.
 This suggests a “dependently typed” generalization of the Yoneda
 lemma, analogous to the full induction principle for identity types.
 Dependent Yoneda lemma. If A is a Segal type and Bx y f is a
 covariant family dependent on x y A and f homA x y , then
 evaluation at xx idx defines an equivalence
 ev-id 
∏
 ∏
 xyA
 fhomA xy
 Bx y f
 ∏
 xA
 Bxx idx
 This is useful for proving equivalences between various types of
 coherent or incoherent adjunction data.
Dependent Yoneda is directed path induction
 Takeaway: the dependent Yoneda lemma is directed path induction.
 Path induction: If Bx y p is a type family dependent on x y A and
 p x Ay, then there is a function
 )
 path-ind 
(∏
 xA
 Bxx reflx
 ∏
 xyA
 ∏
 px Ay
 Bx y p
 Thus, to prove Bx y p it suffices to assume y is x and p is reflx.
 Dependent Yoneda Lemma: If Bx y f is a covariant family dependent
 on x y Aandf homA x y andAis Segal, then there is a function
 )
 id-ind 
(∏
 xA
 Bxx idx
 ∏
 xyA
 ∏
 fhomA xy
 Bx y f
 Thus, to prove Bx y f it suffices to assume y is x and f is idx.
References
 For considerably more, see:
 Emily Riehl and Michael Shulman, A type theory for synthetic-categories, arXiv:1705.07442
 To explore homotopy type theory:
 Homotopy Type Theory: Univalent Foundations of Mathematics,
 https://homotopytypetheory.org/book/
 Michael Shulman, Homotopy type theory: the logic of space,
 arXiv:1703.03007
 Thank you

Towards an Implementation of
 Higher Observational Type Theory
 Michael Shulman
 University of San Diego
 jww Thorsten Altenkirch, Ambrus Kaposi, and Elif Uskuplu
 running HoTT @ NYU Abu Dhabi
 20 April 2024
Outline
 1 Introduction
 2 Some choices about the theory
 3 Normalization by evaluation
 4 Higher-dimensional normalization
What is Higher Observational Type Theory?
 H.O.T.T. is a third style of homotopy type theory, after Book HoTT
 and Cubical Type Theory.
 • In Book HoTT, identity types are defined uniformly across all
 types as an inductive family.
 • In Cubical Type Theory, identity types are defined uniformly
 across all types by mapping out of the interval.
 • In Higher Observational Type Theory, identity types are defined
 observationally according to the base type.
 • IdA× B(⟨x0,y0⟩,⟨x1,y1⟩) is a product IdA(x0,x1) × IdB(y0,y1).
 • IdA→B(f0,f1) is (x : A) → IdB(f0 x,f1 x)
 (x0 x1 : A)(x2 : IdA(x0,x1)) → IdB(f0 x0,f1 x1)
 • IdU(A,B) is a type of equivalences A ≃ B.
 HOTT has natural semantics in semicartesian (BCH) cubical sets.
The primitives of HOTT
 1 Any type A has an identity type IdA(x0,x1), which computes∗
 based on the structure of A.
 2 Any term M : A has a reflexivity term reflM : IdA(M,M),
 which computes based on the structure of M.
 • refl⟨a,b⟩ = ⟨refla,reflb⟩ and reflfstu = fstreflu, etc.
 • reflλx.M = λx0x1x2.apx.M(x0,x1,x2), etc.
 3 Any open term x : A ⊢ M : Bx has an apx.M(a0,a1,a2), for
 a2 : IdA(a0,a1), which computes based on M.
 • apx.⟨M,N⟩(a0,a1,a2) = apx.M(a0,a1,a2),apx.N(a0,a1,a2)
 • apx.λy.M(a0,a1,a2) = λy0 y1 y2.ap(x,y).M(a0,a1,a2,y0,y1,y2)
 • apx.MN(a0,a1,a2) =
 apx.M(a0,a1,a2) N[x →a0] N[x →a1] apx.N(a0,a1,a2)
 (This is what requires our definition of IdA→B.)
 4 Any square a22 : Ida02,a12
 sym(a22) : Ida20,a21
 IdA
 (a20, a21) has a symmetry
 (a02, a12), which computes based on a22.
 IdA
From parametric type theory to HOTT
 Cubical Type Theory can be obtained by defining a fibrancy
 predicate in a non-univalent substrate theory (Orton–Pitts).
 We intend to obtain HOTT similarly. The rule
 IdA→B(f0,f1) is (x0x1 : A)(x2 : IdA(x0,x1)) → IdB(f0 x0,f1 x1)
 suggests that the substrate should be internal binary parametricity,
 where Id is a “bridge type”. This satisfies all the same rules as the
 identity type in HOTT except
 • IdU(A,B) is a type of correspondences A → B → U.
What we want
 What we want
 1 A proof assistant implementing HOTT!
 For that we need...
 2 A typechecking algorithm
 For that we need (as for any dependent type theory)...
 3 An equality-testing algorithm
 And for that we need (more or less)...
 4 A normalization algorithm (computing with open terms).
 Roughly speaking, we test equality by normalizing both terms and
 comparing normal forms.
What we have
 To be presented today
 1 A normalization algorithm for a version of “Parametric OTT”.
 2 An implementation of this algorithm in OCaml, along with a
 typechecker for a prototype proof assistant called Narya.
 NOT being presented today
 A proof that this algorithm is correct!
 However:
 • The algorithm aligns with general principles of NbE.
 • The implementation is very strongly typed, so it serves as a
 partial formalization of correctness.
 • Narya has been tested on many examples and seems to work.
Outline
 1 Introduction
 2 Some choices about the theory
 3 Normalization by evaluation
 4 Higher-dimensional normalization
Higher-dimensional structure
 The higher structure of HOTT is generated by low-dimensional
 primitives like “refl” and “sym”. But many different such
 composites produce the same operation.
 Image credit: John Baez
 sym(apsym(sym(x222)))
 ≡apsym(sym(apsym(x222)))
 A normalization algorithm must implement such equalities.
 Our choice
 Represent higher dimensions directly internally, evaluating each
 composite of refl and sym to a cubical operator in canonical form.
 The user can still restrict themselves to refl and sym.
Σ-types vs records
 The identity type of a Σ-type is “defined to be” another Σ-type:
 IdΣ(x:A).B(x)(u, v) ≈ Σ(p : IdA(π1u,π1v)).Idp
 B(π2u,π2v)
 In a proof assistant, Σ-types are just a particular record type:
 def Σ (A : Type) (B : A → Type) : Type := sig (
 fst : A,
 snd : B fst,
 )
 In general, the identity type of any record type should be another
 record type, but it can’t be an instance of the same record type.
 And similarly for inductive and coinductive types.
(Non-)computation with types
 Our choice
 Refrain from computing definitionally with any identity types.
 For example Id (Σ A B) u v is not definitionally equal to
 Σ (Id A (u .fst) (v .fst))
 (p → Id B (u .fst) (v .fst) p (u .snd) (v .snd))
 but instead behaves like a record type defined as
 sig (
 fst : Id A (u .fst) (v .fst),
 snd : Id B (u .fst) (v .fst) fst (u .snd) (v .snd),
 )
 They are definitionally isomorphic, and their fields and constructors
 have the same names, so we can usually pretend they are the same.
 Inductive, coinductive, and even function types are similar.
Outline
 1 Introduction
 2 Some choices about the theory
 3 Normalization by evaluation
 4 Higher-dimensional normalization
Old-style normalization
 Old view of normalization
 1 Formulate reduction rules such as (λx.M)N ⇝ M[x → N]
 2 Prove that applying these reductions to any term eventually
 leads to a normal form, a term that cannot be further reduced.
 However, this is not very efficient. For example:
 (λx.λy.M)N P ⇝ (λy.M)[x → N] P
 ≡ λy.M[x → N] P
 ⇝M[x →N] [y →P]
 We have to traverse the term M (which could be large) twice:
 once to substitute N for x, then again to substitute P for y.
 (Also worry about variable capture, or incrementing De Bruijn indices, etc.)
TowardsNbE
 First idea
 Don’tactuallycompute(λy.M)[x→N],butkeepitasaclosure.
 Then,whenit isappliedtoafurtherargumentP,computethe
 simultaneoussubstitutionM[x→N,y→P].
 However, if itnever isappliedtoafurtherargument,wedohaveto
 actuallycomputeitasλy.M[x→N] togetanormal form.
 Totrackthis,andensurethatclosuresneverappear innormal
 forms,weusetwodifferentkindsofterms:
 • termsdonotcontainclosures,anduseDeBruijnindices.
 • valuescontainclosures,anduseDeBruijnlevels.
 (Useof levels/indiceseliminatesvariablecaptureandindexincrements.)
Normalization by evaluation
 Normalization has two steps:
 1 evaluation of a term M into a value, using an environment that
 assigns a value to every free (index) variable in M.
 2 readback of a value into a normalized term.
 In particular:
 • There is no “substitution” operation: evaluation does it all.
 • When readback finds a closure (λy.M)[x → N], it restarts
 evaluation with y bound to a variable, M[x → N, y → y], then
 reads back the result and re-wraps it in λy.
 • Readback can be type-directed and perform η-expansion.
 • If we define the type of values to contain no redexes, we can
 guarantee statically that the result is a normal form.
 • There’s a close connection to mathematical proofs by
 categorical gluing along a restricted Yoneda embedding.
Outline
 1 Introduction
 2 Some choices about the theory
 3 Normalization by evaluation
 4 Higher-dimensional normalization
Matchingunderbinders
 InordinaryNbE,matchinghappensduringevaluation.
 Example
 Toevaluatetheterm“ifMthenNelseP”,wefirstevaluateMtoa
 valueandinspecttheresult. If it is“true”,weproceedtoevaluate
 N; if it is“false”,weproceedtoevaluateP.
 However, thisstyledoesn’tplaywellwithmatchingunderbinders.
 Example
 Toevaluate“apx.M(p0,p1,p2)”,wehavetoinspectMto
 implementrules likeforpairs:
 apx.⟨M,N⟩(p0,p1,p2)≡ apx.M(p0,p1,p2),apx.N(p0,p1,p2)
 Butevaluatingx.Mproducesaclosure,notactuallycomputingthe
 bodyMtoanythingwecanmatchagainst!
ap is a form of substitution
 “ap” is a lot like substitution:
 1 They are never∗ normal forms: they always reduce away,
 computing on both introduction and elimination forms.
 2 The user doesn’t need direct access to them. For “ap”, it
 suffices to use “refl” on a function.
 apx.M(p0,p1,p2) ≡ reflλx.M p0 p1 p2
 3 Their computation rules are similar:
 ⟨M,N⟩[x → P] ≡ M[x → P],N[x → P]
 Thus, we replace “ap” by a higher-dimensional substitution, which
 in NbE becomes higher-dimensional evaluation.
Higher-dimensional environments
 Definition
 An n-dimensional environment associates to each (index) variable an
 n-dimensional cube of values.
 n =0 a:A
 n =1 a0:A,a1:A, a2 : IdA(a0,a1)
 n =2 a00:A, a01 :A, a02 : IdA(a00,a01),
 a10 : A, a11 : A, a12 : IdA(a10,a11),
 a20 : IdA(a00,a10), a21 : IdA(a01,a11),
 a22 : Ida02,a12
 IdA
 (a20, a21)
Faces and evaluation
 For any k-dimensional face ϕ of an n-dimensional cube, an
 n-dimensional environment θ has a k-dimensional face environment
 θ ∗ϕ. E.g. the faces of the 1-dimensional
 
  
x→ a0:A, a1 :A, a2 : IdA(a0,a1) ,
 y → b0 :B, b1 : B, b2 : IdB(b0,b1)
 
 
 are the 0-dimensional [x → a0, y → b0] and [x → a1, y → b1].
 Evaluating a term M in an n-dimensional environment θ produces
 an n-dimensional value M[θ], whose boundary consists of M[θ ∗ ϕ]
 for the faces ϕ of n. For example, if ⟨x,y⟩ : A × B, then
 ⟨x, y⟩ x → (a0,a1,a2),y → (b0,b1,b2) ≡ ⟨a2,b2⟩
 which lies in IdA×B(⟨a0,b0⟩,⟨a1,b1⟩).
ap is higher evaluation
 Now instead of apx.M(a0,a1,a2) we have
 M[x → (a0,a1,a2)].
 In particular, the computation rule for reflexivity of an abstraction,
 which “starts” higher substitution, is
 reflλx.M ≡ λx0x1x2.M[x → (x0,x1,x2)].
 In NbE, this should be an evaluation rule in some environment θ.
 But if θ starts out 0-dimensional, we need to evaluate M in a
 1-dimensional environment that we can extend by (x0,x1,x2).
 reflλx.M[θ] ≡ λx0 x1 x2.M[?,x → (x0,x1,x2)]
 We need an operation of “degenerate environments”.
ap is higher evaluation
 Now instead of apx.M(a0,a1,a2) we have
 M[x → (a0,a1,a2)].
 In particular, the computation rule for reflexivity of an abstraction,
 which “starts” higher substitution, is
 reflλx.M ≡ λx0x1x2.M[x → (x0,x1,x2)].
 In NbE, this should be an evaluation rule in some environment θ.
 But if θ starts out 0-dimensional, we need to evaluate M in a
 1-dimensional environment that we can extend by (x0,x1,x2).
 reflλx.M[θ] ≡ λx0 x1 x2.M[reflθ,x → (x0,x1,x2)]
 We need an operation of “degenerate environments”.
Degeneracies
 Any m-dimensional degeneracy δ of an n-dimensional cube maps an
 n-dimensional object M to an m-dimensional one M⟨δ⟩. E.g.
 reflM ≡M⟨ρ⟩ symM ≡M⟨σ⟩
 Like substitution/evaluation, M⟨δ⟩ is defined by traversing M.
 But unlike evaluation, both M and M⟨δ⟩ are values.
 This is necessary to evaluate degeneracies:
 (reflx)[x → M] ≡ M⟨ρ⟩
 where M, being in an environment, is a value.
 (NB: For afficionados of modal type theory, θ ∗ ϕ and M⟨δ⟩ may remind you of
 locks and keys.)
Degenerate environments
 An m-dimensional degeneracy δ of an n-dimensional cube also maps
 any n-dimensional environment θ to a degenerate environment θ ∗ δ.
 For instance, [x → a,y → b] ∗ ρ (reflexivity) is
 
  
x→ a:A, a:A, refla : IdA(a,a) ,
 y → b:B, b :B, reflb : IdB(b,b)
 This is how we evaluate degeneracies in general:
 (M⟨δ⟩)[θ] ≡ M[θ ∗δ].
 And act on closures by degeneracies:
 (λy.M)[θ] ⟨δ⟩ ≡ (λy.M)[θ ∗ δ]
 
 
 In particular, the actual evaluation of reflexivity of an abstraction is
 (λx.M)⟨δ⟩ [θ] ≡ (λx.M)[θ ∗ δ]
 which is, of course, a closure and doesn’t go under the λ until
 applied or read back.
Some categorical remarks
 In combination, environments are acted on by arbitrary morphisms
 in the BCH cube category (composites of faces and degeneracies).
 θ ∗(ϕ◦δ) = (θ∗ϕ)∗δ
 In an algebraic presentation, substitutions (∼ environments) are
 indexed by a dimension:
 θ : Γ n −→ ∆
 and are acted on by morphisms in the cube category:
 θ : Γ n −→ ∆
 ψ : m→n
 θ ∗ψ : Γ m −→ ∆
 Thus, we have a cubical set of substitutions from Γ to ∆. That is,
 The category of contexts is enriched over cubical sets.
 We thus expect an enriched version of categorical gluing to appear
 in a formal proof of normalization.
Higher-dimensional NbE
 With these modifications...
 . . . and a lot of omitted work and details...
 . . . we get a normalization by evaluation algorithm.
 degeneracies
 terms
 eval
 values
 readback
 normals
 Using this for equality-checking, we then implement a typechecker.
 https://github.com/mikeshulman/nary












What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Recursively, Rethinking Corecursively
 David Jaz Myers
 June 19, 2017
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Mathematical Metaphors
 This talk will be about two specific mathematical metaphors, but
 what are mathematical metaphors,
 why make them,
 and how can they be misused?
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Mathematical Metaphors
 In this talk, we will look closely at the mathematical metaphor
 between
 Complex Systems and Recursive Functions
 Wewill see how this metaphor a lot of standard theories in science
 and philosophy, usually those that fall under the rubrik of “realism”.
 Wewill also find that this metaphor can lead us to some shaky
 philosophical positions.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 What is a function
 A function is a process that turns an input into an output.
 F(input) = output
 If a function takes inputs of a type Inputs and gives outputs of a
 type Outputs, we write
 F : Inputs Outputs
 For example,
 F : Numbers Numbers
 F(n) = 2n +1
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 What is Recursion?
 A function is recursive when its output on a complicated input is
 determined by its output on simpler inputs.
 Ultimately, the output of a recursive function is determined by its
 simplest inputs.
 Wecall these simplest inputs atoms, or base cases, and the rules
 for building them up constructors.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 What is Recursion?
 So to define a recursive function we need
 to know how to break apart complicated inputs into simpler
 ones,
 simplest inputs (so we eventually stop breaking things apart),
 to know how to put outputs together in a way that relates to
 taking inputs apart!
 Or, more pithily, we need:
 to know how to analyze inputs,
 into their atomic components,
 so that we can construct outputs.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 A Lengthy Example
 Let’s calculate the length of a list! This is a function which takes a
 list as input and gives a number as output.
 Length : Lists Numbers
 A list is something like:
 [first item, second item, third item
 Wecan break down a list like this:
 last item]
 AList = [first item, Rest of the List]
 or the list is Empty.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 A Lengthy Example
 Let’s calculate the length of a list! This is a function which takes a
 list as input and gives a number as output.
 Length : Lists Numbers
 Numbers can be built up by counting:
 0 is a number, and
 (1 +anumber) is a number.
 This is related to taking lists apart because, secretly, numbers are
 like lists of tally marks:
 4 =
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 A Lengthy Example
 Definition (Length of a List)
 The length of a list is given by the function defined by:
 Length(Empty) 0
 Length([first itemRest of List]) 1 + Length(Rest of List)
 This works because
 Empty is an atom. There are no simpler lists, so we can stop
 breaking things apart.
 The Rest of the List is simpler (i.e. smaller) than the list we
 started with. This means we eventually get to the Empty list.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Running a Recursive Program
 Wecan run a recursive program greedily:
 Every time we see something we don’t understand, we compute it.
 Length([123]) = 1+Length([23])
 =1+(1+Length([3]))
 =1+(1+(1+Length(Empty)))
 =1+(1+(1+0))
 =1+(1+1)
 =1+2
 =3
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Recursively About Everything
 This way of thinking should be familiar to you from popular ways of
 thinking about physics.
 Claim
 Physics is like a recursive function
 Physics : Systems Systems
 which recurses all the way to the fundamental particles, and then
 builds more complicated phenomena out of the way they behave.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Recursively About Everything
 Or from philosophy of language:
 Claim
 Meaning is like a recursive function
 Meaning : Utterances Meanings
 which builds the meaning of, say, sentences out of the meaning of
 words.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Recursively About Everything
 Or from sociology
 Claim
 A society is like a recursive function
 Society : Societies 
Societies
 which is determined by the behavior of individuals which are, of
 course, indivisible.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Recursively About Everything
 Or from economics
 Claim
 The economy is like a recursive function
 Economy : Markets Markets
 which is determined by the behavior of agents who act rationally.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Analysis is Recursive
 Definition
 [Analysis] might be defined as a process of isolating or
 working back to what is more fundamental by means of
 which something, initially taken as given, can be
 explained or reconstructed.– Stanford Encyclopedia
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 A Philosophical Problem
 In his book The Case for Idealism, John Foster argues that some
 things must have inscrutable, intrinsic properties.
 Foster’s argument for inscrutable intrinsic properties
 Suppose that all properties of all things were extrinsic, that is,
 defined in relation to other things.
 A)
 (B
 Now, consider a world containing two things, A and B, each
 defined only by their disposition to repel the other.
 Foster claims this leads to an infinite regress, and therefore a
 contradiction.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 A Philosophical Problem
 Foster’s argument for inscrutable intrinsic properties (cont’d)
 The back and forth must stop somewhere:
 “A is the thing which 
X”
 X is the end of the line, it is not defined in relation to anything else.
 Therefore, it is both
 inscrutable, and
 intrinsic.
 This argument rests on two (recursive) assumptions:
 1 Wemust ‘evaluate’ greedily.
 2 There must be a base case.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Do WeHaveto Make Those Assumptions?
 is there another way?
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Corecursion
 A function is corecursive when its output is determined by simpler
 outputs.
 Wecall the rules for breaking apart the output observers.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 What is Corecursion
 So, to define a corecursive function we need
 to know how to observe the output of our function in simpler
 ways,
 that relate to how we observe our inputs!
 Wecan think of the observers as being experimental setups with
 which we will test the output of our function.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 What is Corecursion
 The main idea behind corecursion is:
 If we know how our function behaves in all experimental
 setups, we know what it does.
 This is essentially the same as one of the fundamental principles
 of science:
 If we can predict how something behaves in all experimental
 setups, then we know what it is.
 So long as we believe that a function is what it does.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Stream and Chill
 Let’s have some fun with streams to get our heads around
 corecursion.
 A stream is an infinite list, so we can’t keep the whole thing in
 memory, but we can observe it piece by piece.
 So, let’s set up two experiments:
 1 Head, where we test what the first thing in the stream is.
 2 Tail, where we see what’s left.
 Now wecan define functions corecursively, since we know how to
 observe their behavior.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Stream and Chill
 Let’s define a function
 Every Other : Streams Streams
 that will take a stream and return the stream of only every other
 value. For example:
 Every Other(01234 )=(024 )
 To define this, we just need to define how it looks in all the
 experiments.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Stream and Chill
 Definition (The Every Other Function)
 Define the Every Other function by
 EO(stream)Head = streamHead
 EO(stream)Tail = EO(streamTailTail)
 This works because
 EO(stream) is covered by the observers Head and Tail, they
 tell us all we need to know about it.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Running a Corecursive Program
 Wecan’t evaluate a corecursive program greedily, because the
 calculation would never end! We have to be lazy:
 Only compute things when we absolutely need to.
 So if you wrote down
 EO((0123 ))
 That would be totally chill.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Running a Corecursive Program
 But, if we want to know a specific value of EO((0123 )), then
 we can calculate
 EO((0123 ))TailTailHead
 =EO((0123 )TailTail)TailHead
 =EO((0123 )TailTailTailTail)Head
 =(0123 )TailTailTailTailHead
 =(1234 )TailTailTailHead
 =(2345 )TailTailHead
 =(3456 )TailHead
 =(4567 )Head
 =4
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Corecursion and Diff´ erance
 If someone asks you what “EO” means, you could tell them that its
 meaning is deferred until we test it with the observers Head and
 Tail.
 If they ask you what “Head” and “Tail” mean, you could only tell
 them the different ways you end up using them.
 Definition
 Diff´ erance is Derrida’s pun on the words defer and differ.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Corecursively
 Who amI?
 How can I find out?
 Do I have to find my ‘true self’, the core of my being, to know who I
 am?
 Or do I only have to look at the way I affect the people and places
 around me?
 Thinking corecursively, we don’t have to be anxious about finding
 our true selves.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Revisiting Foster
 Let’s look back at Foster’s argument for inscrutable intrinsic
 properties. He claims that the world in which
 A only repels B and B only repels A
 cannot exist because it leads to an infinite regress.
 Only leads to infinite regress if we are greedy.
 If we are lazy, this is a perfectly fine definition.
 There is nothing inscrutable about it.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Revisiting Foster
 Foster’s argument shows a fundamental confusion that often
 underlies recursive thinking:
 the confusion between names and things
 Names are like atoms, we don’t break them apart.
 Things (such as functions) can be named, even when we
 define them corecursively.
 But that doesn’t mean that they have base cases!
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Limits of the Metaphor
 To define a function corecursively, we must cover it by observers.
 Head and Tail tell us all there is to know about a stream.
 But in the informal world, we never have access to all the contexts
 in which an object appears,
 Wecan never get all sides of the story.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Going Forward: Physics
 Physicists have been thinking corecursively for a long time:
 A physical quantity can only be assigned specific values given
 a local coordinate system, or gauge.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Going Forward: Physics
 Principle of Relativity
 The physical laws have the same form in all choices of gauge.
 A change in gauge is called a gauge symmetry.
 In other words, if we rotate our experimental setup, we get a
 rotated result.
 r(X) = r( X)
 The relationship between the observations X and r(X)
 depends on how X was rotated to r(X).
 To fully know an object, we must not only know how it behaves in
 various contexts,
 we must also know how those contexts relate.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 In Conclusion
 Thinking recursively makes us believe that
 There are basic objects and basic truths about them at the
 bottom of all phenomena, and
 To know anything at all, we need to know about these basic
 things.
 Thinking corercursively makes us believe that
 Things only make sense in context (in an experiment, relative
 to an observer, etc.), and
 Knowing how a thing behaves in context is all there is to know
 about it
 There are no basic objects or basic truths
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Bridging the Divide
 In this talk, I made a stark division between recursive and
 corecursive thinking.
 But in actually programming languages (like Haskell), you can use
 recursion and corecursion together depending on which is more
 convenient.
 Weshould use recursive and corecursive thinking together,
 depending on what needs to be done.
 But most importantly, we need to remember that metaphors matter.
 Don’t get trapped in a single metaphor
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 References I
 AndreasAbel, Brigitte Pientka, David Thibodeau, and Anton
 Setzer, Copatterns: Programming infinite structures by
 observations, SIGPLAN Not. 48 (2013), no. 1, 27–38.
 MichaelBeaney, Analysis, The Stanford Encyclopedia of
 Philosophy (Edward N. Zalta, ed.), spring 2015 ed., 2015.
 J.Rutten. C. Kupke, M. Niqui, Stream differential equations:
 concrete formats for coinductive definitions., Technical Report
 No. RR-11-10 (2011), 1– 28.
 BarryDainton, Time and space: Second edition,
 Mcgill-Queens University Press, 2010.
 DexterKozen and Alexandra Silva, Practical Coinduction,
 2014.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 References II
 J.Rutten, An introduction to (co)algebra and (co)induction.,
 Advanced topics in bisimulation and coinduction. (D. Sangiorgi
 and J. Rutten, eds.), Cambridge University Press, Cambridge,
 2011.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively









---




















Local Higher-Order Fixpoint IterationFlorian BruseSchool of Electrical Engineering and Computer ScienceUniversity of Kassel, GermanyJ¨org KreikerInstitute for Computer ScienceUniversity of Applied Sciences Fulda, GermanyMartin Lange Marco S¨alzerSchool of Electrical Engineering and Computer ScienceUniversity of Kassel, GermanyLocal ﬁxpoint iteration describes a technique that restricts ﬁxpoint iteration in function spaces toneeded arguments only. It has been studied well for ﬁrst-order functions in abstract interpretation andalso in model checking. Here we consider the problem for least and greatest ﬁxpoints of arbitrarytype order. We deﬁne an abstract algebra of simply typed higher-order functions with ﬁxpoints thatcan express ﬁxpoint evaluation problems as they occur routinely in various applications, includingprogram veriﬁcation. We present an algorithm that realises local ﬁxpoint iteration for such higher-order ﬁxpoints, prove its correctness and study its optimisation potential in the context of severalapplications.1 IntroductionFixpoints are ubiquitous in computer science. They serve to explain the meaning of recursion in pro-gramming languages [35], database queries [1], formal languages and automata theory [31]; they arebeing used as logical quantiﬁers in descriptive complexity [19] or as specialised operators, for instancein temporal logics [15], etc.Fixpoints often link a denotational and an algorithmic view onto computational problems, mostspeciﬁcally through Kleene’s Fixpoint Iteration Theorem [21]: start with the least, resp. greatest valueof the underlying lattice, and then keep applying the function under consideration until the sequencebecomes stable. This theorem provides the algorithmic foundations for many applications in which ﬁx-points play an important role. For instance in model checking, ﬁxpoint operators are used to describecorrectness properties [17], and methods based on ﬁxpoint iteration are being used to establish the sat-isfaction of such properties by models of programs [5]. Fixpoints are used in programming languagesemantics to explain the meaning of recursive programs. This extends to static analysis methods. Forinstance in the original formulation of abstract interpretation [13], collecting semantics extends programsemantics to powersets of semantic values ordered by subset inclusion [14]. Computing program proper-ties then amounts to solving ﬁxpoint equations over a number of speciﬁc (powerset) domains. Fixpointiteration also provides a standard means for the evaluation of recursive database queries [1].In many applications, the elements of the lattices in which ﬁxpoints are being sought, are functionsthemselves. In strictness analyses for functional languages [27, 11] for instance, properties under con-sideration are sets of functions. Denotational semantics is perhaps the application domain which is mosteasily seen to need lattices of functions, possibly of higher order, in order to explain the meaning of,for example, functional programs of higher order. Certain inﬁnite-state model checking problems, inparticular so-called higher-order model checking [29] are tightly linked to the evaluation of ﬁxpoints infunctions spaces as well [22].
98Local Higher-Order Fixpoint IterationWe are concerned with the problem of ﬁnding ﬁxpoints in such a lattice of functions of some higherorder. Kleene ﬁxpoint iteration in its pure form can still be employed here, but in many situations it isna¨ıve and inefﬁcient for the following reason. Suppose one is not interested in the entire ﬁxpoint f(whichis a function of some type M→N) but only in the value of this fon some particular argument x∈M.Na¨ıve ﬁxpoint iteration would start by approximating this function with the least one f0:=7→ ⊥Nthat maps anything to the least element of the lattice N, and successively compute better approximationsf1,f2,... until fi+1=fifor some i. Then it would return fi(x)which equals f(x).This procedure has then also computed values f(y)for any y∈M. It has been observed that a moreefﬁcient approach would be goal-driven and avoid the computation of fon any unnecessary argument.Note that, since fis deﬁned recursively, the value f(x)may depend on some but not all values f(y)fory∈M. The term neededness analysis was coined to describe the goal-driven evaluation of ﬁxpoints infunction lattices, avoiding the computation of function values on arguments that do not contribute to thecomputation of the one value of interest.Neededness analysis has been studied well for lattices of ﬁrst-order functions as they often arise inabstract interpretation [20]. In groundness analyses for logical programs such as [12], instead of needed-ness, one rather speaks of a ﬁxpoint computations being local [18], when a solver tries to only computethe values of as few variables as possible. Neededness analysis has also been studied in the context ofmodel checking complex program properties which cannot be described in the standard temporal logicsof regular expressivity (CTL,µ-calculus) but in extensions using predicate transformers [2]. This can beseen as a notion of on-the-ﬂy model checking for ﬁxpoints of order 1. Since “local” is also a synonym for“on-the-ﬂy” in model checking [8], we stick to the term local ﬁxpoint iteration here rather than the morecumbersome neededness analysis, when referring to a method to avoid the computation of all argumentsof ﬁxpoints which are functions themselves.In this paper we consider the applicability of local ﬁxpoint iteration in function lattices to arbitraryhigher orders. To this end, we deﬁne a simple abstract and typed higher-order ﬁxpoint algebra in Sect. 2which can be used to describe evaluation problems involving ﬁxpoints in such lattices. We then give ageneric local algorithm for evaluating ﬁxpoint terms in higher-order lattices in Sect. 3. It optimises thena¨ıve ﬁxpoint iteration method by localising the evaluation of recursively deﬁned functions at the toporder. A formal proof of its correctness is omitted due to space constraints. In Sect. 4 we present somecomputation problems which are special instances of the evaluation of higher-order ﬁxpoints in variousdomains and discuss local evaluation’s optimisation potential by comparing numbers of iteration, resp.argument computation steps on some hand-crafted examples. In Sect. 5 we brieﬂy sketch limitations tothe local approach of ﬁxpoint iteration for higher-order ﬁxpoints, in the form of obstacles to overcomewhich do not exist in the ﬁrst-order case. We conclude in Sect. 6 with an outlook onto further work inthis area.2 An Abstract Higher-Order Fixpoint AlgebraTypes and higher-order lattices. Let be some base type. Types are derived from the grammarτ::=|τv× · · · ×τv→τ,v::= +,−,±where the annotations vare called variances, and they specify the dependency of the values of a functionof typeτv11×···×τvnn→τon their arguments. In particular, if vi= +, then this dependency is monotonic,if vi=−then it is antitonic, and it vi=±then it is unspeciﬁed.
F. Bruse, J. Kreiker, M. Lange and M. S¨alzer99The order of a typeτis ord():=0 and ord(τv11× · ·· ×τvnn→τ):=max{ord(τ),ord(τ1) + 1,...,ord(τn) + 1}As usual, a function fon a partially ordered set (M,≤)is monotonic if for all x,y∈Mwith x≤ywehave f(x)≤f(y). It is antitonic if for all such x,ywe have f(y)≤f(x). A lattice is a partial order inwhich suprema and inﬁma, denoted x⊔y,x⊓y, resp. FXand dXfor any X⊆Mexist, for as long as Xis ﬁnite. A lattice is complete if these also exist for arbitrary X. Complete lattices always contain a leastand a greatest element, usually denoted ⊥and ⊤here. A ﬁnite lattice is trivially complete.Let M= (M,≤)and Mi= (Mi,≤i)for some i=1,...,nbe complete lattices. Remember thefollowing constructions on lattices:Inverse:M−:= (M,≥)where x≥yiff y≤x. For notational convenience we also let M+:=M.It should be clear that these two operations not only preserve the property of being a lattice but alsocompleteness.Flattening:M±:= (M,=), where =denotes equality as usual. Note that M±is in general not alattice anymore, let alone a complete one.Product:∏ni=1Mi:= (M1× ·· · × Mn,⊑)where (x1,...,xn)⊑(y1,...,yn)iff xi≤iyifor all i=1,...,n. The product lattice is complete if all its components are complete.Higher-order:M1→M2:= ({f:M1→M2|fis monotonic },⊑)where f⊑gif f(x)≤2g(x)forall x∈M1. The lattice of componentwise ordered monotonic functions from M1to M2is complete if M2is complete. Completeness of M1is not required, not even the property of being a lattice since ≤1is notused in the deﬁnition of ⊑.We can use these constructions to associate, with each typeτ, a complete lattice Mτ, given a com-plete lattice Minterpreting the ground type :JKM:=M,Jτv11× · · · ×τvnn→τKM:=n∏i=1(JτiKM)vi→JτKMNote that each JτKMis indeed a complete lattice given the remarks above, as the ﬂattening operation thatbreaks the lattice property is only used on the argument side of the function operator. Moreover, if Misﬁnite, then so is JτKMfor allτ.Abstract higher-order ﬁxpoint algebra. Let Mbe a complete lattice and Func ={f:τf,g:τg,...}be a set of computable and typed functions on M, possibly of higher-order. Note that ifτf=, then fis not really a function but rather a constant. For simplicity we speak of functions in this case as well.Let Var :={x:τx,y:τy,...}be a set of typed variables. We writeτx, resp.τffor the uniquelydetermined type of variable x, resp. function f. We will also simply write x∈Var instead of (x,τx)∈Varand likewise for the members of Func.Terms of the abstract higher-order ﬁxpoint algebra over Func,µHO(Func)or simplyµHO whenFunc is clear from the context, are built viaϕ,ϕ1,...,ϕn::=x|f|ϕ(ϕ1,...,ϕn)|λxv11,...,xvnn.ϕ|µx.ϕ|νx.ϕwhere x1,...,xn∈Var,f∈Func and v1,...,vn∈ {+,−,±}.A termϕis closed if it contains no free variables, where an occurrence of a variable xis free if itis not under the scope of someλ...x... orµxorνxin the syntax tree ofϕ. In the following, we aremainly interested in closed terms; others will usually only occur as subterms of these. Hence, we willoften simply speak of terms when in fact we mean closed terms at syntactic top-level.
100Local Higher-Order Fixpoint IterationΓ⊢f:τfΓ⊢ϕ:τv11× · · · ×τvnn→τΓv1⊢ϕ1:τ1... Γvn⊢ϕn:τnΓ⊢ϕ(ϕ1,...,ϕn):τv∈ {+,±}{xv,...} ⊢ x:τxΓ[xvii|i=1,...,n]⊢ϕ:τΓ⊢λxv11,...,xvnn.ϕ:τv1x1× · · · ×τvnxn→τΓ[x+]⊢ϕ:τxΓ⊢σx.ϕ:τxFigure 1: The typing rules for abstract higher-order ﬁxpoint algebra.We assume terms to be well-named, i.e. each variable is bound at most once. Clearly, any term canalways be made well-named by renaming bound variables.For better readability, we simply writeσx(yv11,...,yvnn).ϕinstead ofσx.λyv11,...,yvnn.ϕ, forσ∈{µ,ν}.In order to give terms a well-deﬁned semantics via the Knaster-Tarski Theorem, eachϕin a termµx.ϕorνx.ϕneeds to denote a function that is monotone in its argument x. Monotonicity is guaranteedfor well-typed terms, to be explained next, and then formally stated as Lemma 2.1 below. Note that thevariances are used to track information about the monotonicity or antitonicity of functions in particulararguments, and that a monotonic function can be built for instance by composing two antitonic ones.Atyping statement is a triple Γ⊢ϕ:τwhereϕis a term,τis a type, and Γis a typing contextconsisting of typing hypotheses of the form xvfor x∈Var and vbeing a variance. For a typing context Γ,let Γ+:=Γ; let Γ−result from Γby replacing in it every x+by x−and vice-versa; and let Γ±=Γ+∩Γ−,i.e. the context which only contains typing hypotheses of the form x±from Γ. The typing context Γ[xv]is obtained by removing xv′from Γfor any v′, and adding xvinstead.A termϕhas typeτif the typing statement /0 ⊢ϕ:τis derivable using the typing rules given inFig. 1. The rules are standard; they state, for instance, that in function applicationϕ(ϕ1,...,ϕn),ϕmusthave a function type with narguments which are the types of the respective argument terms. Moreover,the arguments themselves have to be typbale in the respective derived typing contexts. For example, ifϕis antitonic in its ﬁrst argument, thenϕ1has to be typable in the typing context Γ−, where Γis the contextused to type the whole application. This reﬂects the fact that an antitonic function from some lattice is amonotonic function from the inverse of this lattice (cf. the lattice deﬁnitions above and the deﬁnition ofthe semantics below). The rules for ﬁxpoint formulasσxτ.ϕrequire the termϕto be of the same type asx, since being a ﬁxpoint intuitively means x=ϕ(x), and at the same time ensure thatϕis monotonic inx. A term is well-typed, if it is of some type.Variance annotations are only used to guarantee well-typedness (and therefore the existence of ﬁx-points). We will always assume that terms are well-typed, and therefore often drop typing annotationsfor better readability. Note that for closed terms, a unique type for each subterm can easily be recovered.The semantics of terms. Let Mbe a complete lattice, and suppose that all base functions Func ={f:τf,...}have an interpretation fMin the family of higher-order lattices over Maccording to theirtypes. A termϕofµHO(Func)over Func ={f:τf,...}and a set of typed variables Var ={x:τx,...}gets interpreted in this family of lattices. In order to explain the value inductively, we need variable in-terpretationsηwhich assign values in lattices over Mto any variable with free occurrences in subterms:for each x:τx∈Var we haveη(x)∈JτxKM. The value ofϕover Mand underηis denoted JϕKMηand
F. Bruse, J. Kreiker, M. Lange and M. S¨alzer101is deﬁned inductively as follows.JxKMη:=η(x)Jϕ(ϕ1,...,ϕn)KMη:=JϕKMη(Jϕ1KMη,...,JϕnKMη)JfKMη:=fMJλxv11,...,xvnn.ϕKMη:= ( f1,..., fn)7→ JϕKMη[x17→ f1,...,xn7→ fn]Jµx.ϕKMη:=G{f∈JτxKM|JϕKMη[x7→ f]⊑f}Jνx.ϕKMη:=l{f∈JτxKM|f⊑JϕKMη[x7→ f]}The fourth clause, in particular its right-hand side, denotes the function that maps a tuple (f1,..., fn)ofobjects from Jτx1KM×· ··× JτxnKMto the value JϕKMη[x17→ f1,...,xn7→ fn]where the subscript index denotes thevariable environment that results fromηby replacing its bindings for x1,...,xnaccordingly. For the lasttwo clauses, note that the values on the right-hand side are well-deﬁned according to the Knaster-TarskiTheorem [32] since each JτxKMis a complete lattice. Note that the semantics ofµHO are easily seen tobe invariant underβ-reduction.Lemma 2.1. Let Mbe a lattice,ϕbe a term of typeτ′under the typing assumptions Γ,xv. If v = +,then JϕKMηis monotone inη(x); if v =−then JϕKMηis antitone inη(x).Proof. By a straightforward induction on the syntax tree ofϕ.Remark 2.2. Over ﬁnite lattices, each of the type lattices is ﬁnite as well. According to Kleene’s FixpointTheorem, the least and greatest ﬁxpoints of a termσx.ϕinµHO under a variable interpretationηcanbe computed by a sequence of approximations as follows: x0η=⊤τxifσ=ν,x0η=⊥τxifσ=µ, andxi+1η=JϕKMη[x7→xiη]. Then, for each ﬁnite lattice Mthere is n∈Nsuch that Jσx.ϕKMη=xnη. Moreover,these approximations are deﬁnable inµHO, independently ofη:ˆσis deﬁned by x0η=σx.x, and xi+1ηisdeﬁned by the substitution instanceϕ[xiη/x].Evaluation problems. We consider the following generic evaluation problem: given a (closed) termϕofµHO(Func)with symbols in Func interpreted in the higher-order lattices over a ﬁnite M, computeJϕKM.This problem is clearly decidable when all basic functions in Func are computable. A na¨ıve algorithmwill simply compute the value of each subterm in a bottom-up fashion using Kleene iteration to evaluateﬁxpoint expressions, and possibly storing function values as tables. Note that if Mis ﬁnite, so is JτKMfor anyτ, but the size and height of JτKMare k-fold exponential in the size, resp. height of Mwhenk=ord(τ).Even for low orders, such a na¨ıve algorithm may perform far too many unnecessary computationsteps. Consider the following special local variant of the evaluation problem: given a ﬁnite completelattice M, a closed termϕ0:=µx.ϕof typeτv→(which is then necessarily the same asτx) for somev,τ,ϕ, and a termψof typeτ, compute Jϕ0(ψ)KM.Note how this problem formulation describes a situation in which na¨ıve ﬁxpoint iteration obviouslyperforms too many evaluation steps in general: it computes Jϕ0KMusing Kleene iteration which resultsin a function of typeτv→. Depending on the order ofτ, this function is huge in terms of its argumentsbut still ﬁnite. We would then also compute JψKM. Then we obtain the value Jϕ0(ψ)KMby application,for instance through a simple look-up in the table representing Jϕ0KM, where JψKMoccurs as someargument. Clearly, the value of Jϕ0KMon all other arguments is irrelevant, and the reason for theircomputation is questionable.
102Local Higher-Order Fixpoint Iteration01234567F0:⊥⊥⊥⊥⊥⊥⊥⊥F1:⊤⊥⊥⊥⊥⊥⊥⊥F2:⊤⊥⊥⊥⊥⊤⊥⊥F3:⊤⊥⊥⊥⊥⊤⊥⊥5 0F0:⊥ ⊥F1:⊥ ⊤F2:⊤ ⊤F3:⊤3 2 1 4F0:⊥⊥⊥⊥F1:⊥⊥⊥⊥F2:⊥⊥⊥F3:⊥ ⊥F4:⊥Figure 2: Global (left) vs. local ﬁxpoint iteration for a ﬁrst-order function F.A (ﬁrst-order) example. Consider the Boolean lattice B={⊤,⊥} and the normal Boolean functionsFuncbool ={∧,∨:+×+→,¬:−→,0,1: }interpreted in the standard way. Let n>0 andϕn:=µF(x0,...,xn−1|{z }~x).null(~x)∨even(~x)∧F(half(~x))∨¬even(~x)∧F(add(add(~x,dbl(~x)),(1,0,...,0))).over Var ={x0,...,xn−1:,F:(±)n→}where, for ~x= (x0,...,xn−1)and ~y= (y0,...,yn−1),•null(~x)returns ⊤iff ~xencodes the numerical value 0, for instance null(~x) = Vn−1i=0¬xi;•even(~x)returns ⊤iff~xencodes an even number, for instance even(~x) = ¬x0;•half and dbl represent the operations “÷2” and “·2” on bit strings;•add(~x,~y)yields a bit string representing the addition of the two values modulo 2n.It is not difﬁcult to ﬁnd Boolean functions realising these operations.Intuitively,ϕndeﬁnes a search procedure. Note that any value of ~xencodes a number in the range[2n] = {0,...,2n−1}which we will simply denote~xas well. For any value ~x, deﬁne a sequence (~xk)k≥0via ~x0=~x,~xk+1=~xk/2 if ~xis even, and ~xk+1=3·~xk+1 if ~xis odd. Hence, suppose~b∈ {0,1}nencodessuch a number, thenϕn(~b)is true iff this sequence eventually hits the value 0.Let n=3. The graph on the right depicts the sequence on all values in [23]. Assuming01234567that the Boolean function ∧only evaluates its second argument when the ﬁrst one isnot ⊥, this graph suggests how local ﬁxpoint iteration of this ﬁrst-order function Fcan be more efﬁcient when the value of the ﬁxpoint Fis only needed on one particularargument. The effect of global ﬁxpoint iteration is depicted in Fig. 2 (left). Here, theiteration starts with the least function F0:7→ ⊥, and it terminates when the currentapproximation equals the last one.Local ﬁxpoint iteration on the other hand only adds the arguments successively to those tables. Con-sider the case of evaluatingϕ3(1,0,1)which means iterating the numerical series beginning at 5. First weonly tabulate the approximant F0on the value under consideration, i.e. 5. In order to compute F1(5)weneed F0(0), so 0 gets added as a new argument and receives the initial value ⊥there. Then we computeF2(5)and F1(0), and so on. The iteration stabilises when no change is being recorded anymore, thuscomputing values as they are shown in the table of Fig. 2 (middle).The effect of computingϕ3(1,1,0), i.e. beginning the numerical series at 3 is similar. Here, however,0 is never reached. Hence, all values encountered are ⊥, and the iteration stabilises when no furtherarguments are needed, as shown in Fig. 2 (right).Even though the local iteration computes a value of F4while the global one only reaches F3, itshould be clear that local evaluation performs fewer computation steps in general.
F. Bruse, J. Kreiker, M. Lange and M. S¨alzer103Remark 2.3. It is well-known that ﬁxpoint iteration does not need to record the entire history of itscomputation but, for each variable, merely the value of the last iteration. In the left table (with only oneﬁxpoint variable) in Fig. 2, this corresponds to two successive rows: the upper for the last approximatevalue of a function, and the lower for the current value. In the two tables on the right using local iteration,this corresponds to diagonals, but the picture is more complicated in general, for instance for ﬁxpointterms in which the ﬁxpoint variable has multiple occurrences.The tables shown in Fig. 2 not only give an idea of how local ﬁxpoint iteration works, their width andheight are also good measures for the space, resp. time needed to compute such a higher-order ﬁxpoint.3 Local Fixpoint Evaluation for FullµHOThe algorithm. Procedure EVAL in Alg. 1 solves the evaluation problem forµHO terms of arbitraryhigher order and ﬁnite lattices using local ﬁxpoint iteration. It takes four parameters: (1) a termϕ∈µHO(Func)over some Func. It is not necessarily of type , but the algorithm is assumed to be startedwith a full list of arguments (see below) in order to realise local ﬁxpoint iteration. (2) A ﬁnite, and, hence,complete lattice Mwith an interpretation of any f:τf∈Func as an object in JτfKM. (3) A list T1,...,Tkof arguments toϕ. The following invariant is maintained: if the type1ofϕisτ1→ ·· · →τk→, thenTi∈JτiKMfor all 1 ≤i≤k. (4) A global variable EN V that is used to interpret free variables. Valuesofλ-bound variables are stored as full functions2, values of ﬁxpoint variables may be stored as partialapproximations as described at the end of the previous section.In order to bridge the gap between a variable assignmentη, which assigns a value to each variablewhich is deﬁned at every argument, and the global variable EN V which only stores partial approximationsfor ﬁxpoint-bound variables, consider the following deﬁnition. It turns a state of EN V into a well-deﬁnedvariable assignment:ηEN V(x)(T1,...,Tk) = ENV(x)(T1,...,Tk),if xisλ-boundENV(x)(T1,...,Tk),if xis ﬁxpoint-bound and E NV(x)(T1,...,Tk)is deﬁnedˆσx,otherwiseHere, ˆσxis ⊥, resp. ⊤for variables that are bound to a least, resp. greatest ﬁxpoint.Note that, due to the invariants, a state of Alg. 1, i.e. a call of EVAL(ϕ,T1,...,Tk)with a value of ENVand over some higher-order lattice M, can be thought of as computing the object JϕKMηENV (T1,...,Tk),which is always a member of JKM. The algorithm computes this value recursively by descendingthrough the syntax tree ofϕ. Fixpoints are resolved by Kleene iteration until the semantics computedstabilises, but the value is only computed at the arguments indicated plus all those arguments that arediscovered as necessary to obtain the value for the original argument.We explain the algorithm’s functionality by considering the different cases for its argumentϕ. Uponreaching a basic function symbol, EVAL simply applies the semantics of this basic function to the argu-ments in the argument list. When EVAL reaches a variable xand the value of that variable at argument(T1,...,Tk)is deﬁned, then its value is returned. Otherwise, the variable must be ﬁxpoint-bound, andEVAL has discovered a new tuple of arguments at which the value of this ﬁxpoint is needed. This valueis initialised as ˆσx, which also registers (T1,...,Tk)in ENV. In this case the initial value is returned.1Variances are not important in this section. In order to reduce clutter, we do not display them.2This might appear wasteful at ﬁrst, butλ-bound variables are never of the highest type (by order) that occurs in the term tobe evaluated except in pathological cases, which can be eliminated byβ-reduction before calling EVAL.
104Local Higher-Order Fixpoint IterationAlgorithm 1 Neededness-based evaluation for abstract higher-order ﬁxpoint algebra.procedure EVAL(ϕ,T1,...,Tk):⊲global (partial) ENV :Var →Sx∈Var JτxKMswitchϕ:case f:return f(T1,...,Tk)case x:if ENV (x)(T1,...,Tk) = undef then E NV(x):=ENV (x)[(T1,...,Tk)7→ ˆσx]return ENV(x)(T1,...,Tk)caseλx1,...,xn.ϕ′:ENV(x1):=T1;...;EN V(xn):=Tnreturn EVA L(ϕ′,Tn+1,...,Tk)caseϕ′(ϕ1,...,ϕn):for i=1,...,ndoletτ1→ ·· · →τk′→=type(ϕi)fi:={(T′1,...,T′k′)7→ EVAL(ϕi,T′1,...,T′k′)|T′i∈JτiKM,i=1,...,n}return EVA L(ϕ′,f1,..., fn,T1,...,Tk)caseσx.ϕ′:ENV(x):={(T1,...,Tk)7→ ˆσx}repeatf:=ENV(x)for all (T′1,...,T′k)∈dom(ENV(x)) doENV(x):=ENV (x)[(T′1,...,T′k)7→ EVAL(ϕ′,T′1,...,T′k)]until f=EN V(x)return ENV(x)(T1,...,Tk)At aλ-abstraction, a number of arguments corresponding to the abstracted variables are trans-ferred from the argument list to ENV, i.e. they are now treated as bound variables. In an applicationϕ(ϕ1,...,ϕk), EVAL computes, for each argument, its full semantics by a number of recursive calls toEVAL3. The obtained values (as functions) are then added to the list of arguments.Upon reaching a ﬁxpoint binder for variable x, EVAL (re-)sets ENV(x)to the singleton deﬁnition thatinitialises the value of the ﬁxpoint at (T1,...,Tk)to the default value of ˆσx. Then, for each argument tuplethat is already discovered as necessary for the value at (T1,...,Tk), the algorithm computes a new value.Note that, during this process EVAL can reach the variable case and discover new argument tuples. Thisprocedure of updating the value at all known argument tuples is repeated until both no new argumentsare discovered for one round, and the value of the ﬁxpoint at each of the tuples agrees with that of thelast round. If this has happened, the value of the last iteration at (T1,...,Tk)is returned.Correctness. The formal correctness proof for Alg. 1 uses the following lemma which formalises theconverse of Lemma 2.1. Take a termϕthat is typed with hypotheses Γ,xv. Not only is it monotone (ifv= +), respectively antitone (if v=−) in the value of x. If the value ofϕalso differs genuinely undertwo variable interpretations that only differ in x, then xmust occur freely inϕand there are arguments tothe value of xon which this difference manifests itself. We write x⊏yto denote that x⊑ybut x6=y.Lemma 3.1. Let Mbe a ﬁnite, and hence, complete lattice,ηbe a variable interpretation, f1,f2∈Jτ′Kwithτ′=τ′1→ ·· · →τ′k→for someτ′1,...,τ′k, let T1,...,Tnbe values with Ti∈JτiKfor i =1,...,n,v∈ {+,−}, andϕbe aµHO term such that Γ,xv:τ′⊢ϕ:τv11→ ·· · →τvnn→. IfJϕKMη[x7→ f1](T1,...,Tn)⊏JϕKMη[x7→ f2](T1,...,Tn)3This can be done lazily, in case the argument is not needed or has been already computed. We omit the details for this inorder to keep the presentation simple.
F. Bruse, J. Kreiker, M. Lange and M. S¨alzer105then x appears freely inϕ, and there are T′1,...,T′nsuch thatf1(T′1,...,T′n)≷f2(T′1,...,T′n)with ≷=⊏if v = + and ≷=⊐if v =−.Proof. By induction on the structure ofϕ. Details are omitted.Next we state correctness of Alg. 1. It is not hard to imagine local ﬁxpoint iteration to be sound (forleast ﬁxpoints, resp. complete for greatest ones) since it clearly only performs part of a global ﬁxpointiteration that is sound and complete according to Kleene’s Theorem. For completeness one has to seethat the value of a ﬁxpoint function on some argument is determined solely by the value of that functionon its dependent arguments. Hence, it sufﬁces to iterate on these until stability is reached.We write EVALη(ϕ,[T1,...,Tk]) for the result of the call of Alg. 1 on Mwith argumentsϕand[T1,...,Tk]such that ENV satisﬁesη=ηEN V.Theorem 3.2. Let Mbe a ﬁnite, and, hence, complete lattice,ηbe a variable interpretation,ϕbe aterm of typeτ1→ ·· · →τk→, let T1,...,Tkbe values with Ti∈JτiKM, and EN V be such thatη=ηEN V.Then EVALη(ϕ,[T1,...,Tk]) = JϕKMη(T1,...,Tk).Proof. The proof is, again, by induction on the structure ofϕ. Details are omitted.A natural question that arises is the one after the time and space complexity of local higher-orderﬁxpoint iteration. Two aspects need to be considered here. First of all, it should be obvious that localevaluation cannot improve the worst-case. It is in fact not hard to construct examples which a ﬁxpointterm of higher-order such that its evaluation causes all argument values to be explored. Consider the(order-1) term νF(x).F((x∧♦¬x)∨(¬x∧x))(ff)with ∧,∨,¬,♦,interpreted in the usual wayknown from modal logic, over the powerset lattice induced by the Kripke structuren−1... 2 1 0Even though the term evaluates to {0,...,n−1}, local ﬁxpoint iteration will successively discover all 2narguments to the ﬁrst-order function Fbefore termination. It is also possible to extend this example toan arbitrary higher order.Second, the question after the space and time complexity of Alg. 1 cannot be answered withoutmaking assumptions on the representation of the lattice and the complexity of evaluating base functions.So far, no assumptions have been made explicitly, even though it is clear that such functions should atleast be computable for otherwise Alg. 1 would not be well-deﬁned. A reasonable assumption is thateach base function of order kcan be evaluated in time and space that is at most (k−1)-fold exponentialin the size of the underlying lattice, with 0-fold meaning polynomial and (−1)-fold meaning logarithmic.Logarithmic bounds may seem highly restrictive at ﬁrst glance, but they make sense in cases where theunderlying lattice is obtained as the powerset lattice of some other structure, see the example above. Ifthis assumption is met, then it is not too hard to see that Alg. 1 runs in time and space that is at mostk-fold exponential with kbeing the order of the input term. This also assumes that the lattice is given ina logarithmically sized representation. Otherwise, the complexity drops by one exponential.
106Local Higher-Order Fixpoint Iteration03n2n2n−1nn−11abcabc01 2 ··· 3n⊤⊥Figure 3: Directed graph Gnwith n>1 (left) for the language-constrained reachability problem exampleand corresponding base lattice (right).4 ApplicationsWe present four applications of ﬁxpoint evaluation in higher-order lattices using local ﬁxpoint evaluation,and estimate how many computation steps can be saved compared to a na¨ıve bottom-up and globalﬁxpoint iteration.Constrained reachability problems. Reachability problems – to decide whether some node is reach-able from another in a directed graph – are ubiquitous in computer science. In some applications, simplereachability is too coarse; instead one wants to put constraints on the form of path under which thetarget node can be reached from the source, for instance in terms of distance, weight, shape or allowedsequence of edges. The latter can easily be formalised as a reachability problem constrained by some for-mal language. This has been investigated thoroughly for regular [6] and context-free languages [7, 3, 24]for applications in database theory, model checking, or in static program analysis for heap-manipulatingprograms [25, 16]. Little has been done for larger classes of languages.We consider the context-sensitive language Labc :={anbncn|n≥1}over the alphabet Σ:={a,b,c},and the problem whether for some given nodes s,tof a directed, edge-labelled graph G= (V,E)thereis a path from sto twhose edge labels form a word in Labc. Reachability problems can be interpretedin powerset lattices (2V,⊆)as they can be seen as least ﬁxpoints of functions from sets of nodes to setsof nodes. However, if Gis backwards-deterministic, i.e. for all v,u,w∈V,a∈Σwe have (v,a,u)∈E∧(w,a,u)∈E⇒v=w, it is possible to formalise such problems over a smaller lattice.Consider the graph Gndepicted in Fig. 3 on the left. It contains a central state 0 and around this threeloops: an a-loop with n−1 states, a b-loop with nstates and a c-loop with n+1 states. It is backwards-deterministic. Let be interpreted by the lattice shown in Fig. 3 on the right. Intuitively, ⊥can be readas “a path from source to the target has not been found yet”, and ⊤signals that such a path has beenfound.We use base functions F:={0 : ,a,b,c:+→,ite :±×+→}as follows. The constant0 denotes the state 0. For any v∈V,a(v)is the a-predecessor of v; likewise for band c. The value is ⊥if no such predecessor exists, in particular when applied to ⊥. The value on ⊤is ⊤itself. For instance,c(0) = 3n,c(2n) = 0, c(v) = v−1 if 2n<v≤3n,c(⊤) = ⊤and c(v) = ⊥otherwise.In the powerset lattice (2V,⊆),ite could simply be interpreted as set union. However, here we
F. Bruse, J. Kreiker, M. Lange and M. S¨alzer107P→b·B(S)S→ | · SB(I)→ε|I·c·B(I)|I·b·B(S·I)bcbccc01⊤⊥n···Figure 4: Higher-order grammar Gind (left); example word (middle); lattice Mw(right).interpret it as an if-then-else in the following way. Note that it is only monotonic in its second argument.ite(x,y):=(⊤,if x=0y,otherwiseNow let Var ={f,g:+→,x:,F:(+→)±×(+→)±×±→}and consider the termϕreach :=µF(f,g,x).itef(g(x)),F(a◦f,b◦g,c(x))a,b,c(0)whereψ◦χ:=λx.ψ(χ(x)).Using ﬁxpoint unfolding andβ-reduction one can see that the value ofϕreach becomes ⊤whena(b(c(0)) = 0 or a(a(b(b(c(c(0)))))) = 0 or a3(b3(c3(0))) = 0 and so on. Hence, evaluatingϕreachsolves the reachability question “is there a path from 0 to 0 under some word in Labc ?”We analyse how much computation power is being saved when computing the answer to the questionof whether there is an Labc -path from 0 to 0 in Gn. We compare four situations arising from the use of thestandard powerset lattice vs. the optimised ﬂat lattice of Fig. 3, as well as local vs. global enumerationof all arguments to higher-order functions.Note that the three cycles in Gnhave lengths n,n+1 and n+2 respectively, which are always co-prime for each n≥2. Hence, the shortest Labc-path from 0 to 0 is the one that performs (n+1)(n+2)many rounds on the a-cycle, then n(n+2)rounds on the b-cycle and then n(n+1)rounds on the c-cycle.The following table shows the computational effort needed to evaluateϕreach in terms of the numberof arguments, resp. width of the table representing the function F. It also shows the space that is neededin order to represent one argument, i.e. a triple (f,g,x)where f,gare ﬁrst-order functions and xis alattice element. Finally, in all cases the height of the table, i.e. the number of ﬁxpoint iterations neededuntil Fstabilises, is in O(n3).powerset lattice ﬂat latticeevaluation global local global localwidth of table for F22O(n)O(n3)2O(n·log n)O(n3)size of arguments 2O(n)O(nlog n)Parsing of programming languages with indentation. Some programming languages like HASKELLor PYTHON use indentation as a syntax element. Such an effect can be described conveniently by thehigher-order grammar Gind shown in Fig. 4 (left), over the terminal alphabet Σ={b,c,}(for “block”,
108Local Higher-Order Fixpoint Iteration“code” and “space”). We refrain from deﬁning higher-order grammars [26, 34] formally, since the tech-nicalities needed for this small example are quite intuitive: B(I)generates a block of code at indentationlevel I. The block can either be empty, contain one line followed by the rest of this block at the sameindentation level, or start a new block at a greater indentation level. The symbol Sis used to generate asequence of space characters ‘ ’. Finally, Pgenerates a program as a block at some initial indentationlevel. An example word, generated from this grammar and formatted in order to visualise indentationbest, is shown in the middle of Fig. 4.Suppose a word w=a0...an−1∈Σ∗is given. This gives rise to an interpretation of the symbols inthe grammar above as follows. Let Mwbe the lattice shown in Fig. 4 on the right. We use the followingbase functions, derived from the terminal symbols and the constructors in the higher-order grammar.Funcind ={b,c, ,ε:±×±→|{z }τ,·,|:τ+×τ+→τ,start,end :}Intuitively, b,c,andεare used to mark sections of the word in which the corresponding symbols(resp. the empty word) occur. The top and bottom element of the lattice are used to signal true/false. Theinterpretation of these functions is therefore simplyε(i,j) = (⊤,if i=j⊥,otherwise and a(i,j) = (⊤,if i+1=jand ai=a⊥,otherwisefor a∈Σ. The two constructors |and ·denoting disjunctive choice and concatenation in the higher-ordergrammar are interpreted as follows.(f|g)(i,j) = (⊤,if f(i,j) = ⊤or g(i,j) = ⊤⊥,otherwiseand(f·g)(i,j) = (⊤,if there is hs.t. f(i,h) = ⊤=g(h,j)⊥,otherwiseFinally, we need two constants start and end which are interpreted as 0 and n, respectively.The nonterminals in the higher-order grammar can be seen as (ﬁxpoint) variables, hence we haveVar ={P,S,I:τ,B:τ+→τ}. Then Gind immediately becomes a second-order term ofµHO overFuncind and Var, since recursion in grammars is captured by least ﬁxpoints. The problem of evaluat-ing P(start,end)over Mwis then equivalent to parsing ww.r.t. Gind.Clearly, the space and time needed to evaluate P(start,end)is dominated by the ﬁxpoint iteration forBas the only second-order variable. The number of possible arguments to it is 2O(n2). Local ﬁxpointiteration only discovers a fraction of these, though. Note that Bis initially evaluated on S, and – whenthe recursive is called on argument I– it needs the values of Bon Iitself as well as on S·I. Hence, itonly ever discovers S,S2,S3,.... Moreover, it is not hard to see that Skmaps two positions (i,j)of anunderlying word w=a0...an−1to ⊤, if j−1−i≥kand ah=for h=i,..., j−1. Hence, the numberof possible arguments to Bdiscovered in this way is bounded by n−1 and so, again, local higher-orderﬁxpoint iteration realises an exponential reduction in space complexity in this example.Model checking Higher-Order Fixpoint Logic. Fixpoints play a fundamental role in model checking,where properties of the runtime behaviour of programs are typically expressed in temporal logics, the
F. Bruse, J. Kreiker, M. Lange and M. S¨alzer109most prominent of which are LTL and CTL. Fixpoints are used there to express limit behaviour as inreachability, safety and fairness [17]. The true power of ﬁxpoints is unleashed in logics that extendmodal logic by extremal ﬁxpoint quantiﬁers like the well-known modalµ-calculus [23]. A lesser knownextension of this is Higher-Order Fixpoint Logic (HFL) [33], a highly expressive speciﬁcation logic thatmixes modal logic, a typedλ-calculus and ﬁxpoint quantiﬁers. Its model checking problem is decidableover ﬁnite transition systems, albeit k-fold exponential in the order of involved function types [4, 10].We refer to the literature for a self-contained deﬁnition of HFL [33]. With the preliminary work onabstract higher-order ﬁxpoint algebra in Sect. 2 we can simply present HFL as a special instantiation ofthis algebra. The base type is interpreted as the powerset lattice (2S,⊆)of the state set of a transitionsystem with edge labels from some set Aand propositional labels from some set P. This gives rise toan interpretation of all higher-order types as functions on sets of states; a function of type +→forinstance is known as a (monotonic) predicate transformer.The set of ground functions then is F:={∧,∨:+×+→,¬:−→} ∪ {hai,[a]:+→|a∈A} ∪ {p:|p∈P}reﬂecting the Boolean, modal and propositional parts of the logic. Well-typedterms of the higher-order ﬁxpoint algebra over this Fare exactly the formulas of HFL; and the standardsemantics is the same as the one derived from the generic semantics in Sect. 2 for this set of terms.Consider the following formula describing the propertyϕ=“there is an inﬁnite b-path such that thei-th node on this path is the start of an a-path of length 2iending in a p-node, for any i≥0”, as well asthe family of transition systems Tnon the right.ϕ:=νF.λf.(f p)∧hbi(F(f◦f))(λx.haix)0p1 2 ··· n−1aba,baabaaaabwhereϕ◦ψ:=λx.ϕ(ψx), over Var ={x:,f:+→,F:(+→)+→}. We use Fin thefollowing to abbreviate the subformulaνF.λf.(f p)∧ hbi(F(f◦f)).Only state 1 satisﬁesϕ. Now note that Fis a second-order ﬁxpoint taking as arguments a terminterpreted as a ﬁrst-order function of the kind 2[n]→2[n]. Hence, even for n=2, there already are 256of them, and na¨ıve ﬁxpoint iteration would tabulate all of them ﬁrst before computing the values of Fon them. On the other hand, all that is needed is F’s value on functions hhaii2iwhere hhaii(S) = {t∈[n]| ∃t∈[n]s.t. sa−→t}. The following puts the number of such different functions which are beingdiscovered by local ﬁxpoint iteration in relation to the number of otherwise possible function argument.n2 3 4 5 6 7 . . .possible arguments to F256 1.6·1061.8·1019 1.5·1048 3.9·10115 ... ... ...discovered in local iteration 2 2 2 3 3 4 . . .The numbers can be veriﬁed either through manual computation of the functions hhaii2ifor i=0,1,...on each Tnor using the implementation of Alg. 1 mentioned in the conclusion below.Abstract interpretation of functional languages. Strictness analysis for (lazy) functional languagestries to ﬁgure out whether an argument to a function must always be evaluated. In this case compilersmay force the evaluation of the argument thus saving space and time to create closures and allowingfor parallelisation. Strictness analysis may be formulated as an abstract interpretation as e.g. in [11]. Afunction f:D×D×· · ·× D→Dis strict in its i-th argument, when f(d1,d2,...,di−1,⊥D,di+1,...dk) =⊥Dfor a concrete base domain D. As this may be uncomputable, in [11], functions are interpretedabstractly over the domain 2:={0,1}(with 0⊑1), where 0means deﬁnitely undeﬁned, and where 1means might be deﬁned. Examples of abstract interpretations of common base values are (for x,y,z∈2),
110Local Higher-Order Fixpoint Iteration•constants of base domains such as integers or boolean values are abstracted to 1(not undeﬁned);•ﬁrst-order functions such as addition are strict in all arguments, e.g., x+y=0unless x=y=1;•if-then-else: ite(x,y,z) = x∧(y∨z), where elements of 2are read as boolean values. If-then-elsemight only be deﬁned if both the condition and at least one of the then-else arguments might bedeﬁned. Otherwise it is deﬁnitely undeﬁned. Note that, in this example, we use ite in the traditionalsense of functional programming.As an example of the application of EVAL to the abstract interpretation of functional languages, wechoose F:={ite :+×+×+→}and Var ={x:,f:+→,p:+→,I:(+→)+×(+→)+×+→}. Consider the termϕ:=µI(f,p,x).ite(p(x),I(f,p,f(x)),x). It essentiallydescribes an iterated application of some function funtil a predicate pholds. In order to show thatϕis strict in xfor given functions f0and p0, one needs to evaluateϕ(f0,p0,0)by ﬁxpoint unfoldingandβ-reduction. If p0(0) = 0, that is, the termination predicate is itself strict, then EVAL terminates inone step proving strictness ofϕin its third argument. If p0(0) = 1, that is, pis essentially a constanttrue or constant false predicate, we need to evaluate 1∧(ϕ(f0,p0,f0(0)) ∨0) =ϕ(f0,p0,f0(0)) next. Iff0(0) = 0, that is, f0is strict itself, we have reached a ﬁxpoint and can conclude strictness in xas well.If, however, f0(0) = 1, we obtain an overall result of 1, not showing strictness in x. This is plausiblefor constant functions fand p. Using local iteration this is in fact the only computation that takesplace, whereas a na¨ıve global ﬁxpoint computation would start by tabulating all possible triples of type(+→)+×(+→)+×+, which, for the lattice 2, amounts to 4 ·4·2=32 table columns.5 Limitations of Neededness Analysis and OptimisationAs mentioned in the introduction to Sect. 3, Algorithm EVAL does not use local evaluation on operand-side subterms but rather computes their value fully. If such an operand has a function type, its value on allits arguments might not be needed either. Consider the ﬁrst example from Sect. 4 about formal-languageconstrained reachability problems. Clearly, the values of the order-1-functions stored in the parametersfand gare not needed at most arguments. Hence, computing their value fully appears to be wasteful.Algorithm EVAL computes values of operand-side subterms fully due to the termination criterionfor the computation of ﬁxpoint terms: iteration stops when both no new argument tuples have beendiscovered during a round of the repeat-loop computing the semantics, and the value of the ﬁxpointin question is stable on all existing tuples. This, of course, requires some way of deciding whether adiscovered argument is actually new. Going back to the example in Sect. 4, Algorithm EVAL successivelydiscovers the argument tuples [a,b,c(0)],[a2,b2,c2(0)],... Eventually, these argument tuples begin torepeat, which is when the loop terminates. However, deciding whether e.g. [ai,bi,ci(0)] is the sameargument tuple as [aj,bj,cj(0)] requires knowing the value of the function type arguments at all theirarguments. One could assume that it is enough to know just their value on arguments actually neededin the iteration, but this approach fails readily: already for [a,b,c(0)] and [a2,b2,c2(0)], for n≥2, wesee that ci(0) = 3n−(i+1)for i∈ {1,2}, whence ai(bi(ci(0))) = ⊥for either i, and, in fact, all i≤n(n−1)(n−2), since these differ on hitherto undiscovered arguments. Hence, any algorithm that testsequality of function type arguments only on tuples already identiﬁed as necessary for the computationmust fail here. Moreover, since the base functions a,b,care actually interpreted, instead of e.g. treeconstructors as in the case of higher-order model checking, a simple ﬂow analysis (e.g. 0-CFA) fails todetect which functions are duplicates unless one also inspects the behavior of the base functions. Hence,safe approach to avoid the error sketched above is to compute values of argument-side functions – whichare necessarily not of the highest type order occurring in the term under consideration – in full.
F. Bruse, J. Kreiker, M. Lange and M. S¨alzer111However, this does not mean that this is always necessary. In the example from Sect. 4, one canreadily see that the value of e.g. fwill always be ⊥on all arguments that are not in {0,...,n−1}, sincefonly contains powers of a. This kind of domain-speciﬁc approach, together with e.g. ﬂow analysis andthe choice of an appropriate lattice, could be used to cut down the amount of computations necessary.6 ConclusionWe have lifted the notion of local ﬁxpoint iteration, resp. neededness analysis, for the evaluation ofﬁrst-order ﬁxpoint functions to ﬁxpoint functions at arbitrary higher order. For generality purposes wehave deﬁned an abstract algebraµHO combining a simply typedλ-calculus over (possibly higher-order)base functions with ﬁxpoints at arbitrary type orders. The examples in Sect. 4 show that this can vastlyreduce the number of values that are being computed in ﬁxpoint iterations, compared to the na¨ıve globalapproach.A conceptual implementation ofµHO and Alg. 1 is available.4It does not compete with specialisedtools like higher-order model checkers but rather focuses on displaying the effect that local ﬁxpointiteration has in comparison to global iteration for higher-order ﬁxpoints.Work on ﬁxpoint iteration for higher-order functions can be continued in several directions. The mostpressing issue is an extension to fully local ﬁxpoint iteration, which would also employ local evaluationat orders beneath the top one, bearing in mind the obstacles to overcome which have been discussed inSect. 5. Signiﬁcant progress on this front likely requires giving up the full genericity of the algorithm. Forexample, many intersection-type based HORS model checkers (e.g. [9, 30]) require backwards reasoningalongside the base functions. For example, acceptance of an automaton in a node of a tree depends on itschildren, i.e. the arguments to the tree constructor in question, and the relationship is readily available.Conversely, in the present form, our algorithm makes no assumptions on the (behavior of) the basefunctions, whence it can not infer which values of a given argument might yield a desired function value.The acute reader may have wondered whyµHO does not feature operators ⊔,⊓for suprema andinﬁma at arbitrary types. It would in fact be possible to add these, and algorithm EVAL can be extendedaccordingly to handle them just like other base functions are being handled. They are not included in thesyntax ofµHO here for the following reason: when ⊔,⊓are present in the syntax one would expect thedistributivity laws likeϕ⊔(ψ⊓χ)≡(ϕ⊓ψ)⊔(ϕ⊓χ)to hold. But in arbitrary lattices, such laws donot necessarily hold; they only do in distributive latttices. In order not to confuse the issue or make falseassumptions we therefore prefer to introduce ⊔,⊓as base functions when necessary and appropriate.This prevents us from restricting the semantics ofµHO terms to distributive lattices only. Note that thelattices depicted in Figs. 3 and 4 are not distributive.Algorithm EVAL makes no assumptions on the order in which needed arguments are evaluated. Indata ﬂow analyses, giving precedence to the arguments in the form of heuristics has turned out to bebeneﬁcial for efﬁciency purposes, c.f. [28, Chp. 6]. It remains to be seen whether such heuristics can beextended to higher orders as well.Most static program analyses in abstract interpretation work with rather rich lattices as base domainswhich cannot be cast into the scheme of a simply typedλ-calculus over a single base type as it is usedhere. We remark, though, that an extension to a many-sorted logic over several base types is straight-forward, not only regarding the type system but, most importantly, algorithm EVAL. The same holds forproduct types on the right of function arrows. It then remains to be seen how far the type system can beenriched without seriously interfering with the ability to evaluate higher-order ﬁxpoints locally.













---






---





































 Towards third generation HOTT
 Part 3: Univalent universes
 Michael Shulman
 University of San Diego
 joint work with Thorsten Altenkirch and Ambrus Kaposi
 CMU HoTT Seminar
 May 12, 2022
The story so far
 Plan for the three talks:
 1 Basic syntax of H.O.T.T.
 2 Symmetries and semicartesian cubes
 3 From semicartesian cubes to univalent universes
Outline
 1 The magic of semicartesian cubes
 2 Paths in exponentials
 3 The parametricity universe
 4 The universe of brant types
 5 Cubical spaces
 6 Explaining the universe
The semicartesian cube category
 The semicartesian cube category has, as objects, nite sets.
 A morphism 
(mn) is a function : n m
 +
 that is injective on the preimage of m.
 The symmetric monoidal structure m n is disjoint union.
 The automorphisms of n are the symmetric group Sn.
 The presheaf category = Set op has a Day convolution
 monoidal structure. Write n for the representable ( n).
 A-enriched category with n-powers has ID-structure:
 1 
x : Ay :A IdA(x y):U
 A
 A A
Cubical paths and cylinders
 Let E be a presheaf category (such as Set). The category E op of
 cubical objects is-enriched with copowers and powers:
 (KMapE op(XY)) = E op(K XY)=E op(XK Y)
 In particular, it has path spaces 1 X and cylinders 1 X.
 Path spaces are de ned by shifting, while cylinders are magic:
 ( 1 X)n=Xn 1
 ( 1 X)n=Xn+Xn+
 Xn k
 k n
 =2 Xn+n Xn 1
 Almost no other cube category satis es the magic cylinder formula;
 we need symmetries but no diagonals or connections.
The magic of semicartesian cylinders
 For example:
 ( 1 X)2=X2+X2+X1+X1
The magic of semicartesian cylinders
 For example:
 ( 1 X)2=X2+X2+X1+X1
The magic of semicartesian cylinders
 For example:
 ( 1 X)2=X2+X2+X1+X1
The magic of semicartesian cylinders
 For example:
 ( 1 X)2=X2+X2+X1+X1
The magic of semicartesian cylinders
 For example:
 ( 1 X)2=X2+X2+X1+X1
The magic of semicartesian cylinders
 For example:
 ( 1 X)2=X2+X2+X1+X1
 2
 1
 1
 2
 2
 1
 2
 1
The magic of semicartesian cylinders
 For example:
 ( 1 X)2=X2+X2+X1+X1
 2
 1
 1
 2
 2
 1
 2
 1
Amazing right adjoints
 Since the path-space is shifting, ( 1 X)n = Xn 1, it preserves all
 colimits, hence has an ( amazing ) right adjoint
 E op( 1 XY)=E op(X Y)
 This also has a berwise version:
 Y
 1 
W
 YW Y
 W
 1 
W
 The berwise version maps E op ( 1 W) to E opW.
Outline
 1 The magic of semicartesian cubes
 2 Paths in exponentials
 3 The parametricity universe
 4 The universe of brant types
 5 Cubical spaces
 6 Explaining the universe
Identity types of exponentials
 For cartesian cubes, powers coincide with cartesian exponentials. So
 1 
(A B)=A ( 1 B),andIdA B(f g)= (x:A)IdB(fx gx).
 exponential A 
In the semicartesian case, we need to relate the cartesian
 B with the monoidal path-space ( 1 
our desired rule
 ). To get
 IdA B(f g) = (u:A) (v:A) (q:IdA(uv))IdB(f (u) g(v))
 we want a pullback in E op:
 1 
(A B) ( 1 A) ( 1 B)
 (A B) (A B) (( 1 A) B) (( 1 A) B)
Identity types of exponentials
 We want a pullback in E op:
 1 
(A B) ( 1 A) ( 1 B)
 (A B)2 (( 1 A) B)2
Identity types of exponentials
 By Yoneda, we want a pullback in Set for all X E op:
 E op(X 1 (A B)) E op X ( 1 A) ( 1 B)
 E op(X (A B)2) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(X 1 (A B)) E op X ( 1 A) ( 1 B)
 E op(X (A B)2) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op( 1 XA B) E op X ( 1 A) ( 1 B)
 E op(X (A B)2) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op( 1 XA B) E op X ( 1 A) ( 1 B)
 E op(X (A B)2) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op X ( 1 A) ( 1 B)
 E op(X (A B)2) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op X ( 1 A) ( 1 B)
 E op(X (A B)2) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op X ( 1 A) ( 1 B)
 E op(2 XA B) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op X ( 1 A) ( 1 B)
 E op(2 XA B) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op X ( 1 A) ( 1 B)
 E op((2 X) AB) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op X ( 1 A) ( 1 B)
 E op((2 X) AB) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op X ( 1 A) 1 B
 E op((2 X) AB) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op X ( 1 A) 1 B
 E op((2 X) AB) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op 1 (X ( 1 A))B
 E op((2 X) AB) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op 1 (X ( 1 A))B
 E op((2 X) AB) E op(X (( 1 A) B)2)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op 1 (X ( 1 A))B
 E op((2 X) AB) E op(2 X ( 1 A) B)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op 1 (X ( 1 A))B
 E op((2 X) AB) E op(2 X ( 1 A) B)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op 1 (X ( 1 A))B
 E op((2 X) AB) E op((2 X) ( 1 A)B)
Identity types of exponentials
 Now we apply universal properties...
 E op(( 1 X) AB) E op 1 (X ( 1 A))B
 E op((2 X) AB) E op((2 X) ( 1 A)B)
 And now, by Yoneda again...
Identity types of exponentials
 We equivalently want a pushout in E op:
 (2 X) ( 1 A) 1 (X ( 1 A))
 (2 X) A ( 1 X) A
Identity types of exponentials
 Which means a pushout in E for all n:
 ((2 X) ( 1 A))n ( 1 (X ( 1 A)))n
 ((2 X) A)n (( 1 X) A)n
Identity types of exponentials
 Which means a pushout in E for all n:
 ((2 X) ( 1 A))n ( 1 (X ( 1 A)))n
 (2 X)n An (( 1 X) A)n
Identity types of exponentials
 Which means a pushout in E for all n:
 ((2 X) ( 1 A))n ( 1 (X ( 1 A)))n
 2 (Xn An) (( 1 X) A)n
Identity types of exponentials
 Which means a pushout in E for all n:
 (2 X)n ( 1 A)n ( 1 (X ( 1 A)))n
 2 (Xn An) (( 1 X) A)n
Identity types of exponentials
 Which means a pushout in E for all n:
 2 (Xn An+1) ( 1 (X ( 1 A)))n
 2 (Xn An) (( 1 X) A)n
Identity types of exponentials
 Which means a pushout in E for all n:
 2 (Xn An+1) ( 1 (X ( 1 A)))n
 2 (Xn An) ( 1 X)n An
Identity types of exponentials
 Which means a pushout in E for all n:
 2 (Xn An+1) ( 1 (X ( 1 A)))n
 2 (Xn An) (2 Xn+n Xn 1) An
Identity types of exponentials
 Which means a pushout in E for all n:
 2 (Xn An+1) ( 1 (X ( 1 A)))n
 2 (Xn An) 2 (Xn An)+n (Xn 1 An)
Identity types of exponentials
 Which means a pushout in E for all n:
 2 (Xn An+1) ( 1 (X ( 1 A)))n
 2 (Xn An) 2 (Xn An)+n (Xn 1 An)
 ( 1 (X ( 1 A)))n
 =2 (X ( 1 A))n+n (X ( 1 A))n 1
 =2 (Xn ( 1 A)n)+n (Xn 1 ( 1 A)n 1)
 =2 (Xn An+1)+n (Xn 1 An)
Identity types of exponentials
 Which means a pushout in E for all n:
 2 (Xn An+1) 2 (Xn An+1)+n (Xn 1 An)
 2 (Xn An) 2 (Xn An)+n (Xn 1 An)
 ( 1 (X ( 1 A)))n
 =2 (X ( 1 A))n+n (X ( 1 A))n 1
 =2 (Xn ( 1 A)n)+n (Xn 1 ( 1 A)n 1)
 =2 (Xn An+1)+n (Xn 1 An)
Identity types of exponentials
 2 (Xn An+1) 2 (Xn An+1)+n (Xn 1 An)
 2 (Xn An) 2 (Xn An)+n (Xn 1 An)
 But this is just a coproduct of two pushout squares:
 2 (Xn An+1) 2 (Xn An+1)
 2 (Xn An) 2 (Xn An)
 n (Xn 1 An)
 n (Xn 1 An)
 Thus, it is a pushout, completing the proof of our desired rule
 IdA B(f g) = (u:A) (v:A) (q:IdA(uv))IdB(f (u) g(v))
 The same ideas work for dependent types and for-types.
Outline
 1 The magic of semicartesian cubes
 2 Paths in exponentials
 3 The parametricity universe
 4 The universe of brant types
 5 Cubical spaces
 6 Explaining the universe
Paths in the universe
 If U classi es small maps, then
 E op(X 1 U)=E op( 1 XU)
 so 1 U classi es small maps over cylinders.
 By extensivity , a map Y 
coproduct too:
 Yn
 =
 ( 1 X)decomposes Yn as a
 An +Bn + k nCnk
 ( 1 X)n Xn+Xn+ k nXn k
 =
Cubesovercylinders
 X2 X2
 X1
 X1
Cubesovercylinders
 X2 X2
 A2 B2
 X1
 X1
Cubesovercylinders
 X2 X2
 X1
 C20
 X1
Cubes over cylinders
 X2
 C21
 X1
 X1
 X2
Cubesovercylinders
 X2 X2
 X1
 C20
 X1
 C21 =
Cubesovercylinders
 X2 X2
 X1
 C20
 X1
 A1 B1
Cubical operators over cylinders
 Let 
(mn), so : n m
 +.
 An +Bn + k nCnk Am+Bm+ mCm
 Xn +Xn + k nXn k Xm+Xm+ mXm
 Any preserves the rst two summands, so we have
 AB E op with maps A X and B X.
 Sn 
(k) = 
(nn) permutes the summands Xn k , and its
 subgroup Sn k acts on Xn k . Thus, Cnk = Cnk k k.
 If 
m, then maps Xn k to Xm . These
 (k) 
assemble the Cnk into C E op with a map C X.
 If 
+, then maps Xn k to one of the rst Xm s.
 These assemble into maps C 
A and C B over X.
Cubical operators over cylinders
 Let 
(mn), so : n m
 +.
 An +Bn + k nCnk Am+Bm+ mCm
 Xn +Xn + k nXn k Xm+Xm+ mXm
 Any preserves the rst two summands, so we have
 AB E op with maps A X and B X.
 Sn 
(k) = 
(nn) permutes the summands Xn k , and its
 subgroup Sn k acts on Xn k . Thus, Cnk = Cnk k k.
 If 
m, then maps Xn k to Xm . These
 (k) 
assemble the Cnk into C E op with a map C X.
 If 
+, then maps Xn k to one of the rst Xm s.
 These assemble into maps C 
A and C B over X.
Cubical operators over cylinders
 Let 
(mn), so : n m
 +.
 An +Bn + k nCnk Am+Bm+ mCm
 Xn +Xn + k nXn k Xm+Xm+ mXm
 Any preserves the rst two summands, so we have
 AB E op with maps A X and B X.
 Sn 
(k) = 
(nn) permutes the summands Xn k , and its
 subgroup Sn k acts on Xn k . Thus, Cnk = Cnk k k.
 If 
m, then maps Xn k to Xm . These
 (k) 
assemble the Cnk into C E op with a map C X.
 If 
+, then maps Xn k to one of the rst Xm s.
 These assemble into maps C 
A and C B over X.
Cubical operators over cylinders
 Let 
(mn), so : n m
 +.
 An +Bn + k nCnk Am+Bm+ mCm
 Xn +Xn + k nXn k Xm+Xm+ mXm
 Any preserves the rst two summands, so we have
 AB E op with maps A X and B X.
 Sn 
(k) = 
(nn) permutes the summands Xn k , and its
 subgroup Sn k acts on Xn k . Thus, Cnk = Cnk k k.
 If 
m, then maps Xn k to Xm . These
 (k) 
assemble the Cnk into C E op with a map C X.
 If 
+, then maps Xn k to one of the rst Xm s.
 These assemble into maps C 
A and C B over X.
Typefamiliesovercylinders
 Thus, fromY ( 1 X),weextractaspaninE op X:
 A C B
 X
 Theorem
 ForX E op,wehaveanequivalenceofcategories
 E op ( 1 X) (E op X)( )
 (AlsogeneralizestomanyotherK replacing 1.)
Identitytypesof theparametricityuniverse
 Corollary
 ThereisaU0 E op thatclassi essmallmaps,andsuchthatthere
 isatrivial bration(i.e.amapwithRLPagainstmonos)
 ( 1 U0) (AB:U0)(A B U0)
 This isnotanisomorphism: theisomorphiccopiesCnkhavetobe
 classi edseparately.
Identitytypesof theparametricityuniverse
 Corollary
 ThereisaU0 E op thatclassi essmallmaps,andsuchthatthere
 isatrivial bration(i.e.amapwithRLPagainstmonos)
 ( 1 U0) (AB:U0)(A B U0)
 This isnotanisomorphism: theisomorphiccopiesCnkhavetobe
 classi edseparately.
 Trivial brationshavesections, soweinterpretasyntacticretraction
 (A B U0) IdU0(AB) (A B U0) p p
 Thus withU0modelsatheoryof internalparametricity,whose
 identitytypes consistofarbitrarycorrespondences.
Outline
 1 The magic of semicartesian cubes
 2 Paths in exponentials
 3 The parametricity universe
 4 The universe of brant types
 5 Cubical spaces
 6 Explaining the universe
Towards a universe of brant types
 Wed like to de ne U to be
 the subtype of U0 whose identity type
 correspondences are one-to-one.
 For any span A C B, i.e. correspondence C : A B U0,
 we have the type of assertions that it is one-to-one:
 is11(C) :
 (a:A)isContr( (b:B)C(ab))
 (b:B)isContr( (a:A)C(ab))
 If ABC lie in a slice E op X, so does is11(C) E op X.
The universal correspondence
 We pull back the universal type family along the adjunction counit:
 1 
U0
 ( 1 U0) U0
 This yields a type family over the cylinder 1 ( 1 U0), hence a
 universal correspondence over 1 U0:
 A0 C0 B0
 1 
U0
 Thus we have the classifying object is11(C0) E op ( 1 U0).
 BUT: this is a predicate on 1 U0, not U0 itself.
A rst-order approximation
 We can x this with the berwise amazing right adjoint:
 U1 =
 Theorem
 is11(C0)U0
 The classifying map 
U0 of a type family 
P : U0 lifts to U1
 if and only if the correspondence Id P is one-to-one.
 P : U0 lifts to U1
 if and only if the correspondence Id P is one-to-one.
A rst-order approximation
 We can x this with the berwise amazing right adjoint:
 U1 =
 Theorem
 is11(C0)U0
 The classifying map 
U0 of a type family 
P : U0 lifts to U1
 if and only if the correspondence Id P is one-to-one.
 BUT: This correspondence is still U0-valued: even for 
Id P :P[ ] P[ ] U0
 So we cant consistently use U1 as the universe.
 P : U1,
A second-order approximation
 For any A C B wehave a type classif(CU1) of U1-valued
 classifying maps for C, i.e. pullback squares
 C U1
 A B U1
 Then we de ne a further improved universe:
 U2 =
 The identity types of 
is11(C1) classif(C1U1)U1
 P : U2 are one-to-one and U1-valued.
A limit construction
 We continue inductively and take a limit:
 Un+1 = 
is11(Cn) classif(CnUn)Un
 U =lim n
 (
 Theorem
 Un 
U1 U0)
 The identity types of 
P : U are one-to-one and U-valued.
 U classi es maps with contractible spaces of uniform Kan llers.
Higher coinduction for IdU
 .
 U is a higher coinductive type: the terminal coalgebra of a functor
 involving 
Its higher destructors assemble into
 : IdU(AB) 1-1-Corr(AB)
 The magic cylinder formula implies a formula for paths in 
Thus, IdU is also a higher coinductive type.
 By higher coinduction (univ. prop. of lim and 
: 1-1-Corr(AB) 
such that p
 Even if 
p.
 for U0 were an isomorphism, this wouldnt be:
 IdU contains more data than 1-1-Corr.
 .
 ) we de ne
 IdU(AB)
Fibrancy of type-formers
 We lift all the type-formers from U0 to U by higher coinduction.
 E.g. for-types:
 (A:U)(A 
(A:U0)(A 
We must show that:
 U) U
 0
 U0) U0
 takes identi cations to one-to-one correspondences.
 These correspondences are isomorphic to some-type.
Strictifying identity types
 This amounts to specifying the computation rules for ap and Id :
 apXY (x:X)Y(x)(A2B2) (IdA2B2
 XY (x:X)Y(x)
 Id
 (x:A)B(s t) 
)
 (q:Id A( 1s 1t))Id q
 (
 x:A)B( 2s 2t)
 such that the latter equality holds up to isomorphism for powers
 ( 1 ) inE op.
 This works because the identity types of a-type are another-type (and similarly for all other type-formers).
 This is the coherence theorem strictifying Id = to a
 de nitional equality.
Conclusion: cubicaluniverses
 Theorem-in-progress
 H.O.T.T.hasamodel inE op, foranypresheaf toposE.
 Inparticular, ithasamodel in .
 Conjecture
 Bygluingwithaglobal-sectionsornervefunctorvaluedin or
 presheavesthereof,wecanprovecanonicityandnormalization.
 Notethat
 1Wemusthavesymmetryin , tointerpret Id andIdU.
 2Wemusthavesymmetryinsyntax, forthenervetoliein .
Outline
 1 The magic of semicartesian cubes
 2 Paths in exponentials
 3 The parametricity universe
 4 The universe of brant types
 5 Cubical spaces
 6 Explaining the universe
Towards higher topos models
 Symmetry solves syntactic problems, but creates semantic ones:
 The syntax-like model in doesnt present classical homotopy.
 There could be an equivariant version. But theres another way.
 Two approaches to de ning higher homotopical structures:
 1 As diagrams of sets
 E.g. quasicategories
 More parsimonious
 2 As diagrams of spaces
 E.g. complete Segal spaces
 Often better-behaved
Cubical spaces
 LetEbeatype-theoreticmodelpresheaftopos,e.g.:
 E=sSet, simplicial sets,withtheKanmodel structure
 (presentsthehomotopytheoryofspaces).
 E=simplicialpresheaves,withaleftexact localizationofthe
 injectivemodel structure(presentsan( 1)-topos).
 Theorem(cf.RezkSchwede Shipleyforthesimplicialversion)
 Theinjectivemodel structureonE op admitsaleftBous eld
 localization,calledtherealizationmodel structure, suchthat:
 1 It isQuillenequivalenttoE.
 2 It isalsoatype-theoreticmodel topos.
 (Thoughnotaleftexact localizationoftheinjectiveone.)
Theuniverseof realization brations
 Theorem
 IfU0rlz classi esrealization brations,andUrlz=limnUnrlzas
 before, thereisatrivial bration
 ( 1 Urlz) (AB:Urlz)1-1-Corr(AB)
 Corollary
 Therealizationmodel structureinterpretsallofH.O.T.T.
 Thus,H.O.T.T.hasmodels inallGrothendieck( 1)-toposes.
Why IdU has no-rule
 1 IdU0(AB) is not isomorphic to A B U0.
 2 IdU contains higher destructors in addition to 1-1-Corr.
 3 Injective bration structures over a cylinder contain more data
 than those on a span.
 4 Homotopical constancy structures over a cylinder contain more
 data than those on a span.
 5 Syntactically, IdU must contain additional sym data.
Outline
 1 The magic of semicartesian cubes
 2 Paths in exponentials
 3 The parametricity universe
 4 The universe of brant types
 5 Cubical spaces
 6 Explaining the universe
What kind of type is the universe?
 Traditionally, the universe is thought of (informally) as inductively
 de ned, with constructors 
, and Tarski eliminator El
 de ned by recursion.
 Not true internally, but informs meaning explanations and
 inductive-recursive universe constructions.
 An observational Id would also be de ned by recursion over
 these constructors, with clauses for Id , Id , etc.
 But:
 Its hard (not impossible) to make an inductively de ned
 universe univalent.
 Suggests a closed universe, which has to be rede ned whenever
 we add new type formers.
Co-meaning explanations
 We instead consider U (still informally) to be coinductively de ned.
 Now El and Id are destructors.
 Each type former , , ...is de ned by corecursion, specifying
 its elements, and its identity types of the same class .
 E.g. 
is a corecursive function
 (A:U)(A 
U)
 which makes sense because Id is another-type.
 U,
 Explains an open universe: can introduce new type formers
 without rede ning U, just applying its corecursion principle.
 The semantic universe of brant types is higher coinductive.
 This gives a philosophical reason for the coinductive behavior of
 IdU, having but no .
Back to Bishop
 Recall Bishops dicta:
 A set is de ned by describing exactly what must be
 done in order to construct an element of the set and what
 must be done in order to show that two elements are equal.
 An operation f from A into B is called a function if
 whenever aa A and a =a, we have f(a) = f(a).
Coinductive synthetic-groupoids
 Under propositions-as-types, this naturally becomes coinductive:
 A type is de ned by describing exactly what must be
 done in order to construct an element of the type and
 de ning a type of ways to identify any two such elements.
 An operation f from A into B is called a function if for
 aa :A we have a function from a = a to f(a) = f(a).
Coinductive synthetic-groupoids
 Under propositions-as-types, this naturally becomes coinductive:
 A type is de ned by describing exactly what must be
 done in order to construct an element of the type and
 de ning a type of ways to identify any two such elements.
 An operation f from A into B is called a function if for
 aa :A we have a function from a = a to f(a) = f(a).
 If we augment it with a bit of univalence:
 We de ne a type U whose elements are types, where
 two types are identi ed by a one-to-one correspondence.
 Every element of every type is identi ed with itself. For
 a type A : U, this yields its own type of identi cations.
 We get a philosophical vision that leads ineluctably to H.O.T.T.,
 as a theory of coinductive-groupoids




















Under consideration for publication in Theory and Practice of Logic Programming
 1
 (Co)recursion in Logic Programming: Lazy vs Eager∗
 arXiv:1402.3690v4  [cs.PL]  20 May 2014
 J ´ ONATHAN HERAS
 School of Computing, University of Dundee, UK
 (e-mail: jonathanheras@computing.dundee.ac.uk)
 EKATERINAKOMENDANTSKAYA
 School of Computing, University of Dundee, UK
 (e-mail: katya@computing.dundee.ac.uk)
 MARTINSCHMIDT
 Institute of Cognitive Science, University of Osnabr¨uck, Germany
 (e-mail: martisch@uos.de)
 submitted 1 January 2003; revised 1 January 2003; accepted 1 January 2003
 Abstract
 CoAlgebraic Logic Programming (CoALP) is a dialect of Logic Programming designed to bring a more
 precise compile-time and run-time analysis of termination and productivity for recursive and corecursive
 functions in Logic Programming. Its second goal is to introduce guarded lazy (co)recursion akin to func
tional theorem provers into logic programming. In this paper, we explain lazy features of CoALP, and
 compare them with the loop-analysis and eager execution in Coinductive Logic Programming (CoLP). We
 conclude by outlining the future directions in developing the guarded (co)recursion in logic programming.
 KEYWORDS:Logic Programming, Recursion, Corecursion, Termination, Productivity, Guardedness.
 1 Introduction
 Logic Programming (LP) was conceived as a recursive programming language for first-order
 logic. Prolog and various other implementations of LP feature eager derivations, and therefore
 termination has been central for logic programming (de Schreye and Decorte 1994). However,
 unlike e.g. functional languages, LP has not developed an operational semantics supporting ex
plicit analysis of termination. In typed programming languages like Coq or Agda, it is possible
 to introduce syntactic (static) checks that ensure structural recursion, and hence termination of
 programs at run-time. In Prolog, there is no support of this kind.
 Example 1.1 (BitList) Consider the following recursive program that defines lists of bits.
 1.bit(0) ←
 2.bit(1) ←
 3.bitlist([]) ←
 4.bitlist([X|Y]) ← bit(X),bitlist(Y)
 ∗ The work of the first two authors was supported by EPSRC Grant EP/K031864/1.
2
 J. Heras, E. Komendantskaya, and M. Schmidt
 76
 01
 Terminating
 54
 23
 ▲▲▲▲▲
 76
 01
 Non-terminating
 Recursion
 54
 23
 76
 01
 Productive
 54
 23
 ▲▲▲▲▲
 76
 01
 Non-productive
 Corecursion
 Fig. 1. Distinguishing well-founded and non-well-founded cases of recursion and corecursion.
 54
 23
 It is a terminating program, however, if the order of clauses (3) and (4), or the order of atoms in
 clause (4) is accidentally swapped, the program would run into an infinite loop.
 This example illustrates that non-terminating (co)recursion is distinguished only empirically
 at run-time in LP. This distinction is not always accurate, and may depend on searching strategies
 of the compiler, rather than semantic meaning of the program.
 Coinductive Logic Programming (CoLP) (Gupta et al. 2007; Simon et al. 2007) has been in
troduced as a means of supporting corecursion in LP. A representative example of coinductive
 programmingis to reason about an infinite data structure, for example an infinite stream of bits.
 Example 1.2 (BitStream) Given the definition of bits as in Example 1.1, an infinite stream of
 bits is defined as:
 1.stream([X|Y]) ← bit(X),stream(Y)
 Note that unlike BitList, we no longer have the base case for recursion on stream.
 The tradition (Coquand 1994) has a dual notion to termination for well-behaving corecur
sion– and that is productivity. If termination imposes the condition that any call to an induc
tively defined predicate like bit must terminate, then productivity requires that every call to a
 coinductive predicate like stream must produce some partially observed structure in a finite
 number of steps. E.g. calling stream(X)?,the program must compute an answer [0|Y] observ
ing the component 0 in finite time. Moreover, the productivity imposes a second condition: the
 computation must be able to proceed corecursively, e.g. in our example, the condition is for Y
 to be an infinite productive datastructure. This situation is explained in e.g. (Abel et al. 2013;
 Bertot and Komendantskaya 2008).
 CoLP deals with programs like BitStream by using a combination of eager evaluation, SLD
resolution and loop analysis. In simplified terms, for a goalstream(X)?the resolventloop detec
tion would allow to return an answer X=[0|X]; by observing the “regular” pattern in resolvents
 involving Clause (1) in the derivations. Similarly to standard (recursive) LP, non-terminating
 cases of corecursion (where no regular loop can be found) are not formally analysed in CoLP.
 Example 1.3 (BadStream) BadStream is not productive; that is, it would be executed infinitely
 without actually constructing a stream:
 1.badstream([X|Y]) ← badstream([X|Y])
 Adifferent case of corecursion is the below example, which is productive, but cannot be han
dled by CoLP loop detector, as the stream it defines is not regular.
 Example 1.4 (TakeFirstN) The program TakeFirstN defines the stream of natural numbers,
(Co)recursion in Logic Programming: Lazy vs Eager
 and allows to construct a list with the first n elements of the stream by calling taken.
 1.from(X,[X|Y]) ← from(s(X),Y)
 2.take(0,Y,[]) ←
 3.take(s(X),[Y|Z],[Y|R]) ← take(X,Z,R)
 4.taken(N,X) ← from(0,Y),take(N,Y,X)
 3
 In CoLP, calls to e.g. taken(s(s(0)),X)?fall into infinite computationsthat are not handled
 by the loop detection procedure. Similar to how LP would be unable to handle BitList with
 swapped atoms in clause (4) though in principle the program describes a well-founded inductive
 structure, CoLP would not be able to handle TakeFirstN although it is a perfectly productive
 stream. For the query taken, it is intuitively clear that, the construction of the first n elements of
 the stream should take a finite number of derivation steps.
 CoalgebraicLogicProgramming(CoALP)(Komendantskaya and Power 2011;Komendantskaya et al. 2014a)
 gives a new (coalgebraic) operational semantics for LP; and in particular it offers new methods
 to analyse termination and productivity of logic programs. Using CoALP, we present here a
 coherent operational treatment of recursion and corecursion in LP, and discuss new methods
 to distinguish well-founded and non-well-founded cases of (co)recursion in LP, as outlined in
 Figure 1. Unlike Prolog or CoLP, CoALP is a first lazy dialect of logic programming; and it fea
tures guarded (co)recursion akin to structural recursion and guarded corecursion in e.g. Coq or
 Agda (Coquand 1994; Abel et al. 2013). The current implementation of CoALP in parallel lan
guage Go is available in (Komendantskaya et al. 2014b); and is tested on a few benchmarks in
 this paper. Here, weabstract fromsomeofthetechnicaldetailsavailable in (Komendantskaya et al. 2014a)
 and from implementation details available in (Komendantskaya et al. 2014c) and give a higher
level discussion of the issues of termination and productivity in LP.
 The rest of the paper is structured as follows. In Section 2, we explain the role of laziness in
 semantics and implementation of CoALP; in Section 3, we discuss the effect of guarded corecur
sion. Section 4 is devoted to discussion of our current work on soundness properties for corecur
sive logic programming.
 2 LazyCorecursion in Logic Programming
 CoALP uses the standard syntax of Horn-clause logic programming (Lloyd 1987), but offers a
 newderivation algorithm in place of the SLD-resolution. One of the main distinguishing features
 of CoALPis its laziness. To our knowledge, it is the first lazy dialect of logic programming. The
 issue is best explained using the following example:
 Example 2.1 Given the program BitList and the query bitlist([X|Y]), the standard algo
rithm of SLD-resolution (Lloyd 1987) will eagerly attempt to find a derivation, e.g.:
 bitlist([X|Y])−→bit(X),bitlist(Y) X=0
 −→bitlist(Y)Y=[]
 −→✷
 For the program BitStream this will give rise to an infinite SLD-derivation:
 stream([X|Y])−→bit(X),stream(Y) X=0
 −→stream(Y)Y=[X1|Y1]
 −→ stream([X1|Y1])...
 In the above setting, there is no natural place for laziness, as ultimately the strong side of the
 procedure is a fully automated proof search. Fibrational coalgebraic operational semantics of LP
4
 J. Heras, E. Komendantskaya, and M. Schmidt
 θ1
 →
 1. stream(X)
 3. bitlist(X)
 θ2
 →... θ3
 →
 stream([X1|Y])
 bit(X1)
 stream(Y)
 2. bitlist(X)
 θ1
 1
 →
 stream([0|[X1|Y2]])
 bit(0)
 ✷
 stream([X1|Y2])
 bit(X1)
 bitlist([])
 ✷
 θ2
 1
 →
 θ2
 2
 →... θ2
 3
 bitlist([X1|Y])
 bit(X1)
 list(Y)
 →
 bit(0)
 ✷
 stream(Y2)
 bitlist([0|[]])
 →... →∞
 bitlist([])
 ✷
 Fig. 2. 1: Three coinductive trees representing a coinductive derivation for the goal G = stream(X) and the program
 BitStream, with θ1 = X/[X1|Y], θ2 = X1/0 and θ3 = Y/[X1|Y2]. 2-3: Coinductive trees representing two coinductive
 derivations for the goal G = bitlist(X) and the program BitList, with θ1
 1 = X/[], θ2
 1 = X/[X1|Y], θ2
 2 = X1/0, and
 θ2
 3 = Y/[].
 presented in (Komendantskaya et al. 2014a) inspired us to introduce a structure which we call
 coinductive tree; we use it as a measure for the size of lazy steps in derivations:
 Definition 2.1 Let P be a logic program and G =← A be an atomic goal. The coinductive tree
 for A is a (possibly infinite) tree T satisfying the following properties.
 • Aisthe root of T.
 • Eachnodein T iseither an and-node(labelled by an atom) or an or-node (labelled by “•”). The
 root of the tree is an and-node.
 • For every and-node A′ occurring in T, if there exist exactly m > 0 distinct clauses C1,...,Cm in
 P (a clause Ci has the form Bi ← Bi
 1,...,Bi ni
 , for some ni), such that A′ = B1θ1 = ... = Bmθm,
 for mgus θ1,...,θm, then A′ has exactly m children given by or-nodes, such that, for every i ∈
 {1,...,m}, the ith or-node has ni children given by and-nodes Bi
 1θi,...,Bi ni
 θi.
 In such a case, we sayCi and θi are internal resolvents of A′.
 Coinductivetrees resemble an-orparallel trees (Gupta and Costa 1994),see (Komendantskaya et al. 2014a;
 Komendantskaya et al. 2014c) for discussion of their parallel features. However, they restrict
 mgus used to form nodes to term-matching. Given two first order atomic formulae A and B, an
 mgu θ for A and B is called a term-matcher if A = Bθ. In Definition 2.1, note the condition
 A′ =B1θ1 =...=Bmθm.
 Example 2.2 Figure 2shows coinductivetrees for various goals in BitStream and BitList; com
pare with SLD-derivations in Example 2.1. Note that each of those trees is finite by construction
 of Definition 2.1; and we do not impose any additional conditions. The size of coinductive trees
 varies, but it is automatically determined by construction of the definition.
 Wenowdefinederivations between coinductive trees– a lazy analogue of SLD-derivations.
 Definition 2.2 Let G = A,T be a goal given by an atom ← A and the coinductive tree T
(Co)recursion in Logic Programming: Lazy vs Eager
 5
 induced by A, and let C be a clause H ← B1,...,Bn. Then, the goal G′ is coinductively derived
 from G andC using the mgu θ if the following conditions hold:
 ⋆ Q(¯ t) is a node in T.
 ⋆⋆ θisanmguofQ(¯ t) andH.
 ⋆⋆⋆ G′ isgivenbythe(coinductive) tree Tθ with the root Aθ.
 Definition 2.3 AcoinductivederivationofP∪{G}consistsofasequenceofgoalsG=G0,G1,...
 and a sequence θ1,θ2,... of mgus such that each Gi+1 is derived from a node A ∈ Ti (where Ti
 is the coinductive tree of Gi) and a clause C using a non-empty substitution θi+1. In this case,
 A,C,θi+1 is called a resolvent.
 Coinductive derivations resemble tree rewriting. They produce the “lazy” corecursive effect:
 derivations are given by potentially infinite number of steps, where each individual step is exe
cuted in finite time.
 Example 2.3 Figure 2 shows three possible coinductive derivations for BitStream and BitList.
 Note that two derivations for BitList terminate (with ✷ closing all branches). Note also, that
 this time, due to the and-or parallel nature of coinductive treees, changing the order of atoms or
 clauses in the program BitList will not change the result.
 For terminating coinductive derivations, we require at least one or-subtree of the coinductive
 tree to be closed (with ✷ leaves). We also say in such cases that the coinductive tree contains
 a success subtree. The last coinductive trees in the second and third derivation of Figure 2 are
 themselves success subtrees.
 Due to its and-or parallel properties (Komendantskaya et al. 2014c), CoALP is more robust
 than eager sequential SLD-resolution when it comes to reflecting program's operational mean
ing; and mere change in the clause order would not place a terminating recursive function into
 a non-terminating class, cf. Figure 1. Yet more importantly, this new coinductive derivation pro
cedure allows us to characterise productive and non-productive programs with better precision.
 In Introduction, we have seen that according to eager interpreter of CoLP, both programs Bad
Stream and TakeFirstN are non-terminating; despite of one being productive, and another
non-productive. Next example shows that under lazy execution, productive programs with irreg
ular pattern of resolvents can be handled more naturally.
 Example 2.4 Figure 3 shows the first steps in the derivation for the program TakeFirstN and
 the goal taken(s(s(0)),X). Unlike CoLP, CoALP is able to compute the second element of
 the stream in finite time.
 There will be classes of non-terminating and non-productive programs for which coinductive
 trees grow infinite, and lazy derivations fail being ”lazy”. The program BadStream is one such
 example. We will consider this issue in the next section.
 3 Guarding (Co)recursion
 The previous section introduced coinductive trees, which allowed us to distinguish terminating
 and productive programs like BitStream, BitList, TakeFirstN from non-productive programs
 like BadStream, by simply observing that coinductive trees remain finite for the former, while
6
 J. Heras, E. Komendantskaya, and M. Schmidt
 θ2
 −→
 taken(s2(0),[X1,X2|R2])
 θ1
 −→
 taken(s2(0),X)
 taken(s2(0),[X1|R1])
 from(0,[X1|Y1])
 from(0,[X1, X2|Y2])
 take(s2(0),[X1|Y1],[X1|R1])
 from(0,Y)
 take(s2(0),Y,X)
 →
 take(s(0),Y1,R1)
 →
 take(s2(0),[X1,X2|Y2],[X1,X2|R2])
 take(s(0),[X2|Y2],[X2|R2])
 take(0,Y2,R2)
 Fig. 3. First steps of the derivation for the goal taken(s2(0),X)– s2(0) denotes s(s(0))– and the program
 TakeFirstN, with θ1 = Y/[X1|Y1],X/[X1|R1] and θ2 = Y1/[X2|Y2],R1/[X2|R2]. As take is an inductive predicate, and
 from is coinductive; resolvents for take nodes are given priority.
 connected(O,Z)
 edge(O,Y)
 connected(Y,Z)
 edge(Y,Y1)
 connected(Y1,Z)
 edge(Y1,Y2)
 connected(Y2,Z)
 .
 .
 .
 Fig. 4. The infinite coinductive tree for the program GC from from Example 3.1, for the database edge(0,1) ←.
 growinginfinite for the latter. It was especially significant that this new approachwas, unlike Pro
log, robust to permutations of clauses and atoms, and, unlike CoLP, was working with productive
 irregular streams. Curiously, the following logic programfails to producefinite coinductive trees:
 Example 3.1 (GC) Let GC (for graph connectivity) denote the logic program
 1.connected(X,X) ←
 2.connected(X,Y) ← edge(X,Z),connected(Z,Y)
 It would be used with database of graph edges, like edge(0,1)←.
 The programgives rise to infinite coinductive trees, see Figure 4. It would terminate in LP, but,
 similarly to our discussion of BitList, would lose the termination property if the order of clauses
 (1) and (2) changes, or if the order of the atoms in clause (2) changes.
 Thereasonbehindinfinityofcoinductivetreesfor the aboveprogramis the absenceoffunction
 symbols– “constructors” in the clause heads. The lazy nature of coinductive trees was in part
 due to the term-matching used to compute them. Term-matching loses its restrictive power in the
 absence of constructors. A very similar procedure of guarding recursion by constructors of types
 is used in e.g. Coq or Agda. This observation would suggest an easy way to fix the GC example,
 by introducing reducing dummy-constructors:
 Example 3.2 (Guarded GC)
 1.connected(X,cons(Y,Z)) ← edge(X,Y),connected(Y,Z)
 2.connected(X,nil) ←
(Co)recursion in Logic Programming: Lazy vs Eager
 7
 Considerations of this kind led us to believe that our lazy (co)recursive approach opens the
 way for a compile-time termination and productivity checks akin to respective checks in Coq or
 Agda (Coquand 1994; Abel et al. 2013). The programmer would be warned of non-terminating
 cases and asked to find a guarded reformulation for his functions. In Coq and Agda, different
 checks are imposed on recursive functions (“structural recursion” condition) and corecursive
 functions (“guardedness” checks). In logic programming terms, where types or predicate an
notations are unavailable, we can formulate a uniform productivity property for recursive and
 corecursive programs, as follows:
 Definition 3.1 Let P be a logic program, P is productive if for any goal G, the coinductive tree
 for P∪{G} has a finite size.
 The above is a semantic property; syntactically, we need to introduce guardedness checks
 to ensure productivity. The intuitive idea is to ensure that every coinductive program behaves
 like BitStream:BitStream is guarded by the coinductive function symbol (or “guard”) scons
 (denotedby [.|.]);and henceall coinductivetrees for it are finite, see Figure 2. On the contrary,
 Comember lacks a guarding constructor.
 Example 3.3 (Comember) The predicate comember is true if and only if the element X occurs
 an infinite number of times in the stream S.
 1.drop(H,[H|T],T) ←
 2.drop(H,[H1|T],T1) ← drop(H,T,T1)
 3.comember(X,S) ← drop(X,S,S1),comember(X,S1)
 Comemberisun-productivefor e.g.thecoinductivetree arising from the querycomember(X,S)
 contains a chain of alternating •'s and atoms comember(X,S1), comember(X,S2), etcetera,
 yielding an infinite coinductive tree.
 We will give a high-level formulation of guardedness checks here, for more technical discus
sion, see (Komendantskaya et al. 2014a).
 Guardedness Check 1 (GC1): If the same predicate Q occurs in the head and in the body of
 a clause, then there must exist a function symbol f occurring among the arguments of Q; such
 that the number of its occurrences is reduced from head to body.
 Example 3.4 (Guarded Comember) We propose the following guarded definition of comem
ber, thereby simplifying it and reducing an extra argument to drop.
 1.drop(H,[H|T]) ←
 2.drop(X,[H|T]) ← drop(X,T)
 3.gcomember(X,[H|T]) ← drop(X,[H|T]),gcomember(X,T)
 In CoALP, the goal gcomember(0,nats)will lazily search for 0 in an infinite stream of natural
 numbers, but it never falls into un-productive coinductive trees, as CoLP would do.
 GC1wouldbesufficientforsomeprograms,likeBitStream,wherethereisonlyone(co)inductive
 clause; but not in the general case. LP in general is not compositional, that is, composing two
 programs may yield a program that has semantic properties not present in the initial programs.
8
 J. Heras, E. Komendantskaya, and M. Schmidt
 stream2’([s(0)|Y],[s(Y1)|Z1])
 nat(0)
 ✷
 θ1
 −→... θ2
 −→
 stream2’([s(X)|Y],Z)
 nat(X)
 stream-aux([s(X)|Y],Z)
 →
 stream-aux([s(0)|Y],[s(Y1)|Z1])
 nat(Y1)
 stream2’([s(0)|Y],[s(Y1)|Z1])
 nat(0)
 ✷
 .
 .
 .
 Fig. 5. Coinductive derivation of stream2’([s(X)|Y],Z)and the program from Example 3.5 producing an infinite coinductive tree,
 with θ1 =X/0andθ2 =Z/[s(Y1)|Z1].ThefigurealsorepresentsoneGC-derivation generated duringGC3.GC3 detectsthe un-guarded
 loop; see the underlined atoms.
 Same rule applies in CoALP: if both P1 and P2 are productive programs, their composition is not
 guaranteed to be a productive program; the next check is imposed to cover the compositional
 cases.
 Guardedness Check 2 (GC2): For every clause head A, construct a coinductive tree with the
 root A. If there are atoms Q(¯ t) and Q(¯ t′) in the coinductive tree such that Q(¯ t′) is a child of Q(¯ t),
 apply GC1 to the clause Q(¯ t) ← Q(¯ t′).
 GC1–GC2handle some programs well, but they are still insufficient in the general case. The
 following program passes the checks GC1-GC2, but is not productive in the sense of Defini
tion 3.1, see Figure 5.
 Example 3.5 (Un-productive Program that passes GC1-GC2)
 1.stream2’([s(X)|Y],Z) ← nat(X), stream-aux([s(X)|Y],Z)
 2.stream-aux(X,[s(Y)|Z]) ← nat(Y), stream2’(X,[s(Y)|Z])
 Guardedness Check 3 (GC3): For every clause head A, start a coinductive derivation with
 the goal A imposing GC2 condition to every coinductive tree in the derivation, and imposing the
 following termination conditions:
 1. Terminate coinductive derivation if GC2 fails for at least one tree.
 2. Terminatecoinductivederivationif all branchesare eitherclosed with ✷ or containguarded
 loops only.
 Note that the checks GC1-GC3 we have introduced here are a pre-processing (compile
time) mechanism of CoALP. Once the program passed the guardedness checks, it does coin
ductive derivations lazily; and does not require any loop-detection procedures at run-time. If a
 program fails GC1-GC3, the programmer will be asked to re-formulate the definitions as we
 have seen in Examples 3.2 and 3.4. The first implementation of guardedness checks is available
 at (Komendantskaya et al. 2014b).
 We finish this section with Table 1 comparing how SWI-Prolog, CoLP and CoALP handle
 variousrecursive and corecursiveprograms.For CoALP,we also benchmarkguardednesschecks.
 For coinductive programs, CoLP can only handle coinductive programs that contain a regular
 pattern and fails otherwise (cf. Table 1); on the contrary, CoALP, in its lazy style, works for
 any productive program. This is illustrated, for instance, with the programs TakeFirstN and
 TakeRepeat. Table 1 shows that CoALP is slower than the CoLP interpreter and SWI-Prolog
(Co)recursion in Logic Programming: Lazy vs Eager
 CoALP
 GCtime: 0.0002s
 TakeFirstN†
 Takerepeat†
 Comember†
 GComember†
 SumFirstn†
 FibStream†
 Infinite Automata†
 Knights
 Finite Automata
 Ackermann
 Yes
 Yes
 Yes
 Yes
 Yes
 Yes
 Yes
 Yes
 Yes
 runtime: lazy execution
 GCtime: 0.0009s
 runtime: lazy execution
 Not guarded
 GCtime: 0.0011s
 runtime: lazy execution
 GCtime: 0.0013s
 runtime: lazy execution
 GCtime: 0.0006s
 runtime: lazy execution
 GCtime: 0.0011s
 runtime: lazy execution
 GCtime: 0.225s
 runtime: 3.002s
 GCtime: 0.0011s
 runtime: 0.0023s
 GCtime: 0.001s
 runtime: 13.23s
 CoLP
 No
 Yes (0.0001s)
 Yes? (0.0001s)
 Yes? (0.0001s)
 No
 No
 Yes (0.0001s)
 Yes (1.13s)
 Yes (0.04s)
 Yes (7.692s)
 SWI-Prolog
 No
 No
 No
 No
 No
 No
 No
 Yes (0.012s)
 Yes (0.0005s)
 Yes (3.192s)
 9
 Table 1. Execution of different programs in CoALP, CoLP and SWI-Prolog. Examples marked with † involve both
 inductive and coinductive predicates. In the table, “No” means that the system runs forever without returning an answer,
 and “Yes?” indicates that the program succeeds if it contains a regular pattern and fails otherwise.
 note that SWI-Prolog is a fully-tuned mature programming language and the CoLP interpreter
 runs on top of SWI-Prolog, as opposed to our implementation of CoALP in Go from scratch.
 4 Work-in-Progress: Soundness for Corecursion
 There are two main directions for CoALP's development, both related to soundness:
 (I) We are in the process of establishing soundness of GC1-GC3 that is, the property that, if a
 program P is guarded by GC1-GC3, then it is productive in CoALP.
 Proving this property in the general case is a challenge; and involves pattern analysis on re
solvents and also a proof of termination of GC1-GC3. Example 3.5 and Figure 5 give a flavor
 of the complicated cases the guardedness checks need to cover. Note that GC1-GC3 provide the
 guarding property only in the CoALP setting, and the same idea of guarding (co)recursion by
 constructors would fail for standard LP or CoLP, as many examples of this paper show.
 (II) Soundness of coinductive derivations needs to be established. This challenge is best illus
trated by the following example.
 Example 4.1 (Soundness for Comember) To check the validity of a query in Comember (Ex
ample 3.3) for an arbitrary stream, one needs to satisfy two conditions: 1) finding an element to
 dropin afinitetime, 2) findingguaranteesthat this finite computationwill be repeated an infinite
 numberof times for the given stream. CoLP would handlesuch a case for all streams that consist
 of a regular finite repeating pattern and will not be able to handle cases when the input stream
 is not regular. CoLP would fail to derive true or falsity of e.g. the query comember(0,nats),
 where nats is the stream of natural numbers, as CoLP falls into an infinite non-terminating
 computation and fails to produce any response to the query. CoALP in its current implemen
tation will handle any case of corecursion, including comember(0,nats), but in its lazy, and
 therefore partial, style.
10
 J. Heras, E. Komendantskaya, and M. Schmidt
 Similarly, TakeFirstN falls into an infinite loop with CoLP, but unfolds lazily with CoALP,
 see Figure 3. Laziness on its own, however, does not guarantee soundness.
 For inductive programs and recursive functions, CoALP yields the same theorems of sound
ness and completeness as classical LP (Lloyd 1987); cf. (Komendantskaya et al. 2014a). The
 only adaptation to the already described coindutive derivation procedure is the requirement that
 the derivation terminates and gives an answer whenever a success subtree is found. Thus, gener
alisation of standard soundness and completeness for induction in CoALP is not very surprising.
 Soundness of CoALP for coinductive programs is conceptually more interesting: it has to
 include a number of guarantees that need to be checked at compile-time and run-time, that is:
 1. Identification of the guarding pattern coming from sound guardedness checks;
 2. Guaranteethat the guardingpattern will be producedin a finite numberof derivation steps;
 3. Guarantee that the guarding pattern will be re-produced an infinite number of times.
 Item 3. in particular may allow for a few different solutions. In its basic form, it can be a
 repeated regular pattern, as it is done in CoLP. In a more sophisticated form, it can cover ir
regular patterns, as long as more involved guarantees of infinite execution are be provided, cf.
 Example 1.4 and Figure 3.
 To conclude, we have described a new methodto analyse termination and productivityof logic
 programsby meansof lazyguardedcorecursionin CoALP, as outlinedin Figure1. We advocated
 a new style of programming in LP, where the programmer is in charge of providing termination
 or productivity measures for (co)recursive programs at compile-time, as it is done in some other
 declarative languages with recursion and corecursion. Finally, we outlined the main directions
 towards establishing soundness results for CoALP outputs.
 References
 ABEL, A. ET AL. 2013. Copatterns: programming infinite structures by observations. In POPL'13. ACM
 SIGPLANNotices, vol. 48. 27–38.
 BERTOT, Y. AND KOMENDANTSKAYA, E. 2008. Inductive and coinductive components of corecursive
 functions in Coq. ENTSC 203, 5, 25–47.
 COQUAND, T. 1994. Infinite objects in type theory. In TYPES'93. LNCS, vol. 806. 62–78.
 DE SCHREYE, D. AND DECORTE, S. 1994. Termination of logic programs: the never-ending story. J. of
 Logic Programming 19–20, Supplement 1, 199–260. Special Issue: Ten Years of Logic Programming.
 GUPTA, G. ET AL. 2007. Coinductive logic programming and its applications. In ICLP'07. LNCS, vol.
 4670. 27–44. Interpreter Available at http://www.utdallas.edu/˜gupta/meta.html.
 GUPTA, G. AND COSTA, V.1994. Optimalimplementation of and-or parallel prolog. In PARLE'92.71–92.
 KOMENDANTSKAYA, E. ET AL. 2014a. Coalgebraic logic programming: from semantics to implementa
tion. J. Logic and Computation.
 KOMENDANTSKAYA, E. ET AL. 2014b. CoALP webpage: software and supporting documentation.
 http://staff.computing.dundee.ac.uk/katya/CoALP/.
 KOMENDANTSKAYA, E. ET AL. 2014c. Exploiting parallelism in coalgebraic logic programming.
 ENTCS 303, 121–148.
 KOMENDANTSKAYA,E. AND POWER, J. 2011. Coalgebraic derivations inlogic programming. In CSL'11.
 LIPIcs. Schloss Dagstuhl, 352–366.
 LLOYD, J. 1987. Foundations of Logic Programming, 2nd ed. Springer-Verlag.
 SIMON, L. ET AL. 2007. Co-logic programming: Extending logic programming with coinduction. In
 ICALP'07. LNCS, vol. 4596. 472–483.






































 AUNIFIEDFRAMEWORKFORGENERALIZED
 MULTICATEGORIES
 G.S.H.CRUTTWELLANDMICHAELA.SHULMAN
 Abstract. Notionsofgeneralizedmulticategoryhavebeendefinedinnumerouscon
textsthroughouttheliterature,andincludesuchdiverseexamplesassymmetricmulti
categories, globularoperads, Lawveretheories, andtopological spaces. Ineachcase,
 generalizedmulticategoriesaredefinedasthe“laxalgebras”or“Kleislimonoids”rela
tivetoa“monad”onabicategory. However, themeaningsof thesewordsdiffer from
 authortoauthor,asdothespecificbicategoriesconsidered.Weproposeaunifiedframe
work:byworkingwithmonadsondoublecategoriesandrelatedstructures(ratherthan
 bicategories),onecandefinegeneralizedmulticategoriesinawaythatunifiesallprevious
 examples,whileatthesametimesimplifyingandclarifyingmuchofthetheory.
 Contents
 1 Introduction 1
 2 Virtualdoublecategories 9
 3 Monadsonavirtualdoublecategory 14
 4 Generalizedmulticategories 20
 5 Compositesandunits 27
 6 2-categoriesofT-monoids 33
 7 Virtualequipments 36
 8 Normalization 43
 9 Representability 49
 A Composites inModandH-Kl 57
 B Comparisonstoprevioustheories 61
 1. Introduction
 Amulticategory isageneralizationof acategory, inwhichthedomainof amorphism,
 ratherthanbeingasingleobject, canbeafinitelistofobjects.Aprototypicalexample
 isthemulticategoryVectofvectorspaces, inwhichamorphism(V1,...,Vn) Wisa
 multilinearmap. Infact,anymonoidalcategorygivesamulticategoryinacanonicalway,
 wherethemorphisms(V1,...,Vn) WaretheordinarymorphismsV1⊗...⊗Vn W.
 ThefirstauthorwassupportedbyaPIMSCalgarypostdoctoral fellowship,andthesecondauthor
 byaNationalScienceFoundationpostdoctoral fellowshipduringthewritingofthispaper.
 2000MathematicsSubjectClassification: 18D05,18D20,18D50.
 Keywordsandphrases:Enrichedcategories,changeofbase,monoidalcategories,doublecategories,
 multicategories,operads,monads.
 cG.S.H.CruttwellandMichaelA.Shulman,2010.Permissiontocopyforprivateusegranted.
 1
2
 The multicategory Vect can be seen as arising in this way, but it is also natural to view
 its multicategory structure as more basic, with the tensor product then characterized as
 a representing object for “multimorphisms.” This is also the case for many other multi
categories; in fact, monoidal categories can be identified with multicategories satisfying a
 certain representability property (see [Her00] and §9).
 In addition to providing an abstract formalization of the passage from “multilinear
 map” to “tensor product,” multicategories provide a convenient way to present certain
 types of finitary algebraic theories (specifically, strongly regular finitary theories, whose
 axioms involve no duplication, omission, or permutation of variables). Namely, the objects
 of the multicategories are the sorts of the theory, and each morphism (X1,...,Xn)
 Y
 represents an algebraic operation of the theory. When viewed in this light, multicategories
 (especially those with one object, which correspond to one-sorted theories) are often
 called non-symmetric operads (see [May72]). The original definition of multicategories in
 [Lam69] (see also [Lam89]) was also along these lines (a framework for sequent calculus).
 The two viewpoints are related by the observation that when A is a small multicategory
 representing an algebraic theory, and C is a large multicategory such as Vect, a model
 of the theory A in C is simply a functor of multicategories A
 C. This is a version of
 the functorial semantics of [Law63].
 Our concern in this paper is with generalized multicategories, a well-known idea which
 generalizes the basic notion in two ways. Firstly, one allows a change of “base context,”
 thereby including both internal multicategories and enriched multicategories. Secondly,
 and more interestingly, one allows the finite lists of objects serving as the domains of
 morphisms to be replaced by “something else.” From the first point of view, this is
 desirable since there are many other contexts in which one would like to analyze the
 relationship between structures with coherence axioms (such as monoidal categories) and
 structures with universal or “representability” properties. From the second point of view,
 it is desirable since not all algebraic theories are strongly regular.
 For example, generalized multicategories include symmetric multicategories, in which
 the finite lists can be arbitrarily permuted. “Representable” symmetric multicategories
 correspond to symmetric monoidal categories. Enriched symmetric multicategories with
 one object can be identified with the operads of [May72, Kel05, KM95]. These describe
 algebraic theories in whose axioms variables can be permuted (but not duplicated or
 omitted). In most applications of operads (see [EM06, BM03] for some recent ones), both
 symmetry and enrichment are essential.
 An obvious variation of symmetric multicategories is braided multicategories. If we
 allow duplication and omission in addition to permutation of inputs, we obtain (multi
sorted) Lawvere theories [Law63]; a slight modification also produces the clubs of [Kel72b,
 Kel92]. There are also important generalizations to “algebraic theories” on more compli
cated objects; for instance, the globular operads of [Bat98, Lei04] describe a certain sort
 of algebraic theory on globular sets that includes many notions of weak n-category.
 A very different route to generalized multicategories begins with the observation
 of [Bar70] that topological spaces can be viewed as a type of generalized multicategory,
3
 when finite lists of objects are replaced by ultrafilters, and morphisms are replaced by a
 convergence relation. Many other sorts of topological structures, such as metric spaces,
 closure spaces, uniform spaces, and approach spaces, can also be seen as generalized
 multicategories; see [Law02, CT03, CHT04].
 With so many different faces, it is not surprising that generalized multicategories have
 been independently considered by many authors. They were first studied in generality by
 [Bur71], but have also been considered by many other authors, including [Lei04], [Lei02],
 [Her01], [CT03], [CHT04], [Bar70], [Web05], [BD98], [Che04], and [DS03]. While all these
 authors are clearly doing “the same thing” from an intuitive standpoint, they work in
 different frameworks at different levels of generality, making the formal definitions difficult
 to compare. Moreover, all of these approaches share a certain ad hoc quality, which, given
 the naturalness and importance of the notion, ought to be avoidable.
 In each case, the authors observe that the “something else” serving as the domain
 of morphisms in a generalized multicategory should be specified by some sort of monad,
 invariably denoted T. For example, ordinary multicategories appear when T is the “free
 monoid” monad, globular operads appear when T is the “free strict ω-category” monad,
 and topological spaces appear when T is the ultrafilter monad. All the difficulties then
 center around what sort of thing T is a monad on.
 Leinster [Lei02, Lei04] takes it to be a cartesian monad on an ordinary category C, i.e.
 Chas pullbacks, T preserves them, and the naturality squares for its unit and multiplica
tion are pullback squares. Burroni [Bur71], whose approach is basically the same, is able
 to deal with any monad on a category with pullbacks. Hermida [Her01] works with a carte
sian 2-monad on a suitable 2-category. Barr and Clementino et. al. [Bar70, CT03, CHT04]
 work with a monad on Set equipped with a “lax extension” to the bicategory of matri
ces in some monoidal category. Weber [Web05] works with a “monoidal pseudo alge
bra” for a 2-monad on a suitable 2-category. Baez-Dolan [BD98] and Cheng [Che04] (see
 also [FGHW08]) use a monad on Cat extended to the bicategory of profunctors (although
 they consider only the “free symmetric strict monoidal category” monad).
 Inspecting these various definitions and looking for commonalities, we observe that in
 all cases, the monads involved naturally live on a bicategory, be it a bicategory of spans
 (Burroni, Leinster), two-sided fibrations (Hermida), relations (Barr), matrices (Clementino
 et. al., Weber), or profunctors (Baez-Dolan, Cheng). What causes problems is that the
 monads of interest are frequently lax (preserving composition only up to a noninvertible
 transformation), but there is no obvious general notion of lax monad on a bicategory,
 since there is no good 2-category (or even tricategory) of bicategories that contains lax
 or oplax functors.
 Furthermore, merely knowing the bicategorical monad (however one chooses to formal
ize this) is insufficient for the theory, and in particular for the definition of functors and
 transformations between generalized multicategories. Leinster, Burroni, Weber, and Her
mida can avoid this problem because their bicategorical monads are induced by monads
 on some underlying category or 2-category. Others resolve it by working with an extension
 of a given monad on Set or Cat to the bicategory of matrices or profunctors, rather than
4
 merely the bicategorical monad itself. However, the various definitions of such extensions
 are tricky to compare and have an ad hoc flavor.
 Our goal in this paper (and its sequels) is to give a common framework which in
cludes all previous approaches to generalized multicategories, and therefore provides a
 natural context in which to compare them. To do this, instead of considering monads
 on bicategories, we instead consider monads on types of double categories. This essen
tially solves both problems mentioned above: on the one hand there is a perfectly good
 2-category of double categories and lax functors (allowing us to define monads on a double
 category), and on the other hand the vertical arrows of the double categories (such as
 morphisms in the cartesian category C, functions in Set, or functors between categories)
 provide the missing data with which to define functors and transformations of generalized
 multicategories.
 The types of double categories we use are neither strict or pseudo double categories,
 but instead an even weaker notion, for the following reason. An important intermediate
 step in the definition of generalized multicategories is the horizontal Kleisli construction
 of a monad T, whose (horizontal) arrows X
 Y are arrows X
 TY. Without strong
 assumptions on T, such arrows cannot be composed associatively, and hence the horizontal
 Kleisli construction does not give a pseudo double category or bicategory. It does, however,
 give a weaker structure, which we call a virtual double category.
 Intuitively, virtual double categories generalize pseudo double categories in the same
 way that multicategories generalize monoidal categories. There is no longer a horizontal
 composition operation, but we have cells of shapes such as the following:
 X0
 X0
 X0
 f
 Y0
 Y0
 p1
 X1
 X1
 p2
 X2
 X2
 α
 q
 p3
 · · ·
 · · ·
 pn
 Xn
 Xn
 Xn
 g
 Y1
 Y1
 We will give an explicit definition in §2. Virtual double categories have been studied
 by [Bur71] under the name of multicat´egories and by [Lei04] under the name of fc
multicategories, both of whom additionally described a special case of the horizontal
 Kleisli construction. They are, in fact, the generalized multicategories relative to the “free
 category” or “free double category” monad (depending on whether one works with spans
 or profunctors). In [DPP06] virtual double categories were called lax double categories,
 but we believe that name belongs properly to lax algebras for the 2-monad whose strict
 algebras are double categories. (We will see in Example 9.7 that oplax double categories
 in this “2-monadically correct” sense can be identified with a restricted class of virtual
 double categories.)
 Next, in §§3–4 we will show that for any monad T on a virtual double category X,
 one can define a notion which we call a T-monoid. In fact, we will construct an entire
 new virtual double category KMod(X,T) whose objects are T-monoids, by composing
 the “horizontal Kleisli” construction mentioned above with the “monoids and bimodules”
 construction Mod described in [Lei04, §5.3], which can be applied to any virtual double
5
 category. Then in §6 we will construct from KMod(X,T) a 2-category KMon(X,T) of T
monoids, T-monoid functors, and transformations, generalizing the analogous definition
 in [Lei04, §5.3]. This requires a notion of when a virtual double category has units, which
 we define in §5 along with the parallel notion of when it has composites. (These definitions
 generalize those of [Her00] and can also be found in [DPP06]; they are also a particular
 case of the “representability” of [Her01] and our §9.)
 For particular X and T, the notion of T-monoid specializes to several previous defini
tions of generalized multicategories. For example, if X consists of objects and spans in a
 cartesian category C and T is induced from a monad on C, we recover the definitions of
 Leinster, Kelly, and Burroni. And if X consists of sets and matrices enriched over some
 monoidal category V and T is a “canonical extension” of a taut set-monad to X, then we
 recover the definitions of Clementino et. al.
 However, the other definitions of generalized multicategory cannot quite be identified
 with T-monoids for any T, but rather with only a restricted class of them. For instance,
 if X consists of categories and profunctors, and T extends the “free symmetric monoidal
 category” monad on Cat (this is the situation of Baez-Dolan and Cheng), then T-monoids
 are not quite the same as ordinary symmetric multicategories. Rather, a T-monoid for
 this T consists of a category A, a symmetric multicategory M, and a bijective-on-objects
 functor from A to the underlying ordinary category of M. There are two ways to restrict
 the class of such T-monoids to obtain a notion equivalent to ordinary symmetric multi
categories: we can require A to be a discrete category (so that it is simply the set of
 objects of M), or we can require the functor to also be fully faithful (so that A is simply
 the underlying ordinary category of M). We call the first type of T-monoid object-discrete
 and the second type normalized.
 In order to achieve a full unification, therefore, we must give general definitions of
 these classes of T-monoid and account for their relationship. It turns out that this requires
 additional structure on our virtual double categories: we need to assume that horizontal
 arrows can be “restricted” along vertical ones, in a sense made precise in §7. Pseudo
 double categories with this property were called framed bicategories in [Shu08], where
 they were also shown to be equivalent to the proarrow equipments of [Woo82] (see also
 [Ver92]). Accordingly, if a virtual double category X has this property, as well as all units,
 we call it a virtual equipment.
 Our first result in §8, then, is that if T is a well-behaved monad on a virtual equip
ment, object-discrete and normalized T-monoids are equivalent. However, normalized
 T-monoids are defined more generally than object-discrete ones, and moreover when T
 which are insufficiently well-behaved, it is the normalized T-monoids which are of more
 interest. Thus, we subsequently discard the notion of object-discreteness. (Hermida’s gen
eralized multicategories also arise as normalized T-monoids, where X consists of discrete
 f
 ibrations in a suitable 2-category K and T is an extension of a suitable 2-monad on K.
 Weber’s definition is a special case, since as given it really only makes sense for generalized
 operads, for which normalization is automatic; see §B.16.) In Table 1 we summarize the
 meanings of T-monoids and normalized T-monoids for a number of monads T.
6
 MonadT on T-monoid Normalized
 T-Monoid
 Pseudo
 T-algebra
 Id V-Mat V-enriched
 category
 Set Set
 Id Span(C) Internal category
 inC
 ObjectofC ObjectofC
 Id Rel OrderedSet Set Set
 Id R+-Mat MetricSpace Set Set
 Powerset Rel ClosureSpace T1ClosureSpace Complete
 Semilattice
 Mod(powerset) 2-Prof Modular Closure
 Space
 ClosureSpace Meet-Complete
 Preorder
 Ultrafilter Rel TopologicalSpace T1space Compact
 Hausdorffspace
 Mod(ultrafilter) 2-Prof ModularTop.
 Space
 TopologicalSpace OrderedCompact
 Hausdorffspace
 Ultrafilter R+-Mat Approachspace ? Compact
 Hausdorffspace
 Freemonoid Span(Set) Multicategory ? Monoid
 Mod(freemonoid) Set-Prof “Enhanced”
 multicategory
 Multicategory Monoidal
 category
 Free sym. strict
 mon.cat.
 Set-Prof “Enhanced” sym.
 multicategory
 Symmetric
 multicategory
 Symmetric mon.
 cat.
 Freecategory Span(Grph) Virtualdouble
 category
 ? Category
 Mod(freecategory) Prof(Grph) ? Virtualdouble
 category
 Pseudo double
 category
 Freecat.w/
 finiteproducts
 Set-Prof ? Multi-sorted
 Lawveretheory
 Cat. w/ finite
 products
 Freecat.w/
 smallproducts
 Set-Profls ? MonadonSet Cat. w/ small
 products
 Freepresheaf
 Sop Set
 Span Setob(S) FunctorA S FunctorA S
 w/discretefibers
 Functor
 Sop Set
 Mod(freepresheaf) Prof(Setob(S)) ? FunctorA S Pseudofunctor
 Sop Cat
 Freestrict
 ω-category
 Span(Globset) Multi-sorted
 globularoperad
 ? Strictω-category
 Mod(freeω-cat.) Prof(Globset) ? Multi-sorted
 globularoperad
 Monoidal
 globularcat.
 FreeM-set (Ma
 monoid)
 Span(Set) M-graded
 category
 ? M-set
 Table1:Examplesofgeneralizedmulticategories.Theboxesmarked“?”donothaveany
 establishedname; inmostcasestheyalsodonotseemveryinteresting.
7
 Now, what determines whether the “right” notion of generalized multicategory is a
 plain T-monoid or a normalized one? The obvious thing distinguishing the situations of
 Leinster, Burroni, and Clementino et. al. from those of Baez-Dolan, Cheng, and Hermida
 is that in the former case, the objects of X are “set-like,” whereas in the latter, they are
 “category-like.” However, some types of generalized multicategory can be constructed
 starting from two different monads on two different virtual equipments, one of which
 belongs to the first group and the other to the second.
 For example, observe that an ordinary (non-symmetric) multicategory has an under
lying ordinary category, containing the same objects but only the morphisms (V1)
 W
 with unary source. Thus, such a multicategory can be defined in two ways: either as
 extra structure on its set of objects, or as extra structure on its underlying category. In
 the second case, normalization is the requirement that in the extra added structure, the
 multimorphisms with unary source do no more than reproduce the originally given cate
gory. Thus, ordinary multicategories arise both as T-monoids the “free monoid” monad
 on sets and spans, and as normalized T-monoids for the “free monoidal category” monad
 on categories and profunctors.
 Our second result in §8 is a generalization of this relationship. We observe that
 the virtual equipment of categories and profunctors results from applying the “monoids
 and modules” construction Mod to the virtual equipment of sets and spans. Thus, we
 generalize this situation by showing that for any monad T on a virtual equipment, plain
 T-monoids can be identified with normalized Mod(T)-monoids. That this is so in the
 examples can be seen by inspection of Table 1. Moreover, it is sensible because application
 of Mod takes “set-like” things to “category-like” things.
 It follows that the notion of “normalized T-monoid” is actually more general than
 the notion of T-monoid, since arbitrary T-monoids for some T can be identified with the
 normalized S-monoids for some S (namely S = Mod(T)), whereas normalized S-monoids
 cannot always be identified with the arbitrary T-monoids for any T. (For instance, this is
 not the case when S is the “free symmetric monoidal category” monad on categories
 and profunctors.) This motivates us to claim that the “right” notion of generalized
 multicategory is a normalized T-monoid, for some monad T on a virtual equipment.
 Having reached this conclusion, we also take the opportunity to propose a new naming
 system for generalized multicategories which we feel is more convenient and descriptive.
 Namely, if (pseudo) T-algebras are called widgets, then we propose to call normalized
 T-monoids virtual widgets. The term “virtual double category” is of course a special
 case of this: virtual double categories are the normalized T-monoids for the monad T on
 Prof(Grph) whose algebras are double categories.
 Of course, “virtual” used in this way is a “red herring” adjective1 akin to “pseudo” and
 “lax”, since a virtual widget is not a widget. The converse, however, is true: every widget
 has an underlying virtual widget, so the terminology makes some sense. For example, the
 observation above that every monoidal category has an underlying multicategory is an
 1The mathematical red herring principle states that an object called a “red herring” need not, in
 general, be either red or a herring.
8
 instance of this fact. Moreover, it often happens that virtual widgets share many of the
 same properties as widgets, and many theorems about widgets can easily be extended to
 virtual widgets. Thus, it is advantageous to use a terminology which stresses the close
 connection between the two. Another significant advantage of “virtual widget” over “T
multicategory” is that frequently one encounters monads T for which T-algebras have
 a common name, such as “double category” or “symmetric monoidal category,” but T
 itself has no name less cumbersome than “the free double category monad” or “the free
 symmetric monoidal category monad.” Thus, it makes more sense to name generalized
 multicategories after the algebras for the monad than after the monad itself.
 By the end of §8, therefore, we have unified all existing notions of generalized multi
category under the umbrella of virtual T-algebras, where T is a monad on some virtual
 equipment. Since getting to this point already takes us over 40 pages, we leave to future
 work most of the development of the theory and its applications, along with more specific
 comparisons between existing theories (see [CS10a, CS10b]).
 However, we do spend some time in §9 on the topic of representability. This is a central
 idea in the theory of generalized multicategories, which states that any pseudo T-algebra
 (or, in fact, any oplax T-algebra) has an underlying virtual T-algebra. Additionally, one
 can characterize the virtual T-algebras which arise in this way by a “representability”
 property. This can then be interpreted as an alternate definition of pseudo T-algebra
 which replaces “coherent algebraic structure” by a “universal property,” as advertised
 in [Her01]. In addition to the identification of monoidal categories with “representable”
 multicategories, this also includes the fact that compact Hausdorff spaces are T1 spaces
 with additional properties, and that fibrations over a category S are equivalent to pseudo
functors Sop
 Cat. In [CS10b] we will extend more of the theory of representability
 in [Her01] to our general context.
 Finally, the appendices are devoted to showing that all existing notions of generalized
 multicategory are included in our framework. In Appendix A we prepare the way by
 giving sufficient conditions for our constructions on virtual double categories to preserve
 composites, which is important since most existing approaches use bicategories. Then in
 Appendix B we summarize how each existing theory we are aware of fits into our context.
 We have chosen to postpone these comparisons to the end, so that the main body of
 the paper can present a unified picture of the subject, in a way which is suitable also as
 an introduction for a reader unfamiliar with any of the existing approaches. It should be
 noted, though, that we claim no originality for any of the examples or applications, or
 the ideas of representability in §9. Our goal is to show that all of these examples fall into
 the same framework, and that this general framework allows for a cleaner development of
 the theory.
 1.1. Acknowledgements. The first author would like to thank Bob Par´e for his sug
gestion to consider “double triples”, as well as helpful discussions with Maria Manuel
 Clementino, Dirk Hofmann, and Walter Tholen. The second author would like to thank
 David Carchedi and the patrons of the n-Category Caf´e blog for several helpful conversa
tions. Both authors would like to thank the editor and the referee for helpful suggestions.
9
 1. Virtual double categories
 The definition of virtual double category may be somewhat imposing, so we begin with
 some motivation that will hopefully make it seem inevitable. We seek a framework which
 includes all sorts of generalized multicategories. Since categories themselves are a particu
lar sort of generalized multicategory (relative to an identity monad), our framework should
 in particular include all sorts of generalized categories. In particular, it should include
 both categories enriched in a monoidal category V and categories internal to a category
 Cwith pullbacks, so let us begin by considering how to unify these two situations.
 We start by recalling that both V-enriched categories and C-internal categories are
 particular cases of monoids in a monoidal category. On the one hand, if V is a cocomplete
 closed monoidal category and O is a fixed set, then V-enriched categories with object
 set O can be identified with monoids in the monoidal category of O-graphs in V—i.e.
 (O × O)-indexed families of objects of V, with monoidal structure given by “matrix
 multiplication.” On the other hand, if C is a category with pullbacks and O is an object
 of C, then C-internal categories with object-of-objects O can be identified with monoids
 in the monoidal category of O-spans in C—i.e. diagrams of the form O ← A → O, with
 monoidal structure given by span composition.
 Now, both of these examples share the same defect: they require us to fix the objects
 (the set O or object O). In particular, the morphisms of monoids in these monoidal
 categories are functors which are the identity on objects. It is well-known that one can
 eliminate this fixing of objects by combining all the monoidal categories of graphs and
 spans, respectively, into a bicategory. (In essense, this observation dates all the way back
 to [B´en67].) In the first case the relevant bicategory V-Mat consists of V-matrices: its
 objects are sets, its arrows from X to Y are (X × Y)-matrices of objects in V, and its
 composition is by “matrix multiplication.” In the second case the relevant bicategory
 Span(C) consists of C-spans: its objects are objects of C, its arrows from X to Y are
 spans X ←A→Y in C, and its composition is by pullback. It is easy to define monoids
 in a bicategory to generalize monoids in a monoidal category2.
 However, we still have the problem of functors. There is no way to define morphisms
 between monoids in a bicategory so as to recapture the correct notions of enriched and
 internal functors in V-Mat and Span(C). But we can solve this problem if instead of
 bicategories we use (pseudo) double categories, which come with objects, two different
 kinds of arrow called “horizontal” and “vertical,” and 2-cells in the form of a square:
 2Monoids in a bicategory are usually called monads. However, we avoid that term for these sorts of
 monoids for two reasons. Firstly, the morphisms of monoids we are interested in are not the same as the
 usual morphisms of monads (although they are related; see [LS02, §2.3–2.4]). Secondly, there is potential
 for confusion with the monads on bicategories and related structures which play an essential role in the
 theory we present.
10
 BothV-Mat andSpan(V)naturallyenlargetopseudodoublecategories, interpreting
 theirexistingarrowsandcompositionashorizontalandaddingnewverticalarrows. For
 V-Mat thenewvertical arrowsare functionsbetweensets,while forSpan(C)thenew
 vertical arrowsaremorphisms inC. Wecannowdefinemonoids inadoublecategory
 (relativetothehorizontal structure)andmorphismsbetweensuchmonoids(makinguse
 oftheverticalarrows)soastorecapturethecorrectnotionof functor inbothcases(see
 Definition2.8).
 Thefinalgeneralizationfrompseudodoublecategoriestovirtualdoublecategoriesis
 moredifficult tomotivateat themoment,butasremarkedinthe introduction,wewill
 finditessential in§4.Conceptually(and, infact, formally),avirtualdoublecategoryis
 relatedtoapseudodoublecategoryinthesamewaythatamulticategoryisrelatedtoa
 monoidal category. Thus, justasonecandefinemonoids inanymulticategory, onecan
 likewisedosoinanyvirtualdoublecategory.
 2.1. Definition.AvirtualdoublecategoryXconsistsof thefollowingdata.
 •AcategoryX(theobjectsandverticalarrows),withthearrowswrittenvertically:
 X
 Y
 f
 •Forany twoobjectsX,Y ∈X, aclassof horizontal arrows,writtenhorizontally
 withaslashthroughthearrow:
 X Y p
 •Cells,withverticalsourceandtarget,andhorizontalmulti-sourceandtarget,written
 asfollows:
 Y0 Y1 q
 X0
 Y0
 f
 X0 Xn Xn
 Y1
 g
 X0 X1
 p1 X1 X2
 p2 X2 ··· p3 ··· Xn
 pn
 α (2.2)
 Note that this includescellswithsourceof length0, inwhichcasewemust have
 X0=Xn; suchcellsarevisuallyrepresentedas follows:
 X
 Y0
 f
 X
 Y1
 g
 4 4 4 4 4 4 4 4
 Y0 Y1 q
 α
11
 •Forthefollowingconfigurationofcells,
 Z0 Z1 r
 Y0
 Z0
 g0
 Y0 Ym Ym
 Z1
 g1
 Y0 Y1 q1
 X0
 Y0
 f0
 X0 Xn1
 p11...p1n1 _ _ _ _ _ _ _ Xn1
 Y1
 f1
 Y1 Y2 q2
 Xn1
 Y1
 Xn1
 Xn2
 p21...p2n1 _ _ _ _ _ _ _ Xn2
 Y2
 f2
 Xn2
 ··· ··· _ _ _ _ _ _ _ ··· Xnm
 ··· _ _ _ _ _ _ _
 Y2 Ym q3···qm
 _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _
 Xnm
 Ym
 fm α1 α2 α3···αm
 β
 acompositecell
 Z0 Z1 r
 Y0
 Z0
 g0
 Y0 Ym Ym
 Z1
 g1
 Y0
 X0
 Y0
 f0
 X0 Xn1
 p11...p1n1 _ _ _ _ _ _ _ Xn1
 Xn1
 Xn1
 Xn2
 p21...p2n1 _ _ _ _ _ _ _ Xn2
 Xn2
 ··· ··· _ _ _ _ _ _ _ ··· Xnm
 ··· _ _ _ _ _ _ _ Xnm
 Ym
 fm
 β(αm⊡···⊡α1)
 •Foreachhorizontalarrowp,anidentitycell
 X Y p
 X Y p
 X
 X
 Y
 Y
 1p
 •Associativity and identity axioms for cell composition. The associativity axiom
 statesthat
 γ(βm⊡···⊡β1) (αmkm
 ⊡···⊡α11)=γ βm(αmkm
 ⊡···⊡αm1)⊡···⊡β1(α1k1
 ⊡···⊡α11)
 whiletheidentityaxiomsstatethat
 α(1p1
 ⊡···⊡1pn
 )=α and 1q(α1)=α1
 whenevertheseequationsmakesense.
 2.3. Remark.Asmentioned inthe introduction, virtual doublecategorieshavealso
 beencalledfc-multicategoriesbyLeinster[Lei04]andmulticat´egoriesbyBurroni [Bur71].
 Our terminologyischosentoemphasizetheircloserelationshipwithdoublecategories,
 andtofit intothegeneralnamingschemeof§9.
 2.4. Remark.Inmuchofthedouble-categoryliterature, itiscommonforthe“slashed”
 arrows(spans,profunctors,etc.) tobethevertical arrows.Wehavechosentheopposite
 conventionpurelyforeconomyof space: thecells inavirtualdoublecategoryfitmore
 convenientlyonapagewhentheirmulti-sourceisdrawnhorizontally.
12
 2.5. Examples.Assuggested bythediscussion at the beginning of this section, monoidal
 categories, bicategories, 2-categories, multicategories, and pseudo double categories can
 each be regarded as examples of virtual double categories, by trivializing the vertical or
 horizontal structure in various ways; see [Lei02, p. 4] or [Lei04, §5.1] for details.
 We now present the two virtual double categories that will serve as initial inputs for
 most our examples: spans and matrices. (Both are also described in [Lei04, Ch. 5].) For
 consistency, we name all of our virtual double categories by their horizontal arrows, rather
 than their vertical arrows or objects.
 2.6. Example. Let (V,⊗,I) be a monoidal category. The virtual double category
 V-Mat is defined as follows: its objects are sets, its vertical arrows are functions, its
 horizontal arrows X p Y are families {p(y,x)}x∈X,y∈Y of objects of V (i.e. (X × Y )
matrices), and a cell of the form (2.2) consists of a family of V-arrows
 p1(x1, x0) ⊗ p2(x2,x1) ⊗ ··· ⊗ pn(xn,xn−1) α q(fxn,gx0),
 one for each tuple (x0,...,xn) ∈ X0 ×···×Xn. When n = 0, of course, the n-ary tensor
 product on the left is to be interpreted as the unit object of V.
 In particular, if V is the 2-element chain 0 ≤ 1, with ⊗ given by ∧, then the horizontal
 arrows of V-Mat are relations. In this case we denote V-Mat by Rel.
 It is well-known that V-matrices form a bicategory (and, in fact, a pseudo double
 category) as long as V has coproducts preserved by ⊗. However, if we merely want a
 virtual double category, we see that this requirement is unnecessary. (In fact, V could be
 merely a multicategory itself.)
 2.7. Example. For a category C with pullbacks, the virtual double category Span(C)
 is defined as follows: its objects and vertical arrows are those of C, its horizontal arrows
 X p Y arespans X P Y,andacellofthe form (2.2) is a morphism of spans
 P1 ×X1 
P2 ×X2 
···×Xn−1 
Pn 
α Q
 lying over f and g. When n = 0, the n-ary span composite in the domain is to be
 interpreted as the identity span X0
 X0
 X0.
 Note that in this case, we do need to require that C have pullbacks. If C does not
 have pullbacks, a more natural setting would be to consider Span(C) as a “co-virtual
 double category”, in which the horizontal target of a cell is a string of horizontal arrows.
 However, co-virtual double categories do not provide the structure necessary to define
 generalized multicategories.
 We now recall the construction of monoids and modules in a virtual double category
 from [Lei04, §5.3].
13
 2.8. Definition.LetXbeavirtualdoublecategory.ThevirtualdoublecategoryMod(X)
 hasthefollowingcomponents:
 •Theobjects (monoids) consist of fourparts (X0,X,¯ x,ˆ x): anobjectX0 ofX, a
 horizontalendo-arrowX0
 X X0 inX,andmultiplicationandunitcells
 X0 X0
 X X0 X0
 X X0
 X0
 X0
 X0 X0 X0 X
 ¯ x and
 X0
 X0
 X0
 X0
 4 4 4 4 4 4 4
 4 4 4 4 4 4 4
 X0 X0 X
 ˆ x
 satisfyingassociativityandidentityaxioms.
 •Thevertical arrows (monoidhomomorphisms)consist of twoparts (f0,f): a
 verticalarrowX0
 f0 Y0 inXandacell inX:
 X0 X0
 X
 Y0 Y0 Y
 X0
 Y0
 f0
 X0
 Y0
 f0 f
 whichiscompatiblewiththemultiplicationandunitsofXandY.
 •Thehorizontalarrows(modules)consistofthreeparts(p,¯ pr,¯ pl):ahorizontalarrow
 X0
 p Y0 inXandtwocellsinX:
 X0 X0
 X X0 Y0
 p X0
 X0
 Y0
 Y0 X0 Y0 p
 ¯ pr and
 X0 Y0
 p Y0 Y0
 Y X0
 X0
 Y0
 Y0 X0 Y0 p
 ¯ pl
 whicharecompatiblewiththemultiplicationandunitsofXandY.
 •ThecellsarecellsinX:
 (Y0)0 (Y1)0
 (X0)0
 (Y0)0
 f0
 (X0)0 (Xn)0 (Xn)0
 (Y1)0
 g0
 (Y0)0 (Y1)0 q
 (X0)0 (X1)0
 p1 (X1)0 (X2)0
 p2 (X2)0 ··· p3 ··· (Xn)0
 pn
 α
 whicharecompatiblewiththe leftandrightactionsof thehorizontalcells.
 Notethat,asobservedin[Lei04, §5.3],wecandefineMod(X)withoutrequiringany
 hypothesesonthevirtualdoublecategoryX, incontrasttothesituationforbicategories
 orpseudodoublecategories.
14
 2.9. Example. We denote the virtual double category Mod(V-Mat) by V-Prof; its
 objects are V-enriched categories, its vertical arrows are V-functors, its horizontal arrows
 are V-profunctors, and its cells are a generalization of the “forms” of [DS97] (including,
 as a special case, natural transformations between profunctors). When V is closed (hence
 enriched over itself) and symmetric, V-profunctors C H D can be identified with V
functors Dop × C
 V.
 Again, note that because we are working with virtual double categories, we do not
 require that V have any colimits (in fact, V could be merely a multicategory).
 2.10. Example.Let C be a category with pullbacks. We denote the virtual double cat
egory Mod(Span(C)) by Prof(C); it consists of internal categories, functors, profunctors,
 and transformations in C.
 Note that Set-Mat ∼ = Span(Set) and thus Set-Prof ∼
 =Prof(Set).
 1. Monads on a virtual double category
 We claimed in §1 that the “inputs” of a generalized multicategory are parametrized by
 a monad. Why should this be so? Suppose that we have an operation T which, given a
 set (or object) of objects X, produces a set (or object) TX intended to parametrize such
 inputs. For “ordinary” multicategories, TX will be the set of finite lists of elements of X.
 Now, from the perspective of the previous section, the data of a category includes
 an object A0 and a horizontal arrow A0
 A A0 in some virtual double category. For
 example, if we work in Set-Mat, then A is a matrix consisting of the hom-sets A(x,y)
 for every x,y ∈ A0. Now, instead, we want to have hom-sets A(x,y) whose domain x is
 an element of TA0. Thus, it makes sense to consider a horizontal arrow A0
 A TA0 as
 part of the data of a T-multicategory. (We use A0
 TA0 rather than TA0
 A0, for
 consistency with Examples 2.9 and 2.10: the codomain of the horizontal arrow datum of
 a monoid specifies the domains of the arrows in that monoid.)
 However, we now need to specify the units and composition of our generalized multi
category. The unit should be a cell into A with 0-length domain, but its source and
 target vertical arrows can no longer both be identities because A0= TA0. In an ordinary
 multicategory, the identities are morphisms (x) 1x x whose domain is a singleton list;
 in terms of Set-Mat this can be described by a cell
 •••••••
 A0
 A0
 •
 A0
 A0 
••••••••
 A
 ?
 ?
 ?
 ?
 ?
 η
 ?
 ?
 ?
 TA0
 TA0
 where A0 
η TA0 is the inclusion of singleton lists.
15
 Regarding composition, in an ordinary multicategory we can compose a morphism
 (y1, . . ., yn) g z not with a single morphism, but with a list of morphisms (f1,...,fn)
 where (xi1,...,xiki
 ) fi yi. In terms of Set-Mat this represents the fact that we cannot
 ask for a multiplication cell with domain A
 A , since the domain of A does not
 match its codomain, but instead we can consider a cell with domain A
 TA ,where we
 extend T to act on Set-matrices in the obvious way. Now, however, the codomain of TA
 is T2A0; in order to have a cell with codomain A we need to “remove parentheses” from
 the resulting domain ((x11,...,x1k1
 ),...,(xn1,...,xnkn
 )) to obtain a single list. Thus the
 composition should be a cell
 A0
 A0
 A0
 A0
 A0
 TA0
 TA
 A TA0 T2A0
 T2A0
 T2A0
 µ
 A
 TA0
 TA0
 where µ is the “remove parentheses” function. Of course, these functions η and µ are the
 structure maps of the “free monoid” monad on Set. Thus we see that in order to define
 ordinary multicategories, what we require is an “extension” of this monad to Set-Mat.
 In order to have a good notion of a monad on a virtual double category, we need at
 least a 2-category of virtual double categories. Since virtual double categories are them
selves a special case of generalized multicategories, it suffices to observe that generalized
 multicategories of any sort form a 2-category. However, since we have not yet defined
 generalized multicategories in our context, at this point in our exposition it is appropriate
 to give an explicit description of the 2-category vDbl.
 Of course, the objects of vDbl are virtual double categories, and its 1-morphisms
 (called functors of virtual double categories) are the obvious structure-preserving maps:
 functions from the objects, vertical and horizontal arrows, and cells of the domain to
 those of the codomain, preserving all types of source, target, identities, and composition.
 However, the definition of 2-morphisms in vDbl is slightly less obvious.
 3.1. Definition. Given functors X F
 G
 Yofvirtual double categories, a transforma
tion F θ Gconsists of the following data.
 • For each object X in X, a vertical arrow FX θX GX, which form the components
 of a natural transformation between the vertical parts of F and G.
 • For each horizontal arrow X p Y in X, a cell in Y:
 FX
 FX
 θX
 GX
 GX
 Fp
 θp
 Gp
 FY
 FY
 θY
 GY
 GY
 (3.2)
16
 • An axiom asserting that θ is “cell-natural,” meaning that
 θq(Fα) = (Gα)(θp1 
⊡ ···⊡θpn
 )
 whenever this makes sense.
 Virtual double categories, functors, and transformations form a 2-category denoted vDbl.
 3.3. Example.AnylaxmonoidalfunctorV NWinducesfunctorsV-Mat N∗ W-Mat
 and V-Prof N∗ W-Prof in an evident way. Moreover, any monoidal natural transfor
mation N ψ M between lax monoidal functors induces transformations N∗ 
ψ∗ M∗ in
 both cases. In this way (−)-Mat and (−)-Prof become 2-functors from the 2-category of
 monoidal categories to vDbl.
 3.4. Example.Similarly, any pullback-preserving functor C N D between categories
 with pullbacks induces functors Span(C) N∗ Span(D) and Prof(C) N∗ Prof(D), and
 any natural transformation N ψ M between such functors induces transformations
 N∗ 
ψ∗ M∗, thereby making Span(−) and Prof(−) into 2-functors as well.
 3.5. Example. When restricted to bicategories or pseudo double categories, functors
 of virtual double categories are equivalent to the usual notions of lax functor.
 This is a special case of a general fact; see Theorem 9.13. When transformations of
 virtual double categories are similarly restricted, they become icons in the sense of [Lac10]
 (for bicategories) and vertical transformations (for pseudo double categories).
 By a monad on a virtual double category X, we will mean a monad in the 2-category
 vDbl. Thus, it consists of a functor T : X
 X and transformations η: Id
 µ: TT
 T and
 T satisfying the usual axioms. We now give the examples of such monads that
 we will be interested in.
 3.6. Example. Since 2-functors preserve monads, any pullback-preserving monad on a
 category C with pullbacks induces monads on Span(C) and Prof(C). Examples of such
 monads include the following.
 • The “free monoid” monad on Set (or, more generally, on any countably lextensive
 category).
 • The “free M-set” monad (M ×−) on Set, for any monoid M (or more generally,
 for any monoid object in a category with finite limits).
 • The monad (−)+1 on any lextensive category.
 • The “free category” monad on the category of directed graphs.
 • The “free strict ω-category” monad on the category of globular sets.
17
 Many more examples can be found in [Lei04, pp. 103–107]; see also §B.1. By the argument
 above, each of these monads extends to a monad on a virtual double category of spans.
 The assignments V → V-Mat and V → V-Prof are also 2-functorial, but the monads
 we obtain in this way from monads on monoidal categories are not usually interesting for
 defining multicategories. However, there are some general ways to construct monads on
 virtual double categories of matrices, at least when V is a preorder. The following is due
 to [Sea05], which in turn expands on [CHT04].
 3.7. Example. By a quantale we mean a closed symmetric monoidal complete lattice.
 A quantale is completely distributive if for any b ∈ V we have b = {a | a ≺ b},
 where a ≺ b means that whenever b ≤ S then there is an s ∈ S with a ≤ s. (If in this
 definition S is required to be directed, we obtain the weaker notion of a continuous lattice.)
 For us, the two most important completely distributive quantales are the following.
 • The two-element chain 2 = (0 ≤ 1).
 • The extended nonnegative reals R+ = [0,∞] with the reverse of the usual ordering
 and ⊗ =+.
 A functor said to be taut if it preserves pullbacks of monomorphisms (and therefore
 also preserves monomorphisms). A monad is taut if its functor part is taut, and moreover
 the naturality squares of η and µ for any monomorphism are pullbacks. Some important
 taut monads on Set are the identity monad, the powerset monad (whose algebras are
 complete lattices), the filter monad, and the ultrafilter monad (whose algebras are compact
 Hausdorff spaces).
 Now let V be a completely distributive quantale and T a taut monad on Set. For a
 V-matrix X p Y and elements F ∈ TX and G ∈TY, define
 Tp(G,F) =
 v ∈V ∀B⊆Y : G∈TB⇒F∈T(pv[B]) ,
 where
 pv[B] = x ∈X ∃y∈B:v≤p(y,x) .
 It is proven in [Sea05] that this action on horizontal arrows extends T to a monad on
 V-Mat. (Actually, Seal shows that it is a “lax extension of T to V-Mat with op-lax unit
 and counit”; we will show in §B.6 that this is the same as a monad on V-Mat.)
 In [Sea05] this monad on V-Mat is called the “canonical extension” of T (note, how
ever, that it is written backwards from his definition, as our Kleisli arrows will be X
 whereas his are TX
 TY,
 Y). Since V-Mat is isomorphic to its “horizontal opposite,” there
 is also an “op-canonical extension”, which is in general distinct (although in some cases,
 such as for the ultrafilter monad, the two are identical). There are also many other
 extensions: for more detail, see [SS08].
 Another general way of constructing monads on virtual double categories is to apply
 the construction Mod from the previous section, which turns out to be a 2-functor. Its 1
functoriality is fairly obvious and was described in [Lei04, §5.3]; its action on 2-morphisms
 is given as follows.
18
 3.8. Definition.Let X F
 G
 Ybefunctors between virtual double categories, and F θ G
 a transformation. One can define a transformation
 Mod(F) Mod(θ) Mod(G)
 whose vertical-arrow component at an object (X0,X, ¯ x, ˆ x) is the monoid homomorphism
 FX0
 FX0
 θX0
 GX0
 GX0
 F(X)
 θX
 G(X)
 FX0
 FX0
 θX0
 GX0
 GX0
 and whose cell component at a horizontal arrow (p, ¯ pr, ¯ pl) is given by
 FX0
 FX0
 θX0
 GX0
 GX0
 Fp
 θp
 Gp
 FY0
 FY0
 θY0
 GY0
 GY0
 3.9. Proposition. With action on objects, 1-cells, and 2-cells described above, Mod is
 an endo-2-functor of vDbl.
 Note that the 2-functors (−)-Prof and Prof(−) can now be seen as the composites of
 Mod with (−)-Mat and Span(−), respectively.
 3.10. Corollary.A monad T on a virtual double category X induces a monad Mod(T)
 on Mod(X).
 3.11. Example. Any monad T on V-Mat induces a monad on V-Prof. For instance,
 this applies to the monads constructed in Example 3.7.
 3.12. Example. Let V be a symmetric monoidal category with an initial object ∅
 preserved by ⊗. Then the “free monoid” monad T on Set extends to a monad on V-Mat
 as follows: a V-matrix X p Y is sent to the matrix TX Tp TY defined by
 Tp (y1,...,ym),(x1,...,xn) = p(y1,x1) ⊗ ... ⊗p(yn,xn) if n = m
 ∅
 if n= m
 Applying Mod, we obtain an extension of the “free strict monoidal V-category” monad
 from V-Cat to V-Prof.
19
 3.13. Example. Likewise, any monad T on Span(C) extends to a monad on Prof(C).
 But most interesting monads on Span(C) are induced from C, so this gains us little
 beyond the observation that Prof(−) is a 2-functor.
 Not every monad on V-Prof or Prof(C) is induced by one on V-Mat or Span(C),
 however. The following examples are also important.
 3.14. Example.Let V beasymmetric monoidal category with finite colimits preserved
 by ⊗ on both sides. Then there is a “free symmetric strict monoidal V-category” monad
 T on V-Cat, defined by letting the objects of TX be finite lists of objects of X, with
 
 
 TX (x1,...,xn),(y1,...,ym) =
 σ∈Sn1≤i≤n
 ∅
 X(xσ(i), yi) if n = m
 if n= m.
 Anearly identical-looking definition for profunctors extends this T to a monad on V-Prof.
 A similar definition applies for braided monoidal V-categories.
 3.15. Example.For V asin Example 3.14, there is also a “free V-category with strictly
 associative finite products” monad on V-Cat. The objects of this TX are again finite lists
 of objects of X, but now we have
 TX (x1,...,xm),(y1,...,yn) =
 X(xj,yi).
 1≤i≤n1≤j≤m
 If V is cartesian monoidal, then this can equivalently be written as
 TX (x1,...,xm),(y1,...,yn) =
 X(xα(i), yi).
 α: n→m1≤i≤n
 Again, a nearly identical definition for profunctors extends this to a monad on V-Prof.
 3.16. Example.Monadsthatfreely adjoin other types of limits and colimits also extend
 from V-Cat to V-Prof in a similar way. For instance, if V is a locally finitely presentable
 closed monoidal category as in [Kel82], there is a “free V-category with cotensors by
 f
 initely presentable objects” monad on V-Cat. An object of TX consists of a pair3 (v;x)
 where x ∈ X and v ∈V is finitely presentable. On homs we have
 TX(v;x),(w;y) = w,X(x,y)⊗v .
 As before, a nearly identical definition extends this to V-Prof.
 3To be precise, this definition only gives a pseudomonad on V-Cat. It is, however, easy to modify it
 to make a strict monad.
20
 1. Generalizedmulticategories
 Wenowlackonlyonefinal ingredient for thedefinitionof generalizedmulticategories.
 Sincemulticategories are like categories, we expect themtoalsobemonoids insome
 virtual doublecategory. However, aswehave seen in§3, theirunderlyingdatashould
 includeahorizontalarrowA0 TA0ratherthanA0 A0.Thusweneedtoconstruct,
 givenTandX, avirtualdoublecategoryinwhichthehorizontal arrowsarehorizontal
 arrowsoftheformA0 TA0 inX.Thisisthepurposeofthefollowingdefinition.
 4.1. Definition.LetTbeamonadonavirtual doublecategoryX. Definethehori
zontalKleislivirtualdoublecategoryofT,H-Kl(X,T),as follows.
 • ItsverticalcategoryisthesameasthatofX.
 •AhorizontalarrowX p Y isahorizontalarrowX p TY inX.
 •Acellwithnullarysourceusestheunitof themonad,sothatacell
 X
 Y
 f
 X
 Z
 g
 4 4 4 4 4 4 4 4
 Y Z p
 α
 inH-Kl(X,T) isacell
 X
 Y
 f
 X
 TX
 η , , , , ,
 TX
 TZ
 Tg , , , , ,
 Y TZ p
 α
 inX(notethatTg◦η=η◦gbynaturality).
 •Acellwithnon-nullarysourceusesthemultiplicationof themonad,sothatacell
 Y0 Y1
 X0
 Y0
 f
 X0 Xn Xn
 Y1
 g
 Y0 Y1 q
 X0 X1
 p1 X1 X2
 p2 X2 ··· p3 ··· Xn
 pn
 α
21
 inH-Kl(X,T) isacell
 X0
 Y0
 f
 Y0 TY1 q
 X0 TX1
 p1 TX1 T2X2
 Tp2 T2X2 ··· T2p3 ··· TnXn
 Tn−1pnTnXn
 . . .
 µ
 . . .
 TXn
 µ
 TXn
 TY1
 Tg
 α
 inX(notethatTg◦µn−1=µn−1◦Tng,bynaturality).
 •Thecompositeof
 ···
 α1
 ···
 α2
 ···
 αn ···
 β
 isgivenbythecompositeof
 α1
 ···
 µ
 ···
 µ
 µ
 µ
 ···
 ···
 ···
 µ
 µ
 Tµ
 Tα2
 ···
 Tµ
 µ
 µ µ
 µ
 Tµ
 T2µ
 T2α3
 ··· ···
 ··· ···
 ···
 µ
 µ
 Tµ
 T2µ
 T3α4
 µ µ
 µ µ
 Tµ Tµ
 ··· ···
 ···
 ···
 ··· ···
 ···
 ···
 ···
 Tn−2µ Tn−2µ ···
 Tn−1αn
 β
 inX.
 • IdentitycellsusethoseofX:
 X TY p
 X TY p
 X
 X
 TY
 TY
 1P
22
 In general, the associativity for H-Kl(X,T) is shown by using the (cell) naturality of
 µ and η, as well as the monad axioms. The general associativity is too large a diagram
 to show here; instead, we will demonstrate a sample associativity calculation, which is
 representative of the general situation. Consider the following cells in H-Kl(X,T):
 J
 J
 α1
 α2
 β1
 α3
 γ
 J
 α4
 β2
 J
 J
 J
 J
 J
 There are two possible ways to compose these cells: either composing the bottom first:
 (γ(β1 ⊡β2))(α1 ⊡ α2 ⊡α3 ⊡α4)
 or the top two first, followed by composition with the bottom:
 γ((β1(α1 ⊡ α2)) ⊡ (β2(α3 ⊡ α4)))
 The first composite is given by the following composite in X:
 Tµ Tµ
 α1 Tα2
 β1
 T2α3
 µ
 ?
 ?
 ?
 ?
 ?
 ?
 ?
 T3α4
 ?
 µ
 Tβ2
 γ
 By using cell naturality of µ twice, the above becomes
 Tµ Tµ
 α1 Tα2
 β1
 µ µ
 Tα3
 J
 J
 J
 J
 J
 J
 T2α4
 Tβ2
 ?
 ?
 J
 J
 γ
23
 wethenusethemonadaxiomTµ◦µ=µ◦µtoget
 α1 Tα2
 µ µ
 µ µ
 β1
 Tα3 J J J J J J J J
 T2α4
 Tβ2
 γ
 whichisthesecondcompositeγ((β1(α1⊡α2))⊡(β2(α3⊡α4))).
 4.2. Remark.Unfortunately,wedonotknowofanyuniversalpropertysatisfiedbythis
 construction. Inparticular,H-Kl(X,T) isnotaKleisliobject forT invDbl inthesense
 of [Str72a];thelatterwouldinsteadcontainverticalKleisliarrows. Infact, forgeneralX
 thereneednotevenbeacanonical functorX H-Kl(X,T).
 Wecannowgiveourfirstpreliminarydefinitionofgeneralizedmulticategoriesrelative
 toamonadT.
 4.3. Definition.LetT beamonadonavirtual doublecategoryX. AT-monoidis
 definedtobeamonoidinH-Kl(X,T),andlikewiseforaT-monoidhomomorphism.
 WedenotethevirtualdoublecategoryMod(H-Kl(X,T)),whoseobjectsareT-monoids,by
 KMod(X,T).
 Asareference, thedata foraT-monoidconsistsofanobjectX0∈X, ahorizontal
 arrowX0
 X TX0 inX,andcells
 X0 TX0
 X0
 X0
 X0 T2X0 T2X0
 TX0
 µ
 X0 TX0 X
 X0 TX0
 X TX0 T2X0
 TX
 ¯ x and
 X0
 X0
 X0
 TX0
 η
 4 4 4 4 4 4 4
 X0 TX0 X
 ˆ x
 Notethatthesecellshavepreciselytheformswepredictedatthebeginningof§3.
 4.4. Remark.Wehave seen in§2 thatMod isa2-functor. In fact, under suitable
 hypotheses (involvingthenotionsof restrictionandcomposites tobe introduced in§5
 and§7),H-Kl isalsoa(pseudo) functor,andthussoisKMod. Infact,H-Kl isapseudo
 functor intwodifferentways, correspondingtothetwodifferentkindsofmorphismsof
 monads: laxandcolax. Thiswasobservedby[Lei04] inhiscontext;wewilldiscussthe
 functorialityofH-KlandKModinourframeworkintheforthcoming[CS10a].
 Wenowconsidersomeexamples.
24
 4.5. Example. Of course, if T is the identity monad on any X, then a T-monoid is just
 a monoid, and KMod(X,T) = Mod(X).
 Recall from Example 3.6 that any pullback-preserving monad C T C extends to a
 monad on Span(C).
 4.6. Example. If T is the “free monoid” monad extended to Span(Set) ∼ = Set-Mat,
 then a T-monoid consists of a set A0, a Set-matrix {A((x1,...,xn),y)}xi,y∈A0
 , and com
position and identity functions. It is easy to see that this reproduces the notion of an
 ordinary multicategory. Likewise, T-monoid homomorphisms are functors between multi
categories.
 4.7. Example. If T is the “free category” monad on directed graphs, then a T-monoid
 is a virtual double category. (This is, of course, the origin of the name “fc-multicategory,”
 where fc is a name for this monad.) The vertices and edges of the directed graph A0
 are the objects and horizontal arrows, respectively, while in the span A0 ← A → TA0
 the vertices of A are the vertical arrows and its edges are the cells. Likewise, T-monoid
 homomorphisms are functors between virtual double categories.
 4.8. Example. Let M be a monoid and T = (M ×−) the “free M-set” monoid on
 Span(Set). A T-monoid consists of a set A0 and a family of sets {A(m;x,y)}x,y∈A0,m∈M.
 The composition and identity functions make it into an M-graded category, i.e. a category
 in which every arrow is labeled by an element of M in a way respecting composition and
 identities. The case M = Z may be most familiar.
 4.9. Example. Let C be a lextensive category and T the monad (−)+1 on Span(C).
 A T-monoid consists of an object A0 and a span A0 ← A → A0 +1; since S is extensive,
 A decomposes into two spans A0 ← A1 → A0 and A0 ← B → 1. The composition and
 identity functions then make the first span into an internal category in C and the second
 into an internal diagram over this category.
 4.10. Example. Let S be a small category, and let T be the monad on Setob(S) whose
 algebras are functors S
 Set. Thus, for a family {Ax}x∈ob(S) in Setob(S) we have
 (TA)x =
 Ay ×S(x,y).
 y∈ob(S)
 This T preserves pullbacks, so it induces a monad on Span Setob(S) . A T-monoid
 A M TAcanbeidentified with a category over S, i.e a functor A
 S. Namely, the
 elements of the set Ax are the objects of the fiber of A over x ∈ ob(S), while M can be
 broken down into a collection of spans
 Ax
 Mx,y
 Ay ×S(x,y),
 which together supply the arrows of A and their images in S. The morphisms of T
monoids are likewise the functors over S.
25
 4.11. Example. If T is the “free strict ω-category” monad on Span(Globset), then
 a T-monoid of the form 1
 T1 can be identified with a globular operad in the sense
 of [Bat98], as described in [Lei04]. General T-monoids are globular multicategories (or
 many-sorted globular operads) as considered in [Lei04, p.273–274].
 Recall from Example 3.7 that any taut monad T on Set (such as the identity monad,
 the powerset monad, the filter monad, or the ultrafilter monad) extends to a monad on
 V-Mat for any completely distributive quantale V (such as 2 or R+). We will show
 in §B.6 that in such a case, our T-monoids are the same as the (T,V)-algebras studied
 by [CT03, CHT04, Sea05], and others; thus we have the following examples.
 4.12. Example. If T is the identity monad, then KMod(V-Mat,T) = V-Prof. Thus,
 for V = 2, T-monoids are preorders; and for V = R+, T-monoids are metric spaces (in
 the sense of [Law02]).
 4.13. Example. If T is the ultrafilter monad, and V = 2, then a T-monoid consists
 of a set equipped with a binary relation between ultrafilters and points satisfying unit
 and composition axioms. If we call this relation “convergence,” then the axioms precisely
 characterize the convergence relation in a topological space; thus T-monoids are topolog
ical spaces, and T-monoid homomorphisms are continuous functions. This observation is
 originally due to [Bar70] (note that although his construction of an ultrafilter monad on
 Rel looks quite different, it is in fact the same).
 4.14. Example. If T is the powerset monad, and V = 2, then T-monoids are closure
 spaces. A closure space consists of a set A equipped with an operation c(−) on subsets
 which is:
 • extensive: X ⊆ c(X),
 • monotone: Y ⊆ X ⇒c(Y)⊆c(X), and
 • idempotent: c(c(X)) ⊆ c(X).
 4.15. Example. If T is the ultrafilter monad and V = R+, then T-monoids are
 equivalent to approach spaces. An approach space is a set X equipped with a function
 d : X ×PX [0,∞]such that
 • d(x,{x}) = 0,
 • d(x,∅) = ∞,
 • d(x,A∪B) = min{d(x,A),d(x,B)}, and
 • ∀ǫ ≥0,d(x,A) ≤ d(x,{x : d(x,A) ≤ ǫ})+ǫ.
 Approach spaces have found applications in approximation theory, products of metric
 spaces, and measures of non-compactness: for more detail, see [Low88].
 Finally, we consider T-monoids relative to the additional examples of monads on
 V-Prof from the end of §3.
26
 4.16. Example. Let T be the “free symmetric strict monoidal V-category” monad on
 V-Prof from Example 3.14. If A0 is a discrete V-category, then a T-monoid A0
 A TA0
 is a symmetric V-enriched multicategory (known to some authors as simply a “multi
category”). Likewise, from the “free braided strict monoidal V-category” monad we
 obtain braided multicategories.
 If A0 is not discrete, then a T-monoid (for V = Set) is a symmetric multicategory in
 the sense of [BD98] and [Che04]: in addition to the multi-arrows, there is also another
 type of arrow between the objects of the multicategory which forms a category, and which
 acts on the multi-arrows.
 4.17. Example. Let T be the “free category with strictly associative finite products”
 monad on Set-Prof from Example 3.15. If A0 is a one-object discrete category, then a
 T-monoid A0
 A TA0 isaLawvere theory, as in [Law63]. If A0 has more than one object,
 but is still discrete, then a T-monoid A0
 A TA0 is a “multi-sorted” Lawvere theory.
 This is a little different from the more usual definition of Lawvere theory, but the
 equivalence between the two is easy to see. A Lawvere theory is commonly defined to
 be a category A with object set N such that each object n is the n-fold product 1n.
 This implies that A(m,n) ∼ = A(m,1)n, so it is equivalent to give just the collection of
 sets A(m,1) with suitable additional structure. Since T1 has object set N, a T-monoid
 1
 T1 also consists of sets A(m,1) for m ∈ N, and it is then straightforward to verify
 that the additional structures in the two cases are in bijective correspondence. Note,
 however, that the morphisms between such T-monoids do not correspond to all of the
 morphisms between theories considered in [Law63], but only those of “degree one;” the
 others are only visible from the “category with object set N” viewpoint.
 The relationship between these two definitions of Lawvere theory is analogous to the
 way in which an operad can also be defined as a certain sort of monoidal category with
 object set N. In fact, both arise from a very general adjunction between T-algebras and
 T-monoids; see Remark 9.16 and the forthcoming [CS10b].
 4.18. Example. If T is the “free V-category with strictly associative finite products”
 monad on V-Prof from Example 3.15 and A0 is a one-object discrete V-category, then
 a T-monoid A0
 A TA0 is a “V-enriched finite product theory.” If A0 is unchanged
 but T is instead the “free V-category with finitely presentable cotensors” monad from
 Example 3.16, then a T-monoid A0
 A TA0 isa“Lawvere V-theory”asdefined in [Pow99]
 (with the same caveat as in the previous example). To obtain a “multi-sorted Lawvere
 V-theory” we need T to adjoin both finite products and finite cotensors.
 4.19. Example. If T is any of
 • the “free symmetric strict monoidal category” monad,
 • the “free category with strictly associative finite products” monad, or
 • the “free category with strictly associative finite coproducts” monad,
27
 but now considered as a monad on Span(Cat), then a T-monoid with a discrete category
 of objects is a club in the sense of [Kel72b] and [Kel72a] (relative to P, S, or Sop, in
 Kelly’s terminology). See also §B.4.
 4.20. Remark. When T is the “free symmetric strict monoidal category” monad on
 Set-Prof, the horizontal arrows between discrete categories in H-Kl(Set-Prof,T) are the
 generalized species of structure of [FGHW08] (called structure types in [BD01]). The
 esp´eces de structures of [Joy81, Joy86] are the particular case of horizontal arrows 1
 1
 in H-Kl(Set-Prof,T). Likewise, when T is the analogous monad on Span(Gpd), the hor
izontal arrows in H-Kl(Span(Gpd),T) are the (generalized) stuff types of [BD01, Mor06].
 We can see from these examples that for virtual double categories whose objects are
 “category-like,” it is often the T-monoids whose objects are discrete which are of particular
 interest. We will make this notion precise in §8, and propose that often a better solution
 is to consider “normalized” T-monoids.
 First, however, we must develop some additional machinery for virtual double cate
gories. We will describe when horizontal arrows have units and composites, as well as
 when horizontal arrows can be “restricted” along vertical arrows. With this theory in
 hand, we can then return to study “object discrete” and “normalized” T-monoids, as well
 as when such T-monoids are “representable”.
 4.21. Remark.If V is a complete and cocomplete closed symmetric monoidal category,
 then the virtual double category V-Prof is itself “almost” of the form H-Kl(X,T). We take
 X to be the double category Sq(V-Cat), whose objects are V-categories, whose vertical
 and horizontal arrows are both V-functors, and whose cells are V-natural transformations,
 and we define TA = VAop to be the enriched presheaf category of A. The observation is
 then that a V-profunctor A B canbe identified with an ordinary V-functor A VBop,
 so that V-Prof is almost the same as H-Kl(Sq(V-Cat),T). This is not quite right, since
 T is not really a monad due to size issues. But these problems can be dealt with, for
 instance by using “small presheaves” as in [DL07].
 Assuming the functoriality of H-Kl mentioned in Remark 4.4, this observation implies
 that if S is another monad on V-Cat related to T by a distributive law, or equivalently a
 monad in the 2-category of monads and monad morphisms (see [Bec69, Str72b]), then S
 induces a monad on V-Prof, which we can in turn use to define generalized multicategories
 as S-monoids in V-Prof. For example, since a symmetric monoidal structure on A extends
 to TA by Day convolution, the “free symmetric monoidal V-category” monad distributes
 over T, inducing its extension to V-Prof considered in Example 3.14. This is the argument
 used in [FGHW08] to construct the bicategory H(H-Kl(V-Prof,T)). Similar arguments
 apply to the monad from Example 3.15.
 1. Composites and units
 In §2 we introduced (virtual) double categories as a framework in which one can define
 monoids and monoid homomorphisms so as to include both enriched and internal cate
28
 gories with the appropriate notions of functor. However, we would certainly like to be
 able to recover natural transformations as well, but this requires more structure than is
 present in a virtual double category.
 It is not hard to see that the vertical category of any (pseudo) double category can be
 enriched to a vertical 2-category, whose 2-cells f ⇒ g are the squares of the form
 A
 g
 B
 A
 f
 B,
 and that in examples such as Prof(C) and V-Prof these 2-cells recover the appropriate
 notion of natural transformation. In a virtual double category this definition is impossible,
 since there may not be any horizontal identity arrows. However, it turns out that we can
 characterize those horizontal identities which do exist by means of a universal property.
 In fact, it is not much harder to characterize arbitrary horizontal composites (viewing
 identities as 0-ary composites). In this section we study such composites; in the next
 section we will use them to define vertical 2-categories.
 5.1. Definition. In a virtual double category, a cell
 p1
 is said to be opcartesian if any cell
 r1
 f
 r2
 . . . rm
 p1
 factors through it uniquely as follows:
 r1
 r1
 f
 r2
 r2
 . . . rm
 ···
 . . . rm
 p1
 p2 ... pn
 ⇓opcart
 q
 p2 ... pn s1 s2 ... sk
 ⇓
 t
 p2 ... pn s1 s2 ... sk
 ⇓opcart
 q
 ⇓
 t
 g
 ···
 s1
 s2
 ... sk
 g
 .
 If a string of composable horizontal arrows is the source of some opcartesian cell, we
 say that it has a composite. We refer to the target q of that cell as a composite of the
 given string and write it as p1 ⊙ ··· ⊙ pn. Similarly, if n = 0 and there is an opcartesian
29
 cell of the form
 we say that X has a unit UX.
 X
 5
 5
 5
 5
 5
 5
 5
 5
 5
 5
 ⇓ 5 5 5
 5
 5
 5
 X UX
 X.
 These universal properties make it easy to show that composites and units, when
 they exist, behave like composites and units in a pseudo double category. For example,
 composites and units are unique up to isomorphism: given two opcartesian arrows with
 the same source, factoring each through the other gives an isomorphism between their
 targets. Likewise, the composite of opcartesian cells is opcartesian, so composition is
 associative up to coherent isomorphism whenever all relevant composites exist. More
 precisely, if p ⊙ q exists, then (p ⊙q) ⊙r exists if and only if p⊙q ⊙r exists, and in that
 case they are isomorphic. It follows that if p ⊙ q and q ⊙r exist, then
 (p ⊙q)⊙r ∼ = p⊙(q⊙r),
 each existing if the other does. Similarly, any string in which all but one arrow is a unit:
 X UX ... UX X p Y UY ... UY Y
 has a composite, which is (isomorphic to) p.
 The following theorem, which was also observed in [DPP06], is a straightforward gen
eralization of the relationship between monoidal categories and ordinary multicategories
 described in [Her00]. It is also a special case of the general relationship between pseudo
 algebras and generalized multicategories, as in [Lei04, §6.6], [Her01], and §9 of the present
 paper.
 5.2. Theorem.A virtual double category is a pseudo double category if and only if every
 string of composable horizontal arrows (including zero-length ones) has a composite.
 Proof (sketch). “Only if” is clear, by definition of how a pseudo double category
 becomes a virtual one. For “if”, we use the isomorphisms constructed above; we invoke
 again the universal property of opcartesian cells to show coherence.
 5.3. Example. If V has an initial object ∅ which is preserved by ⊗ on both sides, then
 V-Mat has units: the unit of a set X is the matrix
 UX(x,x′) = I x=x′
 ∅ x=x′.
 If V has all small coproducts which are preserved by ⊗ on both sides, then V-Mat has
 composites given by “matrix multiplication.” For instance, the composite of matrices
 X p Y andY q Zis
 (p ⊙q)(z,x) =
 p(y, x) ⊗ q(z,y).
 y∈Y
30
 5.4. Example.SinceSpan(C) isapseudodoublecategory, all compositesandunits
 always exist. Composites are givenbypullback, and theunit ofX is theunit span
 X←X→X.
 RegardingunitsinV-ProfandProf(C),wehavethefollowing.
 5.5. Proposition.Foranyvirtual doublecategoryX,allunitsexist inMod(X). For
 anymonoidA, itsunitcell
 A0 A0 A
 A0
 A0
 ••••••••
 ••••••••
 A0
 A0
 ? ? ? ? ? ? ? ?
 ? ? ? ? ? ? ? ?
 A0 A0 |
 ⇓ˆa
 isopcartesianinMod(X).Therefore,UAisAitself,regardedasanA-A-bimodule.
 Proof.Firstly, theunitaxiomsofAshowthatˆais, infact,acell inMod(X).Nowwe
 mustshowthatcomposingwithˆagivesabijectionbetweencells
 D E P
 B
 D
 g
 B CC
 E
 f
 B A p1...pm_ _ _ _ _ A C q1...qn _ _ _ _ _
 ⇓α and
 D E p
 B
 D
 g
 B CC
 E
 f
 B A p1...pm_ _ _ _ _ A A AA C q1...qn _ _ _ _ _
 ⇓β
 inMod(X).Clearlycomposinganycellβofthesecondformwithˆagivesacellαofthe
 first form. Conversely, givenαof thefirst form, therearetwocellsof thesecondform
 definedbylettingAactfirstonpmfromtherightandq1fromtheleft,respectively.These
 areequalbyoneof theaxioms forαtobeacell inMod(X);we letβbetheircommon
 value. (Inthecasewhenm=0orn=0,weusetheactionofforginstead.)Theother
 axiomsforαthencarryovertoβtoshowthatit isacell inMod(X).
 TheunitaxiomsfortheactionofAonbimodulesshowthatα→β→αistheidentity,
 whiletheequivarianceaxiomsforβregardingthetwoactionsofAshowthatβ→α→β
 istheidentity.Thuswehaveabijection,asdesired.
 Therefore, sinceV-Prof=Mod(V-Mat)andProf(C)=Mod(Span(C)), theyboth
 alwayshaveallunits. Bycontrast, extraassumptionsonXarerequiredforcomposites
 toexist inMod(X);herearethetwoexamplesofgreatest interesttous.
 5.6. Example.IfVhassmallcolimitspreservedby⊗onbothsides, thenV-Profhas
 allcomposites; thecompositeofenrichedprofunctorsA p BandB q Cisgivenby
 thecoend
 (p⊙q)(z,x)= y∈B
 p(y,x)⊗q(z,y).
31
 5.7. Example. If C has coequalizers preserved by pullback, then Prof(C) has all com
posites; the composite of internal profunctors is an “internal coend.”
 Together with Proposition 5.5, these examples will suffice for the moment. In appendix
 A we will give general sufficient conditions for composites to exist in Mod(X), and for
 composites and units to exist in H-Kl(X,T).
 5.8. Remark. If Definition 5.1 is satisfied only for m = k = 0, we say that the cell is
 weakly opcartesian. We do not regard a weakly opcartesian cell as exhibiting its target
 as a composite of its source, since the weak condition is insufficient to prove associativity
 and unitality. However, a weakly opcartesian cell does suffice to detect its target as the
 composite of its source, if we already know that that composite exists. Furthermore, if
 any composable string in X is the source of a weakly opcartesian cell and moreover weakly
 opcartesian cells are closed under composition, then one can show, as in [Her00], that in
 fact every weakly opcartesian cell is cartesian; see also §9.
 Virtual double categories having only weakly opcartesian cells seem to be fairly rare;
 one example is V-Mat where V has colimits which are not preserved by its tensor product.
 Note that in this case, V-Prof need not even have weakly opcartesian cells, since we
 require ⊗ to preserve coequalizers simply to make the composite of two profunctors into
 a profunctor.
 If X and Y have units, we say that a functor (or monad) X F Y is normal if it
 preserves opcartesian cells with nullary source, which is to say it preserves units in a
 coherent way. Likewise, if X and Y have all units and composites (i.e. are pseudo double
 categories), we say that X F Y is strong if it preserves all opcartesian cells.
 5.9. Example. Any functor Span(C)
 functor C
 Span(D) induced by a pullback-preserving
 Dis strong, and in particular normal.
 5.10. Example. It is also easy to see that Mod(F) is normal for any functor F, by the
 construction of units in Proposition 5.5.
 5.11. Examples. If V is a cocomplete symmetric monoidal category in which ⊗ pre
serves colimits on both sides, then V-Mat has all composites, and the extension of the
 “free monoid” monad to V-Mat from Example 3.12 is easily seen to be strong. Since
 the “free strict monoidal V-category” monad on V-Prof is obtained by applying Mod
 to this, it is normal by our above observation. In fact, it is also strong, essentially be
cause the tensor product of reflexive coequalizers is again a reflexive coequalizer (see, for
 example, [Joh02, A1.2.12]).
 5.12. Examples. The “free symmetric strict monoidal V-category” monad on V-Prof
 from Example 3.14 is also normal, essentially by definition, as are the “free V-category
 with strictly associative finite products” monad from Example 3.15 and its relatives from
 Example 3.16. A more involved computation with coequalizers shows that the first is
 actually strong, and the second is strong whenever V is cartesian monoidal. However, it
 seems that the others are not in general strong.
32
 5.13. Examples. The monads on V-Mat constructed in Example 3.7 are not generally
 strong or even normal. Two notable exceptions are the ultrafilter monads on Rel and
 R+-Mat, of which the first is strong and the second is normal.
 We write vDbln for the locally full sub-2-category of vDbl determined by the virtual
 double categories with units and normal functors between them; thus Mod is a 2-functor
 vDbl
 vDbln. In fact, we have the following.
 5.14. Proposition.Mod is right pseudo-adjoint to the forgetful 2-functor vDbln
 vDbl.
 Proof. “Pseudo-adjoint” means that we have a pseudonatural η and ǫ that satisfy the
 triangle identities up to coherent isomorphism. We take ǫX to be the forgetful functor
 Mod(X)
 X which sends a monoid to its underlying object and a module to its un
derlying horizontal arrow; this is strictly 2-natural. If X has units, we take ηX to be
 the “unit assigning” functor X
 Mod(X) which sends X to UX (which has a unique
 monoid structure) and X p Y to itself considered as a (UX,UY)-bimodule; this is only
 pseudonatural since normal functors preserve units only up to isomorphism. But if we
 choose the units in Mod(X) according to Proposition 5.5, then the triangle identities are
 satisfied on the nose.
 In particular, if 1 denotes the terminal virtual double category, then the category
 of normal functors 1
 X is equivalent to the vertical category of X. It then follows
 from Proposition 5.14 that the category of arbitrary functors 1
 X is equivalent to the
 vertical category of Mod(X). Thus, Proposition 5.14 generalizes the well-known observa
tion (which dates back to [B´en67]) that monoids in a bicategory B are equivalent to lax
 functors 1
 B.
 5.15. Remark. It follows that Mod is a pseudomonad on the 2-category vDbln, and so
 in particular it has a multiplication
 Mod(Mod(X))
 Mod(X).
 (5.16)
 Inspection reveals that an object of Mod(Mod(X)) consists of an object X of X, two
 monoids X A X andX M X,andamonoidhomomorphism A M whose vertical
 arrow components are identities. The multiplication (5.16) simply forgets the monoid A.
 This idea will be further discussed in [CS10b].
 5.17. Remark.If X is a virtual double category in which all units and composites exist
 (equivalently, it is a pseudo double category), then it has a horizontal bicategory H(X)
 consisting of its objects, horizontal arrows, and cells of the form
 ⇓
 .
 Clearly when C and V satisfy the required conditions for all composites to exist in our
 examples, we recover in this way the usual bicategories of matrices, spans, and enriched
33
 and internal profunctors. Any functor between pseudo double categories likewise induces
 a lax functor of horizontal bicategories, but this is not true of transformations without
 additional assumptions; see Remarks 7.26 and A.5.
 1. 2-categories of T-monoids
 As proposed in the previous section, we now use the notion of units introduced there to de
f
 ine 2-categories of generalized multicategories, generalizing the approach taken in [Lei04,
 §5.3].
 6.1. Proposition. Let X be a virtual double category in which all units exist. Then it
 has a vertical 2-category VX whose objects are those of X, whose morphisms are the
 f
 vertical arrows of X, and whose 2-cells A
 g
 α B are the cells
 g
 A UA
 ⇓α
 A
 f
 B UB
 B
 in X.
 Proof. This is straightforward; note that when composing 2-cells we must use the iso
morphisms UA 
∼ = UA ⊙UA.
 In particular, for any X, Mod(X) has a vertical 2-category, which we denote Mon(X)
 and call the 2-category of monoids in X. (This 2-category is closely related to various
 2-categories of monads in a bicategory; see [LS02, §2.3–2.4].) It turns out that in this
 case, the description of the 2-cells of Mon(X) can be rephrased in a way that looks much
 more like a natural transformation.
 f
 6.2. Proposition. Giving a 2-cell A
 g
 α B in Mon(X) is equivalent to giving a cell
 ••••••••
 A0
 A0
 g0
 ?
 ?
 ?
 ⇓α0
 B0
 B0
 B0
 |
 ?
 ?
 ?
 f0
 ?
 ?
 B0
 B0
 B0
 B
34
 inXsuchthat
 B0 B0 B
 A0
 B0
 f0
 A0 A0
 A A0
 B0
 f0 ⇓f
 A0
 B0
 g0
 •••••••••
 α0
 ⇓
 B0 B0 B
 ⇓¯ b
 B0 B0 B
 B0
 B0
 B0 B0 B0
 B0
 = B0 B0 B
 A0
 B0
 g0
 A0 A0
 A A0
 B0
 g0 g⇓
 A0
 B0
 f0
 ? ? ? ? ? ? ? ? ?
 ⇓α0
 B0 B0 B
 ⇓¯ b
 B0 B0. B
 B0
 B0
 B0 B0 B0
 B0.
 (6.3)
 Proof.This isan instanceofProposition5.5,wherem=n=0, f=g=1A, and
 P=UA.
 6.4. Example.Recall thatinV-Prof=Mod(V-Mat),theobjectsareV-enrichedcat
egoriesandtheverticalmorphismsareV-enrichedfunctors; thus thesearetheobjects
 andmorphismsofMon(V-Mat). Recalling fromExample2.6thedefinitionof cells in
 V-Mat,Proposition6.2 implies thata2-cell A
 f
 g
 αB inMon(V-Mat) isgivenbya
 familyofmorphismsI αx B(fx,gx) forxanobjectofA, suchthat foreveryx,ythe
 followingsquarecommutes:
 B(gx,gy)⊗B(fx,gx) B(fx,gy).
 A(x,y)
 B(gx,gy)⊗B(fx,gx)
 g⊗αx
 A(x,y) B(fy,gy)⊗B(fx,fy) αy⊗f B(fy,gy)⊗B(fx,fy)
 B(fx,gy).
 ThisispreciselytheusualdefinitionofaV-enrichednaturaltransformation;thuswehave
 Mon(V-Mat)≃V-Cat.
 6.5. Example.Likewise,Mon(Span(C))≃Cat(C) isthe2-categoryof internal cate
gories, functors,andnaturaltransformationsinC.
 6.6. Examples.Ontheotherhand, thevertical 2-categoryofSpan(S) is justS, re
gardedasa2-categorywithonlyidentity2-cells. Thevertical 2-categoryofV-Matde
pends onwhatVis, butusually it isnotvery interesting. Thus, ingeneral, vertical
 2-categoriesareonlyinterestingforvirtualdoublecategorieswhoseobjectsare“category
like”ratherthan“set-like.”
 Now, ifT isamonadonavirtualdoublecategoryX,wewriteKMon(X,T) forthe
 2-categoryV(KMod(X,T))andcall itthe2-categoryofT-monoids inX. Itsobjectsare
 T-monoids, itsmorphismsareT-monoidhomomorphisms,andits2-cellsmaybecalledT
monoidtransformations.ByProposition6.2andthedefinitionofH-Kl(X,T),aT-monoid
35
 transformationα:f g:A Bisspecifiedbyacell
 B0 TB0 B
 A0
 B0
 g0
 ••••••••
 A0
 TB0
 Tf0◦ηA0=ηTB0◦f0
 ? ? ? ? ? ? ? ?
 B0 TB0 |
 ⇓α0
 suchthat
 TA0 T2A0 B
 A0
 TA0
 η
 A0 TA0
 A TA0
 T2A0
 η ⇓ηA
 TB0 T2B0 TB
 TA0
 TB0
 Tf0
 TA0 T2A0 T2A0
 T2B0
 T2f0 ⇓Tf
 A0
 B0
 g0
 α0
 ⇓
 B0 TB0 B
 ⇓¯ b
 B0 TB0 B
 B0
 B0
 B0 T2B0 T2B0
 TB0
 µB
 = B0 TB0 B
 A0
 B0
 g0
 A0 TA0
 A TA0
 TB0
 Tg0 g⇓
 TA0
 T2B0
 T(ηB0)◦Tf0
 ⇓Tα0
 TB0 T2B0 TB
 ⇓¯ b
 B0 TB0. B
 B0
 B0
 B0 T2B0 T2B0
 TB0.
 µB
 Manyauthorshavedefinedthis2-categoryKMon(X,T)inseeminglyad-hocways,whereas
 it fallsquitenaturallyoutof the frameworkofvirtualdoublecategories. Thiswasalso
 observedin[Lei04,§5.3]; see§B.1.
 6.7. Example.LetT be the“freemonoid”monadonSet-Mat, so thatT-monoids
 areordinarymulticategories. IfA f
 g
 Bare functors, thenaccordingtotheabove, a
 transformationf α gconsistsof, foreachx∈A,amorphismαx∈B(η(fx),gx)(that
 istosay,amorphism(fx) αx gxwithsourceoflengthone)suchthatforanymorphism
 ξ: (x1,...,xn) yinA,wehave
 αy◦f(ξ)=g(ξ)◦(αx1
 ,...,αxn
 ).
 This istheusualnotionoftransformationforfunctorsbetweenmulticategories.
 6.8. Example.WhenT is the“freecategory”monadondirectedgraphs, sothatT
monoids arevirtual double categories, T-monoidtransformations are the sameas the
 transformationswedefinedin3.1.
 6.9. Example.LetUbetheultrafiltermonadonRel,sothatU-monoidsaretopological
 spaces. IfA f
 g
 Barecontinuousmaps (i.e.U-monoidhomomorphisms), thenthere
 existsatransformationf g(whichisnecessarilyunique) justwhenforallx∈A, the
 principalultrafilterηfxconvergestogxinB. This isequivalenttosayingthatf≥g in
36
 the pointwise ordering induced by the usual specialization order on B. The situation for
 other topological examples is similar.
 Any normal functor clearly induces a strict 2-functor between vertical 2-categories. In
 fact, if 2-Cat denotes the 2-category of 2-categories, strict 2-functors, and strict 2-natural
 transformations, then we have:
 6.10. Proposition. There is a strict 2-functor V: vDbln
 2-Cat.
 In particular, any normal monad T on X induces a strict 2-monad on V(X). As we saw
 in §3, most monads on virtual double categories are “extensions” of a known monad on
 their vertical categories (or vertical 2-categories), so this construction usually just recovers
 the familiar monad we started with. In §9, we will show that V(T)-algebras are closely
 related to T-monoids.
 1. Virtual equipments
 If we have succeeded in convincing the reader that virtual double categories are inevitable,
 she may be justified in wondering why they have not been more studied. Certainly,
 virtual double categories involve significant complexity above and beyond pseudo double
 categories, and the latter suffice to describe the important examples Span(C), V-Mat,
 V-Prof, and Prof(C) as long as V and C are suitably cocomplete. However, even pseudo
 double categories have generally received less publicity than bicategories.
 One possible reason for this is that in most of the (pseudo or virtual) double categories
 arising in practice, the vertical arrows are more tightly coupled to the horizontal arrows
 that we have heretofore accounted for; in fact they can almost be identified with certain
 horizontal arrows. For example, a V-functor A f B is determined, up to isomorphism,
 by the V-profunctor A B(1,f) B defined by B(1,f)(b,a) = B(b,fa). Furthermore, this
 coupling is very important for many applications, such as the formal definition of weighted
 limits and colimits (see [Str83, Woo82]), so a mere double category (pseudo or virtual)
 would be insufficient for these purposes. Because of this, many authors have been content
 to work with bicategories, or bicategories with a collection of horizontal arrows singled
 out (such as the “proarrow equipments” of [Woo82]).
 However, while not all pseudo double categories exhibit this type of coupling, it is
 possible to characterize those that do (and they turn out to be equivalent to the “proarrow
 equipments” mentioned above). The basic idea of this dates back to [BS76], but it has
 recently been revived in various equivalent forms; see for instance [Ver92, GP99, GP04,
 DPP07, Shu08] and the Notes at the end of [Lei04, Ch. 5]. Since this type of coupling
 also plays an important role in the theory of generalized multicategories, in this section
 we give the basic definitions appropriate to the virtual case.
 The basic idea is the following. The profunctor B(1,f) considered above can be
 constructed in two stages: first we consider the hom-profunctor B(−,−): B
 B, and
 then we precompose it with f on one side. We already know from §5 that hom-profunctors
37
 are the units in V-Prof, so it remains only to characterize precomposition with functors
 in terms of V-Prof. This is accomplished by the following definition.
 7.1. Definition. A cell
 p
 f ⇓cart g
 q
 in a virtual double category is cartesian if any cell
 r1
 fh
 factors through it uniquely as follows:
 r1
 h
 f
 r2
 ⇓
 q
 r2
 ⇓
 . . . rn
 . . . rn
 p
 ⇓cart
 q
 (7.2)
 gk
 k
 g
 .
 If there exists a cartesian cell (7.2), we say that the p is the restriction q(g,f). The
 notation is intended to suggest precomposition of a profunctor q(−,−) with f and g.
 When f or g is an identity, we write q(g,1) or q(1,f), respectively. It is evident from the
 universal property that restrictions are unique up to isomorphism, and pseudofunctorial;
 that is, we have q(1,1) ∼
 =q and q(1,g)(1,f) ∼ = q(1,gf) coherently.
 We say that X has restrictions if q(g,f) exists for all q, f, and g, and that a functor
 preserves restrictions if it takes cartesian cells to cartesian cells. We write vDblr for
 the sub-2-category of vDbl determined by the virtual double categories with restrictions
 and the restriction-preserving functors.
 7.3. Examples. The virtual double categories V-Mat, V-Prof, Span(C), and Prof(C)
 have all restrictions. Restrictions in V-Mat are given by reindexing matrices, restrictions
 in Span(C) are given by pullback, and restrictions in V-Prof and Prof(C) are given by
 precomposing with functors.
 Note that the restrictions in V-Prof and Prof(C) are induced by those in V-Mat and
 Span(C), in the following general way.
 7.4. Proposition. If X is a virtual double category with restrictions, then Mod(X) also
 has restrictions.
38
 Proof. If B p D is a bimodule in X and
 f0
 A0
 A
 ⇓f
 A0
 f0
 B0 B
 B0
 and
 C0
 g0
 C0
 C
 ⇓g
 g0
 D0 D
 D0
 are monoid homomorphisms, then the restriction p(g0,f0) in X becomes an (A,C)-bimodule
 in an obvious way, making it into the restriction p(g,f) in Mod(X).
 The other ingredient in the construction of generalized multicategories also preserves
 restrictions.
 7.5. Proposition. If X is a virtual double category with restrictions and T is a monad
 on X, then H-Kl(X,T) also has restrictions.
 Proof. Let A p TB beahorizontal arrow in X, regarded as a horizontal arrow A B
 in H-Kl(X,T). It is easy to verify that the restriction of p along C f A and D g B
 in H-Kl(X,T) is given by the restriction p(Tg,f) in X.
 Therefore, if X has restrictions, so does KMod(X,T) for any monad T on X. Moreover,
 by Proposition 5.5, KMod(X,T) always also has units. As suggested in the introduction
 to this section, units and restrictions together are an especially important combination,
 so we give a special name to this situation.
 7.6. Definition. A virtual equipment is a virtual double category in which all units
 and all restrictions exist.
 We write vEquip for the locally full sub-2-category of vDbl determined by the virtual
 equipments and the normal restriction-preserving functors between them (however, see
 Theorem 7.24). We can now observe that Mod is a 2-functor from vDblr to vEquip.
 7.7. Examples. Span(C), V-Prof, and Prof(C) are always virtual equipments, and
 V-Mat is a virtual equipment whenever V has an initial object preserved by ⊗. More
 generally, Mod(X) and KMod(X,T) are virtual equipments whenever X has restrictions.
 If A f B is a vertical arrow in a virtual equipment, we define its base change
 objects to be
 B(1,f) = UB(1,f)
 These come with cartesian cells
 f
 B(1,f)
 UB
 and
 and
 B(f,1) = UB(f,1).
 B(f,1)
 f
 .
 (7.8)
 UB
39
 ByfactoringUf throughthesecartesiancells,weobtaintwofurthercells
 UA
 f
 B(f,1)
 and
 UA
 f
 B(1,f)
 (7.9)
 suchthatthefollowingequationshold:
 UA
 f
 B(1,f)
 f
 UB
 =
 UA
 f ⇓Uf f
 UB
 UA
 f
 B(f,1)
 f
 UB
 =
 UA
 f ⇓Uf f
 UB
 (7.10)
 Moreover, thefollowingequationsalsohold:
 UA B(1,f)
 f
 B(1,f) UB
 B(1,f)
 ⇓opcart
 =
 UA
 ⇓opcart
 B(1,f)
 B(1,f)
 (7.11)
 B(f,1) UA
 f
 UB B(f,1)
 B(f,1)
 ⇓opcart
 =
 B(f,1)
 ⇓opcart
 UA
 B(f,1)
 (7.12)
 Wecanverifythesebypostcomposingeachsidewiththeappropriatecartesiancell,using
 the equations (7.10), and invoking theuniqueness of factorizations throughcartesian
 cells. Inthe terminologyof [DPP07], equations (7.10)–(7.12)aresaidtomakeB(1,f)
 andB(f,1)intoacompanionandaconjointoff, respectively.
 7.13. Example.InSpan(C),thebasechangeobjectsB(1,f)andB(f,1)arethespans
 A
 A
 1A
 A
 B
 f < < < < <
 and
 A
 B
 f
 A
 A
 1A < < < < <
 respectively.Theseareoftencalledthegraphoff.
40
 7.14. Example. In V-Mat, for a function f: X
 is the matrix
 Y the base change object Y(1,f)
 Y(1,f)(x,y) = I if f(x) = y
 ∅ otherwise.
 7.15. Example. In V-Prof, the base change objects B(1,f) and B(f,1) are the rep
resentable distributors defined by B(1,f)(b,a) = B(b,fa) and B(f,1)(a,b) = B(fa,b).
 Base change objects in Prof(C) are analogous.
 At first glance, base change objects may seem only to be a particular special case of
 restrictions. However, it turns out that all restrictions can be recovered by composition
 with the base change objects (hence the name).
 7.16. Theorem. Let B p D be a horizontal arrow and f: A B and g: C D be
 vertical arrows in a virtual equipment. Then B(1,f)⊙p⊙D(g,1) exists and is isomorphic
 to p(g,f).
 Proof. Consider the composite
 B(1,f)
 f
 UB
 p
 1p
 p
 ⇓opcart
 p
 Dg
 UD
 g
 (7.17)
 By the universal property of restriction, this factors through the cartesian cell defining
 p(g, f) to give a canonical cell
 B(1,f)
 p
 p(g,f)
 D(g,1)
 We claim that this cell is opcartesian. To show this, suppose given a cell
 _
 h
 _
 _
 B(1,f)
 p
 q
 D(g,1)
 _
 _
 _
 k
 .
 (7.18)
 .
41
 Weneedtofactorituniquelythrough(7.18).Afactorizationisgivenbythecomposite
 _ _ _ _ _
 ssssssssss
 ssssssssss p(g,f)
 ssssssssss
 ssssssssss _ _ _ _ _
 K K K K K K K K K K
 K K K K K K K K K K
 L L L L L L L L L L L
 L L L L L L L L L L L
 _ _ _ _ _ UA
 f
 p(g,f)
 g
 UC
 _ _ _ _ _
 h
 _ _ _ _ _ B(1,f) p D(g,1) _ _ _ _ _
 k
 q
 .
 Toverifythatthis isafactorization,andthat it isunique,weusetheequations(7.10)
(7.12).Thedetailsaresimilartotheproofof [Shu08,Theorem4.1].
 7.19. Corollary.For vertical arrows f:A Band g:B C, the composite
 B(1,f)⊙C(1,g)alwaysexistsandisisomorphictoC(1,gf),anddually.
 Wealsohavethefollowingdualresult.
 7.20. Theorem.Forarrowsf:A Bandg:C Dinavirtual equipment,we
 haveabijectionbetweencellsof theform
 A p
 f
 C
 g
 B q D
 and
 BB(f,1)A p CD(1,g)D
 B q D
 Proof.The inversebijections aregivenbycomposingwiththe cells (7.8) and(7.9).
 (Recallthatallcompositeswithunitsexistinanyvirtualdoublecategory.)Thefactthat
 theyareinverses followsfrom(7.10)–(7.12).
 ItfollowsthatinthesituationofTheorem7.20, ifthecompositeB(f,1)⊙p⊙D(1,g)
 exists, thenit isa“corestriction”or“extension”ofpalongfandg—that is, itsatisfies
 auniversalpropertydual tothatofarestriction.
 CombiningTheorems7.16and7.20,weobtainthefollowing.
 7.21. Corollary.Inavirtualequipment, thereisabijectionbetweencellsoftheform
 p
 h
 B(1,f)
 k
 D(1,g) q
 and
 p
 h f
 g k
 q
 Takingpandqtobeunitsandhandktobeidentities,weobtain:
42
 7.22. Corollary. For vertical arrows f,g: A
 a bijection between cells f
 g in VX and cells
 B(1,f)
 ⇓
 ,
 B(1,g)
 B(f,1).
 B in a virtual equipment X, there is
 which respects composition. Similarly, we have a bijection between cells f
 B(g,1)
 g and cells
 Now suppose that X is a virtual equipment which moreover has all composites. Then
 it has a horizontal bicategory HX, and Corollaries 7.19 and 7.22 imply that f → B(1,f)
 defines a pseudofunctor VX
 HX which is locally full and faithful. Furthermore, it is
 easy to verify that B(f,1) is right adjoint to B(1,f) in HX.
 This structure—a pseudofunctor which is bijective on objects, locally full and faithful,
 and which takes each 1-cell to one having a right adjoint—was defined in [Woo82] to
 constitute an equipment. This is the structure we referred to in the introduction to
 this section, which many authors have used where we would find double categories more
 natural. In fact, it is not hard to show (see [Shu08, Appendix C]) that an equipment in
 the sense of [Woo82] is equivalent to a virtual equipment which has all composites (this
 was called a framed bicategory in [Shu08]). Therefore, from now on we use equipment
 to mean a virtual equipment having all composites (thereby justifying the terminology
 “virtual equipment”).
 7.23. Remark. It was shown in [Shu08] that an equipment can equally well be defined
 as a pseudo double category in which all restrictions exist, or in which all “extensions”
 exist (in the sense mentioned after Theorem 7.20), or in which there exist base change
 objects with cells (7.8) and (7.9) satisfying (7.10)–(7.12). In the virtual case, we have a
 Goldilocks trifurcation: merely having base change objects is too weak, and having all
 extensions is too strong, but having restrictions (together with units) is just right.
 We now consider how functors and transformations interact with restrictions.
 7.24. Theorem. Any functor F between virtual equipments preserves restriction.
 Proof.Theproofgiven for equipments in [Shu08, Theorem 6.4] applies basically verbatim
 to virtual equipments.
 In particular, vEquip is in fact a full sub-2-category of vDbln. Note, though, that an
 arbitrary functor F between virtual equipments still may not preserve units, so that while
 we have F(B(1,f)) ∼ = FUB(1,Ff), neither need be the same as FB(1,Ff). Of course,
 they are the same if F is normal.
 F
 Now, recall that any transformation X
 G
 α Y of functors between virtual equip
ments induces a strictly 2-natural transformation V(α) of 2-functors between vertical 2
categories. In particular, we have αB◦F(f) = G(f)◦αA for any vertical arrow f : A
 B
43
 in X. However, we also have the cell component
 F(B(1,f))
 αA ⇓αB(1,f) αB
 G(B(1,f))
 of α. If F and G are normal, so that F(B(1,f)) ∼ = FB(1,Ff) and G(B(1,f)) ∼ =
 GB(1,Gf), then by Corollary 7.21, αB(1,f) induces a 2-cell αB ◦ F(f)
 G(f) ◦ αA,
 which seems to be trying to make V(α) into an oplax natural transformation. Fortu
nately, however, this is an illusion.
 7.25. Proposition. In the above situation, the 2-cell αB ◦F(f)
 by αB(1,f) is an identity.
 G(f)◦αA induced
 Proof. This follows by inspection of how this 2-cell is constructed, and use of the cell
 naturality of α.
 7.26. Remark. Recall from Remark 5.17 that any functor X F Y between pseudo
 double categories induces a lax functor H(X) H(F) H(Y) between horizontal bicate
gories, but not every transformation F
 G induces a transformation H(F)
 H(G).
 It is true, however, that if X and Y are equipments, then any transformation F α G
 induces an oplax transformation H(F) H(α) H(G) whose component at X is GX(1,αX).
 Likewise, the components (GX)(αX,1) form a lax transformation H(G)
 H(F). See
 also Remark A.5.
 1. Normalization
 With the notion of virtual equipment under our belt, we now return to the general theory
 of generalized multicategories. We observed in §4 that for virtual double categories whose
 objects are “category-like,” such as V-Prof and Prof(C) (as opposed to those such as
 V-Mat and Span(C), whose objects are “set-like”), general T-monoids often contain too
 much structure. For instance, if T is the “free strict monoidal category” monad on
 Set-Prof, then a T-monoid consists of a category A, a multicategory M and a bijective
on-objects functor from A to the underlying category of M. Usually, the morphisms of
 A constitute superfluous data which we would like to eliminate. (This is not always true,
 though: in [Che04] these extra morphisms played an important role.)
 The obvious way to eliminate this extra data, which we adopted in describing examples
 of this sort in §4, is to require A to be a discrete category; this way the extra morphisms
 simply do not exist. However, a different way to eliminate it is to require the given functor
 A Mtoinduceanisomorphism between A and the underlying category of M; this way
 the extra morphisms exist, but are determined uniquely by the rest of the structure. In
 this section we define general analogues of both approaches, show their equivalence under
44
 general hypotheses, and argue that when they are not equivalent it is usually the second
 approach that is more useful. (This second approach was also the one taken in [Her01].)
 8.1. Definition. Let X be a virtual equipment and let T be a monad on Mod(X). A
 T-monoid A M TA is called object-discrete if A is a monoid in X of the form UX.
 We write dKMod(Mod(X),T) for the full sub-virtual-equipment of KMod(Mod(X),T)
 determined by the object-discrete T-monoids, and dKMon(X,T) for its vertical 2-category.
 Note that object-discreteness is only defined for a monad on a virtual equipment of the
 form Mod(X).
 8.2. Definition. Let T be a monad on a virtual equipment X. A T-monoid A M TA
 is normalized if its unit cell
 A UA A
 η
 A M
 TA
 is cartesian in X.
 We write nKMod(X,T) for the full sub-virtual-equipment of KMod(X,T) determined
 by the normalized T-monoids, and nKMon(X,T) for its vertical 2-category. Unlike object
discreteness, normalization is defined for monads on any virtual equipment.
 Now, to prove an equivalence between normalization and object-discreteness, we need
 to introduce the following definitions.
 8.3. Definition. A monoid homomorphism
 A0
 A0
 f0
 B0
 B0
 A A0
 f
 f
 A0
 f0
 B0
 B0
 in a virtual double category X is called bijective on objects (or b.o.) if f0 is an iso
morphism. It is called fully faithful (or f.f.) if the cell f is cartesian.
 8.4. Lemma. If A0
 A A0 is a monoid in a virtual double category X with restrictions
 and X f A0 is any vertical arrow, then X A(f,f) X is also a monoid, and its defining
 cartesian cell is a monoid homomorphism with f as its vertical part.
 Proof. To obtain a multiplication for A(f,f), we compose two copies of the defining
 cartesian cell with the multiplication of A, then factor the result through the defining
 cartesian cell. The unit is similar.
45
 8.5. Lemma. If X has restrictions, then (b.o., f.f.) is a factorization system on the
 category Mon(X) of monoids and monoid homomorphisms in X.
 Proof. Orthogonality is supplied by the universal property of cartesian cells, together
 with the fact that isomorphisms are orthogonal to anything. Factorizations are given by
 restriction along the vertical arrow component of a monoid homomorphism, using the
 previous lemma.
 8.6. Theorem. Let X be a virtual equipment, and let T be a monad on Mod(X) which
 preserves b.o. morphisms. Then
 (i) dKMod(Mod(X),T) is coreflective in KMod(Mod(X),T) (that is, its inclusion has
 a right adjoint in vEquip),
 (ii) nKMod(Mod(X),T) is reflective in KMod(Mod(X),T), and
 (iii) the induced adjunction
 dKMod(Mod(X),T)
 is an adjoint equivalence.
 nKMod(Mod(X),T)
 Proof. We first prove (i). Let A M TA be a T-monoid in Mod(X), where A0
 A A0 is
 a monoid in X. The unit of A is a monoid homomorphism e: UA0
 M(Te,e): UA0
 A, so by Lemma 8.4,
 T(UA0
 ) is an (object-discrete) T-monoid. Likewise, if B N TB
 is another T-monoid and M p TN is a horizontal arrow in KMod(Mod(X),T), then
 p(TeB,eA) is a horizontal arrow from M(TeA,eA) to N(TeB,eB). It is straightforward to
 extend these constructions to a functor KMod(Mod(X),T)
 dKMod(Mod(X),T).
 Note that M(Te,e) comes with a natural map to M, whose vertical arrow component
 is e, and likewise for p(TeB,eA). This supplies the counit of the desired coreflection. We
 obtain the unit by observing that if M were already object-discrete, then e would be the
 identity, so we would have M(Te,e) ∼
 =M. The triangle identities are easy to check.
 Note that the horizontal arrow in X underlying M(Te,e) is the restriction of A0
 M (TA)0
 along the identity e0: A0 = A0 and the map (Te)0: T(UA0
 )0
 (TA)0. Since e is b.o. and
 T preserves b.o. morphisms, (Te)0 is an isomorphism; thus the coreflection of M leaves
 its underlying horizontal arrow in X essentially unmodified.
 We now prove (ii); let A and M be as before. We first observe that the T-monoid M
 in Mod(X) has an underlying monoid in Mod(X), namely M(η,1). (This is a special case
 of a general functoriality result we will prove in [CS10a].) As noted in Remark 5.15, a
 monoid in Mod(X) consists of two monoids in X and a monoid homomorphism between
 them whose vertical arrow components are identities. In this case the first monoid is of
 course A. We denote the second by A′ and the monoid homomorphism by c: A
 A′.
 Note that the underlying horizontal arrow of A′ in X is just M(η,1).
46
 Nowsince T preserves b.o. morphisms, TA Tc TA′ is b.o., hence (TA)0 
(Tc)0 (TA′)0
 is an isomorphism. By restricting along its inverse and using the identity (A′)0 = A0,
 from A0
 M (TA)0 we obtain a horizontal arrow (A′)0
 (TA′)0. We abuse notation
 by continuing to denote this M (since restriction along isomorphisms leaves an arrow
 essentially unchanged). Now A′ is a restriction of M, so it acts on M from the left via the
 multiplication of M. And since T preserves restrictions, TA′ is a restriction of TM, so it
 also acts on M from the right via the multiplication of M. Thus, the horizontal arrow in
 X underlying M also admits the structure of a horizontal arrow A′
 TA′ in Mod(X),
 which we denote M′. Likewise, the multiplication and unit of the T-monoid M induce a
 multiplication and unit on M′, making it also into a T-monoid, and we have a canonical
 T-monoid homomorphism M
 M′ which is an isomorphism in X. By definition of A′,
 M′ is normalized. There is an analogous construction on horizontal arrows, and together
 they extend straightforwardly to a functor KMod(Mod(X),T)
 nKMod(Mod(X),T).
 Now recall that A′ came equipped with a b.o. monoid homomorphism A
 A′. It is
 straightforward to check that this homomorphism underlies a T-monoid homomorphism
 M M′; inthis way we obtain the unit of the desired reflection. We obtain its counit
 by observing that if M is already normalized, then A ∼
 = A′ and hence M ∼ = M′. The
 triangle identities are again easy to check.
 Finally, to show (iii), we observe that the unit of the reflection and the counit of
 the coreflection are isomorphisms on the underlying horizontal arrow in X (since they
 are restrictions along an isomorphism). Moreover, the reflection and coreflection functors
 both invert morphisms with this property. Statement (iii) then follows formally.
 Recall that we began this section by observing that ordinary multicategories can be
 recovered as either object-discrete or normalized T-monoids, when T is the “free strict
 monoidal category” monad on Set-Prof. Since this T preserves b.o. morphisms, this
 statement is indeed an instance of Theorem 8.6. However, ordinary multicategories can
 also be obtained as arbitrary S-monoids, when S is the free monoid monad on Set-Mat =
 Span(Set). Noting that in this case T = Mod(S), we generalize this statement to the
 following.
 8.7. Theorem. Let S be a monad on a virtual equipment X. Then the monad Mod(S)
 on Mod(X) preserves b.o. morphisms, and we have a diagram
 dKMod(Mod(X),Mod(S))
 W
 W
 W
 W
 W
 W
 W
 W
 W
 KMod(Mod(X),Mod(S))
 W
 W
 W
 W
 W
 W
 W
 W
 W
 W
 W
 nKMod(Mod(X),Mod(S))
 gggggggggggggggggggg
 KMod(X,S)
 which serially commutes (up to isomorphism). Moreover, the two diagonal functors
 dKMod(Mod(X),Mod(S))
 KMod(X,S)
 nKMod(Mod(X),Mod(S))
 are equivalences.
 KMod(X,S)
 (8.8)
 (8.9)
47
 Proof.Bydefinition, Mod(S) takes a monoid A0
 A A0 toSA0
 SA SA0, soitpreserves
 b.o. morphisms since S preserves isomorphisms. We define the middle vertical arrow
 KMod(Mod(X),Mod(S))
 by applying the 2-functor Mod to the functor
 H-Kl(Mod(X),Mod(S))
 KMod(X,S)
 H-Kl(X,S)
 (8.10)
 which takes a monoid A0
 A A0 to its underlying object A0, and similarly for hori
zontal arrows. (This is again a special case of the general functorial result of [CS10a].)
 Thus, (8.10) takes a Mod(S)-monoid A M Mod(S)(A) to the S-monoid A0
 M SA0.
 We define the diagonal functors by composition with this, so that the triangles
 ?
 ?
 ?
 ?
 and •••• commute by definition. The other triangles commute up to isomorphism because
 the reflection and coreflection were defined to fix A0, replace A, and restrict M along an
 isomorphism, whereas (8.10) simply forgets about A.
 Now, by the 2-out-of-3 property for equivalences, it suffices to show that (8.8) is an
 equivalence. We will construct an explicit inverse to it. By Proposition 5.14, to construct
 a normal functor
 KMod(X,S)
 KMod(Mod(X),Mod(S))
 it suffices to construct a not-necessarily-normal functor
 KMod(X,S)
 H-Kl(Mod(X),Mod(S)).
 (8.11)
 (8.12)
 We define (8.12) on objects by sending an S-monoid A0
 A TA0 to UA0
 , and likewise on
 vertical arrows. A horizontal arrow A p B in KMod(X,S) has an underlying horizontal
 arrow A0
 p TB0 in X, which acquires a T(UB0
 )-module structure from the following
 composite:
 A0
 A0
 A0
 A0
 A0
 A0
 A0
 A0
 p
 =
 p
 TB0
 TB0
 TB0
 TB0
 TB0
 TB0
 TB0
 TB0
 ¯
 pr
 p
 T(UB0) TB0
 TB0
 Tˆ b
 TB
 Tη
 T2B0
 T2B0
 T2B0
 T2B0
 µ
 TB0
 TB0
 This defines (8.12) on horizontal arrows; its action on cells is straightforward. The in
duced normal functor (8.11) takes an S-monoid A0
 A TA0 to itself, regarded as an
 (UA0
 ,T(UA0
 ))-bimodule. Clearly (8.11) followed by the forgetful functor is the iden
tity, while the composite in the other direction is precisely the coreflection functor into
 dKMod(Mod(X),Mod(S)); this completes the proof.
48
 8.13. Examples.Asremarked above, when S isthe“freemonoid” monadonSpan(Set),
 this shows that ordinary multicategories (i.e. S-monoids) can also be identified with
 object-discrete or normalized Mod(S)-monoids. Likewise, virtual double categories are
 S-monoids for the “free category” monad, and thus can also be identified with object
discrete or normalized Mod(S)-monoids.
 8.14. Example. Since topological spaces can be identified with U-monoids in Rel, they
 can also be identified with object-discrete or normalized Mod(U)-monoids in 2-Prof. In the
 terminology of [Tho09], a Mod(U)-monoid is a modular topological space. It is normalized
 precisely when its order is the specialization order, so that it is equivalent to an ordinary
 topological space—i.e. a U-monoid, as required by Theorem 8.7.
 By no means are all interesting monads on Mod(X) of the form Mod(S). However,
 many of them do preserve b.o. morphisms, so that Theorem 8.6 at least applies.
 8.15. Example. The “free symmetric strict monoidal category” monad on Set-Prof =
 Mod(Set-Mat) preserves b.o. morphisms but is not of the form Mod(S) for any monad S
 on Set-Mat. We have seen in Example 4.16 that object-discrete T-monoids are symmetric
 multicategories; hence so are normalized T-monoids.
 8.16. Example. The “free category with strictly associative finite products” monad on
 Set-Prof also preserves b.o. morphisms but is not of the form Mod(S). We have seen in
 Example 4.17 that object-discrete T-monoids are multi-sorted Lawvere theories; hence so
 are normalized T-monoids.
 8.17. Non-Example.RecallfromExample4.19thatclubs areT-monoidsin Span(Cat)
 with a discrete category of objects, where T is a monad like the previous two. However,
 since Span(Cat) is not of the form Mod(X), the theory of this section does not apply to
 clubs. In particular, their “object-discreteness” is not an instance of our definition, and
 is not the same as normalization.
 When T does not preserve b.o. morphisms, however, normalized and discrete T
monoids can be quite different, even on a virtual equipment of the form Mod(X). In
tuitively, saying that T preserves b.o. morphisms says that the possible domains of multi
morphisms in a T-multicategory depend only on its objects. If this fails to be true,
 then how many morphisms are included in the underlying monoid can change what these
 possible domains are.
 8.18. Example.Let T bethe “free category with equalizers” monad on Set-Prof. Then
 T evidently does not preserve b.o. morphisms, but it is the identity on discrete categories.
 Therefore, an object-discrete T-monoid is just a category, whereas a normalized T-monoid
 can have morphisms whose domain is a “formal equalizer” of ordinary morphisms.
 More interestingly, normalized T-monoids for the “free category with finite limits”
 monad (which also does not preserve b.o. morphisms) can be considered a generalization
 of Lawvere theories to finite-limit logics. We can also continue to generalize to more
 powerful logics (or “doctrines”).
49
 These examples suggest that when T does not preserve b.o. morphisms, it is often the
 normalized, rather than the object-discrete, T-monoids that better capture the desired
 notion of T-multicategory. Note also that normalization makes sense for any monad
 on a virtual equipment, while object-discreteness only makes sense for monads on virtual
 equipments of the form Mod(X). Finally, we will see in the next section that normalized T
monoids are the most natural notion to compare with pseudo T-algebras. This inspires us
 to take normalized T-monoids as our preferred definition of “generalized multicategory,”
 and to make the following informal definition.
 8.19. Definition. If T is a monad on a virtual equipment for which (possibly pseudo)
 T-algebras are called widgets, then normalized T-monoids are called virtual widgets.
 The reasons for this definition were summarized in the introduction. In §9 we will prove
 that any widget has an underlying virtual widget, further justifying the terminology. Of
 course, we have seen that a number of types of virtual algebras already have their own
 names, such as “multicategory” and “Lawvere theory.” When such common names exist,
 we of course use them in preference to terms such as “virtual monoidal category” or
 “category with virtual finite products.”
 Note that “virtual widget” is, strictly speaking, ambiguous: knowing the notion of
 widget determines at most the vertical 2-category VX and the 2-monad VT, rather than
 X and T themselves. However, many 2-categories that arise in practice come with an
 obvious “natural” extension to a virtual equipment, so in practice there is little ambiguity.
 (In fact, there is a general construction of an equipment from a well-behaved 2-category;
 see [CJSV94].) One case of ambiguity is if “widget” is the name for T-algebras in Set
 or Cat, but we consider T-monoids in V-Mat or V-Prof; in this case we may speak of
 V-enriched virtual widgets.
 8.20. Remark.The discussion above suggests that when the objects of X are category
like, it is the normalized T-monoids (i.e. virtual T-algebras) that are more important,
 while when the objects of X are set-like, it is the non-normalized T-monoids (i.e. virtual
 Mod(T)-algebras) that are more important. This does seem to usually be the case, but
 there are exceptions on both sides, such as the following.
 • As we have already remarked, the multicategories of [BD98] and [Che04] are non
normalized T-monoids, when T is the “free symmetric strict monoidal category”
 monad on Set-Prof (whose objects are obviously category-like).
 • Let U bethe ultrafilter monad on Rel, whose objects are set-like. We have seen that
 a U-monoid is just a topological space, but it is easy to verify that a U-monoid is
 normalized just when it is a T1-space—certainly also an important concept.
 1. Representability
 We now turn to a general version of the comparison between monoidal categories and
 multicategories. Of course, we first need to identify the analogue of a monoidal category
50
 in the general case. We saw in §8 that ordinary multicategories have two different faces in
 our setup: they are the S-monoids where S is the “free monoid” monad on Span(Set), and
 also the normalized T-monoids, where T = Mod(S) is the “free strict monoidal category”
 monad on Set-Prof. Monoidal categories, however, are more visible from the second point
 of view: they are the pseudo V(T)-algebras in V(Set-Prof) = Cat.
 Accordingly, in this section we will assume that T is a monad on a virtual equipment
 X whose objects are “category-like,” and seek to compare (pseudo) V(T)-algebras with
 (normalized) T-monoids. We will additionally have to assume that T is a normal monad
 as defined in §6, since otherwise it doesn’t even induce a 2-monad on V(X). If we are
 given instead a monad S on a virtual double category whose objects are “set-like,” then
 in order to apply the theory of this section we simply consider Mod(S) instead; some
 examples of this can be found later on. Generalizing the terminology of [Lei04, p. 165],
 we may call a (pseudo) V(Mod(S))-algebra an S-structured monoid.
 Actually, the most natural approach to the comparison turns out to be via oplax T
algebras. Recall that for a 2-monad T on a 2-category, an oplax T-algebra is an object
 A with a map a: TA Aand 2-cells
 T2A µ
 Ta
 a
 TA
 a
 TA a A
 A η
 C
 C
 C
 C
 C
 C
 C
 C
 {{{
 TA
 C
 and
 C
 {{{
 ba
 C
 C
 C
 C
 C
 C
 a
 A
 (9.1)
 satisfying certain straightforward axioms. We call it normal if a is an isomorphism, and a
 pseudo T-algebra if both a and a are isomorphisms. Finally, if T is a monad on a virtual
 equipment, we will always abuse terminology by saying “T-algebra” (with appropriate
 prefixes) to mean V(T)-algebra.
 9.2. Theorem. Let T be a normal monad on a virtual equipment X. Then:
 (i) Any oplax T-algebra TA a A in VX gives rise to a T-monoid A A(a,1) TA, which
 is normalized if and only if A is normal.
 (ii) A T-monoid A M TA arises from an oplax T-algebra if and only if M ∼ = A(a,1)
 for some vertical arrow TA a A.
 Proof. If a: TA
 induces a unit cell
 A is an oplax T-algebra, then by definition of A(a,1), the 2-cell a
 A UA A
 η
 AA(a,1)
 TA.
51
 Likewise, by the dual of Corollary 7.21, a induces a multiplication
 A A(a,1) TA(TA)(Ta,1)T2A
 µ
 A
 A(a,1)
 TA
 and using the isomorphism (TA)(Ta,1) ∼ = T(A(a,1)) (since T is normal) we obtain a
 multiplication cell. The axioms to make A(a,1) into a T-monoid follow directly from the
 axioms for an oplax T-algebra. To complete (i), we observe that a is an isomorphism if
 and only if the induced cell UA
 A(aη,1) ∼ = A(a,1)(η,1) is an isomorphism, which says
 precisely that the unit defined above is cartesian. Conversely, if A M TA is a T-monoid
 and M ∼ = A(a,1), then the same bijections supply 2-cells a and a satisfying the same
 axioms making a: TA
 A into an oplax T-algebra; this shows (ii).
 The following example may serve to clarify the connection between normality of oplax
 T-algebras and normalization of T-monoids.
 9.3. Example. Let T be the “free strict monoidal category” monad on Set-Prof. Then
 an oplax T-algebra is an oplax monoidal category: a category A equipped with tensor
 product functors
 A×···×A A
 (x1, . . ., xn) → x1 ⊗... ⊗ xn
 for n ≥ 0, and transformations
 x
 x11 ⊗... ⊗xnkn
 x
 x11 ⊗... ⊗x1k1
 ⊗...⊗xn1 ⊗...⊗xnkn
 satisfying certain evident axioms. Note that the 0-ary tensor product 
is a “lax unit”
 and the 1-ary tensor product x is not necessarily isomorphic to x, only related by the
 given unit transformation x
 x.
 As mentioned previously, a T-monoid consists of a category A, a multicategory M with
 the same objects, and an identity-on-objects functor from A to the underlying ordinary
 category of M. Now Theorem 9.2 says that we can make an oplax monoidal category
 into a T-algebra by defining the multimorphisms in M from (x1,...,xn) to y to be the
 morphisms x1 ⊗...⊗xn
 y in A.
 Note that the morphisms from x to y in the underlying ordinary category of M are the
 morphisms from x to y in A. The functor A M is defined by composing with the unit
 transformation x
 x. Clearly this is fully faithful (i.e. the T-monoid is normalized)
 just when x
 x is an isomorphism (i.e. the oplax T-algebra is normal).
 The following characterization of pseudo T-algebras is now obvious.
52
 9.4. Corollary. A normalized T-monoid A M TA arises from a pseudo T-algebra
 if and only if
 (i) M ∼ = A(a,1) for some TA a A, and
 (ii) the induced 2-cell a is an isomorphism.
 We say that a normalized T-monoid is weakly representable if it satisfies (i), and
 representable if it satisfies both (i) and (ii) (hence is equivalent to a pseudo T-algebra).
 9.5. Example. When T is the “free strict monoidal category” monad on Set-Prof,
 Corollary 9.4 specializes to the characterization of monoidal categories as representable
 multicategories, as in [Her00] and [Lei04, §3.3]. We will see in §B.14 that it also includes
 the general representability notion of [Her01]. The analogue of Theorem 9.2 in the lan
guage of [Bur71] can be found in [Pen09], which uses “representable” for what we call
 “weakly representable” and “lax algebra” for what we call an “oplax algebra.”
 9.6. Remark. Strictly speaking, the notion of monoidal category obtained in this way
 is the “unbiased” version, which is equipped with a specified n-ary tensor product for all
 n ≥ 0, instead of the usual “biased” version having only a binary and nullary product
 (see [Lei04, §3.1]). This is generally what happens for pseudoalgebras: if T is a monad
 whose strict algebras are some strict structure, then pseudo T-algebras are an “unbiased”
 sort of weak structure. Generally the unbiased version is equivalent to the biased one,
 but there is real mathematical content in this statement; for instance, the equivalence of
 biased and unbiased monoidal categories is essentially equivalent to Mac Lane’s coherence
 theorem.
 9.7. Example. Recall that virtual double categories can be identified with S-monoids
 for the “free category” monad S on directed graphs, and hence also with normalized
 Mod(S)-monoids. In this case it is easy to check that for a normalized Mod(S)-monoid
 A M TA, we have M ∼ = A(a,1) iff every composable string of horizontal arrows is
 the source of a weakly opcartesian arrow (see Remark 5.8). Thus, such virtual double
 categories can be identified with “normal oplax double categories,” which are equipped
 with n-ary composites for all n and comparison maps
 p11 ⊙ ···⊙pnkn
 and invertible comparison maps
 p11 ⊙··· ⊙p1k1
 ⊙···⊙pn1 ⊙···⊙pnkn
 p ∼ = p
 satisfying analogous axioms to an oplax monoidal category. Condition (ii) in Corollary 9.4
 is then equivalent to requiring weakly opcartesian cells to be closed under composition.
 As observed in Remark 5.8, this suffices to ensure we have a pseudo double category, i.e.
 a pseudo Mod(S)-algebra.
53
 9.8. Example.Let U bethe ultrafilter monad on Rel. We have seen that a U-monoid is
 a topological space, and a normalized U-monoid is a T1-space. A vertical U-algebra (which
 is automatically strict, since V(Rel) is locally discrete) is a compact Hausdorff space, and
 in this case Theorem 9.2 tells us what we already knew: any compact Hausdorff space is,
 in particular, a T1 topological space.
 Now consider the induced monad Mod(U) on Mod(Rel). The objects of Mod(Rel) =
 2-Prof are preorders. In the language of [Tho09], a strict Mod(U)-algebra is an ordered
 compact Hausdorff space, whereas by Theorem 8.7 a normalized Mod(U)-monoid is simply
 a topological space. Thus, Theorem 9.2 tells us that any ordered compact Hausdorff space
 X can be equipped with a topology in which an ultrafilter F converges to a point y if and
 only if the (unique) limit of F in X is ≤ y in the given preorder.
 The next three examples can all be found in [Her01] (see B.14 for more on the com
parison between our setting and Hermida’s).
 9.9. Example. Let S be a small category and T the monad on C = Setob(S) whose
 algebras are functors S
 Set, as in Example 4.10, and consider the monad Mod(T)
 on Mod(Span(C)) = Prof(C). A strict Mod(T)-algebra is a functor S
 Cat, while a
 pseudo Mod(T)-algebra is a pseudofunctor S
 Cat. Now by Theorem 8.7, normalized
 Mod(T)-monoids can be identified with T-monoids, which as we saw can be identified with
 functors A
 S. It is then easy to verify that a normalized Mod(T)-monoid satisfies
 9.4(i) iff the corresponding functor A
 S admits all weakly opcartesian liftings, and
 9.4(ii) iff weakly opcartesian arrows are closed under composition. Thus, in this case
 Corollary 9.4 specializes to the classical equivalence between pseudofunctors S
 Cat
 and opfibrations over S.
 9.10. Example. Let T be as in Example 9.9, but now consider T-monoids rather than
 Mod(T)-monoids. A T-monoid is normalized just when for each x ∈ S, the induced span
 A(x,1)
 M A(x,1)is the identity span; i.e. when the fibers of A
 categories. Such a normalized T-monoid satisfies 9.4(i) iff A
 S are discrete
 S admits all weakly
 opcartesian liftings, which in this case are automatically opcartesian by discreteness.
 Thus, Corollary 9.4 also specializes to the equivalence of functors S
 Set and discrete
 opfibrations over S.
 9.11. Example. Let T be the “free strict ω-category” monad on Span(Globset), for
 which we saw in Example 4.11 that T-monoids on 1 are globular multicategories. Pseudo
 T-algebras are an “unbiased” version of the monoidal globular categories of [Bat98]. Thus
 any monoidal globular category has an underlying globular multicategory, and can be
 characterized among the latter by a representability property.
 The requirement that T be normal in Theorem 9.2 cannot be dispensed with.
 9.12. Example. Let P be the extension of the powerset monad to Rel described in
 Example 3.7. Since V(Rel) = Set is locally discrete, oplax P-algebras are just P
algebras in Set, which can be identified with complete meet-semilattices (the structure
 map PA Atakes a subset X ⊆A to its meet X).
54
 Now, we have observed in Example 4.14 that P-monoids can be identified with closure
 spaces. If we attempt to follow the prescription of Theorem 9.2, starting from a complete
 join-semilattice we would define the “closure operation” c(X) = { X}; but this is neither
 extensive nor monotone.
 On the other hand, if we first apply Mod, we obtain a monad Mod(P) on Mod(Rel) =
 2-Prof, which is normal. By Theorem 8.7, normalized Mod(P)-monoids can be identified
 with P-monoids, i.e. closure spaces. With a little effort, pseudo Mod(P)-algebras can
 be identified with meet-complete preorders (that is, preorders that are complete as cate
gories). Theorem 9.2 then tells us that from a meet-complete preorder we can construct
 a closure space with c(X) = {x | X ≤ x}, which is certainly true.
 We can also make the correspondence of Theorem 9.2 functorial. Recall that for
 any T we have a 2-category KMon(X,T) of T-monoids, defined to be the vertical 2
category of KMod(X,T). It turns out that while T-monoids correspond to oplax T
algebras, morphisms of T-monoids correspond to lax T-algebra morphisms. Recall that a
 lax T-morphism between oplax T-algebras consists of a map f : A
 B and a 2-cell
 TA Tf
 a
 f
 TB
 b
 A f
 B
 satisfying certain straightforward axioms. And if A f
 g
 B are two such, a T-transformation
 is a 2-cell f α g such that
 TA Tf
 a
 A
 f
 f
 g
 α B
 Tf
 TB
 b
 TA
 =
 a
 Tα TB
 Tg
 f
 b
 A f
 B.
 We write Oplax-T-Algl for the resulting 2-category.
 9.13. Theorem. Let T be a normal monad on a virtual equipment X. Then there
 is a strict 2-functor Oplax-T-Algl
 KMon(X,T), whose underlying 1-functor is fully
 faithful, and which becomes 2-fully-faithful (that is, an isomorphism on hom-categories)
 when restricted to normal oplax T-algebras.
 Proof. For oplax T-algebras (A,a) and (B,b), regarded as T-monoids A A(a,1) TA and
 B B(b,1) TB, a morphism of T-monoids consists of a vertical arrow f: A
 B and a
55
 2-cell
 AA(a,1)
 f
 TA
 Tf
 BB(b,1)
 TB
 satisfying certain axioms. But by definition of B(b,1), and by Theorem 7.20 applied to
 A(a,1), this 2-cell is equivalent to one
 TA UTA
 a
 A
 f
 TA
 Tf
 TB
 b
 UB
 .
 This defines a 2-cell b◦Tf
 f ◦a in V(X), which is precisely the additional data required
 to make f into a lax T-algebra morphism. It is easy to verify that the axioms of a T
monoid morphism are equivalent to the axioms of a lax T-algebra morphism under this
 translation, and that composition is preserved.
 Now let f,g: A
 transformation β: f
 B be two such morphisms, and recall from §6 that a T-monoid
 g consists of a cell
 •••••••A
 A
 g
 •
 ⇓β
 ?
 ?
 ?
 ?
 ?
 ?
 ?
 ηTB◦f
 ?
 B
 B
 B
 |
 B(b,1)
 TB
 TB
 TB
 satisfying a certain axiom. Equivalently, β is a 2-cell bηf
 T-algebra transformation α: f
 g in V(X). Thus, given a
 g, it is natural to define β to be the composite
 bηf b bf f α g,
 where b: bη
 1 is the oplax unit map of B. With this definition, the axiom that must be
 satisfied for β to be a T-monoid transformation becomes the following equality of pasting
56
 diagramsinVX.
 B TB b
 A
 B
 f
 A TA a TA
 TB
 Tf f
 ?? ??
 TB T2B Tb
 B
 TB
 η
 B TB b
 TB
 T2B
 η =
 B
 B • • • • • • • • • •
 • • • • • • • • • •
 A
 B
 g
 OO OO
 α
 b b
 ?? ??
 B TB b
 b
 ?? ??
 B TB b
 B
 B
 B T2B T2B
 TB
 µB
 =
 B TB b
 A
 B
 g
 A TA a TA
 TB
 Tg g
 ?? ??
 TA
 TB
 Tf
 TB
 T2B
 TηB
 TB
 TB • • • • • • • • • •
 • • • • • • • • • •
 Tα
 Tb b
 ?? ??
 TB T2B Tb
 b
 ?? ??
 B TB. b
 B
 B
 B T2B T2B
 TB.
 µB
 (9.14)
 Thecellmarked“=”isanidentitybyProposition7.25appliedtoacellcomponentofη.
 Now, twooftheaxiomsforanoplaxT-algebrasaythat
 TB T2B Tb
 B
 TB
 η
 B TB b TB
 T2B
 η =
 B
 B • • • • • • • • • •
 • • • • • • • • • • b b
 ?? ??
 B TB b
 b
 ?? ??
 B TB b
 B
 B
 B T2B T2B
 TB
 µB
 = B TB
 b
 b
 1B
 =
 TB
 T2B
 TηB
 TB
 TB • • • • • • • • • •
 • • • • • • • • • • Tb b
 ?? ??
 TB T2B Tb
 B TB b
 b
 ?? ??
 B TB. b
 B
 B
 B T2B T2B
 TB.
 µB
 Afterremovingthesecompositesfrom(9.14),what isleft issimplytheequationforαto
 beaT-algebratransformation; thusαis suchpreciselywhenβ=α.bf isaT-monoid
 transformation.And,ofcourse, ifBisnormal, thenbf isanisomorphism,andsoαcan
 berecovereduniquelyfromβ.We leave it tothereader toverifythat thisassociation
 preservesbothtypesof2-cellcomposition.
 TherestrictiontonormaloplaxalgebrasinthefinalstatementofTheorem9.13cannot
 bedispensedwitheither.
 9.15. Example.LetAandBbeoplaxmonoidal categories, regardedasT-monoids
 forthe“freestrictmonoidalcategory”monadTasinExample9.3,andletA f
 g
 Bbe
 laxmonoidal functors. AT-algebratransformationf g is, inparticular, anatural
 transformationf g,andthereforehascomponentsfx αx gx.However, ifweunravel
 thedefinitionofaT-monoidtransformation,weseethat itscomponentsareof theform
 fx βx gx.Thus,whenBisnotnormal, therecanbenobijectioningeneral.
57
 9.16. Remark. Recall from §1 that generalized multicategories can be regarded as
 “algebraic theories.” For instance, ordinary multicategories correspond to strongly regular
 f
 initary theories, while Lawvere theories correspond to arbitrary finitary theories. In
 language introduced by Jon Beck, one may say that the monad T provides the “doctrine”
 in which the theories are written. (Motivated by this, some authors use the word doctrine
 to mean simply a 2-monad.) If X is a T-monoid, regarded as a theory in the doctrine
 T, and A is a pseudo T-algebra, then it is natural to define a model of X in A to be a
 T-monoid homomorphism from X to (the underlying T-monoid of) A.
 Now, frequently the functor of Theorem 9.13 has a left adjoint FT when restricted
 to pseudo T-algebras and morphisms. In such a case, a model of X in A can equally
 be defined as a T-algebra morphism FTX
 A. That is, FTX is the “free T-algebra
 containing a model of X.” Following [Law63], this is sometimes called the “functorial
 semantics” of T.
 For example, when X is a Lawvere theory, FTX is the category with finite products
 that incarnates it. (In fact, as we remarked in Example 4.17, Lawvere theories are often
 defined to be certain categories with finite products.) Likewise, when X is a Lawvere V
theory, then FTX is the V-category with finite cotensors that incarnates it (see [Pow99])
 and when X is an ordinary or non-symmetric operad, FTX is the “PROP” or “PRO”
 associated to it (see [BV73]).
 This adjunction can also be used to characterize representability. It turns out that the
 strict (resp. pseudo) algebras for the induced 2-monad T⊣ on KMon(X,T) can be identified
 with strict (resp. pseudo) T-algebras. Moreover, T⊣ is a “lax-idempotent” 2-monad in the
 sense of [KL97], so that A is a pseudo T⊣-algebra precisely when the unit A
 T⊣A has a
 left adjoint. Thus the “structure” imposed by the 2-monad T has been transformed into
 “property-like structure” imposed by T⊣. In particular cases, these observations can be
 found in [Her00, Her01, Pen09]; in [CS10b] we will study them in our general context.
 A. Composites in Mod and H-Kl
 In this appendix we consider the question of when Mod(X) and H-Kl(X,T) have compos
ites and units, which will be needed for our comparisons with existing theories in the next
 appendix. The first case is easy; the following was also observed in [Shu08].
 A.1. Theorem.If X is an equipment in which each category HX(A,B) has coequalizers,
 which are preserved on both sides by ⊙, then Mod(X) is also an equipment.
 Proof (Sketch). We have seen already that Mod(X) always has units, and inherits
 restrictions from X, so it remains only to construct composites. We define the composite
 of bimodules A p B q C to be the coequalizer of the two actions of B:
 p ⊙B⊙q p⊙q p⊙Bq
58
 and similarly for longer composites. Given a cell
 p
 f
 q
 r
 g
 in Mod(X), to factor it through p ⊙B q, we first factor it through a cartesian cell to
 obtain a cell (p,q)
 r(g, f) in HX(A,C), then factor this through the coequalizer in
 HX(A,C), and finally compose again with the cartesian cell. Thus (p,q)
 p ⊙B q is
 weakly opcartesian; to show that these cells compose we use the fact that ⊙ preserves
 coequalizers.
 Note that we require X to have restrictions, as well as composites, in order to show
 that Mod(X) has composites. We could instead assume explicitly that the coequalizers in
 HX(A,B) satisfy a universal property relative to all cells, but in practice this generally
 tends to hold only because of the existence of restrictions.
 A.2. Example. If V has small colimits preserved by ⊗, then V-Mat satisfies the hy
potheses of Theorem A.1, so (as we have seen) V-Prof is an equipment.
 A.3. Example.IfChaspullbacks andcoequalizers preserved by pullback, then Span(C)
 satisfies the hypotheses of Theorem A.1, so (as we have also seen) Prof(C) is an equipment.
 We have also already seen that H-Kl(X,T) always inherits restrictions from X. How
ever, to show that it has composites, we require fairly strong conditions not just on X
 but on T as well. Recall that a functor between pseudo double categories (and, therefore,
 also between equipments) is called strong if it preserves all composites. We then make
 the following definition:
 A.4. Definition. A transformation F α G of functors X F
 G
 Ybetween equipments
 is horizontally strong if for every horizontal arrow X p Y in X, the cell induced by
 αp under the bijection of Corollary 7.21:
 FX Fp FYGY(1,αY)GY
 FXGX(1,αX)
 GX Gp
 GY
 is an isomorphism. A monad T on an equipment X is horizontally strong if T is a
 strong functor and µ and η are horizontally strong.
 A.5. Remark.Recall from Remark 7.26 that any transformation α of functors between
 equipments induces an oplax transformation H(α) of functors between horizontal bicat
egories. Horizontal strength of α is equivalent to requiring H(α) to be a strong (aka
 pseudo) transformation (hence the name).
59
 F
 A.6. Example. Let C
 G
 α D beatransformation between pullback-preserving func
tors between categories with pullbacks. It is not hard to verify that the induced trans
formation Span(α) is horizontally strong if and only if α is a cartesian natural trans
formation, meaning that all its naturality squares are pullbacks. Therefore, if T is a
 pullback-preserving monad on C, the monad Span(T) is horizontally strong if and only
 if µ and η are cartesian natural transformations. Such a T is often called a cartesian
 monad; see §B.1.
 A.7. Example. We have remarked that most of the monads from Example 3.7 are not
 even strong functors in general, with the exception of the ultrafilter monad on Rel. One
 can verify that for this monad, the multiplication transformation is horizontally strong,
 but the unit transformation is not; hence it is not a horizontally strong monad.
 A.8. Theorem.IfT is ahorizontally strong monad on an equipment X, then H-Kl(X,T)
 is also an equipment.
 Proof (Sketch). It suffices to show that H-Kl(X,T) has composites. A composable
 string of horizontal arrows
 X0
 p1 X1
 p2 ··· pn Xn
 in H-Kl(X,T) consists of horizontal arrows Xi
 pi+1 TXi+1 in X. Since X is an equipment,
 we can form the composite
 p1 ⊙T(p2)⊙···⊙Tn−1(pn)⊙TXn(1,µn−1)
 in X, which clearly supplies a weakly opcartesian cell in H-Kl(X,T). Likewise, TX(1,η) is
 a weak unit for X in H-Kl(X,T). The assumptions on T are required to show that these
 weakly opcartesian cells compose, or equivalently that this composition is associative;
 rather than write this out in detail we merely compute the 3-fold associativity isomorphism
 for X p Y q Z r W.
 (p ⊙Tq⊙TZ(1,µ))⊙Tr⊙TW(1,µ)
 ∼
 =p⊙Tq⊙T2r⊙T2W(1,µT)⊙TW(1,µ) (strength of µ)
 ∼
 =p⊙Tq⊙T2r⊙T2W(1,Tµ)⊙TW(1,µ) (associativity of µ)
 ∼
 =p⊙Tq⊙T2r⊙T(TW(1,µ))⊙TW(1,µ) (normality of T)
 ∼
 =p⊙T(q⊙Tr⊙TW(1,µ))⊙TW(1,µ)
 Of course, in the unit isomorphisms η is used instead of µ.
 (strength of T).
 A.9. Corollary. If T is a horizontally strong monad on an equipment X, such that
 each category HX(A,B) has coequalizers that are preserved by ⊙ on both sides and also
 preserved by T, then KMod(X,T) is also an equipment.
 Proof. The hypotheses ensure that H-Kl(X,T) satisfies the conditions of Theorem A.1.
60
 A.10. Example.IfT isacartesian monad on acategory C with pullbacks, then we have
 seen that the induced monad on Span(C) is horizontally strong; thus H-Kl(Span(C),T)
 is an equipment. If furthermore C has coequalizers that are preserved by pullback and
 by T, then Corollary A.9 implies that KMod(Span(C),T) is also an equipment.
 For example, the “free M-set” monad (M × −) on Set preserves coequalizers, so we
 have an equipment of M-graded categories. However, the “free monoid” monad does not
 preserve coequalizers, and the virtual equipment of ordinary multicategories is not an
 equipment.
 A.11. Example. Let V be a symmetric monoidal category with small coproducts pre
served by ⊗onbothsides, and let T be the extension of the “free monoid” monad on Set to
 a monad on V-Mat defined in Example 3.12. We have already remarked in Examples 5.11
 that T is strong, and an easy calculation shows that it is in fact horizontally strong. Thus,
 H-Kl(V-Mat,T) is an equipment. However, even if V is cocomplete, and in particular
 has coequalizers preserved by ⊗ on both sides, these coequalizers will not in general be
 preserved by T. Thus, the virtual equipment KMod(V-Mat,T) of V-enriched ordinary
 multicategories fails to be an equipment. (For V = Set we have seen this already in the
 previous example.)
 A.12. Example. Let V be a cocomplete symmetric monoidal category with small col
imits preserved by ⊗ on both sides, and let T be the “free symmetric strict monoidal V
category” monad on V-Prof from Examples 3.14 and 4.16. We remarked in Examples 5.12
 that T is strong. In fact, it is easily seen to be horizontally strong, so that H-Kl(V-Prof,T)
 is an equipment. As in the previous example, however, T fails to preserve coequalizers in
 H(V-Prof)(A,B), so that KMod(V-Prof,T) is not an equipment even when V = Set.
 When the horizontal arrows in H-Kl(Set-Prof,T) are identified with the generalized
 structure types of [FGHW08] as in Remark 4.20, their horizontal composites are iden
tified with the substitution operation on structure types. In [FGHW08] the bicategory
 H(H-Kl(Set-Prof,T)) was constructed in this way from structure types and substitution.
 A.13. Example. Let T be the “free category with strictly associative finite products”
 monad on V-Prof from Examples 3.15 and 4.17. We remarked in Examples 5.12 that T
 is strong if V is cartesian monoidal. In fact, in this case it can moreover be shown to be
 horizontally strong, so that H-Kl(V-Prof,T) is an equipment.
 Nowwespecialize to V = Set. If 1 denotes the terminal category, then T1 is equivalent
 to Setop
 f , the opposite of the category of finite sets. Thus, a profunctor 1
 equivalent to a functor Setf
 T1 is
 Set, which (since Set is locally finitely presentable)
 is equivalent to a finitary endofunctor of Set. It is then not hard to verify that the
 equivalence
 H(H-Kl(Set-Prof,T))(1,1) ≃ [Setf,Set]
 is actually an equivalence of monoidal categories, and thus induces an equivalence between
 categories of monoids. But a monoid in H(H-Kl(Set-Prof,T))(1,1) is a T-monoid 1
 T1,
 i.e. a Lawvere theory; thus we recover the classical result of [Law63] that Lawvere theories
 can be identified with finitary monads on Set.
61
 An analogous argument for the “free V-category with finite cotensors” monad on
 V-Prof from Examples 3.16 and 4.18 reproduces the result of [Pow99] that Lawvere V
theories can be identified with finitary V-monads on V. In this case, H-Kl(V-Prof,T)
 seemingly need not be an equipment, but at least the multicategory H-Kl(V-Prof,T)(1,1)
 is a monoidal category, precisely because it can be identified with the monoidal category
 of finitary endofunctors of V under composition.
 A.14. Example. Let Set-Profls denote the virtual double category whose objects are
 locally small categories (that is, large categories with small hom-sets), whose vertical
 arrows are functors, and whose horizontal arrows are profunctors taking values in small
 sets. Then there is a monad T on Set-Profls whose algebras are categories with all small
 products, and we have T1 ≃ Setop. Thus, by analogous reasoning to Example A.13,
 we see that T-monoids 1
 T1 for this T can be identified with arbitrary monads on
 Set. We can likewise obtain monads on any suitable V by using the monad for arbitrary
 cotensors on V-Profls. In [CS10a] we will see that by regarding monads as particular
 generalized multicategories in this way, we can recover the monad associated to an operad
 (as originally defined in [May72]) as a particular case of the functoriality of generalized
 multicategories.
 B. Comparisons to previous theories
 We now describe the existing approaches to generalized multicategories, and show how
 they compare to our theory. Most existing approaches turn out to be instances of our
 theory, applied to a particular sort of monad on a particular sort of virtual equipment.
 Unsurprisingly, however, often more can be said in such special cases that is not true in
 general. Thus, in each section below we briefly mention some of the additional results
 that different authors have obtained in their particular contexts.
 B.1. Cartesian Monads. In order to study and define a type of n-category, Leinster
 developed a theory of cartesian monads and their associated multicategories. This theory
 was developed in a series of papers, eventually culminating in his book [Lei04]. Recall the
 following from Example A.6:
 B.2. Definition. Let E be a category with pullbacks. A monad (T,η,µ) on E is carte
sian if T preserves pullbacks, and all naturality squares of η and µ are pullbacks.
 Given a cartesian monad, Leinster constructs a bicategory E(T) of T-spans and defines
 a (E,T)-multicategory (or simply a T-multicategory) to be a monad in E(T). To compare
 this to our context, recall that whenever E has pullbacks, Span(E) is an equipment, and
 any pullback-preserving monad T on such a E extends to a strong monad on Span(E),
 which is horizontally strong just when T is cartesian. It is then easy to see:
 B.3. Proposition. For a cartesian monad T on E, Leinster’s bicategory E(T) is iso
morphic to H(H-Kl(Span(E),T)).
62
 Therefore, Leinster’s category of (E,T)-multicategories is the vertical category of our
 KMod(Span(E),T). In particular, what he calls a T-operad is a T-monoid 1
 T1 in
 Span(E).
 Actually, Leinster constructs the whole virtual double category KMod(Span(E),T)
 (which he calls an fc-multicategory) in [Lei04, §5.3], and uses it to define transformations
 of his generalized multicategories just as we did in §6. (The notes at the end of his §5.3
 also point in the direction of our §§5 and 7.)
 Leinster also proves most of the results of our §9 in his context, as well as the func
toriality of the construction mentioned in Remark 4.4. Furthermore, he shows that if T
 is a “suitable” cartesian monad on a “suitable” cartesian category E, then the category
 of (E,T)-multicategories is itself monadic over a cartesian category of “T-graphs,” and
 this monad is also cartesian. Thus the process can be iterated, leading to a definition of
 the “opetopes” used in the Baez-Dolan definition of weak n-categories. Finally, Leinster
 also studies algebras for generalized operads, which are closely related to the horizontal
 arrows in KMod(X,T).
 B.4. Clubs. Essentially the same theory was developed in [Kel92] for the case of gen
eralized operads (generalized multicategories on 1). Observe that when T is a cartesian
 monad on a category E with finite limits, so that H-Kl(Span(E),T) is an equipment, then
 in particular the category
 E/T1 ≃H(H-Kl(Span(E),T))(1,1)
 inherits a monoidal structure. It is easy to verify that this monoidal structure on E/T1 is
 the same as that constructed by Kelly (see the explicit description in [Kel92, p. 174–175]).
 Kelly defined a club over T to be a monoid for this monoidal structure; thus such clubs
 can be identified with T-monoids 1
 T1 in Span(E). He also proved that such clubs
 are essentially equivalent to cartesian monads equipped with a “cartesian map” to T.
 (Actually, in [Kel92] it was assumed that T preserves certain pullbacks rather than
 all pullbacks. This suffices to construct a monoidal structure on E/T1, though not for T
 to define a monad on all of Span(E).)
 B.5. Pseudomonads on Prof. The existing theory which is probably closest to our
 approach involves the construction of a Kleisli bicategory from a pseudomonad on a bicat
egory such as V-Prof. A general theory of multicategories based on such pseudomonads
 does not appear to exist in the literature, but it is implicit in [BD98, Che04, FGHW08,
 Gar08, DS03] among other places.
 The general framework is, however, quite simple to state: from a pseudomonad T on a
 bicategory B, one can construct the Kleisli bicategory BT and consider monads in BT as a
 notion of generalized multicategory. (Leinster’s approach is a special case of this for B =
 Span(C), as is Hermida’s for a different bicategory—see §B.14, below.) The relationship
 with our theory is that if T is a horizontally strong monad on an equipment X, it gives
 rise to a pseudomonad H(T) on the bicategory H(X), and H(X)H(T) ≃ H(H-Kl(X,T)) so
 that the resulting notions of multicategory agree.
63
 In the converse direction, of course not every pseudomonad on H(X) arises from a
 monad on X itself, but we have seen that this is true for most monads relative to which
 one may want to define generalized multicategories. In a few cases, however, the extension
 to X may not be vertically strict, necessitating the extension of vEquip to a tricategory.
 Note that if T is also co-horizontally strong, in the sense that its horizontal dual is
 horizontally strong, then it also induces a pseudo-comonad ˆ
 H(T) on the bicategory H(X).
 From this perspective, T-monoids can be identified with lax ˆ
 H(T)-coalgebras. Of course,
 if we work in the horizontal dual, then ˆ
 H(T) becomes a pseudomonad and T-monoids are
 its lax algebras. This is the terminology used by several authors, including Hermida. The
 authors of [DS03] consider the special case when the bicategory B is monoidal and T is its
 free monoid monad, so that T-monoids can be called lax monoids. Such lax monoids can
 also be described directly in terms of B, without the need for cocompleteness hypotheses
 to ensure that T exists.
 B.6. (T,V)-algebras.Following Barr [Bar70], [CT03] started a series of papers which
 described the ideas of a “set-monad with lax extension to V-Mat”, as well as the (T,V)
algebras associated to these monads. Barr’s original idea showed that the “lax algebras”
 of the ultrafilter monad are topological spaces; Clementino and Tholen’s idea extended
 this further, developing a framework that eventually included not only topological spaces,
 but also metric spaces, approach spaces, and closure spaces.
 In the work on (T,V)-algebras, two definitions of set-monad with lax extension to
 V-Mat have been proposed. The original version was applicable to all monoidal V.
 However, it failed to capture all relevant examples, and so a second, slightly different defi
nition was proposed in [Sea05], which captured further examples. However, this definition
 was only applicable when V was a preorder. As we shall see, Seal’s definition turns out
 to be equivalent to asking for a monad on V-Mat (in the case when V is ordered), while
 the original definition is equivalent to asking for a monad on V-Mat which is normal.
 The original definition, given in [CT03], was as follows:
 B.7. Definition. A set monad with lax extension to V consists of a monad
 (T,η,µ) on Set, together with a lax functor TM on V-Mat such that:
 • TM is the same as T on objects and functions (viewed as V-matrices),
 • the comparisons (TMs)(TMr)
 TM(sr) are isomorphisms when r is a function,
 • when viewed as transformations on TM, η and µ have op-lax structure.
 In general, however, this definition was found to be too restrictive, as it didn’t allow
 for examples such as extensions of the powerset monad, whose algebras would be closure
 spaces. To include this type of example, the requirement that TM was the same as T on
 functions needed to be removed. Seal’s definition, given in [Sea05], was the following:
 B.8. Definition. Suppose that V is a monoidal preorder. A set monad with lax
 extension to V consists of a monad (T,η,µ) on Set, together with a lax functor TM on
 V-Mat which is the same as T on objects, and satisfies
64
 (i) Tf ≤ TMf,
 (ii) (Tf)◦ ≤ TMf◦.
 Here, ()◦ denotes taking the opposite V-matrix. By [Sea05, p. 225] the conditions
 imply that if q is a V-matrix and f a function, then TM(qf) = (TMq)(Tf) and TM(f◦q) =
 (Tf)◦(TMq).
 In [Sea05, p. 203], he also shows that when V is completely distributive, η and µ have
 op-lax structure. However, this is not a priori required in his definition. If, however, we
 include this axiom in his definition, then his notion of set monad with lax extension is
 equivalent to giving our notion of a monad on the virtual double category V-Mat.
 B.9. Proposition. Suppose that V is a monoidal preorder. If T is a set monad with
 lax extension TM (in the sense of Seal) for which η and µ are op-lax, then we can define
 a monad T on V-Mat which is T on vertical arrows, and TM on horizontal arrows.
 Conversely, given a monad T on V-Mat, we can define a set monad with lax extension
 which is T on functions, and uses the horizontal action of T to define TM.
 Proof. Suppose that we have a set monad with lax extension to V, in the second sense
 given above. Define a functor T on the double category V-Mat, which is T on vertical
 arrows, and TM on horizontal arrows. Using the η and µ, we get all of the necessary data
 for a monad on V-Mat, with the exception of checking that
 X
 X
 W 
f
 X
 W
 W
 p
 q
 Y
 YY
 g
 Z
 Z
 Z
 implies
 TX
 TX
 TX
 Tf
 TW
 Tp
 TY
 TY
 TY
 Tg
 TW TZ
 TW TZ
 Tq
 TZ
 This is equivalent to checking that p ≤ g◦qf ⇒ TM(p) ≤ (Tg)◦TMq(Tf). But this is easy
 to check by using the two results given after Seal’s definition.
 TM(p) ≤ TM(g◦qf) = (Tg)◦TM(qf) = (Tg)◦(TMq)(Tf)
 Conversely, suppose that we have a monad T on V-Mat. We would like to define a
 lax extension of T (considered as a Set-monad) to V-Mat. Define TM on matrices as for
 T. The only conditions we need to check are Tf ≤ TMf and (Tf)◦ ≤ TMf◦. To show the
 f
 irst is equivalent to showing that TB(1,Tf) ≤ T(B(1,f)). To show this, recall that we
 have a cartesian cell
 A
 A
 f
 B
 B
 B(1,f)
 B
 B
 B
 B
 UB
65
 Moreover, sinceT isafunctor, itpreservescartesiancells(Theorem7.24),andso
 TA TB T(B(1,f))
 TB TB T(UB)
 TA
 TB
 Tf
 TB
 TB
 isalsocartesian.Wecanthusfactorthecell
 TA TB TB(1,Tf)
 TB TB UTB
 TA
 TB
 Tf
 TB
 TB TB
 TB
 TB
 TB TB TB T(UB)
 throughittogetacell
 TA TB TB(1,Tf)
 TA TB T(B(1,f))
 TA
 TA
 TB
 TB
 asrequired.Thesecondinequalityfollowssimilarly,usingthecartesiancell
 B A B(f,1)
 B B UB
 A
 B
 f
 B
 B
 Thus,amonadonV-Matdefinesaset-monadwithlaxextensiontoV-Mat.
 ForgeneralV,wecanalsousetheabovecorrespondencetorecoverthefirstnotionof
 notionofset-monadwithlaxextension: theyarethemonadswhicharenormal.
 B.10. Proposition.Usingtheabovecorrespondence,wegetaset-monadwithlaxex
tensioninthefirstsenseifandonlyif themonadTonV-Mat isnormal.
 Proof.Supposewehaveaset-monadTwithlaxextensionTMinthefirstsense. Then
 wehaveTMf∼ =Tf forall functionsf, andwegetamonadonV-Mat. Moreover,we
 alsohaveT(B(1,f))∼ =TB(1,Tf). Inparticular,wehaveT(A(1,1))∼ =TA(1,1). But
 A(1,1)∼ =UA, sowehaveT(UA)∼ =UTA.ThusT isnormal.
66
 Conversely, suppose that we have a monad T on V-Mat which is normal. We would like
 to show that T(B(1,f)) ∼ = TB(1,Tf). Using the factoring as for the above proposition,
 we get a cell in one direction. To get the other direction, we factor
 TA
 TA
 TA
 Tf
 TB
 TB
 TB
 TB
 TB
 TB
 TB
 TB
 T(B(1,f))
 T(UB)
 UTB
 TB
 TB
 TB
 TB
 TB
 TB
 TB
 TB
 TB
 TB
 TB
 TB(1,Tf)
 TA
 TA
 TA
 through
 Tf
 TB
 TB
 TB
 UTB
 TB
 TB
 TB
 TB
 TB
 TB
 (note that the bottom cell on the left exists by normality of T). The composites of the
 two cells are identities by the universal property of the cartesian cells, and so we have
 T(B(1,f)) ∼ = TB(1,Tf), as required. We also need to check the condition that the
 comparison cell be an isomorphism when r is a function. However, this is equivalent to
 asking that T(r(1,s)) ∼
 =Tr(1,Ts), and this follows from Theorem 7.24.
 For a set-monad with lax extension T, the category of (T,V)-algebras that Tholen,
 Clementino and Seal define is exactly the vertical category of the virtual double category
 KMod(V-Mat,T). They also describe (T,V)-modules, and these are the horizontal arrows
 of KMod(V-Mat,T).
 In addition to providing a more conceptual explanation of the notion of lax extension,
 and a way to compare (T,V)-algebras with other notions of generalized multicategory, our
 general framework improves the theory of (T,V)-algebras in two ways. Firstly, it gives a
 context in which the horizontal Kleisli construction makes sense; such a construction has
 been recognized as desirable (see, for example, [Tho07, p. 7]), but is impossible using only
 bicategories since the monads used in this case are not horizontally strong. Secondly, it
 provides a general reason for the observation of [Tho07, p. 15] that any set-monad with
 lax extension to V-Mat can also be extended to a monad on V-Cat with a lax extension
 to V-Prof (we simply apply the 2-functor Mod).
 There are, however, other special aspects of the theory of (T,V)-algebras which we
 have not discussed. One is that the category of (T,V)-algebras is generally topological
 over Set, and has many other similar formal properties. Another is the use of the “pro”
 construction found in [CHT04]. This is useful to describe additional topological structures;
 for example, monoids in pro-Rel are quasi-uniform spaces. In general, given a virtual
 double category X, one can define a new virtual double category pro-X, and go on to
 describe “pro-generalized multicategories”. Further discussion of this, however, awaits a
 future paper.
 B.11. Non-cartesian monads. The earliest work on generalized multicategories was
 by Burroni in [Bur71]. His framework is very similar to Leinster’s (see §B.1) except
 that he requires nothing at all about the monad T, not even that it preserve pullbacks
 (although the category E must still have pullbacks). This level of generality does not
67
 f
 it into our existing framework, since if T does not preserve pullbacks then it does not
 induce a functor on Span(E) of the sort we have defined. However, it does induce an oplax
 functor between pseudo double categories.
 The simplest way for us to define an oplax functor is to say it is a functor between
 pseudo double categories regarded as co-virtual double categories. Pseudo double cat
egories, oplax functors, and transformations form a 2-category, and we define an oplax
 monad on a pseudo double category to be a monad in this 2-category. Since any functor
 on a category E with pullbacks induces an oplax functor on Span(E), any monad on such
 an E induces an oplax monad on Span(E). Moreover, we can extend our framework to
 deal with oplax functors as follows.
 B.12. Definition. If T is an oplax monad on a pseudo double category X, the hori
zontal Kleisli virtual double category H-Kl(X,T) of T is defined as follows.
 • Its objects, vertical and horizontal arrows, and cells with nullary source are defined
 as when T is a lax functor.
 • A cell
 X0
 X0
 X0
 f
 Y0
 Y0
 Y0
 in H-Kl(X,T) is a cell
 X0
 f
 Y0
 in X.
 p1
 X1
 X1
 p2
 X2
 X2
 α
 q
 p3
 p1⊙T(p2⊙T(···T(pn−1⊙Tpn)···))
 q
 · · ·
 · · ·
 pn
 TnXn
 Xn
 Xn
 Xn
 g
 Y1
 Y1
 Y1
 Tg◦µn
 TY1
 • Composition is defined using the multiplication, unit, and oplax structure of T. For
 example, the composite of
 ⇓α
 is given by the composite
 ⇓β
 ⇓δ
 ⇓γ
 δ ◦(α⊙T(β ⊙Tγ))◦(1⊙µ)◦(1⊙T(1⊙µ))◦T⊙
 in X,where T⊙ is a composite of the oplax structure maps of T:
 p1 ⊙T(p2⊙T(p3⊙T(p4⊙T(p5⊙Tp6))))
 p1 ⊙Tp2⊙T2(p3⊙Tp4⊙T2(p5⊙Tp6))
 Note that when T is a strong functor, both definitions of H-Kl(X,T) make sense;
 however, in this case, they are equivalent.
68
 B.13. Definition. If T is an oplax monad on a pseudo double category X, then a
 T-monoid is a monoid in H-Kl(X,T), and we write KMod(X,T) = Mod(H-Kl(X,T)).
 To compare this to Burroni’s definition, note that H-Kl(X,T) clearly has weak com
posites, and hence is an oplax double category (see Example 9.7). Burroni works instead
 with what he calls a pseudo-category, and what we would probably call a lax-biased bi
category: a bicategory-like structure with units and binary composites and noninvertible
 comparison maps
 p
 p
 p ⊙UA
 UB ⊙p
 p ⊙(q ⊙r) (p⊙q)⊙r.
 satisfying suitable axioms. In fact, of course, Burroni’s “pseudo-category of T-spans”
 extends to a lax-biased double category. Moreover, any lax-biased double category defines
 an oplax double category (and hence a virtual double category), if we take the n-ary
 composite to be
 p1 ⊙···⊙pn = p1⊙(p2⊙···(pn−1 ⊙pn)···).
 (Burroni points this out as well; see [Bur71, p. 66, example 3]. He refers to virtual double
 categories as simply “multicat´egories”.) In this way, Burroni’s Sp(T) is identified with our
 H(H-Kl(Span(E),T)), and thus his “T-categories” can be identified with our T-monoids.
 However, he must move outside the bicategory (or “pseudo-category”) framework to define
 functors of T-categories, whereas they emerge naturally from our setup.
 Burroni also constructs the left adjoint FT from Remark 9.16, and proves the func
toriality of his construction under lax morphisms of monads. Working with his lan
guage, [Pen09] gives a version of the representability results from §9.
 B.14. Cartesian 2-monads. We have described the horizontal arrows A
 Set-Prof as profunctors, i.e. functors Bop × A
 B in
 Set, but it is well-known that such
 functors can equivalently be described by two-sided discrete fibrations, and that this notion
 can be internalized to a sufficiently well-behaved 2-category. [Her01] develops a theory of
 generalized multicategories in such a context.
 Let K be a finitely complete 2-category (see [Str76]). Since its underlying ordinary
 category K0 has pullbacks, we can form the virtual equipment Prof(K0). Now, as observed
 in [Str74], for any object A ∈ K we have an internal category ΦA in K, defined by
 (ΦA)0 = A and (ΦA)1 = A2 (the cotensor with the arrow category 2). Similarly, every
 morphism f: A B defines aninternal functor Φf: ΦA ΦB, and the same for 2-cells;
 thus we have a 2-functor K
 V(Prof(K0)). Moreover, this 2-functor is locally full and
 faithful, i.e. 2-cells f
 g are in bijection with internal natural transformations Φf
 Φg.
 We define an internal profunctor ΦA H ΦB to be a discrete fibration from A to
 B if the object H ∈ K/(A×B) is internally discrete, i.e. (K/(A×B))(C,H) is a discrete
 category for any C ∈ K/(A×B). We define DFib(K) to be the sub-virtual double category
 of Prof(K0) determined by
69
 • The internal categories of the form ΦA,
 • The internal functors of the form Φf,
 • The internal profunctors which are discrete fibrations, and
 • All cells between these.
 Since the pullback of a discrete fibration is a discrete fibration, and UΦA is a discrete
 f
 ibration, DFib(K) is a virtual equipment. Our remarks above show that K ≃ V(DFib(K)).
 Under suitable conditions on K, discrete fibrations can be composed, so that DFib(K)
 becomes an equipment. (Several authors have tried to isolate these conditions, with
 varying degrees of success; in addition to [Her01] see [Str80] and [CJSV94]. Our approach
 sidesteps this issue completely.)
 Now suppose that F : K
 L is a 2-functor that preserves pullbacks and comma ob
jects. Then it preserves internal categories, profunctors, the Φ construction, and discrete
 f
 ibrations, so it induces a normal functor DFib(F): DFib(K)
 2-natural transformation F
 DFib(L). Likewise, any
 G induces a transformation DFib(F)
 DFib(G), so we
 have a 2-functor DFib from finitely complete 2-categories to vEquip. In particular, any
 2-monad T on K whose functor part preserves pullbacks and comma objects induces a
 normal monad on DFib(K), so we can talk about DFib(T)-monoids.
 This is basically the context of [Her01], except that, like most other authors, he works
 only with bicategories. Thus, he assumes that K has the structure required to com
pose discrete fibrations, and that moreover T preserves this structure and that µ and η
 are cartesian transformations. This ensures that DFib(T) is horizontally strong, so that
 H-Kl(DFib(K),DFib(T)) is an equipment. Under these hypotheses, we have:
 B.15. Theorem. The 2-category nKMon(DFib(K),DFib(T)) of normalized DFib(T)
monoids is isomorphic to the 2-category Lax-Bimod(T)-alg defined in [Her01, 4.3 and
 4.4].
 Our Theorem 9.2 is also a generalization of results of [Her01]. Hermida proves fur
thermore that under his hypotheses, the left adjoint FT from Remark 9.16 exists, the
 adjunction is monadic when restricted to pseudo T-algebras, and the induced monad T⊣
 on Lax-Bimod(T)-alg is lax-idempotent (see [KL97]). In [CS10b] we will show that an
 analogous result is true for any monad T on an equipment X satisfying suitable cocom
pleteness conditions.
 B.16. Monoidal pseudo algebras. In [Web05], Weber gives a definition of gener
alized operads enriched in monoidal pseudo algebras. More precisely, for any 2-monad
 T on a 2-category K with finite products, and any pseudo T-algebra A which is also
 a pseudomonoid in a compatible way, he defines a notion of T-operad in A. A general
 description of the relationship of this theory to ours would take us too far afield, so we
 will remark only briefly on how such a comparison should go.
 For any pseudomonoid A in a 2-category K with finite products, there is a virtual
 equipment A-Mat defined as follows. Its objects and vertical arrows are the objects and
70
 arrowsofK.AhorizontalarrowfromX Y isamorphismX×Y p AinK,anda
 cell
 X0
 p1
 f
 X1 _ _ _ Xn−1
 pn Xn
 g
 Y0 q Y1
 isa2-cell
 Y0×Y1 A. q
 (X0×X1)×(X1×X2)×···×(Xn−1×Xn)
 Y0×Y1
 (f,g)
 •••••••••
 (X0×X1)×(X1×X2)×···×(Xn−1×Xn)
 A.
 (p1⊗···⊗pn)
 ? ? ? ? ? ? ? ? ?
 oo oo
 inK,where⊗:A×A AisthemultiplicationofthepseudomonoidA(ifn=0weuse
 itsunit instead).
 Now, ifAisadditionallyapseudoT-algebrainacompatibleway,wemighthopeto
 beabletoextendTtoamonadonA-Mat.However,givenahorizontalarrowX p Y,
 fromX×Y p Awecanformthecomposite
 T(X×Y) Tp TA a A,
 but this isnotyetahorizontal arrowTX TY. Ifweassume thatAadmitswell
behavedleft (Kan)extensions, thenwecandefineTX Tp TY tobetheextensionof
 theabovecompositealongT(X×Y) TX×TY.WecanthenconstructH-Kl(A-Mat,T)
 andKMod(A-Mat,T)asusual.Moreover,wecangiveanequivalentcharacterizationof
 H-Kl(A-Mat,T)whichisvalidevenintheabsenceof leftextensions: ahorizontalarrow
 X Y isamorphismX×TY p AinK,andacell
 X0
 p1
 f
 X1 _ _ _ Xn−1
 pn Xn
 g
 Y0 q Y1
 isa2-cell
 X0×T(X1×...T(Xn−1×TXn)...)
 π p1⊗...⊗pn
 U U U U U U U U U U U U U U U U U U U U
 X0×TnXn
 1×µn−1
 A
 X0×TXn
 f×Tg
 Y0×TY1
 q
 s s s s s s s s s s s s s s s s s s s s s s s s s s
71
 in K. Thus, we obtain a notion of T-monoid in A for any monoidal pseudo algebra A.
 Weber only considers the case of operads, rather than more general multicategories, but
 it is easy to verify that his T-operads in A coincide with those T-monoids in A whose
 underlying object in K is the terminal object 1.
 Actually, there is a good reason that Weber considers only operads: T-monoids
 X0
 X TX0 in this context for which X0 is not discrete, or at least a groupoid, are
 not very familiar objects. In familiar cases such as K = Cat, we would obtain familiar
 types of A-enriched multicategory only by taking the horizontal arrows X
 Y in A-Mat
 to be morphisms X × Yop
 A, rather than X × Y
 A. To put this in a general
 context, however, requires a 2-category K in which “opposites” make sense, such as the
 “2-toposes” of [Web07].




 Recursive Neural Networks
 Sargur Srihari
 srihari@buffalo.edu
 1 
Deep Learning                                                                               
Topics
 Srihari 
• Sequence Modeling: Recurrent and Recursive Nets
 1. Unfolding Computational Graphs
 2. Recurrent Neural Networks
 3. Bidirectional RNNs
 4. Encoder-Decoder Sequence-to-Sequence Architectures
 5. Deep Recurrent Networks
 6. Recursive Neural Networks
 7. The Challenge of Long-Term Dependencies
 8. Echo-State Networks
 9. Leaky Units and Other Strategies for Multiple Time Scales
 10. LSTM and Other Gated RNNs
 11. Optimization for Long-Term Dependencies
 12. Explicit Memory
 2 
Deep Learning                                                                               
Srihari 
Recursive Neural Networks
 • They are yet another generalization of recurrent networks with 
a different kind of computational graph
 • It is structured as a deep tree, rather than the chain structure of 
RNNs
 • The typical computational graph for a recursive network is 
shown next
 3 
Deep Learning                                                                               
Srihari 
Computational graph of a Recursive Network
 • It generalizes  a recurrent 
network from a chain to a tree
 • A variable sequence x(1),x(2),,x(t) 
can be mapped to a fixed size 
representation (the output o), with 
a fixed set of parameters (the 
weight matrices U,V,W)
 • Figure illustrates supervised 
learning case in which target y is 
provided that is associated with 
the whole sequence
 4 
Deep Learning                                                                               
Srihari 
Advantage of Recursive over Recurrent Nets
 • For a sequence of the same length τ, the depth (measured as 
the no. of compositions of nonlinear operations) can be reduced 
from τ to O(log τ), which might help deal with long-term 
dependencies
 • An open question is how best to structure the tree
 5 
Deep Learning                                                                               
Srihari 
Need for Recursive nets in NLP
 • Deep learning based methods learn low-dimensional, real
valued vectors for word tokens, mostly from a large data 
corpus, successfully capturing syntactic and semantic 
aspects of text
 • For tasks where the inputs are larger text units, e.g., 
phrases, sentences or documents, a compositional model 
is first needed to aggregate tokens into a vector with fixed 
dimensionality that can be used for other NLP tasks
 • Models for achieving this fall into two categories: recurrent 
models and recursive models
 6 
Deep Learning                                                                               
Srihari 
Recurrent Model for NLP
 • Recurrent models deal successfully with time series data
 • The recurrent models generally consider no linguistic structure 
aside from the word order
 • They were applied early on to NLP by modeling a sentence as 
tokens processed sequentially and at each step combining the 
current token with previously built embeddings
 • Recurrent models can be extended to bidirectional ones from 
both left to right and right to left
 • These models consider no linguistic structure aside from word 
order
 7 
Deep Learning                                                                               
Srihari 
Recursive Models for NLP
 • Recursive neural models (also referred to as tree models) by 
contrast are structured by syntactic parse trees
 • Instead of considering tokens sequentially, recursive models 
combine neighbors based on the recursive structure of parse 
trees, starting from the leaves and proceeding recursively in a 
bottom-up fashion until the root of the parse tree is reached
 • Ex: for the phrase the food is delicious, following the operation sequence 
((the food) (is delicious)) rather than the sequential order (((the food) is) 
delicious) 
8 
Deep Learning                                                                               
Srihari 
Advantage of Recursive Model for NLP
 • They have the potential of capturing long-distance 
dependencies
 • Two tokens may be structurally closer to each other even 
though they are far away in word sequence
 • Ex: a verb and its corresponding direct object can be far away 
in terms of tokens if many adjectives lie inbetween, but they are 
adjacent in the parse tree
 • However parsing is slow and domain dependent
 • See performance comparison with LSTM on four NLP tasks  at 
https://nlp.stanford.edu/pubemnlp2015_2_jiwei.pdf 
9 
Deep Learning                                                                               
Srihari 
Structure of the Tree
 • One option is to have a tree structure that does not depend on 
the data, such as a balanced binary tree
 • In some application domains, external methods can suggest the 
appropriate tree structure
 • Ex: when processing natural language sentences, the tree structure for 
the recursive network can be fixed to the structure of the parse tree of 
the sentence provided by a natural language parse
 • Ideally, one would like the learner itself to discover and infer the 
tree structure that is appropriate for any given input
 10 
Deep Learning                                                                               
Srihari 
Variants of Recursive Net idea
 • Associate data with a tree structure and associate inputs and 
targets with individual nodes of the tree
 • The computation performed for each node does not have to be the 
artificial neuron computation (affine transformation of all inputs followed 
by a monotone nonlinearity)
 • Can use a tensor operations of bilinear forms
 • Previously found useful to model linear relationships between 
concepts when the concepts are represented by continuous vectors
 11 
Deep Learning                                                                               
Srihari 
Recursive Neural Networks 
• Recursive neural networks 
are also called Tree Nets 
• Useful for learning tree-like 
structures
 • They are highly useful for 
parsing natural scenes and 
language
 12 
Deep Learning                                                                               
Srihari 
Unrolling Recurrent and Tree Nets
 • In RNNs, at each time step the network takes as input its 
previous state s(t-1) and its current input x(t) and produces an 
output y(t) and a new hidden state s(t). 
• TreeNets, on the other hand, don’t have a simple linear 
structure like that. 
• With RNNs, you can ‘unroll’ the net and think of it as a large 
feedforward net with inputs x(0), x(1), …, x(T), initial state s(0), 
and outputs y(0),y(1),…,y(T), with T varying depending on the 
input data stream, and the weights in each of the cells tied with 
each other. 
• You can also think of TreeNets by unrolling them – the weights 
in each branch node are tied with each other, and the weights 
in each leaf node are tied with each other. 
13 
Deep Learning                                                                               
Srihari 
Advantage of Recursive Nets
 • The advantage of Recursive Nets is that they can be very 
powerful in learning hierarchical, tree-like structure. 
• The disadvantages are, firstly, that the tree structure of every 
input sample must be known at training time



Thinking Recursively, Rethinking Corecursively
 David Jaz Myers
 June 19, 2017
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Mathematical Metaphors
 This talk will be about two specific mathematical metaphors, but
 what are mathematical metaphors,
 why make them,
 and how can they be misused?
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Mathematical Metaphors
 In this talk, we will look closely at the mathematical metaphor
 between
 Complex Systems and Recursive Functions
 Wewill see how this metaphor a lot of standard theories in science
 and philosophy, usually those that fall under the rubrik of “realism”.
 Wewill also find that this metaphor can lead us to some shaky
 philosophical positions.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 What is a function
 A function is a process that turns an input into an output.
 F(input) = output
 If a function takes inputs of a type Inputs and gives outputs of a
 type Outputs, we write
 F : Inputs Outputs
 For example,
 F : Numbers Numbers
 F(n) = 2n +1
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 What is Recursion?
 A function is recursive when its output on a complicated input is
 determined by its output on simpler inputs.
 Ultimately, the output of a recursive function is determined by its
 simplest inputs.
 Wecall these simplest inputs atoms, or base cases, and the rules
 for building them up constructors.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 What is Recursion?
 So to define a recursive function we need
 to know how to break apart complicated inputs into simpler
 ones,
 simplest inputs (so we eventually stop breaking things apart),
 to know how to put outputs together in a way that relates to
 taking inputs apart!
 Or, more pithily, we need:
 to know how to analyze inputs,
 into their atomic components,
 so that we can construct outputs.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 A Lengthy Example
 Let’s calculate the length of a list! This is a function which takes a
 list as input and gives a number as output.
 Length : Lists Numbers
 A list is something like:
 [first item, second item, third item
 Wecan break down a list like this:
 last item]
 AList = [first item, Rest of the List]
 or the list is Empty.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 A Lengthy Example
 Let’s calculate the length of a list! This is a function which takes a
 list as input and gives a number as output.
 Length : Lists Numbers
 Numbers can be built up by counting:
 0 is a number, and
 (1 +anumber) is a number.
 This is related to taking lists apart because, secretly, numbers are
 like lists of tally marks:
 4 =
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 A Lengthy Example
 Definition (Length of a List)
 The length of a list is given by the function defined by:
 Length(Empty) 0
 Length([first itemRest of List]) 1 + Length(Rest of List)
 This works because
 Empty is an atom. There are no simpler lists, so we can stop
 breaking things apart.
 The Rest of the List is simpler (i.e. smaller) than the list we
 started with. This means we eventually get to the Empty list.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Running a Recursive Program
 Wecan run a recursive program greedily:
 Every time we see something we don’t understand, we compute it.
 Length([123]) = 1+Length([23])
 =1+(1+Length([3]))
 =1+(1+(1+Length(Empty)))
 =1+(1+(1+0))
 =1+(1+1)
 =1+2
 =3
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Recursively About Everything
 This way of thinking should be familiar to you from popular ways of
 thinking about physics.
 Claim
 Physics is like a recursive function
 Physics : Systems Systems
 which recurses all the way to the fundamental particles, and then
 builds more complicated phenomena out of the way they behave.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Recursively About Everything
 Or from philosophy of language:
 Claim
 Meaning is like a recursive function
 Meaning : Utterances Meanings
 which builds the meaning of, say, sentences out of the meaning of
 words.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Recursively About Everything
 Or from sociology
 Claim
 A society is like a recursive function
 Society : Societies 
Societies
 which is determined by the behavior of individuals which are, of
 course, indivisible.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Recursively About Everything
 Or from economics
 Claim
 The economy is like a recursive function
 Economy : Markets Markets
 which is determined by the behavior of agents who act rationally.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Analysis is Recursive
 Definition
 [Analysis] might be defined as a process of isolating or
 working back to what is more fundamental by means of
 which something, initially taken as given, can be
 explained or reconstructed.– Stanford Encyclopedia
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 A Philosophical Problem
 In his book The Case for Idealism, John Foster argues that some
 things must have inscrutable, intrinsic properties.
 Foster’s argument for inscrutable intrinsic properties
 Suppose that all properties of all things were extrinsic, that is,
 defined in relation to other things.
 A)
 (B
 Now, consider a world containing two things, A and B, each
 defined only by their disposition to repel the other.
 Foster claims this leads to an infinite regress, and therefore a
 contradiction.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 A Philosophical Problem
 Foster’s argument for inscrutable intrinsic properties (cont’d)
 The back and forth must stop somewhere:
 “A is the thing which 
X”
 X is the end of the line, it is not defined in relation to anything else.
 Therefore, it is both
 inscrutable, and
 intrinsic.
 This argument rests on two (recursive) assumptions:
 1 Wemust ‘evaluate’ greedily.
 2 There must be a base case.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Do WeHaveto Make Those Assumptions?
 is there another way?
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Corecursion
 A function is corecursive when its output is determined by simpler
 outputs.
 Wecall the rules for breaking apart the output observers.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 What is Corecursion
 So, to define a corecursive function we need
 to know how to observe the output of our function in simpler
 ways,
 that relate to how we observe our inputs!
 Wecan think of the observers as being experimental setups with
 which we will test the output of our function.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 What is Corecursion
 The main idea behind corecursion is:
 If we know how our function behaves in all experimental
 setups, we know what it does.
 This is essentially the same as one of the fundamental principles
 of science:
 If we can predict how something behaves in all experimental
 setups, then we know what it is.
 So long as we believe that a function is what it does.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Stream and Chill
 Let’s have some fun with streams to get our heads around
 corecursion.
 A stream is an infinite list, so we can’t keep the whole thing in
 memory, but we can observe it piece by piece.
 So, let’s set up two experiments:
 1 Head, where we test what the first thing in the stream is.
 2 Tail, where we see what’s left.
 Now wecan define functions corecursively, since we know how to
 observe their behavior.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Stream and Chill
 Let’s define a function
 Every Other : Streams Streams
 that will take a stream and return the stream of only every other
 value. For example:
 Every Other(01234 )=(024 )
 To define this, we just need to define how it looks in all the
 experiments.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Stream and Chill
 Definition (The Every Other Function)
 Define the Every Other function by
 EO(stream)Head = streamHead
 EO(stream)Tail = EO(streamTailTail)
 This works because
 EO(stream) is covered by the observers Head and Tail, they
 tell us all we need to know about it.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Running a Corecursive Program
 Wecan’t evaluate a corecursive program greedily, because the
 calculation would never end! We have to be lazy:
 Only compute things when we absolutely need to.
 So if you wrote down
 EO((0123 ))
 That would be totally chill.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Running a Corecursive Program
 But, if we want to know a specific value of EO((0123 )), then
 we can calculate
 EO((0123 ))TailTailHead
 =EO((0123 )TailTail)TailHead
 =EO((0123 )TailTailTailTail)Head
 =(0123 )TailTailTailTailHead
 =(1234 )TailTailTailHead
 =(2345 )TailTailHead
 =(3456 )TailHead
 =(4567 )Head
 =4
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Corecursion and Diff´ erance
 If someone asks you what “EO” means, you could tell them that its
 meaning is deferred until we test it with the observers Head and
 Tail.
 If they ask you what “Head” and “Tail” mean, you could only tell
 them the different ways you end up using them.
 Definition
 Diff´ erance is Derrida’s pun on the words defer and differ.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Thinking Corecursively
 Who amI?
 How can I find out?
 Do I have to find my ‘true self’, the core of my being, to know who I
 am?
 Or do I only have to look at the way I affect the people and places
 around me?
 Thinking corecursively, we don’t have to be anxious about finding
 our true selves.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Revisiting Foster
 Let’s look back at Foster’s argument for inscrutable intrinsic
 properties. He claims that the world in which
 A only repels B and B only repels A
 cannot exist because it leads to an infinite regress.
 Only leads to infinite regress if we are greedy.
 If we are lazy, this is a perfectly fine definition.
 There is nothing inscrutable about it.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Revisiting Foster
 Foster’s argument shows a fundamental confusion that often
 underlies recursive thinking:
 the confusion between names and things
 Names are like atoms, we don’t break them apart.
 Things (such as functions) can be named, even when we
 define them corecursively.
 But that doesn’t mean that they have base cases!
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Limits of the Metaphor
 To define a function corecursively, we must cover it by observers.
 Head and Tail tell us all there is to know about a stream.
 But in the informal world, we never have access to all the contexts
 in which an object appears,
 Wecan never get all sides of the story.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Going Forward: Physics
 Physicists have been thinking corecursively for a long time:
 A physical quantity can only be assigned specific values given
 a local coordinate system, or gauge.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Going Forward: Physics
 Principle of Relativity
 The physical laws have the same form in all choices of gauge.
 A change in gauge is called a gauge symmetry.
 In other words, if we rotate our experimental setup, we get a
 rotated result.
 r(X) = r( X)
 The relationship between the observations X and r(X)
 depends on how X was rotated to r(X).
 To fully know an object, we must not only know how it behaves in
 various contexts,
 we must also know how those contexts relate.
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 In Conclusion
 Thinking recursively makes us believe that
 There are basic objects and basic truths about them at the
 bottom of all phenomena, and
 To know anything at all, we need to know about these basic
 things.
 Thinking corercursively makes us believe that
 Things only make sense in context (in an experiment, relative
 to an observer, etc.), and
 Knowing how a thing behaves in context is all there is to know
 about it
 There are no basic objects or basic truths
 David Jaz Myers
 Thinking Recursively, Rethinking Corecursively
What is Recursion?
 Thinking Recursively
 There is Another Way
 Thinking Corecursively
 Bridging the Divide
 In this talk, I made a stark division between recursive and
 corecursive thinking.
 But in actually programming languages (like Haskell), you can use
 recursion and corecursion together depending on which is more
 convenient.
 Weshould use recursive and corecursive thinking together,
 depending on what needs to be done.
 But most importantly, we need to remember that metaphors matter.
 Don’t get trapped in a single metapho



 Dirichlet Functors are Contravariant Polynomial Functors
David Jaz Myers David I. Spivak
April 2020
Abstract
Polynomial functors are sums of covariant representable functors from the category
of sets to itself. They have a robust theory with many applications — from operads and
opetopes to combinatorial species. In this paper, we define a contravariant analogue
of polynomial functors: Dirichlet functors. We develop the basic theory of Dirichlet
functors, and relate them to their covariant analogues.
1 Introduction
A polynomial functor P : Set → Set is a sum of representables
P(X) := X
b∈B
XEb (1)
where the family of sets Eb depends on b ∈ B. This data is known in the computer
science literature as a “container” [2, 3, 1], but since such an indexed family of sets can
be represented by as a function
E
B
π
we will refer to it as a bundle.
Remarkably, all natural transformations between polynomial functors can be represented in terms of their associated bundles. A natural transformation P → P
′
corresponds
to a dependent pair of functions

f : B → B
′
, f♯
: (b : B) → E
′
fb → Eb

noting that f acts covariantly on the base and f
♯ acts contravariantly on fibers. In terms
of bundles, a map π → π
′
is a diagram of the following sort:
E • E′
B B B′
π
f♯
p
π
′
f
We refer to these special spans as contravariant morphisms of bundles.
1
Theorem 1.1 (Theorem 2.17 of [4]). The category of polynomial functors and natural
transformations is equivalent to the category of bundles and contravariant morphisms.
This begs the question: what, then, are we to make of the more obvious, covariant
morphisms of bundles
E E′
B B′
tot(f♯)
π π
′
f
for which f♯
is covariant in each fibers?
It turns out that these covariant maps of bundles correspond to natural transformations
between the appropriate sums of contravariant representables:
D(X) := X
b∈B
E
X
b
.
Polynomial functors as in (1) get their name from the case in which B and E are
finite sets. Consider the function card E(−)
: B → N, which takes the cardinality of each
fiber Eb. Letting Bn := (cardE(−)
)
-1(n), i.e. Bn is the set of elements whose fibers have n
elements, we find that the for any set X, the cardinality
|P(X)| =
X
n∈N
|Bn||X|
n
is a polynomial in the cardinality of X. Similarly,
|D(X)| =
X
n∈N
|Bn|n
|X|
resembles a Dirichlet series in the cardinality of X – without the usual negative sign.1
Accordingly, we call such sums of contravariant representables Dirichlet functors.
We will show in this paper that Dirichlet functors are, quite robustly, the contravariant
analogue of polynomial functors. In particular, the many equivalent ways to say that a
functor is polynomial have contravariant analogues.
Theorem 1.2 (See discussion in 1.18 of [4]). Let P : Set → Set be a functor. Then the
following are equivalent.
1. P is polynomial.
2. P is the sum of covariant representables.
3. There is a bundle π : E → B together with a natural isomorphism
P(X) ∼=
X
b∈B
XEb
.
1Future work of the first author will recover the negative sign by generalizing the theory of Dirichlet
functors to homotopy types.
2
Or, equivalently, a natural isomorphism of P with the composite
Set
∆!E −−→ Set/E
Ππ −−→ Set/B
Σ!B −−→ Set.
4. P is accessible and preserves connected limits.
Analogously, we will prove the following theorem.
Theorem 1.3. Let D : Set op → Set be a contravariant functor. Then the following are
equivalent.
1. D is Dirichlet.
2. D is the sum of contravariant representables.
3. There is a bundle π : E → B together with a natural isomorphism
D(X) ∼=
X
b∈B
E
X
b
.
Or, equivalently, a natural isomorphism of D with the composite
Set op
(∆!B
)
op
−−−−−→ (Set/B)
op
Set/B(−,π)
−−−−−−−→ Set/B
Σ!B −−→ Set.
4. D preserves connected limits.
Note that we no longer need to assume accessiblity. This is a general feature of the
theory of Dirichlet functors; it is a bit “smaller” and more manageable than that of
polynomials. In particular, a Dirichlet functor is determined by its action on the terminal
morphism !0 : 0 → 1 of the empty set. As a corollary, Dirichlet functors form a topos.
Theorem 1.4. The functor Set↓ → Fun(Set op
, Set), given by sending π : E → B to the
induced Dirichlet functor X 7→
P
b∈B EX
b
, is fully faithful, and so gives an equivalence
Set↓ ≃ Dir
between the topos of bundles and the category of Dirichlet functors, with inverse given by
evalutation at !0 : 0 → 1.
Now, object-wise, a Dirichlet functor and a polynomial functor are determined by the
same data — a bundle π : E → B of sets. Accordingly, one would expect for any set N a
transformation
XN 7→ N
X
turning polynomial functors into Dirichlet functors, and vice versa. But the natural transformations between each sort of functor induce different morphisms between the bundles;
natural transformations between polynomial functors induce contravariant bundle morphisms, while natural transformations between Dirichlet functors induce covariant bundle
3
morphisms. However, if we restrict to those morphisms of bundles which are isovariant
on the fibers — that is, the pullback diagrams of the form
E E′
B B′
tot(f♯)
π
p
π
′
f
which preserve the number of elements in each row — we find that such a morphism is
both a co- and contravariant morphism of bundles. It is well known that such cartesian
morphisms of bundles correspond to cartesian natural transformations between polynomial
functors [4, Theorem 3.8] — those whose naturality squares are pullbacks. This is true as
well for Dirichlet functors.
Theorem 1.5. A natural transformation D → D′ of Dirichlet functors is Cartesian if
and only if the corresponding bundle map
E E′
B B′
tot(f♯)
π
p
π
′
f
is a pullback. As a corollary, we have an equivalence of categories
Polyp ≃ Dirp
between polynomial functors with cartesian natural transfromations and Dirichlet functors
with cartesian natural transfromations.
Acknowledgements. We appreciate helpful comments from Andr´e Joyal and Joachim
Kock on an early draft of this paper. Myers was supported by the National Science
Foundation grant DMS-1652600, and Spivak was supported by AFOSR grants FA9550-
19-1-0113 and FA9550-17-1-0058.
2 Dirichlet Functors
Before diving in to the theory of Dirichlet functors, let’s first consider the category Set↓
of bundles of sets and covariant bundle maps. For our proofs to go smoothly, we will need
to explicitly keep track of the self-dualizing isomorphism ↓
∼−→ (↓)
op on the walking arrow.
Definition 2.1. We let ↓ be the walking arrow — the category dom → cod consisting of
a single morphism. We denote by σ : ↓ → (↓)
op the self-dualizing isomorphism of ↓, and
note that σ
-1 = σ
op
.
4
Proposition 2.2. There is an adjoint sextuple:
Set Set const ↓
cod
dom
!
(−)
!(−)
ZC
All three functors Set → Set↓
are fully faithful.
Proof. There is an adjoint triple 1 ↓ . The middle three functors—cod, const, and
dom—are given by restricting along this adjoint triple. The next two, !(−) and !(−)
, are
given by Kan extending, or more concretely:
X 7→
0
X
!X and X 7→
X
1
!X
(2)
It is easy to see that the unit X → dom!X is an isomorphism (it is in fact an identity), so
!(−)
is fully faithful. Therefore, all three functors going from Set → Set↓
are fully faithful.
The existence of the final adjoint can be deduced from the fact that !(−) preserves
all limits. It sends a bundle π : E → B to the largest subset ZC(π) of B for which the
following square is a pullback
0 E
ZC(π) B
p
π
We prove a few quick facts we will use later.
Lemma 2.3. The functor !(−)
: Set → Set↓
from (2) preserves connected colimits.
Proof. To see that !(−) preserves connected colimits, recall that colimits in Set↓
are calculated pointwise. It remains to show, then, that the map of bundles
colim Xi colim Xi
colim 1 1
is an isomorphism in Set↓
, for which it suffices to show that colim 1 is terminal. But the
colimit of a diagram of terminal objects is the set of connected components of its indexing
category. Since we assumed the indexing category was connected, this contains a single
element.
5
Lemma 2.4. The Yoneda embedding y : (↓)
op → Set↓
is equal to the composite
(↓)
op σ
op
−−→ ↓ !0−→ Set
!(−)
−−→ Set↓
.
As a corollary, any π :↓→ Set is naturally isomorphic to the composite
↓
σ−→ (↓)
op !0
op
−−→ Set op
!(−)
op
−−−−→ (Set↓
)
op Set↓
(−,π)
−−−−−−→ Set
by the Yoneda lemma.
Proof. One checks directly.
Now we will define the extent of a bundle π to be the Dirichlet functor extπ : Set op →
Set that it corresponds to. This sends a bundle π : E → B to the functor
extπ(X) :=
X
b∈B
E
X
b
.
We will, however, give a more abstract definition of the extent, and then calculate a
number of presentations of it.
Definition 2.5. Consider the functor !0
op ◦σ : ↓ → Set op picking out the unique morphism 1 → 0 in Set op. Sending a functor Set op → Set to its precomposition with !0
op ◦σ
gives an evaluation functor
Fun(Set op
, Set)
ev!0 −−→ Set↓
.
This functor admits a right adjoint by right Kan extension along !0
op ◦σ : ↓ → Set op; we
define the Dirichlet extent functor ext : Set↓ → Fun(Set op
, Set) to be this right adjoint. It
sends any bundle π to
extπ :≡ ran!0
op ◦σπ.
Proposition 2.6. Let π : E → B be a bundle. The following are equivalent:
1. The extent extπ : Set op → Set of π from Definition 2.5.
2. The functor
X 7→
X
b∈B
E
X
b
,
or equivalently the composite
Set op (∆B)
op
−−−−−→ (Set/B)
op
Set/B(−,π)
−−−−−−−→ Set/B
ΣB −−→ Set.
3. The pullback in Fun(Set op
, Set):
extπ B
Set(−, E) Set(−, B)
p
6
4. The restricted representable functor Set↓
(!(−)
, π).
5. The functor
X 7→ lim(HomSet(!0, X)
op → ↓ op σ
op
−−→ ↓ π−→ Set)
where HomSet(!0, X) is the comma category of !0 : ↓ → Set over X : ∗ → Set.
6. The functor
X 7→ lim((X
⊳
)
op (!⊳
)
op
−−−−→ (1⊳
)
op σ
op
−−→ ↓ π−→ Set)
where (−)
⊳
is the left cone 2-functor, adjoining an initial object.
Proof. We have presented these results in order of most understandable to most computational; we will prove it a somewhat opposite order.
First, we note that the conical limit formula for extπ ≡ ran!0 π as a right Kan extension
says
extπ(X) = lim(HomSet op(X, !0
op ◦σ) → ↓ π−→ Set).
Now, HomSet op(X, !0
op ◦σ) ≃ HomSet(!0 ◦ σ
op, X)
op over ↓. Furthermore, we have the
following equivalence:
HomSet(!0 ◦ σ
op, X)
op ↓
HomSet(!0, X)
op ↓
op
∼ σ
and therefore we may equivalently calculate this limit as
lim(HomSet(!0, X)
op → ↓ op σ
op
−−→ ↓ π−→ Set).
This gives us the equivalence between (1) and (5).
The comma category HomSet(!0, X) simply adjoins an initial object to (the discrete
category) X. Therefore, we find that (5) and (6) are equivalent.
Every set X is the colimit of the diagram X⊳
1
⊳
−→ ↓ !0−→ Set, namely:
X
1 1 · · · 1 1
0
Since, by Lemma 2.3, !(−) preserves connected colimits, we may make the following identification of (4) with (6) using Lemma 2.4:
Set↓
(!X , π) = Set↓
(!
colim(X⊳
!⊳
−→1
⊳
!0−→Set)
, π)
≃ Set↓
(colim(X
⊳ !
⊳
−→ 1
⊳ !0−→ Set
!(−)
−−→ Set↓
), π)
≃ lim((X⊳
)
op (!⊳)
op
−−−−→ (1⊳
)
op !0
op
−−→ Set op
!(−)
op
−−−−→ (Set↓
)
op Set↓
(−,π)
−−−−−−→ Set)
≃ lim((X⊳
)
op (!⊳)
op
−−−−→ (1⊳
)
op σ
op
−−→ ↓ π−→ Set).
7
We see that (3) is equivalent to (4) by noting that the following square of natural
transformations is a pullback:
Set↓
(−, −) Set(cod(−), cod(−))
Set(dom(−), dom(−)) Set(dom(−), cod(−))
p
and restricting the right side to π and the left side to !(−)
.
Finally, we note that the set Set↓
(!X , π) is naturally isomorphic to the set P
b∈B EX
b
,
letting us identify (4) with (2).
Now we are ready to intrinsically characterize the Dirichlet functors.
Definition 2.7. A Dirichlet functor is a contravariant functor D : Set op → Set which
preserves connected limits. We denote by Dir the category of Dirichlet functors and natural
transformations.
Theorem 2.8. The functor ext is fully faithful, and gives an equivalence
Set↓ ≃ Dir .
As a corollary, the category of Dirichlet functors is a topos.
Proof. Since !0 : ↓ → Set op is fully faithful, the counit of the ev!0 ⊢ ext adjunction,
the universal cell defining the right Kan extension ext−, is an isomorphism. Thus ext is
fully faithful. In what follows we show that a functor is Dirichlet—preserves connected
limits—if and only if it is the extent of a bundle, proving the equivalence of Dir with Set↓
.
If D is the extent of a bundle π, then by Prop 2.6, D is naturally isomorphic to the
restricted representable Set↓
(!(−)
, π). By Lemma 2.3, this sends connected colimits in Set
to connected limits.
Now, we show that if D is Dirichlet, then the unit D → extD!0
is an isomorphism. By
Prop 2.6,
extD!0
(X) = lim(X
⊳ !
⊳
−→ 1
⊳ D!0 −−→ Set).
Every set X is the connected colimit of the diagram X⊳ → ↓ !0−→ Set, and therefore if D
preserves this limit, then D(X) is precisely the above limit extD!0
(X).
Remark 2.9. Since polynomial functors preserve connected limits, the composite P ◦ D
of a polynomial functor after a Dirichlet functor is Dirichlet. On the other hand, the
composite D′ ◦D op of two Dirichlet functors does not in general preserve connected limits,
since D op sends connected colimits in Set to connected colimits in Set op, and D′ does not
necessarily preserve these. Furthermore, composites of Dirichlet functors are not in general
accessible.
8
Remark 2.10. The six adjoints of Proposition 2.2 correspond, under the equivalence of
Theorem 2.8, to
ZC(D!0) ∼= the coefficient of 0X in D.
ext(!C
) ∼= X 7→ C × 0
X
cod(D!0) ∼= D(0)
ext(const(C)) ∼= X 7→ C
dom(D!0) ∼= D(1)
ext(!C) ∼= X 7→ C
X
In particular, !(−) corresponds to the Yoneda embedding.
3 Cartesian Transformations between Dirichlet Functors
In this section, we turn to cartesian transformations between Dirichlet functors. We will
show that the category of Dirichlet functors and cartesian transfromations is equivalent
to the category of polynomial functors and cartesian transformations.
Proposition 3.1. A natural transformation φ : D → D′ between Dirichlet functors is
cartesian if and only if the induced bundle map D!0 → D′
!0 is a pullback.
As a corollary, the equivalence Dir ≃ Set↓
restricts to an equivalence
Dirp ≃ Set↓
p
between Dirichlet functors with cartesian natural transformations and bundles with pullback squares.
Proof. We want to show that for any f : D → D′
, the square
D(1) D′
(1)
D(0) D′
(0)
f1
π π
′
f0
(3)
is a pullback in Set iff for all functions g : X → X′
, the naturality square
D(X′
) D′
(X′
)
D(X) D′
(X)
fX′
D(g) D′
(g)
fX
(4)
is a pullback in Set. We will freely use the natural isomorphism D(X) ∼= Set↓
(!X , D!0)
from Proposition 2.6, which allows us to identify Diagram (4) with
Set↓
(!X′ , D!0) Set↓
(!X′, D′
!0)
Set↓
(!X , D!0) Set↓
(!X , D′
!0)
f!0,1
!
1
g
!
1
g
f!0,1
(5)
9
The square in Diagram (3) is a special case of that in Diagram (4), namely for g :=!0;
this establishes the only-if direction.
To complete the proof, suppose that Diagram (3) is a pullback, take an arbitrary
g : X → X′
, and suppose given a commutative solid-arrow diagram as shown:
X X′
D(1) D′
(1)
1 1
D(0) D′
(0)
g
We can interpret the statement that Diagram (5) is a pullback as saying that there are
unique dotted arrows making the diagram commute. So, we need to show that if the front
face is a pullback, then there are unique diagonal dotted arrows as shown, making the
diagram commute. This follows quickly from the universal property of the pullback.
Theorem 3.2. There is an equivalence of categories
Polyp ≃ Dirp
between the category of polynomial functors and cartesian transformations and Dirichlet
functors and cartesian transformations. This equivlalence sends representables to representables
(−)
N 7→ N
(−)
.
Proof. This follows by composing the equivalence of Dirp with Set↓
from Proposition 3.1
with that of [4, Proposition 3.14], noting that Set↓
p
is the category of (1, 1)-polynomials.
Corollary 3.3. Let D be a Dirichlet functor. Then the category Dirp/D of Dirichlet
functors with a cartesian map to D is a topos.
Proof. By Theorem 3.2, this category is equivalent to Polyp/P for a polynomial P. But
this is a topos as observed in [5, Remark 2.6.2].
Now, since Dir is a topos, so is Dir/D. And, as we saw above, Dirp/D is a topos as well.
What is the relationship between Dir/D and Dirp/D?
We will show that Dir/pD is a subtopos of Dir/D with the left exact left adjoint to the
inclusion given by the vertical / cartesian factorization system on Set↓
.
Theorem 3.4. For any π : ↓ → Set, we have a subtopos inclusion
Set↓
/pπ
֒→ Set↓
/π
with left exact left adjoint given by the vertical / cartesian factorization system:
E′ E
B′ B
π 7→
E′ • E
B′ B′ B
p
π
10
As a corollary, Dirp/D ֒→ DirD is a subtopos inclusion.
Proof. We have displayed both the action of the left adjoint and its unit — the universal
map into the pullback. The counit is always an isomorphism since pullbacks are unique
up to unique isomorphism.
That this is lex follows quickly from the fact that taking pullbacks commutes with
taking (finite) limits.


























Neural Networks: The Backpropagation Algorithm 
Annette Lopez Davila 
Math 400, College of William and Mary 
Professor Chi-Kwong Li 
Abstract 
This paper illustrates how basic theories of linear algebra and calculus can be combined with 
computer programming methods to create neural networks.  
Keywords: Gradient Descent, Backpropagation, Chain Rule, Automatic Differentiation, 
Activation and Loss Functions 
1 Introduction 
As computers advanced in the 1950s, researchers attempted to simulate biologically inspired 
models that could recognize binary patterns. This led to the birth of machine learning, an 
application of computer science and mathematics in which systems have the ability to “learn” by 
improving their performance. Neural networks are algorithms that can learn patterns and find 
connections in data for classification, clustering, and prediction problems. Data including 
images, sounds, text, and time series are translated numerically into tensors, thus allowing the 
system to perform mathematical analysis. 
In this paper, we will be exploring fundamental mathematical concepts behind neural networks 
including reverse mode automatic differentiation, the gradient descent algorithm, and 
optimization functions. 
2 Neural Network Architecture 
`       
In order to understand Neural Networks, we must first 
examine the smallest unit in a system: the neuron. A 
neuron is a unit which holds a number; it is a mathematical 
function that collects information. These neurons are 
connected to each other in layers and are assigned an 
activation value; the higher the activation value, the greater 
the activation. Each activation number is multiplied with a 
corresponding weight which describes connection strength 
from node to node. A neural network has an architecture of 
input nodes, output nodes, and hidden layers. For each 
node in a proceeding layer, the weighted sum is computed: 
�
�𝑖 = 𝑤1𝑎1 +𝑤2𝑎2 +⋯𝑤𝑛𝑎𝑛 
�
�ℎ𝑒𝑟𝑒 𝑖 = [1, # 𝑜𝑓 𝑛𝑒𝑢𝑟𝑜𝑛𝑠 𝑖𝑛 ℎ𝑖𝑑𝑑𝑒𝑛 𝑙𝑎𝑦𝑒𝑟] and  n=# of  activation  numbers 
The weighted inputs are added with a bias term in order for the output to be meaningfully active.  
1 
�
�𝑖 = 𝑤1𝑎1 +𝑤2𝑎2 +⋯𝑤𝑛𝑎𝑛 +𝑏 
A neural network’s hidden layers have multiple 
nodes. For the first node in the hidden layer, we 
multiplied the corresponding weights and biases 
against the activation number. This must be 
repeated throughout the nodes in the hidden 
layer. The above equation can be consolidated 
into vectors in order to exemplify this:  
Each row in matrix 𝑤 ⃗  represents the weights corresponding with each hidden layer, while the 
columns represent the weights corresponding to a particular activation number.  
3 The Activation Function 
The function 𝑧𝑖 is linear in nature; thus, a nonlinear activation function is applied for more 
complex performance. Activation functions commonly used include sigmoid functions, 
piecewise functions, gaussian functions, tangent functions, threshold functions, or ReLu 
functions. 
Function Name 
Function 
Sigmoid/Logistic 
1
 1+𝑒−𝛽𝑥
 𝑓(𝑥) =
 Piecewise Linear 
�
�(𝑥) = {
 0 𝑖𝑓 𝑥 ≤ 𝑥𝑚𝑖𝑛
 𝑚𝑥+𝑏 𝑖𝑓 𝑥max >𝑥 >𝑥𝑚𝑖𝑛
 1 𝑖𝑓 𝑥 ≥𝑥max
 Gaussian 
�
�(𝑥) = 1
 √2𝜋𝜎
 Threshold/Unit Step 
−(𝑥−𝜇)2
 2𝜎2
 𝑒
 𝑓(𝑥) = { 0 𝑖𝑓 0 > 𝑥
 1 𝑖𝑓 𝑥 ≥0 
ReLu 
�
�(𝑥) = 𝑚𝑎𝑥(0,𝑥) 
Tanh 
�
�(𝑥) = tanh (𝑥) 
2 
Activation function choice depends on the 
range needed for the data, error, and speed. 
Without an activation function, the neural 
network behaves like a linear regression 
model. The need for an activation function 
comes from the definition of linear 
functions and transformations. Previously 
we discussed the linear algebra from the 
input step to the hidden layer. The solution 
of the function would resolve as a matrix of 
weighted sums. In order to calculate an 
output, the weighted sums matrix becomes 
the “new” activation layer. These activation 
numbers have their own sets of weights and biases. When we substitute the activation matrix for 
the weighted sums matrix, we see that a composition of two linear functions is a linear function 
itself. Hence, an activation function is needed. 
Proof: Composition of Linear Functions 
�
� 1 = 𝑤 ⃗ 1𝑎 +𝑏1 
�
� 2 = 𝑤 ⃗ 2𝑧 1 +𝑏2 
�
� 2 = 𝑤 ⃗ 2(𝑤 ⃗ 1𝑎 + 𝑏1) +𝑏2 
�
� 2 = [𝑤 ⃗ 2𝑤 ⃗ 1]𝑎 + [𝑤 ⃗ 2𝑏 ⃗ 1 + 𝑏 ⃗ 2] 
�
�𝑓 𝑊 =[𝑤 ⃗ 2𝑤 ⃗ 1] 𝑎𝑛𝑑 𝐵 = [𝑤 ⃗ 2𝑏 ⃗ 1 +𝑏 ⃗ 2],𝑡ℎ𝑒𝑛 𝑧 2 = 𝑊𝑎 +𝐵,𝑤ℎ𝑖𝑐ℎ 𝑖𝑠 𝑎𝑙𝑠𝑜 𝑎 𝑙𝑖𝑛𝑒𝑎𝑟 𝑓𝑢𝑛𝑐𝑡𝑖𝑜𝑛 
With the activation function, the new weighted sum becomes: 
ℎ𝑖 = 𝜎(𝑧𝑖)= 𝜎(𝑤1𝑎1 + 𝑤2𝑎2 + ⋯𝑤𝑛𝑎𝑛 +𝑏) 
h1
 ̅̅̅ = 𝜎(𝑤 ⃗ 𝑎 +𝑏 ⃗ ) 
4 The Cost/Loss Function 
A neural network may have thousands of parameters. Some combinations of weights and biases 
will produce better output for the model. For example, in a binary classification problem, the 
algorithm will classify some input as one of two things. The output node with the highest 
activation number will determine how the input is classified. In a binary classification problem, 
there are two labels. For example, an image can be determined to be a cat or dog; the feature 
“cat” is given the label of 0 and “dog” is given label 1. Different weights and biases will produce 
different output. How can we determine which combination of parameters will be most accurate? 
In order to measure error, a loss function is necessary. The loss function tells the machine how 
far away the combination of weights and biases is from the optimal solution. There are many loss 
3 
functions that can be used in neural networks; Mean Squared Error and Cross Entropy Loss are 
two of the most common.  
MSE Cost= 𝛴0.5(𝑦 − 𝑦̂)2 
Cross Entropy Cost= 𝛴( 𝑦̂ 𝑙𝑜𝑔(𝑦) + (1 − 𝑦̂)𝑙𝑜𝑔(1 − 𝑦)) 
The loss function contains every weight and bias in the neural network. That can be a very big 
function! 
�
�(𝑤1,𝑤2,…, 𝑤ℎ, 𝑏1,…,𝑏𝑖) 
5 The Backpropagation Algorithm 
The objective of machine learning involves the optimization of the chosen loss function. With 
every epoch, the machine “learns” by adapting the weights and biases to minimize the loss. 
Optimization theory centers itself on calculus. For neural networks in particular, reverse-mode 
automatic differentiation serves a core role.  
In order to minimize the cost function, one must determine which weights and biases to adjust. 
Computing the gradient with respect to the parameters can help us do just that, as by definition 
the gradient is a vector of partial derivatives of 𝐶(𝑤1, 𝑤2, …, 𝑤ℎ, 𝑏1,…,𝑏𝑖). As we recall, 
derivatives measure the change of a function’s output with respect to its input. The gradient of 
the cost function tells us in which direction 𝐶(𝑤1,𝑤2, … , 𝑤ℎ, 𝑏1,…,𝑏𝑖) decreases most quickly. 
This is often known as Gradient Descent. With each epoch, the machine converges towards the 
local minimum. Automatic differentiation combines the chain rule with massive computational 
power in order to derive the gradient from a potentially massive, complex model. In reverse, this 
algorithm is better known as Backpropagation. Backpropagation is recursively done through 
every single layer of the neural network.  
In order to understand the basic workings of backpropagation, let us look at the simplest example 
of a neural network: a network with only one node per layer.  
We have derived the equations for cost, weighted sum, and activated weighted sum: 
�
�𝐿 = 𝑤𝐿𝑎𝐿−1 +𝑏𝐿
 
�
�𝐿 =𝜎(𝑧𝐿) 
1
 𝐶 =(𝑎𝐿 −𝑦)2 ∗
 
1The cost function is simplified for proof of concept 
4 
5 
 
We can determine how sensitive the cost function is to changes in a single weight. Beginning 
from the output, we can apply the chain rule to every activation layer. For a weight between the 
hidden layer and output layer, our derivative is: 
�
�𝐶𝑘
 𝛿𝑤𝐿
 =𝛿𝑧𝐿
 𝛿𝑤𝐿
 𝛿𝑎𝐿
 𝛿𝑧𝐿
 𝛿𝐶𝑘
 𝛿𝑎𝐿
 
With the definition of the functions, we can easily solve for the partial derivatives: 
�
�𝐶𝑘
 𝛿𝑎 =2(𝑎𝐿−𝑦) 
�
�𝑎𝐿
 𝛿𝑧𝐿
 =𝜎′(𝑧𝐿) 
�
�𝑧
 𝛿𝑤𝐿
 =𝑎𝐿−1 
�
�𝐶𝑘
 𝛿𝑤𝐿
 =𝑎𝐿−1𝜎′(𝑧𝐿) 2(𝑎𝐿−𝑦) 
This method is iterated through every weight, activation number, and bias in the system. 
Previously, we calculated the derivative of one particular cost function with one variable. 
However, in order to account for every weight in that layer, the average of the derivatives is 
taken: 
�
�𝐶
 𝛿𝑤𝐿
 =1
 𝑛∑𝛿𝐶𝑘
 𝛿𝑤𝐿
 𝑛−1
 𝑘=0
 
Similarly, we can calculate the sensitivity of the cost function with respect to a single bias 
between the hidden layer and the output layer and the derivative accounting for every bias in a 
layer: 
�
�𝐶𝑘
 𝛿𝑏𝐿
 =𝛿𝑧𝐿
 𝛿𝑏𝐿
 𝛿𝑎𝐿
 𝛿𝑧𝐿
 𝛿𝐶
 𝛿𝑎𝐿
 = 𝜎′(𝑧𝐿) 2(𝑎𝐿−𝑦)                      𝛿𝐶
 𝛿𝑏𝐿
 =1
 𝑛
 ∑ 𝛿𝐶𝑘
 𝛿𝑏𝐿
 𝑛−1
 𝑘=0
 
What happens when we go beyond the output layer and the preceding hidden layer? The chain 
rule is applied once more, and the derivative changes in account to its partials. For example, the 
derivative below accounts for the partials of the cost function with respect to an input activation 
number.  
�
�𝐶𝑘
 𝛿𝑎𝐿−1
 = 𝛿𝑧𝐿
 𝛿𝑎𝐿−1
 𝛿𝑎𝐿
 𝛿𝑧𝐿
 𝛿𝐶
 𝛿𝑎𝐿
 = 𝑤𝐿𝜎′(𝑧𝐿) 2(𝑎𝐿−𝑦) 
Neural Networks tend to have several thousand inputs, outputs, and nodes; the above equations 
seem highly oversimplified. Although adding complexity changes the formulas slightly, the 
concepts remain the same, as seen below: 
�
�𝐿−1
 𝐶𝑚 = ∑(𝑎𝑗
 𝐿−𝑦𝑗)2
 𝑗=0
 𝑎𝑗 = 𝜎(𝑧𝑗
 𝐿) 
�
�𝑗
 𝐿 = ⋯+𝑤𝑗𝑘
 𝐿𝑎𝑘
 𝐿−1 + ⋯ 
�
�𝐶𝑚
 𝛿𝑤𝑗𝑘𝐿
 𝛿𝐶𝑚
 𝛿𝑎𝐿−1
 =∑ 𝛿𝑧𝑗𝐿
 𝑗=0
 = 𝛿𝑧𝑗𝐿
 𝛿𝑤𝑗𝑘𝐿
 𝑛𝐿−1
 𝛿𝑎𝑗𝐿
 𝛿𝑧𝑗𝐿
 𝛿𝐶𝑚
 𝛿𝑎𝑗𝐿
 𝛿𝑎𝑘𝐿−1
 𝛿𝑎𝑗𝐿
 𝛿𝑧𝑗𝐿
 𝛿𝐶𝑚
 𝛿𝑎𝑗𝐿
 By calculating every derivative of each weight and bias, the gradient vector can be found. 
Although one could try to compute the gradient of a neural network by hand, the vector will 
usually be in complex dimensions unfathomable for us to decipher. Thus, with computational 
help, our neural network can perform such intricate calculations, and repeat them hundreds, if 
not thousands of times until the minimum is reached.  
�
�𝐶
 𝛿𝑤1
 𝛿𝐶
 𝛿𝑏1
 ∇𝐶 =
 ⋮
 𝛿𝐶
 𝛿𝑤𝐿
 𝛿𝐶
 [
 
 
 
 
 
 
 
 
�
�𝑏𝐿
 ]
 
 
 
 
 
 
 
 
6 Applications and Further Research 
Automatic differentiation has many applications other than in machine learning such as in Data 
Assimilation, Design Optimization, Numerical Methods, and Sensitivity Analysis. It is efficient, 
stable, precise, and known to be a better choice than other types of computer-based 
differentiation. Backpropagation has been called into question recently, as it does not learn 
continuously. For example, our brains learn continuously; they do not forget information when 
we learn something new. Because of this, backpropagation may be sidelined in Machine 
Learning in the future.  
Applications of Neural Networks trained with Backpropagation vary greatly. Such applications 
include sonar target recognition, text recognition, network controlled steering of cars, face 
recognition software, remote sensing, and robotics.













---

Superrecursive Features of Interactive 
Computation  
Mark Burgin 
University of California, Los Angeles 
Los Angeles, CA, 90095, USA 
Abstract 
Functioning and interaction of distributed devices and concurrent algorithms are 
analyzed in the context of the theory of algorithms. Our main concern here is how and 
under what conditions algorithmic interactive devices can be more powerful than the 
recursive models of computation, such as Turing machines. Realization of such a 
higher computing power makes these systems superrecursive. We find here five sources 
for superrecursiveness in interaction. In addition, we prove that when all of these 
sources are excluded, the algorithmic interactive system in question is able to perform 
only recursive computations. These results provide computer scientists with necessary 
and sufficient conditions for achieving superrecursiveness by algorithmic interactive 
devices. 
Keywords: distributed computation, concurrent process, interaction, grid automaton, 
super-recursive algorithm  
1 Introduction 
There is a tendency to oppose algorithms and interaction (cf., for example, [17]). 
This opposition is based on a very restricted understanding of algorithms, which is 
based on the Church-Turing Thesis that equates algorithms with Turing machines or 
other mathematical schemas that give rules for computation of a function. Some 
researchers claim that interactive computation is more powerful than Turing machines 
(cf., for example, [6, 7, 14, 15, 17]), while others insist that the Church-Turing Thesis 
still holds (cf., for example, [9]).  However, contemporary understanding extends the 
concept of algorithm, making it closer to the general usage of the word "algorithm". 
Namely, algorithm is informally perceived as a (finite) structure (e.g., a system of 
rules) that contains for some performer (class of performers) exact information (e.g., 
instructions) that allows some performer(s) to pursue a definite goal (cf., for example, 
[3, 4]). 
People need information that is contained in algorithms to make their activity 
efficient and purposeful. Consequently, one main achievement of 20th century scientific 
thought was elaboration of the theory of algorithms and computation. This theory 
studies abstract and real automata, computers and networks, computation and 
communication. In many ways, this theory is the central cornerstone for computer 
science. Many key accomplishments in the theory of algorithms and computation 
converge to the famous Church-Turing Thesis, a statement determining the boundaries 
of algorithmic computations. The Church-Turing Thesis has long been considered as 
the most fundamental law within computing. However, recent developments in the 
theory of algorithms allow overcoming limitations in the Church-Turing Thesis. New 
mathematical models for algorithms and computation have appeared that extend prior 
theory in a manner similar to the way relativity theory and quantum mechanics went 
beyond Newtonian mechanics. These new models are more powerful than the classical 
recursive algorithm models, i.e., Turing machines, partial recursive functions, Lambda
calculus, and cellular automata.  
Algorithms and automata that are more powerful than Turing machines are called 
super-recursive.  
Computations that cannot be realized or simulated by Turing machines are called 
hyper-computations. 
At the first glance, it looks like interactive systems are essentially different from 
computers and cannot be represented by computing models, such as Turing machines. 
Really, as Leeuwen and Wiedermann write [13], the purpose of an interactive system is 
usually not to compute some final result but to react to or interact with the environment 
in which the system is placed and to maintain a well-defined action-reaction behavior. 
Interactive systems are always operating and thus, may be seen as machines on infinite 
strings, but differ in the sense that their inputs are not specified and may depend on 
intermediate outputs and external sources. 
However, if we consider only systems that work with symbolic information, then 
reaction to or interaction with the environment or with another such system is 
information transformation and exchange, or communication. In other words, 
functioning of and interactive system that works with symbolic information consists of 
computation and communication processes. 
In this paper, we analyze sources of an interactive recursive algorithm/machine 
ability to outperform (have more computing power than) conventional Turing 
machines, that is, to be able to compute recursively non-computable functions. We find 
five 
such sources of interactive superrecursiveness. Namely, interactive 
superrecursiveness is possible when: 1) the interactive algorithm is itself super
recursive; 2) (Proposition 1) the interactive algorithm is recursive but contains initial 
information about some recursively non-computable function (has a non-recursive 
oracle); 3) (Proposition 2) the interactive recursive algorithm interacts with a super
recursive algorithm (a non-recursive environment); 4) (Theorems 2, 3 and 4) time of 
interaction is not recursively coordinated; 5) (Theorems 5) communication space is not 
recursively coordinated. The first three cases are not interesting because either 
superrecursive power comes not from interaction (case 1) or as it is well known, if a 
recursive device have access to a super-recursive information, then this device can 
compute recursively non-computable functions.  
However, after finding sources of interactive superrecursiveness, it is natural to ask 
a question if all sources have been found or there are other sources that we have not 
been able to see. To show that our analysis of interactive superrecursiveness is 
complete, we prove (Theorems 6) that if interacting algorithms/devices are recursive 
and their interaction is organized/controlled by a recursive device/algorithms, then 
computable functions are also recursive. 
Thus, we consider algorithms in the form of rules and devices that perform simple 
and constructive operations at each step and give a result after a finite number of steps 
(in finite time). 
All algorithms are divided into three big classes [3]: subrecursive, recursive, and 
super-recursive. Algorithms and automata that have the same computing/accepting 
power [4] as Turing machines are called recursive. Examples are partial recursive 
functions or random access machines. 
Algorithms and automata that are weaker than Turing machines, i.e., that can 
compute fewer functions, are called subrecursive. Examples are finite automata, 
context free grammars or push-down automata. 
Algorithms and automata that are more powerful than Turing machines are called 
super-recursive. Examples are inductive Turing machines, Turing machines with 
oracles or finite-dimensional machines over the field of real numbers. 
It is evident that if an interacting algorithm is super-recursive, then even without any 
interaction, it can perform hypercomputation. Thus, the main question is when taking a 
recursive algorithm (automaton) and providing for it means for interaction, we can 
achieve hypercomputation as a result of interaction. There are different models of 
interactive computational systems: persistent Turing machines [7], a global Turing 
machine or Internet machine [13, 14], Web machines [1], Web automata [11] and 
others. The most general, flexible and powerful model of interactive (computational) 
systems is a grid automaton [2, 3]. 
In the analysis of the computational power of interaction, it is possible to consider 
only two systems as interaction of any finite number of systems can be reduced by 
induction to the case of two systems. Here we do not consider infinite systems. 
By the definition of a recursive algorithm (device or machine), a Turing machine 
can compute any function that a recursive algorithm can compute. That is why we can 
use Turing machines to explore problems of computational power of interaction. In 
addition, it is possible to consider Turing machines with one working tape as Turing 
machines with n tapes can be simulated by a Turing machine with one tape [3]. 
In interaction, machines can change data processed by one of the machines, its 
software (program of computation) and/or hardware. As Turing machines are utilized 
as the model of recursive algorithms, hardware is not changed. At the same time, the 
schema of a universal Turing machine allows one to keep software (rules of 
computation) in memory in the form of processed data. Consequently, we can assume 
that only data processed by machines are changed in interaction. That is, interaction 
goes through memory of machines by changing symbols in memory cells. 
2 Turing machines with infinite output and interaction 
Considering interactive systems and processes, it is necessary to treat concurrent 
systems and processes. At the same time, as Palamidessi and Valencia, write [8], 
infinite behavior is ubiquitous in concurrent systems (e.g., browsers, search engines, 
reservation systems). Thus, it becomes crucial to study and compare systems with 
tentatively infinite input and output. 
Here we can ask a question how it is possible to compare Turing machines that 
work and were designed to work with finite words and system that process potentially 
infinite words. If we consider this question in a primitive way, can come to two opposite 
but mutually simple conclusions. The first one says that if Turing machines cannot work 
with infinite words and interactive systems can, then interactive systems are evidently 
more powerful than Turing machines. The second conclusion is that Turing machines 
and interactive systems are incomparable. 
However, the second conclusion brings us to a paradoxical result. Indeed, in the 
theory of computation and automata, it is proved that finite automata (finite state 
machines) are weaker than Turing machines. Nevertheless, there are finite automata (for 
instance, Büchi and Muller automata [2, 11]) that work with infinite words.  
There is a simple solution to this paradox. Namely, to show that actually Turing 
machines can work with infinite words. Turing machines can do this in the same way as 
finite automata do. Specifically, if an infinite sequence of symbols is given to a Turing 
machine, the machine can transform it into an infinite output sequence of symbols, 
separating the input into finite parts and working with one part at a time. However, this 
transformation of infinite strings of symbols always has a specific property.  
Let us assume that all words in the alphabet of considered Turing machines are 
ordered and all words are given to the considered Turing machine one by one in this 
order. Then we have the following result. 
Theorem 1. The output of a Turing machine with infinite output (TMIO) is a 
recursively enumerable set of finite words and any recursively enumerable set of finite 
words is an output of a Turing machine with infinite output. 
This result remains true even if the input words go in an arbitrary but recursively 
enumerable order.  
Corollary 1. If the input of a Turing machine is a recursively enumerable set of 
finite words, then the corresponding output is also a recursively enumerable set of finite 
words. 
Thus, recursive enumerability is the essence of the Turing machine output even 
when this output is infinite and input is recursive. More exactly, we have the following 
result. 
Corollary 2. a) If the infinite input string is recursively partitioned (divided) into a 
set of finite words, then the corresponding output of any Turing machine with this input 
is also a recursively enumerable set of finite words. 
b) If the infinite input string is partitioned (divided) into a set of finite words that is 
not recursively enumerable, then there is a Turing machine such that working with this 
input, it will as output a set of finite words that is not be a recursively enumerable. 
For convenience, we give here a general structure (schema) of a Turing machine T 
with infinite input/output and interaction: 
T = (A, R, Q, P, F, q0 , LI , Lw , LiO , i =  1, 2, 3, … ) 
Here 
A is the alphabet of T ; 
R is the system of rules of T ; 
Q is the set of states of T ; 
P is the set of output states of T ; 
F is the set of final states of T ; 
q0 is the start of state of T ; 
LI is the input tape of T ; 
Lw is the working tape of T ; 
{ LiO , i =  1, 2, 3, … } is the system of output tapes of T . 
It is necessary to remark that a machine can perform infinite computations but have 
finite input and finite output. 
3 Types of interactive computing systems and processes 
Treating here interactive processes in systems of computational devices (automata 
or algorithms), we consider their activity as information processing in a general case 
and as computation when we are interested in their computing power. 
Time is important parameter of interactive systems in general and computing 
systems, which usually consist of various interacting devices, in particular. The most 
popular model is the linear physical time. However, now some physical theories are 
based on a two-dimensional model of time [22, 25], is used in the theory of databases 
[27], and branching time plays an important role in computational models [23, 24]. 
We consider here situations when interactive systems (processes) interact only in 
form of information exchange, that is, they communicate. In addition, we assume that 
interaction goes in a communication space, which is a special media designed for 
communication [8]. In our theoretical model, we use such a communication space as a 
linear tape of cells, in which one cell can contain one symbol. 
Several interactive processes can go on even in one computing device (information 
processing system with one processor) when different programs realize these processes. 
Moreover, even one sufficiently complex program can organize many processes. 
Operating system of a computer is an example of such a program.  
It is possible to divide all interactive computations into three types [5]: free 
interactive computations, partially free interactive computations, and algorithmic or 
procedural interactive computations. 
In turn, there are two types of procedural (algorithmic) concurrent computations: 
implicitly procedural (algorithmic) concurrent computations and explicitly procedural 
(algorithmic) concurrent computations. 
Definition 1. An interactive computation is called free if interactions between 
processes go without any rules. 
For instance, it is demonstrated in [2] that a system of two finite automata 
interacting without any rules can eventually compute any function. However, when 
interaction of processes is not specified, at least, by some rules, the enveloping 
(computational) process can lead to deadlocks, data corruption when different 
processes change common data without concordance, and other safety violations.  
An opposite situation is when all interactions are controlled by definite rules. These 
rules can be local and global. 
Definition 2. An interactive computation (functioning of a grid array/automaton) is 
called implicitly procedural (algorithmic) if all interactions between processes go 
according to local rules (where each set of local rules form an algorithm of local 
interactions). 
For instance, each process (algorithm or device) in a system has its own interaction 
rules. However, for some processes these rules can coincide.  
A more rigid type is explicitly procedural algorithmic computation. 
Definition 3. An interactive computation (functioning of a grid array/automaton) is 
called explicitly procedural (algorithmic) if all interactions between processes go 
according to some system of rules (algorithm). 
Algorithmic control of interacting processes is naturally considered as an operation 
with these processes [5]. 
Definition 4. Explicitly algorithmic functioning of a grid array/automaton is called 
algorithmic operation (AO). 
An intermediate situation between free and algorithmic computations is partially 
free functioning. 
Definition 5. An interactive computation (functioning of a grid array/automaton) is 
called partially free if not all interactions between processes are specified by rules. 
4 Sources of interactive superrecursiveness 
In this section, we describe five main sources of interactive superrecursiveness. For 
completeness, we present here results describing the first three evident sources. The first 
one is when the algorithms itself is super-recursive. The second is also simple and is 
described in the following proposition. 
Proposition 1. An interactive recursive algorithm (a Turing machine) A can 
compute a recursively non-enumerable set if it can contain a recursively non
enumerable set as its initial information. 
Indeed, taking a recursively non-enumerable set that constitutes initial information, 
the machine A gives this set as its output. To do this, we even do not need such a 
powerful model as a Turing machine. In this case, superrecursive computation can be 
realized by a finite automaton A that is provided from the start with a recursively non
enumerable set as its input. 
Another trivial cause for interactive superrecursiveness is interaction with a system 
that can send to algorithm (automaton) A recursive incomputable information. An 
example of such a system is a non-recursive oracle, with which a Turing machine can 
interact [10]. However, we are interested in interaction of algorithms (automata). 
Consequently, we consider the second system as a super-recursive algorithm. 
Proposition 2. An interactive recursive algorithm (a Turing machine) A can 
compute a recursively non-enumerable set if it interacts with a super-recursive 
algorithm B. 
Indeed, the algorithm B can compute a recursively non-enumerable set and give it 
to the machine A. Then the machine A acts as in the previous case. Namely, receiving a 
recursively non-enumerable input, the machine A gives it as its output. To do this, we 
even do not need such a powerful model as a Turing machine. In this case, 
superrecursive mode of functioning can be also realized by a finite automaton A. 
One more source of interactive superrecursiveness is time. This is possible when 
interaction is not synchronized either because automata (algorithms) send and receive 
information at random (or at least, at non-recursively coordinated) moments of time or 
due to incommensurability of the time scales in which automata work. 
Theorem 2. An interactive recursive algorithm (a Turing machine) A can compute 
a recursively non-enumerable set if the temporal sequence of information exchanges is 
recursively non-enumerable. 
This result is proved in [2] for the case when interaction of system automata with 
the communication space is random and thus, non-enumerable [3]. 
It is necessary to remark that machines with random interaction do not compute a 
function on infinite words (strings). The result of their computation is a multivalued 
function. However, it possible to have a deterministic computation on infinite words 
and still to achieve super-recursive results. To do this, it is necessary to have a source 
that controls interaction of system automata with the communication space in a super
recursive way, e.g., the sequence of moments when machines have access to this space 
is non-enumerable. 
Theorem 3. An interactive recursive algorithm (a Turing machine) A can compute 
a recursively non-enumerable set when the time scales in which automata work are not 
commensurable. 
What does it mean incommensurability of the time scales of two automata A and B? 
In a general case, it means that while the automaton A makes some number of moves 
(say, n), the automaton B can make any number of moves (say, m).  
Now let us consider two automata A and B. The automaton A writes the symbol 0 
into the common output tape every other move of its functioning, while each odd move 
the automaton B includes writing the symbol 1 into the common output tape. Symbols 
are written from left to right, and each time the first free cell to the right is filled. When 
these automata work synchronously, their output will have the form 10101010 … 
However, when the time scales in which automata work are not commensurable, it is 
possible that while the automaton B makes one move, the automaton A makes ten 
moves. After this, both automata work synchronously. In this case, their output will 
have the form 0000010101010 … If such a situation can happen not only at the 
beginning, but also at any stage of computation, the output of A and B can be not 
recursively enumerable. 
In this case, incommensurability is not uniform. That is, one time when the 
automaton A makes n moves, the automaton B makes m moves, but another time when 
the automaton A makes n moves, the automaton B makes k moves. Formally it means 
that intervals with the same length in the first time scale can be mapped to intervals with 
different lengths in the second time scale. 
Thus, it is interesting to find whether superrecursiveness is possible when 
incommensurability of the time scales is uniform. Formally it means that intervals with 
the same length in the first time scale are always mapped to intervals with equal lengths 
in the second time scale. When we consider physical time, the time scales are 
isomorphic to the real line. In this case, incommensurability of the scales means that the 
time unit of one scale is mapped to some irrational interval in the second scale. 
Mathematically such a mapping is a stretching or contraction with an irrational 
parameter. 
Theorem 4. An interactive recursive algorithm (a Turing machine) A can compute 
a recursively non-enumerable set when the time scales in which automata work are 
uniformly incommensurable. 
One more source of interactive superrecursiveness is space. This is possible when 
do not determine where they put their data in the communication space.  
Theorem 5. An interactive recursive algorithm (a Turing machine) A can compute 
a recursively non-enumerable set if the sequence of cells where data written by another 
system is recursively non-enumerable. 
To prove this we consider two Turing machines A and B that interact through a 
communication space. This space is a linear one-sided tape. While functioning, the 
machines A and B write symbols 1 or 0 in cells of the interaction tape. 
The machine B works in a very simple way. At the step n, it puts the symbol 1 into 
one of two cells with numbers 2n – 1 and 2n. The other cell is filled by A with the 
symbol 0. By the assumption of the theorem, it is possible that the sequence of zeros 
and ones is recursively non-enumerable. 
After the pair of cells with numbers 2n – 1 and 2n is filled, the machine A gives the 
output 0 if it was 01 in those two cells and the output 1 if it was 10 in those two cells. 
When the sequence of zeros and ones in the communication space is recursively non
enumerable, then the output sequence of the machine A is also recursively non
enumerable. 
5 Interaction with a recursive control 
There are two main kinds of algorithmic control of interaction: global and local. In 
global control, there is an algorithm (automaton) C that controls interaction of A and B. 
Namely, C determines when and where A and B read from and write into the 
communication tape. In addition, time scales LA and LB of both A and B are 
synchronized with time scale LC of C. That is, temporal units 1A and 1B (e.g., second, 
millisecond, etc.) of each scale LA and LB are equal to n1C and m1C for some whole 
numbers n1A and m1B where 1C is a temporal unit of the scale LC . 
In local control, each machine A and B determines when and where A and B read 
from and write into the communication tape. An additional machine C only solves 
conflicts when both A and B try to write different symbols into the same cell. In 
addition, all three time scales LC, LA and LB are synchronized.  
Theorem 4 shows that local rules are insufficient to restrict the computing power of 
the interacting recursive devices to recursive computations. Only a global algorithm 
(device) that organizes interaction can do this. 
Theorem 6. An interactive system (grid automaton) R that consists of a finite 
number of recursive devices (algorithms) interaction of which is organized by a 
recursive automaton (algorithm) can perform only recursive computations (may be, 
infinite), i.e., such a system R is equivalent to a Turing machine. 
Note that in this case time scales of all automata from R are synchronized with the 
time scale of the control automaton and thus, they are synchronized with one another. 
6 Conclusion 
Thus, we have found three trivial (the system itself, initial information or/and 
another system is super-recursive) and two (or actually, three because the temporal 
parameter provides two possibilities) non-trivial (time and space) causes for interactive 
superrecursiveness. In addition, we demonstrate that this list of causes for interactive 
superrecursiveness is complete. 
It would be interesting to study other situations when a system of interacting devices 
(automata or processes) can have higher computational power than the power of each 
constituent of this system. Here we did this for systems consisting of Turing machines 
as it is the most popular model of computation and as it caused the most active 
controversy. However, the same problem can be studied for subrecursive models, such 
as finite and pushdown automata, and super-recursive models, such as inductive and 
limit Turing machines [4]. 
There are also important problems with analyzing the concept of computation: 
What is computation? 
Is computation always algorithmic? 
Is computation always a physical process?















Y IS A LEAST FIXED POINT COMBINATOR
 JOSEPH HELFER
 arXiv:2504.19379v1  [math.LO]  27 Apr 2025
 Abstract. The theory of recursive functions is related in a well-known way to the notion of least
 f
 ixed points, by endowing a set of partial functions with an ordering in terms of their domain of
 definition. When terms in the pure λ-calculus are considered as partial functions on the set of
 reduced λ-terms, they inherit such a partial order. We prove that Curry’s well-known fixed point
 combinator Y produces least fixed points with respect to this partial order.
 1. Introduction
 Curry’s “paradoxical combinator” Y in the pure lambda calculus, defined as
 (1)
 Y=λf. λg.f(gg) λg.f(gg) ,
 is such that YF is a fixed point of F for any lambda term F, in the sense that F(YF) and YF
 are β-equivalent. Y is sometimes called the “least fixed point operator” because there are models
 of the pure lambda calculus in which the domain is equipped with a partial order, and in which it
 the interpretation of Y is a function taking each element of the domain to a least fixed point with
 respect to that partial order (see, e.g., [Bar84, Theorem 19.3.4]).
 However, the set of lambda terms itself can be equipped with the usual partial order (or rather,
 preorder) on partial functions by declaring that F ≤ G for lambda terms F and G if and only
 if, for all lambda terms M, if FM has a β-normal form, then GX has the same β-normal form
 (Definition 2). One can then ask whether YF is always minimal with respect to this order among
 f
 ixed points of F. Surprisingly, this questions does not seem to have appeared in the literature. In
 this note, we answer it in the affirmative.
 In §4, we briefly discuss the question of other fixed-point combinators.
 Acknowledgements: We thank Henk Barendregt for helpful discussions concerning this paper.
 2. Preliminaries
 Let V be an infinite set (of “variables”). We write Λ for the set of lambda terms with variables
 in V, up to α-equivalence. We will refer to the elements of Λ simply as “lambda terms” (or just
 “terms”) rather than “lambda terms up to α-equivalence”.
 Rather than recalling the explicit definition of Λ (for which see, e.g., [Bar84, Chapter 2]), we state
 certain properties which uniquely characterize it. More precisely, we will state certain properties
 of (a) Λ, together with its operations V → Λ (atomic terms), Λ × Λ → Λ (application), and
 V ×Λ → Λ (abstraction), (b) the set FV(M) ⊂ V of free variables of a lambda term M,
 and (c) the operation of substitution M[v . .= N] of lambda terms (M and N terms and v a
 variable). These properties determine Λ together with the mentioned operations on it uniquely up
 to isomorphism, and determine the sets FV(t) and the substitution operation uniquely:
 Date: April 29, 2025.
 1
2
 JOSEPH HELFER
 (a) Every term is of exactly one of the forms (i) v with v ∈ V, (ii) MN for M,N ∈ Λ, or (iii)
 λv.M with v ∈ V and M ∈Λ. Incase (i), v is uniquely determined, and in case (ii), M and
 N are uniquely determined. For case (iii), if λu.M = λv.N, then N = M[u . .= v]. Moreover
 Λ is the least subset of Λ closed under the operations (i)-(iii), i.e., we can use structural
 induction and recursion on Λ.
 (b) The set FV(M) ⊂ V of free variables of a term M is determined by (i) FV(v) = {v} for
 v ∈V, (ii) FV(MN) = FV(M)∪FV(N), and (iii) FV(λu.M) = FV(M)−{u}.
 (c) The substitution operation M[v . .= N] is determined by (i) v[v . .= M] = M and u[v . .=
 M] = u for u ∈ V −{v}, (ii) (M1M2)[v . .= N] = (M1[v . .= N])(M2[v . .= N]), and (iii)
 (λu.M1)[v . .= s] = λu.(M1[v . .= s]) for u ∈ V −(FV(s)∪{v}).
 Note that if u ∈ FV(s)∪{v}, we can always take some u′ ∈ V −(FV(s)∪{v}), and set
 M′
 1 = M1[u . .= u′], and we then have λu.M1 = λu′.M′
 1, so the clauses (i)-(iii) indeed suffice
 to determine the substitution operation.
 For a relation R ⊂ Λ×Λ, we write M ∼R N for (M,N) ∈ R. We say that R is compatible (see
 [Bar84, p. 3.1.1]) if given terms M,M′,N,N′ with M ∼R M′ and N ∼R N′, then MN ∼R M′N,
 MN ∼R MN′, and λv.N ∼R λv.N′ for v ∈ V. For a relation R, we write →R (one-step R
reduction)) for the least compatible relation containing R, we write ↠R (R-reduction) for the
 least preorder containing →R, and ≈R (R-equivalence) for the least equivalence relation contain
ing ↠R (or equivalently, containing →R). It follows that ↠R and ≈R are themselves compatible,
 and are hence the least compatible preorder and equivalence relation containing R, respectively.
 The relations β,η ⊂ Λ×Λ are defined as
 β ={ (λv.M)N,M[v . .= N] | v ∈ V; M,N ∈Λ} and η ={ (λv.Mv),M |v ∈V; M ∈Λ},
 and we define βη = β ∪η. We thus obtain the relations →β, ↠β, and ≈β of one-step β-reduction,
 β-reduction, and β-equivalence, and likewise →βη, ↠βη, and ≈βη.
 Lemma 1. If R is either of β or βη (or more generally if β ⊂ R) then
 M≈RM′⇒M[v . .=N]≈R M′[v . .=N]
 for all M,M′,N ∈ Λ and v ∈ V.
 Proof. Assuming M ≈R M′, we have M[v . .= N] ≈R (λv.M)N ≈R (λv.M′)N ≈R M′[v . .= N]. □
 For the remainder of this section, fix a relation R ⊂ Λ ×Λ.
 Aterm M isin R-reduced, if there is no term N with M →R N. An R-normal form of a term
 M is an R-reduced term N with M ≈R N. We say that R has the Church-Rosser property if
 for all M,N,N′ ∈ Λ, if M ↠R N and M ↠R N′, then there is L ∈ Λ with N ↠R L and N′ ↠R L.
 This implies that every term has at most one R-normal form, and that if N is the R-normal form
 of M, then M ↠R N. By the Church-Rosser theorem, β and βη both have the Church-Rosser
 property [Bar84, §11.1].
 The Y-combinator is the lambda term Y defined in (1).
 Definition 2. We define a preorder ≤R on terms by putting, for F,G ∈ Λ:
 F ≤R G ⇐⇒ (∀M ∈Λ.FM hasanR-normal form⇒GM has the same R-normal form).
 3. The proof
 Theorem. (Y yields R-minimal fixed points.) Let R be either β or βη. Then
 ∀F,M ∈Λ. FM ≈R M ⇒YF ≤RM.
Y IS A LEAST FIXED POINT COMBINATOR
 3
 Wefirst give an outline of the proof. Given a fixed point M of F, and a term N such that (YF)N
 has a normal form N′, we must show that MN has the same normal form. The idea is that the
 reduction (YF)N ↠R N′ should use “nothing about” YF except that it is a fixed point F. Thus,
 we introduce a new variable y which we consider as a “formal fixed point of y” by introducing a new
 reduction rule y → Fy, and show (i) that the reduction FN ↠R N′ gives rise to a parallel reduction
 yN ↠N′, and (ii) that the reduction yN ↠ N′ gives rise to a parallel reduction MN → N′. This
 second step is easy, by substituting M for y in the reduction yN ↠ N′.
 Step (i) is a little trickier, since in the reduction (YF)N ↠ N′, we can make reductions inside
 of YF, whereas we cannot make reductions “inside of y”. This is taken care of by allowing each
 instance of y appearing in the reduction yN ↠ N′ to “correspond” to an arbitrary reduction of
 YF, and keeping track of which instances of y correspond to which such reductions.
 We now proceed with the proof. Henceforth, let R be one of β or βη.
 Definition 3. For any y ∈ V and F ∈ Λ, we define the relation SF,y ⊂ Λ×Λ by SF,y = {(y,Fy)},
 and we set RF,y = R ∪ SF,y. We thus have the relations →SF,y
 , ↠SF,y
 , and ≈SF,y 
of one-step
 Sf,z-reduction, SF,z-reduction, and SF,z-equivalence.
 Lemma 4. Let F ∈Λ and y ∈V −FV(F). Then
 FM ≈RM∧N≈RF,y 
N′ ⇒N[y . .=M]≈R N′[y . .=M]
 for any M,N,N′ ∈ Λ.
 Proof. Since ∼RF,y 
is the least compatible equivalence relation containing RF,y, and since {(N,N′) |
 N[y . .= M] ≈R N′[y . .= M]} is itself a compatible equivalence relation, it suffices to prove the con
clusion in the case where N ∼RF,y 
N′.
 If N ∼R N′ (or more generally if N ≈R N′), then the conclusion follows immediately from
 Lemma 1. If N ∼SF,y 
N′, then N = y and N′ = Fy, hence N[y . .= M] = M ≈R FM =N′[y . .= M]
 since y / ∈ FV(F).
 □
 Lemma 5. Let F ∈Λ and z ∈V. If M ∈Λ is RF,y-reduced, then y / ∈ FV(M).
 Proof. By induction on M, using that y is not RF,y-reduced.
 We now dispense with “step (ii)” from the proof outline above.
 □
 Proposition 6. Let F,M,N ∈ Λ and suppose FM ≈R M. For any y ∈ V −FV(F)∪FV(M)∪
 FV(N), if yN has an RF,y-normal form, then MN has the same R-normal form.
 Proof. If yN hasanRF,y-normal form, then there is a sequence yN = N0,...,Nn where Nn is RF,y
reduced and Ni →RF,y 
Ni+1 for each i < n. Now consider the sequence N0[y . .= M],...,Nn[y . .=
 M]. We have N0[y . .= M] = MN, and by Lemma 5, Nn[y . .= s] = Nn, which is RF,y-reduced and
 hence R-reduced. By Lemma 4, Ni[y . .= M] ≈R Ni+1[y . .= M] for all i, and hence MN ≈R Nn, as
 desired.
 □
 We now proceed to “step (i)” in the above proof outline. We begin with some preliminary
 definitions. As indicated in the outline, we will need to get a handle on what arbitrary R-reductions
 of YF look like.
 Definition 7. Given M,N ∈ Λ, let YM,N be the term λg.M(gg) λg.N(gg) , where g / ∈ FV(M)∪
 FV(N), so that Y = λf.Yf,f.
 For n ≥ 0 and M,N ∈ Λ, we define M(n)N recursively by M(0)N = N and M(n+1)N =
 M(M(n)N). For n ≥ 0, define Yn to be the term λf.f(n)Yf,f, so that Y0 = Y.
4
 JOSEPH HELFER
 Finally, for F ∈ Λ, we let
 ΥF ={YnF′ | F ↠R F′,n≥0}∪{YF′,F′′ | F ↠R F′,F ↠R F′′} ⊂ Λ.
 Lemma 8. Fix F ∈Λ.
 Let M ∈ΥF and suppose M →R M′ for some M′ ∈Λ.
 Then M′ =(F′)(n)N for some F′ ∈ Λ with F ↠R F′, some N ∈ ΥF, and some n ≥ 0.
 Proof. Note first that the unique one-step R-reduction of Yn for n ≥ 0 is Yn+1, as is proven by
 induction on n. Next, for any K,L ∈ Λ, the only one-step R-reductions of YK,L are K(YL,L) and
 YK′,L or YK,L′ with K →R K′ and L →R L′.
 Now, let M and M′ be as in the hypothesis. If M = YnF′ with F ↠R F′, then the three
 possibilities for M′ are Yn+1F′ ∈ ΥF, YnF′′ ∈ ΥF where F′ →R F′′, or finally (F′)(n)YF′,F′,
 where YF′,F′ ∈ ΥF. If M = YF′,F′′, then M′ is either F′YF′′,F′′ or is of the form YF′,F′′′ or
 YF′′′,F′′ with F ↠R F′′′.
 □
 It will be convenient for us to consider an enlarged set V = V ∪V ′ of variables, where V ′ is some
 set disjoint from V . In fact, we take V ′ = V′
 F to be (isomorphic to) to the set ΥF of Definition 7
 for some F ∈ Λ. Given M ∈ ΥF, we write vM for the corresponding element of V′
 F.
 The variables in V ′
 F will allow us to keep track of “which variables correspond to which instances
 of (reductions of) Yf” as indicated in the above outline.
 Definition 9. Fix F ∈ Λ.
 We write ΛF for the set of lambda-terms with variables in VF = V ∪ V′
 F. For M ∈ ΛF,
 we write FVF(M) ⊂ VF for the set of free variables, and we set FV(M) = FVF(M) ∩ V and
 FV′
 F(M) = FV(M)∩V′
 F. Note that Λ = {M ∈ ΛF | FV′
 F(M) = ∅} ⊂ ΛF.
 Wedefine the realization map ρ: ΛF → Λ by substituting M for each vM ∈ V′
 F; and given y ∈ V,
 we define the forgetful or flattening map φy: ΛF → Λ by substituting y for each variable in V′
 F;
 both ρ and φy are defined by recursion in an evident manner.
 Lemma 10. Fix F ∈Λ, and let M,N ∈ ΛF. Then
 ρ(M[v . .= N]) = ρ(M)[v . .= ρ(N)]
 for any v ∈ V −FV(F), and
 φy(M[v . .= N]) = φy(M)[v . .= φy(N)]
 for any v ∈ V and y ∈V −{v}.
 Proof. By induction on M, using that ρ(K) = φy(K) = K for K ∈ Λ, and that K[w . .= L] = K
 for K,L ∈ Λ and w ∈ V −FV(M). We note that the hypothesis v / ∈ FV(F) is equivalent to
 v / ∈ FV(K) for all K ∈ ΥF, and it is the latter which is actually used in the proof.
 □
 We have the following variant of Lemma 5:
 Lemma 11. Fix F ∈ Λ. For any M ∈ ΛF, if ρ(M) is R-reduced, then FV′
 F(M) = ∅ (i.e.,
 M=ρ(M)∈Λ).
 Proof. By induction on M, using that no element of ΥF is R-reduced.
 □
 Now comes the crucial definition. As explained in the above outline, we will be considering a
 sequence of reductions of a term YF, and producing a parallel sequence in which each instance of
 YF or some reduction of it is replaced by some variable y. The following definition is what lets us
 lift each step of the first sequence to a step in the second.
Y IS A LEAST FIXED POINT COMBINATOR
 5
 Definition 12. Fix F ∈ Λ.
 Given N,N′ ∈ Λ and M ∈ ΛF with N →R N′ and ρ(M)=N, we define a new term
 M′ =γN,N′(M) ∈ ΛF
 with ρ(M′) = N′ and φy(M) ↠RF,y 
φy(M′) for any y ∈ V −FV(N) = V −FV(N′).
 ρ
 φy
 N M φyM
 R
 ρ
 φy
 R
 F,y
 N′ M′ φyM′
 The definition of M′ = γN,N′(M) is by recursion on N:
 • If N = v ∈ V, then N′ = M = v, and we set M′ = v; then evidently ρ(M′) = N′ and
 φy(M) = φy(M′) ↠RF,y 
φy(M′).
 • If N =N1N2, we consider several sub-cases:
 (i) If M ∈ V′
 F, then N ∈ ΥF, and by Lemma 8, N′ = (F′)(n)N3 with N3 ∈ ΥF and
 F ↠R F′. We then set M′ = (F′)(n)vN3
 , and then have ρ(M′) = N′ and φy(M) =
 y ↠SF,y 
F(n)y ↠R (F′)(n)y = φy(M′).
 (ii) Otherwise, we have M = M1M2 with ρ(Mi) = Ni for i = 1,2.
 (ii-a) If N1 = λv.L with v / ∈ {y} ∪ FV(F) and N′ = L[v . .= N2], then note that we
 cannot have M1 ∈ V′
 F, since ΥF contains no λ-abstraction terms. Thus, we must
 have M1 = λv.K with ρ(K) = L, and we set M′ = L[v . .= M2]. We then have
 ρ(M′) = ρ(L)[v . .= ρ(M2)] = K[v . .= N2] by Lemma 10, and we have φy(M) =
 λv.φy(L) φy(M2) and φy(M′) = φy(L)[v . .= φy(M2)] again by Lemma 10, and
 hence φy(M) →β φy(M′).
 (ii-b) Otherwise, we have N′ = N′
 1N′
 2 with Ni →R N′
 i and Nj = N′
 j where {i,j} = {1,2}.
 We then set M′
 i = γNi,N′
 i
 (Mi) and M′
 j = Mj, and set M′ = M′
 1M′
 2. We then have
 ρ(M′) = ρ(M′
 1)ρ(M′
 2) = N′
 1N′
 2 = N′; and we have φM(Mi) ↠RF,y 
φy(M′
 i) for
 i =1,2 and hence φy(M) ↠RF,y 
φy(M′).
 (iii) If N = λv.N1 with v / ∈ {y} ∪ FV(F), then M = λv.M1 where ρ(M1) = N1. We again
 consider sub-cases:
 (iii-a) If N1 = N2v for some term N2, and N′ = N2 (i.e., if R = βη and N1 →η N2), then
 M1 =M2v where ρ(M2) = N2, and we set M′ = M2. We then have ρ(M′) = N′
 and φy(M) = λv.φy(M2)v →η φy(M′).
 (iii-b) Otherwise, N′ = λv.N′
 1 with N1 →R N′
 1. We then set M′
 1 = γN1,N′
 1
 (M1) and
 set M′ = λv.M′
 1, and we have ρ(M′) = λv.ρ(M′
 1) = λv.N′
 1 = N′ and φy(M) =
 λv.φy(M1) ↠RF,y 
λv.φy(M′
 1) = φy(M′).
 (End of definition of γN,N′.)
 The following proposition (applied to L = yN), together with Proposition 6, immediately implies
 the theorem.
 Proposition 13. Let F,L ∈ Λ and y ∈ V −FV(F). If L[z . .= YF] has an R-normal form, then L
 has the same RF,y-normal form.
 Proof. Let L be a term such that N0 = L[y . .= YF] has an R-normal form. We thus have a
 sequence N0,...,Nn where Nn is R-reduced and Ni →R Ni+1 for all 0 ≤ i < n.
6
 JOSEPH HELFER
 N0 ··· Nn R
 M0,
 · · · ,
 R
 ρ
 φ
 z
 Mn
 L =φz(M0) ··· φy(Mn) R
 F,y R
 F,y
 Figure 1. The behaviour of the Mi’s and Ni’s in Proposition 13.
 Define M0 = L[y . .= vYF] ∈ ΛF, so that ρ(M0) = N0 and φy(M0) = L. Now for each 0 ≤ i < n,
 define Mi+1 = γNi,Ni+1
 (Mi); we then have ρ(Mi+1) = Ni+1 and φy(Mi) ↠RF,y 
φy(Mi+1), and in
 particular, L ↠RF,y 
φy(Mn) (see Figure 1).
 Since Nn is R-reduced, FV′
 F(Mn) = ∅ by Lemma 11, hence Nn = Mn = φy(Mn) and thus
 L ≈RF,y 
Nn. Since Nn is R-reduced and y / ∈ FV(Nn) (this follows from Nn ≈R N0 = L[y . .= YF]
 and y / ∈ FV(YF)), it is also RF,y-reduced, as desired.
 4. Other fixed point combinators
 □
 There are (infinitely many) other terms M in the λ-calculus which are fixed-point combinators
 in the sense that F(TF) ≈R TF for all F ∈ Λ. A well-known example is Turing’s combinator
 Θ= λx.λy.y(xxy) λx.λy.y(xxy) ; see [Bar84, Definition 6.1.4].
 In the case of Θ, it is easy to adapt the above proof to see that it is also a least fixed point
 combinator. The main point is to modify Definition 7 in light of the possible β-reductions of ΘF:
 the set ΥF should now consist of all terms Θ′F′ with Θ ↠R Θ′ and F ↠R F′. The statement
 of Lemma 8 then still holds, the construction in Definition 12 goes through verbatim, and the
 statement and proof of Proposition 13 go through mutatis mutandis, substituting Θ for Y.
 The proof could similarly be adapted for other fixed-point combinators; it is just a matter of
 modifying Definition 7 so that Lemma 8 remains true. In fact, in light of [Bar84, Theorem 19.3.4],
 it is plausible that every fixed point combinator is a least fixed point combinator. Moreover, given
 the result of [Gol05] giving a recursive enumeration of all fixed point combinators, proving this
 conjecture is perhaps not out of reach.