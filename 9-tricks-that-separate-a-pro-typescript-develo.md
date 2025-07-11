# 9 tricks that separate a pro Typescript developer from an noob 😎   
[Skip to content](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#main-content)   
![resized_logo_UQww2soKuUsjaOGNB38o](files\resized_logo_uqww2sokuusjaognb38o.png)    
[Powered by  Algolia](https://www.algolia.com/developers/?utm_source=devto&utm_medium=referral)   
[Log in](https://dev.to/enter) [Create account](https://dev.to/enter?state=new-user)   
83Add reaction   
21Jump to Comments147SaveBoost   
![oqts452jc4b6o2ezvu89](files\oqts452jc4b6o2ezvu89.png)    
![941751d8-06b8-41bd-9de9-6ea056b7199f](files\941751d8-06b8-41bd-9de9-6ea056b7199f.png)    
[Tapajyoti Bose](https://dev.to/ruppysuppy)   
Posted on May 11   
![sparkle-heart-5f9bee3767e18deb1bb725290cb151c25234768a0e9a2bd39370c382d02920cf](files\sparkle-heart-5f9bee3767e18deb1bb725290cb151c2.svg)    
![multi-unicorn-b44d6f8c23cdd00964192bedc38af3e82463978aa611b4365bd33a0f1f4f3e97](files\multi-unicorn-b44d6f8c23cdd00964192bedc38af3e8.svg)    
![exploding-head-daceb38d627e6ae9b730f36a1e390fca556a4289d5a41abb2c35068ad3e2c4b5](files\exploding-head-daceb38d627e6ae9b730f36a1e390fc.svg)    
![raised-hands-74b2099fd66a39f2d7eed9305ee0f4553df0eb7b4f11b01b6b1b499973048fe5](files\raised-hands-74b2099fd66a39f2d7eed9305ee0f4553.svg)    
![fire-f60e7a582391810302117f987b22a8ef04a2fe0df7e3258a5f49332df1cec71e](files\fire-f60e7a582391810302117f987b22a8ef04a2fe0df.svg)    
657452   
# 9 tricks that separate a pro Typescript developer from an noob 😎   
[#programming](https://dev.to/t/programming) [#javascript](https://dev.to/t/javascript) [#typescript](https://dev.to/t/typescript) [#beginners](https://dev.to/t/beginners)   
**Typescript** is a must-know tool if you plan to master web development in 2025, regardless of whether it's for **Frontend** or **Backend** (or **Fullstack**). It's one of the best tools for avoiding the pitfalls of **JavaScript**, but it can be a bit overwhelming for beginners. Here are **9 tricks** that will kick start your journey from a noob to a pro **Typescript developer**!   
![78d4d5xi3uxrlpdnthst](files\78d4d5xi3uxrlpdnthst.gif)    
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#1-type-inference 1. Type inference   
Unlike what most beginners think, you don't need to define **types** for everything explicitly. **Typescript** is smart enough to infer the **data types** if you help narrow it down.   
```
// Basic cases
const a = false;  // auto-inferred to be a boolean
const b = true;   // auto-inferred to be a boolean
const c = a || b; // auto-inferred to be a boolean

// Niche cases in certain blocks
enum CounterActionType {
  Increment = "INCREMENT",
  IncrementBy = "INCREMENT_BY",
}

interface IncrementAction {
  type: CounterActionType.Increment;
}

interface IncrementByAction {
  type: CounterActionType.IncrementBy;
  payload: number;
}

type CounterAction = IncrementAction | IncrementByAction;

function reducer(state: number, action: CounterAction) {
  switch (action.type) {
    case CounterActionType.Increment:
      // TS infers that the action is IncrementAction
      // & has no payload when the code in this case is
      // being executed
      return state + 1;
    case CounterActionType.IncrementBy:
      // TS infers that the action is IncrementByAction
      // & has a number as a payload when the code in this
      // case is being executed
      return state + action.payload;
    default:
      return state;
 }
}


```
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#2-literal-types 2. Literal types   
When you need a variable to hold specific values, the `literal types` come in handy. Consider you are building a **native library** that informs the user about the **permissions status**, you could implement it as:   
```
type PermissionStatus = "granted" | "denied" | "undetermined";

const permissionStatus: PermissionStatus = "granted"; // ✅
const permissionStatus: PermissionStatus = "random";  // ❌


```
`Literal types` are not only limited to **strings**, but work for **numbers** and **booleans**, too.   
```
interface UnauthenticatedUser {
  isAuthenticated: false;
}

interface AuthenticatedUser {
  data: {
    /* ... */
 };
  isAuthenticated: true;
}

type User = UnauthenticatedUser | AuthenticatedUser;

const user: User = {
  isAuthenticated: false,
}; // ✅


```
NOTE: To make realistic examples above, we have used `Union` with the `literal types`. You can use them as standalone **types**, too, but that makes them somewhat redundant (act as a `type alias`)   
```
type UnauthenticatedUserData = null;


```
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#3-enums 3. Enums   
As **TypeScript** docs define them:   
> Enums are one of the few features TypeScript has which is not a type-level extension of JavaScript.   

Enums allow a developer to define a set of named constants. Using enums can make it easier to document intent or create a set of distinct cases. TypeScript provides both numeric and string-based enums.   
You can define `enums` as follows:   
```
enum PrivacyStatus {
  Public,
  Private,
}


```
As an added functionality, you can also define **string** or **number** `enums`:   
```
enum PrivacyStatus {
  Public = "public",
  Private = "private",
}


```
By default (if unspecified), enums are mapped to numbers starting from 0. Taking the example above, the default `enum` values would be:   
```
enum PrivacyStatus {
  Public,  // 0
  Private, // 1
}


```
But if we introduce a new enum value, the values of the existing `enums` will change too.   
```
enum PrivacyStatus {
  Public,     // 0
  OnlyWith,   // 1
  OnlyExcept, // 2
  Private,    // 3
}


```
If you plan to store the enum values in a **database**, it's strongly recommended to define the values each `enum` should map to, instead of leaving them with the default numbers - else it's easy to run into a plethora of bugs when the list of values the `enum` contains changes.   
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#4-type-guards 4. Type guards   
**Type guards** are a way to narrow down the type of a **variable**. They are functions that help you to check which **type** a value is at runtime.   
```
// continuing from the previous example
interface UnauthenticatedUser {
  isAuthenticated: false;
}

interface AuthenticatedUser {
  data: {
    /* ... */
 };
  isAuthenticated: true;
}

const isAuthenticatedUser = (user: User): user is AuthenticatedUser =>
  user.isAuthenticated;

if (isAuthenticatedUser(user)) {
  // user is AuthenticatedUser & you can access the data property
  console.log(user.data);
}


```
You can define custom **type guards** as per your requirement, but if you are lazy like me, try out [@rnw-community/shared](https://www.npmjs.com/package/@rnw-community/shared) for some commonly used type guards such as `isDefined`, `isEmptyString`, `isEmptyArray`, etc   
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#5-index-signatures-and-records 5. Index Signatures and Records   
When you have **dynamic keys** in an object, you can use an **index signature** or `Record` to define its type:   
```
enum PaticipationStatus {
  Joined = "JOINED",
  Left = "LEFT",
  Pending = "PENDING",
}

// Using index signature
interface ParticipantData {
 [id: string]: PaticipationStatus;
}

// Using Record
type ParticipantData = Record<string, PaticipationStatus>;

const participants: ParticipantData = {
  id1: PaticipationStatus.Joined,
  id2: PaticipationStatus.Left,
  id3: PaticipationStatus.Pending,
  // ...
};


```
NOTE: In the code above, you should use either **index signature** or `Record`, not both - having both in will cause an error saying `Duplicate identifier 'ParticipantData'`   
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#6-generics 6. Generics   
Occasionally, you may want to create a **function** or a **class** that is not restricted to work with multiple types of data. **Generics** allow you to handle just that. You can define a **generic type** by using angle brackets `<>`:   
```
const getJsonString = <T>(data: T) => JSON.stringify(data, null, 2);

// usage
getJsonString({ name: "John Doe" }); // ✅
getJsonString([1, 2, 3]);            // ✅
getJsonString("Hello World");        // ✅


```
**Generics** also allow you to define constraints on the type of data you want to work with. For example, if you want to create a **function** that only works with **objects with ids**, you can do it like this:   
```
const removeItemFromArray = <T extends { id: string }>(
  array: T[],
  id: string
) => {
  const index = array.findIndex((item) => item.id === id);
  if (index !== -1) {
    array.splice(index, 1);
 }
  return array;
};

// usage
removeItemFromArray(
 [
  { id: "1", name: "John Doe" },
  { id: "2", name: "Jane Doe" },
 ],
 "1"
);                                   // ✅
removeItemFromArray([1, 2, 3], "1"); // ❌


```
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#7-immutable-types 7. Immutable types   
**Immutable types** are a way to ensure that the data in your objects or arrays cannot be modified, thus you can prevent unintended side effects and make your code predictable and easy to debug.   
You can use `Readonly` and `ReadonlyArray` to enforce immutability.   
### https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#using-raw-readonly-endraw-for-objects Using Readonly for objects   
```
interface User {
  name: string;
  age: number;
}

const user: Readonly<User> = {
  name: "John Doe",
  age: 30,
};

user.name = "Jane Doe"; // ❌ Error: Cannot assign to 'name' because it is a read-only property


```
### https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#using-raw-readonlyarray-endraw-for-arrays Using ReadonlyArray for arrays   
```
const numbers: ReadonlyArray<number> = [1, 2, 3];

numbers.push(4); // ❌ Error: Property 'push' does not exist on type 'readonly number[]'
numbers[0] = 10; // ❌ Error: Index signature in type 'readonly number[]' only permits reading


```
### https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#deep-immutability Deep immutability   
If you need **deep immutability** (ensuring nested objects are also immutable), you can use libraries like `[deep-freeze](https://www.npmjs.com/package/deep-freeze)` or create custom utility types.   
```
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object ? DeepReadonly<T[P]> : T[P];
};

interface User {
  name: string;
  address: {
    city: string;
    country: string;
 };
}

const user: DeepReadonly<User> = {
  name: "John Doe",
  address: {
    city: "New York",
    country: "USA",
 },
};

user.address.city = "Los Angeles"; // ❌ Error: Cannot assign to 'city' because it is a read-only property


```
You can start off by making the **constant objects read only** - there are hundreds of utilities for using the **immutable pattern**, eg: allows you to use **time travel debugging** in `Redux`, but they would require a lot deeper dive to explain in detail.   
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#8-utility-types 8. Utility types   
**Typescript** introduces several utility types to generate **types** with niche characteristics using **pre-existing types**. These **utility types** are extremely useful when you need to generate a new type that is similar to an already existing type with minor alterations.   
1. `Pick`: Pick the necessary properties from an **object type**   
2. `Omit`: Pick all the properties from an **object type**, omitting the **selected keys**   
3. `Partial`: Make all properties of an **object type** optional   
4. `Required`: Make all properties of an **object type** required   
   
```
interface User {
  name: string;
  age?: number;
  email: string;
}

type PickUser = Pick<User, "name" | "age">;
type OmitUser = Omit<User, "age">;
type PartialUser = Partial<User>;
type RequiredUser = Required<User>;

// PickUser is equivalent to:
// interface PickUser {
//   name: string;
//   age?: number;
// }

// OmitUser is equivalent to:
// interface OmitUser {
//   name: string;
//   email: string;
// }

// PartialUser is equivalent to:
// interface PartialUser {
//   name?: string;
//   age?: number;
//   email?: string;
// }

// RequiredUser is equivalent to:
// interface RequiredUser {
//   name: string;
//   age: number;
//   email: string;
// }


```
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#9-union-amp-intersection-types 9. Union & Intersection types   
As already explored above, we can combine 2 or more **types** using `Union types` - `Union types` are defined using the `\|` operator and combine multiple **types** into a **single type**. This is useful when you want to create a type that can be one of multiple **different types**.   
```
interface UnauthenticatedUser {
  isAuthenticated: false;
}

interface AuthenticatedUser {
  data: {
    /* ... */
 };
  isAuthenticated: true;
}

// Union - user type allows both unauthenticated users and authenticated users to be stored
type User = UnauthenticatedUser | AuthenticatedUser;


```
The `intersection operator` uses the `&` operator and combines the **object types** into a **single type**.   
```
interface UserData {
  name: string;
  email: string;
  phoneNumber: string;
}

interface UserMetaData {
  lastSignedInAt: Date;
  signInLocation: {
    country: string;
    city: string;
 };
}

type UserFullData = UserData & UserMetaData;
// UserFullData is equivalent to:
// interface UserFullData {
//   name: string;
//   email: string;
//   phoneNumber: string;
//   lastSignedInAt: Date;
//   signInLocation: {
//     country: string;
//     city: string;
//   };


```
### https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#wrapping-up Wrapping up   
Now you know how pros use **TypeScript**! Give yourself a **pat on the back**.   
![0vy0xij3kt5frd7hld6m](files\0vy0xij3kt5frd7hld6m.gif)    
GIF   
**That's all folks! 🎉   
# https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#thanks-for-reading Thanks for reading   
Need a **Top Rated Software Development Freelancer** to chop away your development woes? Contact me on [Upwork](https://www.upwork.com/o/profiles/users/~01c12e516ee1d35044/)   
Want to see what I am working on? Check out my [Personal Website](https://tapajyoti-bose.vercel.app/) and [GitHub](https://github.com/ruppysuppy)   
Want to connect? Reach out to me on [LinkedIn](https://www.linkedin.com/in/tapajyoti-bose/)   
Follow my blogs for **bi-weekly new Tidbits** on [Medium](https://tapajyoti-bose.medium.com/)   
**FAQ**   
These are a few commonly asked questions I receive. So, I hope this **FAQ** section solves your issues.   
1. **I am a beginner, how should I learn Front-End Web Dev?**
Look into the following articles:   
2. [Front End Buzz words](https://tapajyoti-bose.medium.com/61-frontend-web-development-buzz-words-every-developer-should-have-in-their-vocabulary-8054e484875)   
3. [Front End Development Roadmap](https://dev.to/ruppysuppy/front-end-developer-roadmap-zero-to-hero-4pkf)   
4. [Front End Project Ideas](https://dev.to/ruppysuppy/5-projects-to-master-front-end-development-57p)   
5. [Transition from a Beginner to an Intermediate Frontend Developer](https://tapajyoti-bose.medium.com/7-tips-to-transition-from-a-beginner-to-an-intermediate-frontend-developer-dd58855724e2)   
6. **Would you mentor me?**   
   
Sorry, I am already under a heavy workload and do not have the time to mentor anyone.   
![9639a040-3c27-4b99-b65a-85e100016d3c](files\9639a040-3c27-4b99-b65a-85e100016d3c.png)    
[MongoDB](https://dev.to/mongodb) Promoted   
![57mGwHn](files\57mgwhn.png)    
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#django-mongodb-backend-quickstart-a-stepbystep-tutorialDjango MongoDB Backend Quickstart! A Step-by-Step Tutorial   
Get up and running with the new Django MongoDB Backend Python library! This tutorial covers creating a Django application, connecting it to MongoDB Atlas, performing CRUD operations, and configuring the Django admin for MongoDB.   
[Watch full video →](https://link.dev.to/ArhxFvr?bb=230872)   
## Top comments (21)   
Subscribe   
![8j7kvp660rqzt99zui8e](files\8j7kvp660rqzt99zui8e.png)    
![04e075eb-b089-4eb0-b83e-85a027b4360c](files\04e075eb-b089-4eb0-b83e-85a027b4360c.png)    
KC   
• [May 11](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2njj1)   
I've read from other articles that enums are to be avoided when it comes to data structuring in TypeScript. Lemme know if you have a different opinion. Overall, this is a well summarized reference and thanks for sharing.   
4 likesLikeReply   
![941751d8-06b8-41bd-9de9-6ea056b7199f](files\941751d8-06b8-41bd-9de9-6ea056b7199f.png)    
Tapajyoti Bose   
• [May 12](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2nkeb)   
Yeah as mentioned in the article, `enums` may break pre-existing data in the **DB** if new values are introduced (if you don't explicitly mention the mapped values). If you plan to use `enums`, mapping them to a specific value is a non-negotiable, but you can always use **constant objects**, mapping the same values.   
I personally prefer using `enums` & haven't run into any issues over the past 4 years apart from the **unmapped enums** being overwritten   
1 likeLikeReply   
![e06f206e-d5aa-428b-9034-e1f607c5a943](files\e06f206e-d5aa-428b-9034-e1f607c5a943.jpg)    
Ndeye Fatou Diop   
• [May 12](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2njlh)   
Great post! Clear and concise!
However I wouldn’t recommend enums since they have many issues. Best to combine an map with as const and create a type from it 🙏   
2 likesLikeReply   
![941751d8-06b8-41bd-9de9-6ea056b7199f](files\941751d8-06b8-41bd-9de9-6ea056b7199f.png)    
Tapajyoti Bose   
• [May 12• Edited on May 12](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2nkec)   
Check out the response to the [other comment](https://dev.to/ruppysuppy/comment/2nkeb)   
> Yeah as mentioned in the article, enums may break pre-existing data in the DB if new values are introduced (if you don't explicitly mention the mapped values). If you plan to use enums, mapping them to a specific value is a non-negotiable, but you can always use constant objects, mapping the same values.   

I personally prefer using `enums` & haven't run into any issues over the past 4 years apart from the **unmapped enums** being overwritten   
1 likeLikeReply   
![e06f206e-d5aa-428b-9034-e1f607c5a943](files\e06f206e-d5aa-428b-9034-e1f607c5a943.jpg)    
Ndeye Fatou Diop   
• [May 13](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2nkib)   
So that is not the only issue with enums. The other thing with enums is that you cannot pass a string (which is the same value as the enum): which is super annoying.   
```
enum Direction {
  Up = "up",
  Down = "down"
}

function move(direction: Direction) {
  console.log(direction);
}

move("down") // <- TS will complain here


```
1 likeLikeThread   
![941751d8-06b8-41bd-9de9-6ea056b7199f](files\941751d8-06b8-41bd-9de9-6ea056b7199f.png)    
Tapajyoti Bose   
• [May 13• Edited on May 13](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2nlb7)   
You can write your own helper to convert the string to enum:   
```
function toDirection(value: string): Direction {
  if (!Object.values(Direction).includes(value as Direction)) {
    throw Error("Invalid direction")
  }
  return value as Direction;
}


```
Personally use [zod](https://zod.dev/?id=native-enums) to tackle the transformations & handle all **Document**/**DTO schemas**   
1 likeLikeReply   
![c908a3a0-ae27-476e-be7c-40c626d72eab](files\c908a3a0-ae27-476e-be7c-40c626d72eab.jpg)    
Deividas Strole   
• [May 11](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2njkn)   
Fantastic article! It really highlights how powerful TypeScript is for catching errors early, improving code readability, and making large-scale projects easier to manage. Once you get used to it, it's hard to go back to plain JavaScript.   
4 likesLikeReply   
![6dd8d6a7-976d-4b0a-8762-74d27f5b9cdd](files\6dd8d6a7-976d-4b0a-8762-74d27f5b9cdd.JPG)    
HexShift   
• [May 12](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2njli)   
Nice! The tips on type inference and literal types are particularly handy, and I appreciate how you've broken things down into easily digestible bits that both newcomers and seasoned devs can benefit from. Shows just how much you can optimize your code with just a few key tricks. One suggestion I’d throw out there is a quick mention of using TypeScript with async/await patterns and sometimes the type inference can be a bit tricky there, so a little guidance would be useful.   
2 likesLikeReply   
![941751d8-06b8-41bd-9de9-6ea056b7199f](files\941751d8-06b8-41bd-9de9-6ea056b7199f.png)    
Tapajyoti Bose   
• [May 12](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2nke6)   
The only difference is when using `async`, the return type gets wrapped by a `Promise`   
```
const asyncFn = async () => 0; // type AsyncFn = () => Promise<number>


```
When you `await`, the `Promise` gets unwrapped & you can use it as a regular value   
```
const func = async () => {
  const result = await asyncFn(); // type Result = number
}


```
Everything else mentioned in the article still holds true regardless whether you are using `Promises` or not   
2 likesLikeReply   
![5d291561-0d60-40cf-a9d3-959dab08f1ac](files\5d291561-0d60-40cf-a9d3-959dab08f1ac.png)    
![subscription-icon-805dfa7ac7dd660f07ed8d654877270825b07a92a03841aa99a1093bd00431b2](files\subscription-icon-805dfa7ac7dd660f07ed8d654877.png)    
Nevo David    
• [May 11](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2njgf)   
Insane, this makes TypeScript feel way less scary tbh. The index signature vs Record part always trips me up lol.   
2 likesLikeReply   
![9be38b1c-9c74-4483-a9f3-fc6d624006c4](files\9be38b1c-9c74-4483-a9f3-fc6d624006c4.png)    
Evgenii Kalkutin   
• [May 12](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2nke2)   
Are you even sleeping lol? I see you in almost every post. or you're bot haha?   
1 likeLikeReply   
![64ee68ee-0e2b-4a6f-b2ca-4c4500a27888](files\64ee68ee-0e2b-4a6f-b2ca-4c4500a27888.png)    
GeorgeLake   
• [May 12](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2njof)   
This was such a fun and insightful read! I love how the tips cut through the fluff and really emphasize habits that elevate code quality—like mastering utility types, using discriminated unions, and embracing type inference wisely. It's kind of like when you switch from watching random content to curated lists on [PlayPelis APK](https://playpeliss.app/)—suddenly, the experience feels smarter and more intentional. Have you found one of these TypeScript tricks that gave you that big "aha!" moment in your own projects?   
2 likesLikeReply   
![941751d8-06b8-41bd-9de9-6ea056b7199f](files\941751d8-06b8-41bd-9de9-6ea056b7199f.png)    
Tapajyoti Bose   
• [May 12](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2nke3)   
Definitely literal types & using type guards - it insanely simplified handling types   
1 likeLikeReply   
![d6d33f8b-41a1-4ba4-87ea-2cb29935bf39](files\d6d33f8b-41a1-4ba4-87ea-2cb29935bf39.jpeg)    
Niclas   
• [May 11](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2njh7)   
Good list, although I do not use the TS-enum but other alternate structures for it.   
2 likesLikeReply   
![e6629bb7-d446-4f40-b537-d2fa664bf5fd](files\e6629bb7-d446-4f40-b537-d2fa664bf5fd.jpg)    
Nathan Tarbert   
• [May 13](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2nkid)   
no joke, posts like this are what got me through my first typescript repo   
2 likesLikeReply   
![bb5768ab-160b-41c4-a1b3-162fc7e5933f](files\bb5768ab-160b-41c4-a1b3-162fc7e5933f.jpg)    
George Gardiakos   
• [May 12](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2nk11)   
This awesome. I use some of these but not all. Thank you!   
2 likesLikeReply   
![a8c0bdbc-efcf-4f71-b2ee-8d6452ebea6d](files\a8c0bdbc-efcf-4f71-b2ee-8d6452ebea6d.png)    
Werliton Silva   
• [May 12](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#comment-2nk24)   
I like it   
2 likesLikeReply   
[View full discussion (21 comments)](https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd/comments)   
[Code of Conduct](https://dev.to/code-of-conduct)• [Report abuse](https://dev.to/report-abuse)   
![9639a040-3c27-4b99-b65a-85e100016d3c](files\9639a040-3c27-4b99-b65a-85e100016d3c.png)    
[MongoDB](https://dev.to/mongodb) Promoted   
![2WTcrk2](files\2wtcrk2.png)    
## https://dev.to/ruppysuppy/9-tricks-that-separate-a-pro-typescript-developer-from-an-noob-jpd#django-mongodb-backend-quickstart-a-stepbystep-tutorialDjango MongoDB Backend Quickstart! A Step-by-Step Tutorial   
Get up and running with the new Django MongoDB Backend Python library! This tutorial covers creating a Django application, connecting it to MongoDB Atlas, performing CRUD operations, and configuring the Django admin for MongoDB.   
[Watch full video →](https://link.dev.to/ArhxFvr?bb=230870)   
![941751d8-06b8-41bd-9de9-6ea056b7199f](files\941751d8-06b8-41bd-9de9-6ea056b7199f_g.png)    
[Tapajyoti Bose](https://dev.to/ruppysuppy)   
Follow   
Top Rated Freelancer \|\| Blogger \|\| Cross-Platform App Developer \|\| Web Developer \|\| Open Source Contributor   
- Location   
   
Kolkata, West Bengal, India   
- Joined   
   
Dec 3, 2020   
