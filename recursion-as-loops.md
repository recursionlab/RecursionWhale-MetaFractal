# Recursion as Loops   
[Write](https://medium.com/new-story?source=post_page---top_nav_layout_nav-----------------------------------------)   
[https://medium.com/me/notifications?source=post\_page---top\_nav\_layout\_nav-----------------------------------------](https://medium.com/me/notifications?source=post_page---top_nav_layout_nav-----------------------------------------)   
![1*rZloFMQOxuS-QQBX8GRKtA](files\1-rzlofmqoxus-qqbx8grkta.jpg)    
Get unlimited access to the best of Medium for less than $1/week.
[Become a member](https://medium.com/plans?source=upgrade_membership---post_top_nav_upsell-----------------------------------------)   
# Recursion as Loops   
![0*UjtgKnXbzz-GNwFy](files\0-ujtgknxbzz-gnwfy_b.png)    
[Computing Macroxela](https://medium.com/@compuxela?source=post_page---byline--920625db2123---------------------------------------)   
Follow   
8 min read   
·   
Apr 30, 2025   
22   
[https://medium.com/plans?dimension=post\_audio\_button&postId=920625db2123&source=upgrade\_membership---post\_audio\_button-----------------------------------------](https://medium.com/plans?dimension=post_audio_button&postId=920625db2123&source=upgrade_membership---post_audio_button-----------------------------------------)   
![0*hqKK8cYYUODLcgAC](files\0-hqkk8cyyuodlcgac.png)    
Recursion can be one of the harder topics to learn or teach. It seems so different from other topics and requires good abstraction skills. It’s also fundamental for other topics like sorting and dynamic programming so we can’t just gloss over it. How can we approach recursion?   
Something that tends to be forgotten about recursion is that recursion is just a type of loop. Any recursion problem can be turned into a loop using a call stack and any iteration can be turned into a recursive function using tail recursion. Both can carry out the same tasks (albeit with different runtimes). Oftentimes, people will get stuck on how the loop works instead of the actual recursion. If we understand how the loop works for a problem, it will be easier to implement in a recursive function. Let’s look at the palindrome problem as an example.   
A palindrome is a string which is identical backwards. The word “kayak” is a palindrome but “programming” is not. Below we have two ways of implementing a function that determines if a word is a palindrome or not.   
**Iterative**   
```
static boolean palindrome(String pal)
{
  for(int i = 0; i < pal.length()/2; i++)
  {
    if(pal.charAt(i) != pal.charAt(pal.length() - i - 1))
    {
      return false;
    }
  }
  return true;
}

```
**Recursive**   
```
static boolean palindrome(String pal)
{
  if(pal.length() == 1 || pal.length() == 0)
  {
    return true;
  }
  if(pal.charAt(0) == pal.charAt(pal.length() - 1))
  {
    return palindrome(pal.substring(1, pal.length() - 1));
  }
  return false;
}

```
The loop version is quite straightforward. It starts by comparing the first and last letters. If they are not the same, it returns false since the word is not a palindrome. If they are equal, it moves on to the second and second to last letters. This process keeps repeating itself until it finds letters that don’t match or reaches the center of the string, exiting the loop and returning true since all letters matched.   
The recursive version does the same thing but with a few tweaks. It has a base case for the trivial cases (when the word has a single letter or no letters are provided). The second conditional still compares the first and last letters. This time though, it recursively calls itself if they are the same by passing a smaller version of the word (first and last letter removed). Otherwise, it exits the conditional and returns false because the word is not a palindrome.   
The loop version did not require the base case and checks if the letters are not equal instead of checking if they are equal but otherwise both of them do the exact same thing. We could create the recursive version by simply modifying the loop version. Why is that? Because all recursive problems can be solved using loops. Recursion is a loop, just a different version. Let’s delve a little deeper into recursion to see why it is a loop.   
Recursion needs the following for it to work properly:   
- Subproblems that can be used to solve the current problem. In other words, we can use smaller versions of the problem to solve bigger versions of it.   
- Calling itself with a smaller input or problem size. It needs to reduce the size of the problem/input then call itself with said changed problem/input.   
- A base case to stop the recursion from going on infinitely.   
   
For a loop to work we need the following:   
- A stopping condition to prevent infinite loops   
- Some computation or task to be repeated   
   
If we compare both of them, we can see that recursion does exactly what a loop does except in a more specific way. Recursion needs a stopping condition (base cases) and some computation or task to be repeated (recursive call). But wait, if we look at the recursive version of the function above, we only call it recursively once. So technically it doesn’t repeat the computation, doesn’t it? It does albeit in a less direct form than regular loops. Loops explicitly show what instructions are repeated. Recursion does this indirectly. Let’s use the previous recursive function with an input of “abcba” to see why.   
\*\*Iteration 1:\*\**palindrome(“abcba”)*   
This iteration skips the base case since the input is not a single letter or empty. Hence it moves to the comparison. Is the first letter the same as the last letter? It is so we make a recursive call while removing the compared letters,   
*palindrome(“abcba”) = palindrome(“bcb”)*   
\*\*Iteration 2:\*\**palindrome(“bcb”)*   
The input has more than one letter so we carry out the comparison of the first and last letters. Both of them are b so we carry out the recursive call and remove said letters,   
*palindrome(“bcb”) = palindrome(“c”)*   
**Iteration 3:**   
The input has only 1 letter so we default to the base case, returning true.   
*palindrome(“c”) = true*   
Let’s compile all the recursive calls below.   
*palindrome(“abcba”) = palindrome(“bcb”)
palindrome(“bcb”) = palindrome(“c”)
palindrome(“c”) = true*   
As we can see, the recursive call did happen multiple times. It kept happening until we reached the stopping condition. This is exactly what a loop does. It repeats itself until reaching a stopping condition. In this case, the recursive call is the repeated computation. Hence, recursion satisfies both conditions for a loop to work.   
Why do we not use just recursion or just loops? Why have different ways of solving the same problem? This is a good question to ask, similar to when to use for loops and when to use while loops. The basic gist is some problems are easier to solve and implement using recursion. For example, can you think of a loop that can traverse a binary tree? It’s possible but tricky to implement. Much easier to use a recursive solution since both children of any node can be considered smaller binary trees. Then we only have to focus on the base cases. Other problems are easier to solve and implement using traditional loops. Not just for us humans but also for computers. When used improperly, recursion can lead to problems like [stack overflow](https://en.wikipedia.org/wiki/Stack_overflow). It’s good to have various options to solve problems or we can run into problems. Like the old saying goes, if we only have a hammer, we will see everything as a nail.   
Knowing that all recursive problems can be solved using traditional loops, we can use that knowledge to help us develop recursive solutions. One way of doing this is to come up with an iterative solution then converting it to a recursive one. Not all problems are easy to apply this to but a sufficient amount of them are that it’s useful to know how to convert from one to the other. Looking at the above examples, we can see that both versions carry out similar comparisons (check the first and last letters), iterate in the same way (both remove/ignore the compared letters and move on to the following ones), and have the same stopping conditions (find letters that don’t match or reach the middle of the string). This leads us to the following guidelines for transforming a loop into a recursive solution.   
- The stopping condition in the loop will be a base case in your recursive solution. We may have to tweak it a bit or add another one but they are fundamentally the same   
- Any modifications to values or return statements in the loop will be incorporated in the recursive calls. Like the previous point, it may require a few tweaks but the changes to any variables or values should remain the same.   
   
Let’s try this out on another problem, factorials. A factorial is simply the product of the current number and all of the previous numbers. The loop version is shown below.   
```
static int factorial(int number)
{
  int factor = 1;
  for(int i = number; i > 0; i--)
  {
    factor = factor*i;
  }
  return factor;
}

```
Using the guidelines above we can determine the following:   
- The stopping condition for the loop is when i reaches 0. That means our base case will be when the input is 0. We also need to consider negative inputs since the loop automatically does this with the greater than comparison. This results in the following code:   
   
```
//Base Case (stopping condition)
if(number <= 0)
{
  return 1;
}

```
- The variable factor is being multiplied by the current number every iteration. This means we need to use such multiplication in our recursive call. The current number is the input while variable factor can be represented by the recursive call. Keeping in mind the rules of recursion, we need to reduce the input in the recursive call. This results in the following code:   
   
```
return number*factorial(number - 1);

```
The final code winds up being this:   
```
static int factorial(int number)
{
  if(number <= 0)
  {
    return 1;
  }
  return number*factorial(number - 1);
}

```
We just turned a loop into a recursive function!! Now, had we used a different loop it might have been harder to transform it (like starting i at 0 and counting up instead of down) but still possible.   
How about the other way around? How do we turn a recursive solution into one using loops? It can actually be easier to transform a recursive solution into an iterative one. We need to follow some similar guidelines as the previous ones.   
- The base cases will be our stopping conditions. It may need some modifications but base cases will tell us when the loop should stop. If we have multiple base cases, we will probably use a while loop. If we have a single base case, a for loop might be sufficient.   
- Any work carried out outside of the recursive calls won’t change much, we can simply move it inside the loop we will use.   
- The recursive call needs to be transformed into what will keep the iteration going. It is in the recursive call where we reduce the problem size so we have to do the same in the loop.   
   
So what does this look like? Let’s apply the guidelines to the code below.   
```
static int ListSum(List<Integer> numbers)
{
  if(numbers.size() == 0)
    return 0;
  return numbers.get(0) + ListSum(numbers.subList(1, numbers.size()));
}

```
This code is rather simple, it takes a list and recursively computes the total sum. Let’s follow the previously mentioned guidelines to our recursive function.   
- The base case happens when the size of the list is 0 i.e. an empty list. In a loop, we probably won’t be working with an empty list but we still need to stop so our stopping condition can be when our counter reaches 0.   
   
```
while(count >= 0)

```
- The work being done outside the recursive call is adding the current element to the result of the recursive call. This means we need some variable to keep track of the sum.   
   
```
total += numbers.get(count);

```
- The recursive call is what drives the computation. In this case, the list is shrunk by one element. We can do something akin to this by reducing our counter at every iteration. This means that we should start our counter at the largest possible value, the size of the list, so we can access all of the elements.   
   
```
int count = numbers.size() - 1;
…
count––;

```
Combining everything in the previous points, the final iterative code winds up being the following:   
```
static int ListSum(List<Integer> numbers)
{
        int count = numbers.size() - 1;
        int total = 0;
        while(count >= 0){
            total += numbers.get(count);
            count--;
        }
        return total;
}

```
Looking at this you might think that it would be better to implement it using a for loop. And you would be right. However, we still managed to transform a recursive solution into an iterative one. Once we have performed such a conversion, we can refactor the iterative implementation however we want without as many problems since we already achieved the bulk of the work.   
We don’t have to find the loop solution to a problem before attempting the recursive one. Like I previously mentioned, some problems are easier to solve recursively than iteratively. However, it is a good start to get our mental gears grinding if we are stuck. It helps us see how recursion is fundamentally just another type of loop and thus we can use many of the same strategies we use with loops in recursion.   
[Recursion](https://medium.com/tag/recursion?source=post_page-----920625db2123---------------------------------------)   
[Programming](https://medium.com/tag/programming?source=post_page-----920625db2123---------------------------------------)   
[Computer Science](https://medium.com/tag/computer-science?source=post_page-----920625db2123---------------------------------------)   
[Loop](https://medium.com/tag/loop?source=post_page-----920625db2123---------------------------------------)   
[Coding](https://medium.com/tag/coding?source=post_page-----920625db2123---------------------------------------)   
22   
![0*UjtgKnXbzz-GNwFy](files\0-ujtgknxbzz-gnwfy_b.png)    
**[Written by Computing Macroxela](https://medium.com/@compuxela?source=post_page---post_author_info--920625db2123---------------------------------------)**   
[13 followers](https://medium.com/@compuxela/followers?source=post_page---post_author_info--920625db2123---------------------------------------)   
· [9 following](https://medium.com/@compuxela/following?source=post_page---post_author_info--920625db2123---------------------------------------)   
Computer Science teacher, programmer, and algorithm enthusiast who loves explaining interesting computer science topics   
Follow   
## No responses yet   
[https://policy.medium.com/medium-rules-30e5502c4eb4?source=post\_page---post\_responses--920625db2123---------------------------------------](https://policy.medium.com/medium-rules-30e5502c4eb4?source=post_page---post_responses--920625db2123---------------------------------------)   
![1*rZloFMQOxuS-QQBX8GRKtA](files\1-rzlofmqoxus-qqbx8grkta_z.jpg)    
Koryogden   
What are your thoughts?   
Cancel   
Respond   
## More from Computing Macroxela   
![0*xotyLmVAmz_JkK8M](files\0-xotylmvamz_jkk8m.jpg)    
![0*UjtgKnXbzz-GNwFy](files\0-ujtgknxbzz-gnwfy.png)    
[Computing Macroxela](https://medium.com/@compuxela?source=post_page---author_recirc--920625db2123----0---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)   
**What is Decrease and Conquer?**   
**[Try solving the following problem](https://medium.com/@compuxela/what-is-decrease-and-conquer-797fae33d0e0?source=post_page---author_recirc--920625db2123----0---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)**   
Nov 27, 2024   
[1](https://medium.com/@compuxela/what-is-decrease-and-conquer-797fae33d0e0?source=post_page---author_recirc--920625db2123----0---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)   
![0*b4gl4k0n-zEOJkAO](files\0-b4gl4k0n-zeojkao.jpg)    
![0*UjtgKnXbzz-GNwFy](files\0-ujtgknxbzz-gnwfy.png)    
[Computing Macroxela](https://medium.com/@compuxela?source=post_page---author_recirc--920625db2123----1---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)   
**Horner’s Method for Polynomial Evaluation**   
**[Assuming we have the expression below, how would you evaluate it for a value of x = 2?](https://medium.com/@compuxela/horners-method-for-polynomial-evaluation-a13dbf9749a2?source=post_page---author_recirc--920625db2123----1---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)**   
Oct 30, 2024   
[https://medium.com/@compuxela/horners-method-for-polynomial-evaluation-a13dbf9749a2?source=post\_page---author\_recirc--920625db2123----1---------------------bebbf1c4\_2f4f\_4eb1\_8f4e\_e3c25b8a9645--------------](https://medium.com/@compuxela/horners-method-for-polynomial-evaluation-a13dbf9749a2?source=post_page---author_recirc--920625db2123----1---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)   
![0*TwHOf4cji4X_fHNR](files\0-twhof4cji4x_fhnr.jpg)    
![0*UjtgKnXbzz-GNwFy](files\0-ujtgknxbzz-gnwfy.png)    
[Computing Macroxela](https://medium.com/@compuxela?source=post_page---author_recirc--920625db2123----2---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)   
**Karatsuba Algorithm: Fast Integer Multiplication**   
**[If you have taken any math class, chances are you know how to multiply. At least know how to multiply two numbers. When we have 23 x 12 it…](https://medium.com/@compuxela/karatsuba-algorithm-fast-integer-multiplication-18c94cea5701?source=post_page---author_recirc--920625db2123----2---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)**   
Oct 23, 2024   
[https://medium.com/@compuxela/karatsuba-algorithm-fast-integer-multiplication-18c94cea5701?source=post\_page---author\_recirc--920625db2123----2---------------------bebbf1c4\_2f4f\_4eb1\_8f4e\_e3c25b8a9645--------------](https://medium.com/@compuxela/karatsuba-algorithm-fast-integer-multiplication-18c94cea5701?source=post_page---author_recirc--920625db2123----2---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)   
![0*RSO1MVUyRFUkkuay](files\0-rso1mvuyrfukkuay.png)    
![0*UjtgKnXbzz-GNwFy](files\0-ujtgknxbzz-gnwfy.png)    
[Computing Macroxela](https://medium.com/@compuxela?source=post_page---author_recirc--920625db2123----3---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)   
**How Does the Fast Fourier Transform Work?**   
**[Few algorithms have had as much of an impact as the Fourier transform. It is considered one of the most important algorithms to have been…](https://medium.com/@compuxela/how-does-the-fast-fourier-transform-work-abf9f2db0e54?source=post_page---author_recirc--920625db2123----3---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)**   
Jan 8   
[https://medium.com/@compuxela/how-does-the-fast-fourier-transform-work-abf9f2db0e54?source=post\_page---author\_recirc--920625db2123----3---------------------bebbf1c4\_2f4f\_4eb1\_8f4e\_e3c25b8a9645--------------](https://medium.com/@compuxela/how-does-the-fast-fourier-transform-work-abf9f2db0e54?source=post_page---author_recirc--920625db2123----3---------------------bebbf1c4_2f4f_4eb1_8f4e_e3c25b8a9645--------------)   
[See all from Computing Macroxela](https://medium.com/@compuxela?source=post_page---author_recirc--920625db2123---------------------------------------)   
## Recommended from Medium   
![1*cInLdmkZOo8a3ISs7tfTvw](files\1-cinldmkzoo8a3iss7tftvw.png)    
![1*mVClgMoX1CgU2whKZ6Xm4A](files\1-mvclgmox1cgu2whkz6xm4a.jpg)    
[Zyad Amr](https://medium.com/@zyadamr.?source=post_page---read_next_recirc--920625db2123----0---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
**Promisifying in JavaScript**   
**[JavaScript is a vast world, and at first glance, it can sometimes seem mysterious. But as you dive deeper, you’ll realize that your initial…](https://medium.com/@zyadamr./promisifying-in-javascript-012f6335b459?source=post_page---read_next_recirc--920625db2123----0---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)**   
Jan 29   
[50](https://medium.com/@zyadamr./promisifying-in-javascript-012f6335b459?source=post_page---read_next_recirc--920625db2123----0---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
![1*KRcAOOHJkpvQsq80O0ADmA](files\1-krcaoohjkpvqsq80o0adma.png)    
![1*yUNfohs9jA6GCDmyCYJTvA@2x](files\1-yunfohs9ja6gcdmycyjtvaat2x.png)    
In   
[JavaScript in Plain English](https://medium.com/javascript-in-plain-english?source=post_page---read_next_recirc--920625db2123----1---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
by   
[Rahul Kaklotar](https://medium.com/@kaklotarrahul79?source=post_page---read_next_recirc--920625db2123----1---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
**Goodbye, Object Oriented Programming**   
**[Understanding OOP: Beyond Classes](https://medium.com/javascript-in-plain-english/goodbye-object-oriented-programming-42d1d88df5de?source=post_page---read_next_recirc--920625db2123----1---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)**   
May 4   
125   
[2](https://medium.com/javascript-in-plain-english/goodbye-object-oriented-programming-42d1d88df5de?source=post_page---read_next_recirc--920625db2123----1---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
![0*E6zeLmIjDjqxakkp](files\0-e6zelmijdjqxakkp.jpg)    
![1*GaJKIwMbuGzom2KJzJESFw](files\1-gajkiwmbugzom2kjzjesfw.png)    
In   
[Science Spectrum](https://medium.com/science-spectrum?source=post_page---read_next_recirc--920625db2123----0---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
by   
[Cole Frederick](https://medium.com/@colefp?source=post_page---read_next_recirc--920625db2123----0---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
**The Coconut Problem**   
**[Martin Gardner’s Favorite Math Puzzle](https://medium.com/science-spectrum/the-coconut-problem-7364e392dd0c?source=post_page---read_next_recirc--920625db2123----0---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)**   
4d ago   
512   
[6](https://medium.com/science-spectrum/the-coconut-problem-7364e392dd0c?source=post_page---read_next_recirc--920625db2123----0---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
![0*MuJ7RURtHkjhb5qg](files\0-muj7rurthkjhb5qg.png)    
![1*aI1Al1SotX9itY-JTg4nQQ](files\1-ai1al1sotx9ity-jtg4nqq.jpg)    
[Shubhamkumarcode](https://medium.com/@shubhamkumarcode?source=post_page---read_next_recirc--920625db2123----1---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
**14 LeetCode Patterns to Solve Any Question**   
**[The only 14 patterns you’ll ever need to master LeetCode Interviews!](https://medium.com/@shubhamkumarcode/14-leetcode-patterns-to-solve-any-question-1dcdcc650bfa?source=post_page---read_next_recirc--920625db2123----1---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)**   
Jan 27   
16   
[1](https://medium.com/@shubhamkumarcode/14-leetcode-patterns-to-solve-any-question-1dcdcc650bfa?source=post_page---read_next_recirc--920625db2123----1---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
![1*YE1-SFBpCbwOkGpnm8RR4A](files\1-ye1-sfbpcbwokgpnm8rr4a.jpg)    
![1*gAy-r-fvT5gUqWi12La1Ww](files\1-gay-r-fvt5guqwi12la1ww.png)    
In   
[Physics In History](https://medium.com/physics-in-history?source=post_page---read_next_recirc--920625db2123----2---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
by   
[Sunny Labh](https://medium.com/@piggsboson?source=post_page---read_next_recirc--920625db2123----2---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
**The Book That Feynman Used to Teach Himself Calculus**   
**[A brief look at the self-taught foundations of 20th century Physics genius](https://medium.com/physics-in-history/the-book-that-feynman-used-to-teach-himself-calculus-63b3ba2c743d?source=post_page---read_next_recirc--920625db2123----2---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)**   
6d ago   
448   
[5](https://medium.com/physics-in-history/the-book-that-feynman-used-to-teach-himself-calculus-63b3ba2c743d?source=post_page---read_next_recirc--920625db2123----2---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
![1*f-1HQQng85tbA7kwgECqoQ](files\1-f-1hqqng85tba7kwgecqoq.png)    
![1*ViyWUoh4zqx294no1eENxw](files\1-viywuoh4zqx294no1eenxw.png)    
In   
[Coding Beauty](https://medium.com/coding-beauty?source=post_page---read_next_recirc--920625db2123----3---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
by   
[Tari Ibaba](https://medium.com/@tariibaba?source=post_page---read_next_recirc--920625db2123----3---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
**This new IDE from Google is an absolute game changer**   
**[This new IDE from Google is seriously revolutionary.](https://medium.com/coding-beauty/new-google-project-idx-fae1fdd079c7?source=post_page---read_next_recirc--920625db2123----3---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)**   
Mar 11   
5.5K   
[326](https://medium.com/coding-beauty/new-google-project-idx-fae1fdd079c7?source=post_page---read_next_recirc--920625db2123----3---------------------99a5c452_61aa_4b83_b744_852a9c4ce95f--------------)   
[See more recommendations](https://medium.com/?source=post_page---read_next_recirc--920625db2123---------------------------------------)   
[Help](https://help.medium.com/hc/en-us?source=post_page-----920625db2123---------------------------------------)   
[Status](https://medium.statuspage.io/?source=post_page-----920625db2123---------------------------------------)   
[About](https://medium.com/about?autoplay=1&source=post_page-----920625db2123---------------------------------------)   
[Careers](https://medium.com/jobs-at-medium/work-at-medium-959d1a85284e?source=post_page-----920625db2123---------------------------------------)   
[Press](mailto:pressinquiries@medium.com)   
[Blog](https://blog.medium.com/?source=post_page-----920625db2123---------------------------------------)   
[Privacy](https://policy.medium.com/medium-privacy-policy-f03bf92035c9?source=post_page-----920625db2123---------------------------------------)   
[Rules](https://policy.medium.com/medium-rules-30e5502c4eb4?source=post_page-----920625db2123---------------------------------------)   
[Terms](https://policy.medium.com/medium-terms-of-service-9db0094a1e0f?source=post_page-----920625db2123---------------------------------------)   
[Text to speech](https://speechify.com/medium?source=post_page-----920625db2123---------------------------------------)   
