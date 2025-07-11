# Lawvere Theories   
Loading [MathJax]/jax/output/SVG/fonts/TeX/Main/Italic/GreekAndCoptic.js   
[Skip to main content](https://www.sciencedirect.com/science/article/pii/S0022404908001485#screen-reader-main-content) [Skip to article](https://www.sciencedirect.com/science/article/pii/S0022404908001485#screen-reader-main-title)   
- [Journals & Books](https://www.sciencedirect.com/browse/journals-and-books)   
- Help   
- [Search](https://www.sciencedirect.com/search)   
   
[My account](https://www.sciencedirect.com/user/login?targetURL=/science/article/pii/S0022404908001485&from=globalheader)   
[Sign in](https://www.sciencedirect.com/user/institution/login?targetURL=/science/article/pii/S0022404908001485)   
- View **[PDF](https://www.sciencedirect.com/science/article/pii/S0022404908001485/pdfft?md5=4c2e26e17ecca8c6607f2d00a4cf2d0f&pid=1-s2.0-S0022404908001485-main.pdf)**   
- Download full issue   
   
Search ScienceDirect   
## Outline   
1. [Abstract](https://www.sciencedirect.com/science/article/pii/S0022404908001485#aep-abstract-id3)   
2. [MSC](https://www.sciencedirect.com/science/article/pii/S0022404908001485#aep-keywords-id5)   
3. [1. Introduction](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec1)   
4. [2. Lawvere -theories and their models](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec2)   
5. [3. Examples](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec3)   
6. [4. Invariance of Lawvere -theories](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec4)   
7. [5. Lawvere -theories and finitary -monads](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec5)   
8. [6. Change-of-base](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec6)   
9. [Acknowledgements](https://www.sciencedirect.com/science/article/pii/S0022404908001485#aep-acknowledgment-id19)   
10. [References](https://www.sciencedirect.com/science/article/pii/S0022404908001485#aep-bibliography-id20)   
   
Show full outline   
## Journal of Pure and Applied Algebra   
[Volume 213, Issue 3](https://www.sciencedirect.com/journal/journal-of-pure-and-applied-algebra/vol/213/issue/3), March 2009, Pages 377-386   
![1-s2.0-S0022404908X0012X-cov150h](files\1-s2-0-s0022404908x0012x-cov150h.gif)    
# Lawvere theories enriched over a general base   
Author links open overlay panelKokiNishizawaab, JohnPowerc   
Show more   
Add to Mendeley   
Share   
Cite   
[https://doi.org/10.1016/j.jpaa.2008.07.009](https://doi.org/10.1016/j.jpaa.2008.07.009) [Get rights and content](https://s100.copyright.com/AppDispatchServlet?publisherName=ELS&contentID=S0022404908001485&orderBeanReset=true)   
Under an Elsevier [user license](http://www.elsevier.com/open-access/userlicense/1.0/)   
Open archive   
## Abstract   
We generalise the correspondence between Lawvere theories and finitary monads on Set in two ways. First, we allow our theories to be enriched in a category V that is locally finitely presentable as a symmetric monoidal closed category: symmetry is convenient but not necessary. And second, we allow the arities of our theories to be finitely presentable objects of a locally finitely presentable V-category A. We call the resulting notion that of a Lawvere A-theory. We extend the correspondence for ordinary Lawvere theories to one between Lawvere A-theories and finitary V-monads on A. We illustrate this with examples leading up to that of the Lawvere Cat-theory for cartesian closed categories, i.e., the Set-enriched theory on the category Cat for which the models are all small cartesian closed categories. We also briefly investigate change-of-base.   
- [Previous article in issue](https://www.sciencedirect.com/science/article/pii/S0022404908001461)   
- [Next article in issue](https://www.sciencedirect.com/science/article/pii/S0022404908001679)   
   
## MSC   
18C10   
18C15   
18C20   
18C30   
## 1. Introduction   
The relationship between Lawvere theories, equationally defined algebraic theories and finitary monads on  is one of the deepest relationships in category theory. The notions of Lawvere theory and finitary monad on  are equivalent; every equationally defined algebraic theory gives rise to a Lawvere theory given by the clone of the equational theory, and every Lawvere theory arises from an equationally defined algebraic theory [[1]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b1). In mathematics, the relationship yields companion approaches to universal algebra [[1]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b1), [[2]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b2) with its usual list of examples. And in computer science, if one makes a routine generalisation from finite sets to countable sets, almost all the monads on  introduced by Moggi in [[15]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b15), [[16]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b16) to model computational effects arise as the Lawvere theories generated by computationally natural equationally defined algebraic theories, which is how the computational effects appear in practice [[6]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b6). The recognition of the various monads as natural Lawvere theories has led and is leading to a deeper analysis of the semantics of such effects [[17]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b17), [[20]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b20), [[18]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b18), [[19]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b19).   
Lawvere theories were introduced in the early 1960s precisely because of their relationship with equationally defined algebraic theories [[13]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b13). Soon afterwards, the relationship between Lawvere theories and monads on  was established [[14]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b14). The notion of monad generalises trivially to base categories other than , whereas the notions of Lawvere theory and equationally defined algebraic theory do not immediately generalise. There were ideas in the air for generalisations of the latter notions [[14]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b14), and it was recognised that monads on categories other than  arise from a generalised notion of algebraic structure, but a generalised formal correspondence does not seem to have been published at the time.   
A generation later, after the underlying results of enriched category theory had been developed [[8]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b8), [[7]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b7), a precise formulation of the notion of -enriched algebraic structure was given and a correspondence with finitary -monads on a locally finitely presentable -category  was proved [[9]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b9), [[22]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b22). The notion was soon used in computer science, for instance in [[5]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b5), [[11]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b11), [[12]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b12). But algebraic structure is not an invariant of a finitary -monad, i.e., an arbitrary finitary -monad arises from many different algebraic presentations, and the various presentations are often equally natural: that is the case even when  is  as any group-theorist could assert. And, because of a delicate inductive step, the details of examples of algebraic structure were often remarkably complicated to give in practice, much more so than for usual presentations of equationally defined algebraic structure relative to . So the lack of a generalised notion of Lawvere theory still affected researchers trying to calculate the details.   
Eventually, in [[21]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b21), the correspondence between Lawvere theories and finitary monads was generalised to one between -enriched Lawvere theories and finitary -monads on the base category  subject to cocompleteness, size and coherence conditions. Taking  to be , that allowed the study of -enriched algebraic structure on categories in terms of Lawvere -theories. Again making the routine generalisation from finitariness to countability, taking  to be  in analysing computational effects, the notion of Lawvere -theory proved to be fundamental, allowing a study of recursion [[6]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b6) and in particular allowing for the incorporation of partiality into the study of the various other effects [[6]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b6). Implicit in the definition of Lawvere -theory was a simplified formulation of the notion of -enriched algebraic structure.   
In this paper, we generalise the correspondence between Lawvere theories and finitary monads a step further. We first choose a category  in which to enrich, and then we choose a base -category . We then define a notion that we call a Lawvere -theory and we extend the above correspondence to one between Lawvere -theories and finitary -enriched monads on the -category . For instance, taking  to be  and  to be the -enriched category, i.e., the ordinary category, , we can consider structure on  as an ordinary category. This allows us to capture structures that we could not capture when  was identified with  as in the past. For instance, we can consider cartesian closed structure in this setting, which was impossible before because of the contravariance involved with closedness [[3]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b3). The techniques we develop here may also help with sophisticated computational effects such as probabilistic nondeterminism, in which one considers the category of dcpo’s as an -enriched category [[6]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b6), but that requires further investigation of size. Our definition of Lawvere -theory implies a simplified formulation of -enriched algebraic structure on : we do not give a precise formulation here, but we illustrate by example that our definition allows less complicated formulation of the equations.   
In regard to technique, the constructions of this paper, yielding the correspondence between Lawvere -theories and -monads on , are essentially the same as in the past [[21]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b21). What is not obvious is how to define the notion of Lawvere -theory. An ordinary Lawvere theory was defined to be a small category  with finite products and an identity-on-objects strict finite product preserving functor , where  is the category of natural numbers and all functions between them. Here, our definition is quite different: taking both  and  to be , our definition of Lawvere -theory consists of a small category  together with an identity-on-objects functor  that strictly preserves finite limits. So we do not assert that  has finite products, but we do assert that  preserves all finite limits of  rather than just its finite products. It is routine to verify that one of our Lawvere theories is one of Lawvere’s ones, as one can readily deduce that  inherits finite products from . But the converse requires more thought: we prove it in Section [2](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec2).   
We use cartesian closed structure on categories as a leading example to illustrate the ideas of the paper. The category  is a convenient base category to illustrate the ideas as it is not itself monadic over , and thus algebraic structure over  is quite different to algebraic structure over . And cartesian closed structure is convenient because it is a familiar notion and because the category of small cartesian closed categories is monadic over  while not being 2-monadic: so  and  are different, with  being  and  being . It is merely meant to act as illustration.   
We could, in principle, attempt to take the correspondence we develop here even further: one can speak of a monad *in* any 2-category, and some of the constructions of this paper extend to that level of generality [[23]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b23). But the issue of size becomes particularly awkward there, and, even not accounting for size, an appropriately generalised notion of Lawvere theory does not appear in [[23]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b23) and the details of the axiomatics would make it awkward.   
In Section [2](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec2), we introduce our definition of Lawvere -theory and the -category of models of a theory. In Section [3](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec3), we analyse the example of cartesian closed structure, where  is  and  is . In Section [4](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec4), we show, in general, how to recover a Lawvere -theory from its -category of models: this gives a construction of a finitary -monad on  from a Lawvere -theory and shows that the definition of Lawvere -theory is invariant with respect to its -category of models. In Section [5](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec5), we start with a finitary -monad on , construct a Lawvere -theory from it, and show how to recover the -monad. Combining this with the work of Section [4](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec4) yields the correspondence we seek between Lawvere -theories and finitary -monads on . Finally, in Section [6](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec6), we consider change-of-base, i.e., given a monoidal functor , we consider the relationship between the models of a Lawvere -theory and those of the induced Lawvere --theory: this is important for examples such as those where  is  but some structure, such as finite product structure, is -enriched, while other structure, such as closed structure, is not.   
## 2. Lawvere -theories and their models   
In this section, we introduce the notions of Lawvere -theory and -category of models of a theory, and we show that ordinary Lawvere theories, more generally the enriched Lawvere theories of [[21]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b21), are special cases, with the respective definitions of model being in agreement. To give the definitions necessarily involves sophisticated enriched category theory: we shall do our best to keep it comprehensible, but we recommend the less category-theoretic reader focus on the examples of  and .   
We assume that  is locally finitely presentable as a symmetric monoidal closed category and that  is a locally finitely presentable -category: symmetry of  is not necessary here, but it is convenient for exposition, includes all our leading examples, and corresponds to most of the relevant literature. The precise definitions of these notions can be found in [[8]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b8), [[7]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b7), but in order to understand the point of this paper, one only needs to know examples that appear in the computer science literature [[2]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b2), [[10]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b10), [[22]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b22).  and  are locally finitely presentable as symmetric monoidal closed categories. Locally finitely presentable -categories are exactly ordinary locally finitely presentable categories, such as , ,  and .  is a locally finitely presentable -category, and that statement extends to  given axiomatically as above.   
We write  for a skeleton of the full sub--category of  given by the finitely presentable objects of , and we let  denote the inclusion -functor. Following the canonical reference for enriched categories [[8]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b8), we denote the composite -functorby , where  is an enriched version of the Yoneda embedding. For example, up to coherent isomorphism, the category  is the category , whose objects are natural numbers and whose arrows are all functions between them. The functor  sends a set  to the functor . For a more complex example,  is the category of finitely presentable categories, i.e., those categories that are freely generated on a finite graph or are given by coequalising a pair of functors between such freely generated categories.   
We next need the idea of a finite cotensor. This generalises the notion of a finite power. A -category  has *finite cotensors* if for every finitely presentable  in  and every  in , there exists an object  of  together with a natural isomorphismFor example, in the case , a finite cotensor means that  is finite and  is a product of  copies of . In the case , the cotensor  is given by the exponential . We write  for the full sub--category of  determined by those -functors that preserve finite cotensors.   
Finally, we need the notion of a *finite enriched limit*. The formal definition is complicated, so we shall not give it directly but rather use a characterisation theorem that makes the notion much easier to grasp [[8]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b8): a -category admits all finite -limits if and only if it admits all finite conical limits and all finite cotensors . Here, the notion of conical limit is exactly as one would expect, bearing in mind that enrichment means one wants an isomorphism in  between the object of cones over a diagram and the homobject of comparison maps, rather than a mere bijection of sets [[8]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b8). We write  for the full sub--category of  determined by those -functors that preserve finite -limits. The -functor  preserves all finite -colimits, and representable -functors preserve -limits, so  factors through . So we sometimes consider  as a -functor from  to . The central result of Gabriel–Ulmer duality, generalised to enriched categories, asserts that  induces an equivalence  of -categories [[7]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b7). Since  is a full sub--category of , we also sometimes consider  as a -functor from  to .   
Finally, we can write the central definition of the paper. We assume that  and  satisfy the axiomatic structure described above, i.e.,  is a locally finitely presentable -category for appropriate .   
**Definition 2.1**   
A *Lawvere*-*theory* is a small -category  together with an identity-on-objects strict finite -limit preserving -functor .   
So the objects of  are *exactly* the objects of . One understands them in this setting to be generalised *arities*, and one understands the arrows of  to be operations. This should become clearer when we study examples. But to see the distinction between preservation of limits and preservation of cotensors in our definition, consider the example of  and , and note that the triangle category is a pushout in  constructed from two copies of the arrow category together with the unit category 1.   
A map of Lawvere -theories from  to  is an identity-on-objects -functor from  to  that commutes with the -functors from . Together with the usual composition of -functors, Lawvere -theories and their maps yield an ordinary category we denote by .   
**Definition 2.2**   
Given a Lawvere -theory  with , define its -category of models by the following pullback in the category - of locally small -categories.We call objects of *models* of .   
So a model consists of an object  of  together with a functor  whose behaviour when restricted to  is completely determined by . Thus a model is determined by  together with data and axioms arising from those maps in L that are not already in Afop.   
There is a subtle 2-categorical point here that is particularly convenient for us. The pullback defining Mod(L) is unusual in that it is also a *bipullback*  [[3]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b3), meaning that if one systematically replaces equality of diagrams in V-Cat by coherent isomorphism, this pullback still satisfies the systematically weakened version of the universal property. That can readily be checked directly, but axiomatically, it holds because the V-functor [J,V] satisfies an isomorphism lifting property. We shall henceforth largely gloss over this point for the sake of exposition.   
It will be easier to explain examples and to characterise the definition in special cases if we first give an alternative definition of the V-category of models as provided by the following proposition.   
**Proposition 2.3**   
*For any Lawvere*A\*-theory*L*with*J:Afop→L*, the following diagram forms a pullback in*V*-*Cat*.\*   
**Proof**   
First observe that L has finite cotensors: J is the identity on objects, so every object of L lies uniquely in the image of J; moreover, J strictly preserves finite cotensors, hence the result. Now note that the squareis a pullback: if M is a V-functor from L to V such that M∘J preserves finite V-cotensors, it follows from the above construction of pullbacks in L that M preserves them. The lemma now follows from the definition of Mod(L) and generalities about pullbacks. □   
We now compare our definitions with those already in the literature. An ordinary Lawvere theory [[1]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b1) is usually defined to be a small category L with finite products together with an identity-on-objects strict finite product preserving functor from Natop to L. A model in Set is defined to be a finite product preserving functor from L to Set. Note that one assumes that L has finite products and that the functor from Natop to L strictly preserves finite products, whereas in our general definition, we asked for strict preservation of finite limits but made no further assumption of existence of any kind of limits in L.   
**Theorem 2.4**   
*An ordinary Lawvere theory is a Lawvere*Set\*-theory and conversely. Moreover, the two definitions of the category of models agree.\*   
**Proof**   
Let L be any ordinary Lawvere theory. It corresponds to a finitary monad T. Moreover, L is isomorphic to the restriction of Kl(T)op to the natural numbers, and the functor J:Natop→L is given by the restriction of the canonical functor Set⟶Kl(T). So J:Natop→L strictly preserves all finite limits of Nat, as the corresponding finite colimits are strictly preserved both by the inclusion into Set and by the canonical functor into Kl(T). So every ordinary Lawvere theory is a Lawvere Set-theory in the above sense. The converse is trivially true. For the statement about models, first observe that Setfop is the free Set-category with finite cotensors, i.e., finite powers, on 1. So ι̃ yields a canonical equivalence Set≃FC(Setfop,Set). So [Proposition 2.3](https://www.sciencedirect.com/science/article/pii/S0022404908001485#pps2.3) implies Mod(L)≃FC(L,Set). But all finite products on Setop, hence also on L, are given by finite powers of copies of 1, i.e., by finite cotensors, and so preservation of finite powers is equivalent, in this setting, to preservation of finite products, hence the result.  □   
Enriching this result, in [[21]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b21), given V satisfying the axioms we have here, a Lawvere V-theory was defined to be a small V-category L with finite V-cotensors together with an identity-on-objects strict finite V-cotensor preserving V-functor J:Vfop→L. The V-category of models of such a Lawvere V-theory was defined to be FC(L,V).   
**Theorem 2.5**   
*If*A*is*V\*, Lawvere*A*-theories are precisely Lawvere*V*-theories defined as above. Moreover, the two definitions of the*V*-category of models agree.\*   
**Proof**   
The proof of the correspondence is given by a simple enrichment of the proof of [Theorem 2.4](https://www.sciencedirect.com/science/article/pii/S0022404908001485#thm2.4). Similarly for the statement about models. □   
## 3. Examples   
In this section, we give three examples of Lawvere A-theories, developing our leading example of the Lawvere Cat-theory for cartesian closed categories. Our first two examples, those of categories with a terminal object and categories with binary products, may be seen as examples of the enriched Lawvere theories of [[21]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b21) as both V and A are Cat. But our final example, that of cartesian closed structure for categories, is genuinely new in that, although A is Cat, this example is not Cat-enriched but is only Set-enriched owing to the contravariance inherent in the notion of closedness [[3]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b3).   
### 3.1. Categories with a terminal object   
Let 0 denote the empty category. Let 1 denote the trivial one object category. Let 2 denote the category {d→c}. And let Δ denote the diagonal functor.   
Put A=V=Cat, and let L be the Cat-category with finite Cat-cotensors freely generated by adding arrows τ:0→1 and σ:1→2 to Catfop subject to commutativity of the following diagrams:This is the Lawvere Cat-theory for a category with an assigned terminal object, i.e., the category of models of this Lawvere Cat-theory is equivalent to the 2-category of small categories with an assigned terminal object.   
By [Theorem 2.5](https://www.sciencedirect.com/science/article/pii/S0022404908001485#thm2.5), to give a model M of L is equivalent to giving a finite Cat-cotensor preserving Cat-functor from L to Cat. So, for any model M, the following diagrams must commute in Cat:So the category M1 has an object t determined by Mτ. The first two diagrams assert that Mσ sends an object x of M1 to an arrow from x to t. The third diagram asserts that Mσ sends the object t to the identity map on t. From this together with functoriality of Mσ and cod, one can deduce uniqueness of the map from an arbitrary object x into t  [[21]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b21).   
For the converse construction, given a category C with a terminal object t, let M from L to Cat send 1 to C and, more generally, send 1X to CX; let Mτ choose t, and for any object x of C, let Mσ send x to the unique arrow from x to t. These constructions make the diagrams commute and respect finite cotensors, so, by construction of L and by definition of a model, determine a model. It is routine to verify that the two constructions are mutually inverse.   
### 3.2. Categories with binary products   
Let 2 denote the discrete category on two objects a and b. Let Cone denote the category given by 2 together with a cone π over it. Let a×b denote the vertex. Let DoubleCone denote the category given by Cone together with a cone μ over it. Let m denote the vertex. Mildly overloading notation, let μ:Cone→DoubleCone send πa and πb to μa and μb, respectively. Similarly, for any arrow f:x→y in C, let f:2→C send d, c and the arrow d→c to x, y and f respectively. For example, μa×b:2→DoubleCone sends d, c and the arrow d→c to m, a×b and μa×b respectively. Let inc denote the inclusion of 2 into Cone and of Cone into DoubleCone.   
Put A=V=Cat, and let L be freely generated by adding arrows β:2→Cone and α:Cone→DoubleCone to Catfop and by insisting that the following diagrams commute:By essentially the same argument as in Section [3.1](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec3.1), this is the Lawvere Cat-theory for a category with binary products.   
### 3.3. Cartesian closed categories   
For our final example of a Lawvere A-theory, consider cartesian closed categories. The category of small cartesian closed categories is not given by the case of A=V=Cat, which is covered in [[21]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b21), owing to the contravariance necessarily involved with closedness [[3]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b3). But it is still an example for us, taking V to be Set and A to be Cat. In principle, one way to see that is by applying [Corollary 5.2](https://www.sciencedirect.com/science/article/pii/S0022404908001485#cry5.2) to the example of cartesian closed structure in [[3]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b3). But the spirit of this paper is to see such structure directly as a model of a generalised Lawvere theory. So we shall outline what is required here, leaving most of the syntactic details to the enthusiastic reader.   
By Sections [3.1 Categories with a terminal object](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec3.1), [3.2 Categories with binary products](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec3.2) and using the work on change-of-base in Section [6](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec6), one obtains the Lawvere Cato-theory for a category with finite products. We now seek to add closedness to that. It is not obvious that one can do that. For each pair of objects (x,y) of a category with finite products C, we need an object [x,y] and a unit map η:y⟶[x,y×x]. That is no problem, similar to the data in Sections [3.1 Categories with a terminal object](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec3.1), [3.2 Categories with binary products](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec3.2). But then one needs to assert that for each arrow of the form x×y⟶z, one obtains a Currying. But that is a problem: the structure of a Lawvere Cat0-theory only allows us to start with an *arbitrary* arrow, not one with a domain of a particular form.   
The way to resolve that, cf [[9]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b9), is by describing closed structure less directly: given an object x, one asks for an endofunctor [x,−] on C, then one asks for a unit and a counit that makes [x,−] a right adjoint to −×x, then one imposes naturality axioms and the triangle equations. To describe all that in detail is lengthy albeit routine, with each piece of data requiring analysis similar to that in Section [3.2](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec3.2), with the added complexity here of needing to assert functoriality explicitly.   
For instance, we introduce an arrow [−,−]ob:1+1→1 and an arrow [−,−]ar:1+2→2 to represent the object and arrow parts respectively of the functor [x,−] for each object x. The following diagrams represent the condition that the domain object and the codomain object determined by [x,−]ar are as expected:It follows from [Definition 2.2](https://www.sciencedirect.com/science/article/pii/S0022404908001485#dfn2.2) that for any model M there exists a C∈Cato such that M∘J=Cato(ι−,C). So the first diagram yields the following diagram in Set:The second diagram is dual.   
We relegate the rest of the operations and diagrams to the enthusiastic reader: in principle, they are not difficult, following the above explanation; but the details are lengthy and require concentration. The cognoscenti may observe that the details are simpler than those generated by the algebraic structure of [[9]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b9) as we do not require the delicate and complicated induction needed there.   
## 4. Invariance of Lawvere A-theories   
In this section, given any Lawvere A-theory, we prove that the forgetful V-functor UL:Mod(L)⟶A is finitarily V-monadic, yielding a finitary V-monad TL on A. We further show how one can reconstruct L from TL.   
First observe that for any Lawvere A-theory L, since A is locally finitely presentable, so equivalent to FL(Afop,V), and since representables preserve finite limits as does J, there is a canonical V-functor J′ such that the following square commutes up to isomorphism:One can make a slightly stronger statement: if one is willing to replace Y by a V-functor that is isomorphic to it, one can force the diagram actually to commute; although a minor point, that is convenient for us.   
Applying the universal property determines a V-functor J″ as follows:Since ι̃ is fully faithful, so is PL, and, since Y is also fully faithful, so is J″.   
**Proposition 4.1**   
*For any Lawvere*A\*-theory*L*and for any objects*X*of*Af*and*M*of*Mod(L)*,Mod(L)(J″JopX,M)*and*A(ιX,ULM)**are canonically*V*-naturally isomorphic in*V*.\*   
**Proof**   
By fully faithfulness of PL, and by the enriched Yoneda lemma, with I the unit of V and since L(JX,−)=PLJ″JopX, and finally as (PLM)JX=([J,V]PLM)X=(ι̃ULM)X=A(ιX,ULM), we have the following string of V-natural correspondences: □   
Recall that UL is defined by a pullback in V-Cat. So its defining diagram commutes exactly rather than just up to coherent isomorphism. That strictness is convenient, but we need care in order to maintain it. So, in the following, when we speak of a left Kan extension along a fully faithful inclusion V-functor, we shall assume that it is chosen to make the induced triangle commute exactly: a Kan extension along a fully faithful V-functor always makes the triangle commute up to coherent isomorphism [[8]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b8), and when that V-functor is an inclusion, we can choose the Kan extension to make the triangle commute exactly.   
**Theorem 4.2**   
UL*has a left*V\*-adjoint given by the left Kan extension of*J″∘Jop*along*ι*.\*   
**Proof**   
Let FL be the left Kan extension of J″∘Jop along ι. It has a right adjoint H that sends a model M to Mod(L)(J″Jop−,M). By [Proposition 4.1](https://www.sciencedirect.com/science/article/pii/S0022404908001485#pps4.1), HM≅Mod(L)(J″Jop−,M)≅A(ι−,ULM)≅ULM.  □   
**Theorem 4.3**   
UL*is finitary*V\*-monadic.\*   
**Proof**   
By [Theorem 4.2](https://www.sciencedirect.com/science/article/pii/S0022404908001485#thm4.2), UL has a left V-adjoint. Let f, g be a UL-split coequaliser pair in Mod(L). Since [L,V] is cocomplete, PLf and PLg have a coequaliser, and the coequaliser can be chosen so that it is strictly preserved by [J,V]. Since a split coequaliser of ULf and ULg is also preserved by ι̃, f and g have a coequaliser in Mod(L) and UL strictly preserves it. So by Beck’s monadicity theorem [[1]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b1) and by remarks on enrichment of monadicity in [[9]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b9), UL is V-monadic. Finitariness of UL follows from that of [J,V] and ι̃.  □   
We define TL to be the finitary V-monad induced by a Lawvere A-theory L by [Theorem 4.3](https://www.sciencedirect.com/science/article/pii/S0022404908001485#thm4.3). By the next corollary, we can reconstruct L from the monadic V-functor UL.   
**Corollary 4.4**   
*One rediscovers*(Lop,Jop,J″)*as the (identity-on-objects,fully faithful) factorisation of*FL∘ι\*.\*   
**Proof**   
The diagram commutes by the construction of FL in [Theorem 4.2](https://www.sciencedirect.com/science/article/pii/S0022404908001485#thm4.2). Moreover, Jop is identity-on-objects and J″ is fully faithful.  □   
## 5. Lawvere A-theories and finitary V-monads   
In this section, we give an equivalence between the category of Lawvere A-theories and that of finitary V-monads on A. We first construct a Lawvere A-theory LT from an arbitrary finitary V-monad T on A. We then show that the construction of Section [4](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec4) allows us to reconstruct T from LT. Finally, we observe that the two constructions extend to an equivalence between the category of Lawvere A-theories and that of finitary V-monads on A.   
For a finitary V-monad T on A, define (KT,JT,ιT) by taking the (identity-on-objects,fully faithful) factorisation of FT∘ι:Since ι and FT preserve finite V-colimits and ιT reflects finite V-colimits, JT is an identity-on-objects strict finite V-colimit preserving V-functor. So we define LT to be KTop.   
Note the similarity between this definition and [Corollary 4.4](https://www.sciencedirect.com/science/article/pii/S0022404908001485#cry4.4). Also observe that, letting FT be the canonical left V-adjoint from A to the Kleisli V-category Kl(T), we could equally have defined (KT,JT,ιT) by taking the (identity-on-objects,fully faithful) factorisation of FT∘ι:This formulation agrees more closely with [Theorem 2.4](https://www.sciencedirect.com/science/article/pii/S0022404908001485#thm2.4) but would make for slightly greater complication in our ongoing exposition.   
**Theorem 5.1**   
*For a finitary*V\*-monad*T*on*A*, let*FT⊣GT*be the canonical*V*-adjunction between the Eilenberg–Moore*V*-category*T*-*Alg*and*A*, and let*QT*send a*T*-algebra*α*to*T*-*Alg(ιT−,α)*. Then, if we allow*QT*to be replaced by a canonically isomorphic functor, the following square yields a pullback:\*   
**Proof**   
Since ιT∘JT=FT∘ι and we have a V-adjunction T-Alg(FTι−,−)≅A(ι−,GT−), the square commutes up to isomorphism. As the V-functor [JTop,V] satisfies the isomorphism lifting property, QT is isomorphic to a V-functor that makes the diagram commute exactly.   
Now let a∈A and M:LT→V satisfy A(ι−,a)≅MJTop. Using the isomorphism, the functoriality data of M yields mapsA(ιm,Tιn)⟶[A(ιn,a),A(ιm,a)]V-natural in m and n. By V-naturality in m and by density of Af in A, these are equivalent to mapsA(ιn,a)⟶A(Tιn,a)V-natural in n, which in turn correspond to the components of a map of the form∫n∈AfA(ιn,a)⊗Tιn⟶awith the V-naturality corresponding to the property of being a cocone. So, as Ta=∫n∈AfA(ιn,a)⊗Tιn, the functoriality data of M yields a map α:Ta→a, cf [[8]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b8), [[21]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b21). It is a T-algebra and satisfies GTα=a. It is routine to verify that α is the unique T-algebra such that QTα is canonically isomorphic to T-Alg(ιT−,α). Conjugating with respect to isomorphisms of V-functors, one can obtain strict commutativity. Functoriality is routine. □   
We remark that this theorem yields an alternative proof of the fact that the V-category of algebras for a finitary V-monad on a locally finitely presentable V-category is itself locally finitely presentable. For the fully faithfulness of QT shows that KT is dense in T-Alg, with the objects of KT all finitely presentable in A and hence in T-Alg. As T-Alg is also V-cocomplete, it is locally finitely presentable.   
**Corollary 5.2**   
*The construction of*TL*from an arbitrary Lawvere*V\*-theory*L*and that of*L*from an arbitrary finitary*V*-monad*T*on*A*extend canonically to an equivalence of categories*LawA≃Mndf(A)*. Moreover, the*V*-categoriesMod(L)**and*TL*-*Alg*are canonically isomorphic.\*   
**Proof**   
By [Theorem 5.1](https://www.sciencedirect.com/science/article/pii/S0022404908001485#thm5.1), T≅TLT for an arbitrary finitary V-monad T on A. Conversely, given an arbitrary Lawvere A-theory L, the Lawvere A-theory LTL is defined to be the (identity-on-objects, fully faithful) factorisation of FTL∘ι:Af→TL-Alg. By [Corollary 4.4](https://www.sciencedirect.com/science/article/pii/S0022404908001485#cry4.4) and since Mod(L)≅TL-Alg, this factorisation agrees with L, and so LTL is isomorphic to L. The two constructions routinely extend to an equivalence of categories.  □   
The final line of [Corollary 5.2](https://www.sciencedirect.com/science/article/pii/S0022404908001485#cry5.2) is delicate. Although there exists a canonical isomorphism as stated, it is not true that, taking V and A to be Set, one has an isomorphism between the usual category of models of a Lawvere theory and the category of algebras for the corresponding monad. That lack of an isomorphism is consistent with our result because our category of models is only equivalent, rather than isomorphic, to Lawvere’s category.   
## 6. Change-of-base   
In this section, we briefly discuss change-of-base category V in which to enrich. Recall that is central to analysis of our leading example, that of cartesian closed structure, in Section [3](https://www.sciencedirect.com/science/article/pii/S0022404908001485#sec3). Changing V affects V-categories A, Lawvere A-theories, and models. We first show that applying the forgetful Set-functor V(I,−):V→Set respects the definitions of Lawvere A-theory L and Mod(L). We then extend the analysis to any finitary symmetric monoidal closed adjunction.   
**Theorem 6.1**   
*For any Lawvere*A\*-theory*L*with*J:Afop→L*, the data*Jo:(Afop)o→Lo*forms a Lawvere*Ao*-theory, for which there is a canonical isomorphism*Mod(L)o≅Mod(Lo)*.\*   
**Proof**   
For any finitary V-monad T on A, the underlying ordinary category T-Algo of the V-category T-Alg is isomorphic to the ordinary category To-Alg determined by the ordinary monad To on Ao  [[9]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b9). It follows from the definition that T is finitary if and only if To is. So, by [Corollary 5.2](https://www.sciencedirect.com/science/article/pii/S0022404908001485#cry5.2), we have Mod(LT)o≅T-Algo≅To-Alg≅Mod(LTo).   
Since A is locally finitely presentable, (Af)o≅(Ao)f  [[7]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b7). Moreover, the underlying ordinary functor of a fully faithful V-functor is necessarily fully faithful. So the following diagram agrees with the (identity-on-objects, fully faithful) factorisation that defines (LT0)op:So (LT)o≅LTo.  □   
Now let V=(Vo,⊗,I,α,λ,ρ,γ) and V′=(Vo′,⊗′,I′,α′,λ′,ρ′,γ′) be locally finitary presentable as symmetric monoidal closed categories and assume that Ψ⊣Φ:V→V′ is a finitary symmetric monoidal closed adjunction [[4]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b4), [[7]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b7).   
**Example 6.2**   
The forgetful Set-functor V(I,−):V→Set generates a finitary symmetric monoidal closed adjunction.   
We may define a 2-functor Φ-Cat:V-Cat→V′-Cat as follows. Let L be a V-category whose composition and identities are given by cL(x,y,z):L(y,z)⊗L(x,y)→L(x,z) and iL(x):I→L(x,x) for each x,y,z∈obL. Then, Φ-Cat(L) is the V′-category whose objects, hom, composition and identities are given by obL, Φ(L(x,y)), ΦcL(x,y,z)∘ϕ2(x,y,z) and ΦiL(x)∘ϕ0 where ϕ2(x,y,z):Φ(L(y,z))⊗′Φ(L(x,y))→Φ(L(y,z)⊗L(x,y)) and ϕ0:I′→ΦI are given canonically by the monoidal functor Φ  [[8]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#b8). Our final result is essentially equivalent to [Theorem 6.1](https://www.sciencedirect.com/science/article/pii/S0022404908001485#thm6.1).   
**Corollary 6.3**   
*For any Lawvere*A\*-theory*L*with*J:Afop→L*, the data*Φ*-Cat(L)**and*Φ*-Cat(J)**form a Lawvere*Φ*-*Cat(A)*-theory for which there is a canonical isomorphism*Φ*-*Cat(Mod(L))≅Mod(Φ-Cat(L))*.\*   
## Acknowledgements   
This research was supported by Core Research for Evolutional Science and Technology (CREST) Program “New High-Performance Information Processing Technology Supporting Information-oriented Society” of Japan Science and Technology Agency (JST). This document in an output from the PMI2 Project funded by the UK Department for Innovation, Universities and Skills (DIUS) for the benefit of the Japanese Higher Education Sector and the UK Higher Education Sector. The views expressed are not necessarily those of DIUS, nor British Council.   
## References   
1. [[1]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb1)   
    Michael Barr, Charles Wells   
    Toposes, Triples and Theories, Grundlagen der Mathematischen Wissenschaften, vol. 278, Springer-Verlag, Berlin (1985)   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Toposes, Triples and Theories&publication_year=1985&author=Michael Barr&author=Charles Wells)   
2. [[2]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb2)   
    Michael Barr, Charles Wells   
    Category Theory for Computing Science   
    (third ed.), Less Publications CRM (1999)   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Category Theory for Computing Science&publication_year=1999&author=Michael Barr&author=Charles Wells)   
3. [[3]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb3)   
    R. Blackwell, G.M. Kelly, A.J. Power   
    Two-dimensional monad theory   
    Journal of Pure and Applied Algebra, 59 (1989), pp. 1-41   
    [View PDF](https://www.sciencedirect.com/science/article/pii/0022404989901606/pdf?md5=0a059c4eaa6b0490bddf0608d8d7c514&pid=1-s2.0-0022404989901606-main.pdf) [View article](https://www.sciencedirect.com/science/article/pii/0022404989901606) [View in Scopus](https://www.scopus.com/inward/record.url?eid=2-s2.0-0001300079&partnerID=10&rel=R3.0.0) [Google Scholar](https://scholar.google.com/scholar_lookup?title=Two-dimensional monad theory&publication_year=1989&author=R. Blackwell&author=G.M. Kelly&author=A.J. Power)   
4. [[4]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb4)   
    Samuel Eilenberg, G. Max Kelly   
    Closed categories   
    S. Eilenberg, D.K. Harrison, S. MacLane, H. Röhrl (Eds.), Proceedings of the Conference on Categorical Algebra, La Jolla, 1965, Springer-Verlag (1965), pp. 421-562   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Closed categories&publication_year=1965&author=Samuel Eilenberg&author=G. Max Kelly)   
5. [[5]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb5)   
    Neil Ghani, Christoph Lüth, Marchi Marchi, John Power   
    Algebras, coalgebras, monads and comonads   
    Proc. of 4th Wksh. on Coalgebraic Methods in Computer Science, Electronic Notes in Theoretical Computer Science, vol. 44(1), CMCS’01, Genova, Italy, 6–7 April 2001, Elsevier, Amsterdam (2001)   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Algebras, coalgebras, monads and comonads&publication_year=2001&author=Neil Ghani&author=Christoph Lüth&author=Marchi Marchi&author=John Power)   
6. [[6]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb6)   
    Martin Hyland, Gordon Plotkin, John Power   
    Combining computational effects: Commutativity and sum   
    Proc. of 17th IFIP World Computer Congress, TC1 Stream/2nd IFIP Int. Conf. on Theoretical Computer Science, IFIP Conf. Proc., vol. 223, TCS 2002, Montréal, Que., Canada, 25–30 August 2002, Kluwer Academic Publishers, Dordrecht (2002), pp. 474-484   
    [Crossref](https://doi.org/10.1007/978-0-387-35608-2_39) [View in Scopus](https://www.scopus.com/inward/record.url?eid=2-s2.0-84891084135&partnerID=10&rel=R3.0.0) [Google Scholar](https://scholar.google.com/scholar_lookup?title=Combining computational effects: Commutativity and sum&publication_year=2002&author=Martin Hyland&author=Gordon Plotkin&author=John Power)   
7. [[7]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb7)   
    G.M. Kelly   
    Structures defined by finite limits in the enriched context 1   
    Cahiers de Top et Geom Differentielle, 23 (1980), pp. 3-42   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Structures defined by finite limits in the enriched context 1&publication_year=1980&author=G.M. Kelly)   
8. [[8]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb8)   
    G.M. Kelly   
    Basic Concepts of Enriched Category Theory, London Mathematical Society Lecture Note Series, vol. 64, Cambridge University Press (1982)   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Basic Concepts of Enriched Category Theory&publication_year=1982&author=G.M. Kelly)   
9. [[9]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb9)   
    G.M. Kelly, A.J. Power   
    Adjunctions whose counits are coequalizers, and presentations of finitary enriched monads   
    Journal of Pure and Applied Algebra, 89 (1993), pp. 163-179   
    [View PDF](https://www.sciencedirect.com/science/article/pii/0022404993900928/pdf?md5=03cc6ce7304e81031f12ef8c3b969115&pid=1-s2.0-0022404993900928-main.pdf) [View article](https://www.sciencedirect.com/science/article/pii/0022404993900928) [View in Scopus](https://www.scopus.com/inward/record.url?eid=2-s2.0-43949165312&partnerID=10&rel=R3.0.0) [Google Scholar](https://scholar.google.com/scholar_lookup?title=Adjunctions whose counits are coequalizers, and presentations of finitary enriched monads&publication_year=1993&author=G.M. Kelly&author=A.J. Power)   
10. [[10]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb10)   
    Y. Kinoshita, J. Power   
    Lax naturality through enrichment   
    Journal of Pure and Applied Algebra, 112 (1996), pp. 53-72   
    [View PDF](https://www.sciencedirect.com/science/article/pii/0022404995001360/pdf?md5=aa8b9ed9bfdf12355279a3be85bc62c2&pid=1-s2.0-0022404995001360-main.pdf) [View article](https://www.sciencedirect.com/science/article/pii/0022404995001360) [View in Scopus](https://www.scopus.com/inward/record.url?eid=2-s2.0-0030583452&partnerID=10&rel=R3.0.0) [Google Scholar](https://scholar.google.com/scholar_lookup?title=Lax naturality through enrichment&publication_year=1996&author=Y. Kinoshita&author=J. Power)   
11. [[11]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb11)   
    Yoshiki Kinoshita, John Power   
    A general completeness result in refinement   
    WADT’99, LNCS, vol. 1827, Springer Verlag (2000)   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=A general completeness result in refinement&publication_year=2000&author=Yoshiki Kinoshita&author=John Power)   
12. [[12]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb12)   
    Yoshiki Kinoshita, John Power, Makoto Takeyama   
    Sketches   
    S. Brookes, M. Mislove (Eds.), Electronic Notes in Theoretical Computer Science, vol. 6, Elsevier (2000)   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Sketches&publication_year=2000&author=Yoshiki Kinoshita&author=John Power&author=Makoto Takeyama)   
13. [[13]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb13)   
    F.W. Lawvere   
    Functorial semantics of algebraic theories   
    Proceedings of the National Academy of Science, 50 (1963), pp. 869-873   
    [Crossref](https://doi.org/10.1073/pnas.50.5.869) [Google Scholar](https://scholar.google.com/scholar_lookup?title=Functorial semantics of algebraic theories&publication_year=1963&author=F.W. Lawvere)   
14. [[14]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb14)   
    F.E.J. Linton   
    Some aspects of equational theories   
    S. Eilenberg, D.K. Harrison, S. MacLane, H. Röhrl (Eds.), Proceedings of the Conference on Categorical Algebra, La Jolla, 1965, Springer-Verlag (1965), pp. 84-94   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Some aspects of equational theories&publication_year=1965&author=F.E.J. Linton)   
15. [[15]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb15)   
    E. Moggi   
    Computational lambda-calculus and monads   
    Proceedings of the 1989 Conference on Logic in Computer Science, IEEE Press (1989), pp. 14-29   
    [View in Scopus](https://www.scopus.com/inward/record.url?eid=2-s2.0-0024927385&partnerID=10&rel=R3.0.0) [Google Scholar](https://scholar.google.com/scholar_lookup?title=Computational lambda-calculus and monads&publication_year=1989&author=E. Moggi)   
16. [[16]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb16)   
    E. Moggi   
    Notions of computation and monads   
    Information and Computation, 93 (1) (1991)   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Notions of computation and monads&publication_year=1991&author=E. Moggi)   
17. [[17]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb17)   
    Gordon Plotkin, John Power   
    Adequacy of algebraic effects   
    Proc. of 4th Int. Conf. on Foundations of Software Science and Computation Structures, Lecture Notes in Computer Science, vol. 2030, FoSSaCS 2001, Genova, Italy, 2–6 April 2001, Springer-Verlag, Berlin (2001), pp. 1-24   
    [Crossref](https://doi.org/10.1007/3-540-45315-6_1) [View in Scopus](https://www.scopus.com/inward/record.url?eid=2-s2.0-84945259051&partnerID=10&rel=R3.0.0) [Google Scholar](https://scholar.google.com/scholar_lookup?title=Adequacy of algebraic effects&publication_year=2001&author=Gordon Plotkin&author=John Power)   
18. [[18]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb18)   
    Gordon Plotkin, John Power   
    Semantics of algebraic operations   
    Proc. of 17th Conf. on Math. Found. of Programming Semantics, Electronic Notes in Theoretical Computer Science, vol. 45, MFPS 2001, Aarhus, Denmark, 23–26 May 2001, Elsevier, Amsterdam (2001)   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Semantics of algebraic operations&publication_year=2001&author=Gordon Plotkin&author=John Power)   
19. [[19]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb19)   
    Gordon Plotkin, John Power   
    Logic for computational effects (work in progress)   
    Proc. of 6th Int. Wksh. on Formal Methods, Electronic Workshops in Computing, IWFM 2003, Dublin City Univ., Ireland, 11 July 2003, British Computer Society (2003)   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Logic for computational effects &publication_year=2003&author=Gordon Plotkin&author=John Power)   
20. [[20]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb20)
Gordon D. Plotkin, John Power, Notions of computation determine monads, in: Foundations of Software Science and Computation Structures, FOSSACS, in: Lecture Notes in Computer Science, vol. 2620, Berlin, Heidelberg, New York, 2002, pp. 342–356   
    [Google Scholar](https://scholar.google.com/scholar?q=Gordon D. Plotkin, John Power, Notions of computation determine monads, in: Foundations of Software Science and Computation Structures, FOSSACS, in: Lecture Notes in Computer Science, vol. 2620, Berlin, Heidelberg, New York, 2002, pp. 342–356)   
21. [[21]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb21)   
    John Power   
    Enriched Lawvere theories   
    Theory and Applications of Categories, 6 (7) (1999), pp. 83-93   
    [Google Scholar](https://scholar.google.com/scholar_lookup?title=Enriched Lawvere theories&publication_year=1999&author=John Power)   
22. [[22]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb22)   
    E.P. Robinson   
    Variations on algebra: Monadicity and generalisations of equational theories   
    Formal Aspects of Computing, 13 (2002), pp. 308-326   
    [View in Scopus](https://www.scopus.com/inward/record.url?eid=2-s2.0-0042523541&partnerID=10&rel=R3.0.0) [Google Scholar](https://scholar.google.com/scholar_lookup?title=Variations on algebra: Monadicity and generalisations of equational theories&publication_year=2002&author=E.P. Robinson)   
23. [[23]](https://www.sciencedirect.com/science/article/pii/S0022404908001485#bb23)   
    R.H. Street, R.F.C. Walters   
    Yoneda structures on 2-categories   
    Journal Algebra, 50 (1978), pp. 350-379   
    [View PDF](https://www.sciencedirect.com/science/article/pii/0021869378901606/pdfft?md5=62b16bd7b521474db7e69e47d51b843c&pid=1-s2.0-0021869378901606-main.pdf) [View article](https://www.sciencedirect.com/science/article/pii/0021869378901606) [View in Scopus](https://www.scopus.com/inward/record.url?eid=2-s2.0-0001102730&partnerID=10&rel=R3.0.0) [Google Scholar](https://scholar.google.com/scholar_lookup?title=Yoneda structures on 2-categories&publication_year=1978&author=R.H. Street&author=R.F.C. Walters)   
   
## Cited by (0)   
Copyright © 2008 Elsevier B.V. All rights reserved.   
## Recommended articles   
- A direct characterization of the monotone convergence space completion   
   
Topology and its Applications, Volume 230, 2017, pp. 99-104   
ZhongxiZhang, QingguoLi   
[View PDF](https://www.sciencedirect.com/science/article/pii/S0166864117303796/pdfft?md5=cdca210a9a3406648194ed826ca4c56c&pid=1-s2.0-S0166864117303796-main.pdf)   
- On the spectral sequence associated to a multicomplex   
   
Journal of Pure and Applied Algebra, Volume 224, Issue 2, 2020, pp. 528-535   
MurielLivernet, …, StephanieZiegenhagen   
- Behavioural semantics for asynchronous components   
   
Journal of Logical and Algebraic Methods in Programming, Volume 89, 2017, pp. 1-40   
R.Ameur-Boulifa, …, A.Savu   
[View PDF](https://www.sciencedirect.com/science/article/pii/S2352220817300287/pdfft?md5=678cb025ed2c15d6b6adfcaa56cfef07&pid=1-s2.0-S2352220817300287-main.pdf)   
Show 3 more articles   
## Article Metrics   
### Citations   
- Citation Indexes19   
   
### Captures   
- Mendeley Readers8   
   
### Mentions   
- Blog Mentions1   
![plumx-logo](files\plumx-logo.png)    
   
[View details](https://plu.mx/plum/a/?doi=10.1016/j.jpaa.2008.07.009&theme=plum-sciencedirect-theme&hideUsage=true)   
- [About ScienceDirect](https://www.elsevier.com/solutions/sciencedirect)   
- [Remote access](https://www.sciencedirect.com/user/institution/login?targetURL=/science/article/pii/S0022404908001485)   
- [Advertise](https://www.elsmediakits.com/)   
- [Contact and support](https://service.elsevier.com/app/contact/supporthub/sciencedirect/)   
- [Terms and conditions](https://www.elsevier.com/legal/elsevier-website-terms-and-conditions)   
- [Privacy policy](https://www.elsevier.com/legal/privacy-policy)   
   
Cookies are used by this site. Cookie settings   
All content on this site: Copyright © 2025 Elsevier B.V., its licensors, and contributors. All rights are reserved, including those for text and data mining, AI training, and similar technologies. For all open access content, the relevant licensing terms apply.   
![pendo-static-5661679399600128_storage_googleapis_com_image](files\pendo-static-5661679399600128_storage_googleap.svg)    
