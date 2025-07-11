Zod specs
Introduction
Sponsors
Ecosystem
Installation
Requirements
From npm
Basic usage
Primitives
Coercion for primitives
Literals
Strings
Datetimes
Dates
Times
IP addresses
IP ranges (CIDR)
Numbers
BigInts
NaNs
Booleans
Dates
Zod enums
Native enums
Optionals
Nullables
Objects
.shape
.keyof
.extend
.merge
.pick/.omit
.partial
.deepPartial
.required
.passthrough
.strict
.strip
.catchall
Arrays
.element
.nonempty
.min/.max/.length
Tuples
Unions
Discriminated unions
Records
Maps
Sets
Intersections
Recursive types
ZodType with ZodEffects
JSON type
Cyclical objects
Promises
Instanceof
Functions
Preprocess
Custom schemas
Schema methods
.parse
.parseAsync
.safeParse
.safeParseAsync
.refine
.superRefine
.transform
.default
.describe
.catch
.optional
.nullable
.nullish
.array
.promise
.or
.and
.brand
.readonly
.pipe
Guides and concepts
Type inference
Writing generic functions
Error handling
Error formatting
Comparison
Joi
Yup
io-ts
Runtypes
Ow
Changelog

Zod logo

Zod
zod.dev
TypeScript-first schema validation with static type inference

Zod CI status Created by Colin McDonnell License npm stars

Website   •   Discord   •   𝕏   •   Bluesky



Zod 4 is now in beta!
Read the announcement 👉



Featured sponsor: Stainless
stainless logo
Learn more about featured sponsorships



## Table of contents
These docs have been translated into Chinese and Korean.

Table of contents
Introduction
Sponsors
Platinum
Gold
Silver
Bronze
Copper
Ecosystem
Resources
API libraries
Form integrations
Zod to X
X to Zod
Mocking
Powered by Zod
Utilities for Zod
Installation
Requirements
From npm
Basic usage
Primitives
Coercion for primitives
Literals
Strings
Datetimes
Dates
Times
IP addresses
IP ranges
Numbers
BigInts
NaNs
Booleans
Dates
Zod enums
Native enums
Optionals
Nullables
Objects
.shape
.keyof
.extend
.merge
.pick/.omit
.partial
.deepPartial
.required
.passthrough
.strict
.strip
.catchall
Arrays
.element
.nonempty
.min/.max/.length
Tuples
Unions
Discriminated unions
Records
Record key type
Maps
Sets
Intersections
Recursive types
ZodType with ZodEffects
JSON type
Cyclical objects
Promises
Instanceof
Functions
Preprocess
Custom schemas
Schema methods
.parse
.parseAsync
.safeParse
.safeParseAsync
.refine
Arguments
Customize error path
Asynchronous refinements
Relationship to transforms
.superRefine
Abort early
Type refinements
.transform
Chaining order
Validating during transform
Relationship to refinements
Async transforms
.default
.describe
.catch
.optional
.nullable
.nullish
.array
.promise
.or
.and
.brand
.readonly
.pipe
You can use .pipe() to fix common issues with z.coerce.
Guides and concepts
Type inference
Writing generic functions
Constraining allowable inputs
Error handling
Error formatting
Comparison
Joi
Yup
io-ts
Runtypes
Ow
Changelog

Introduction
Zod is a TypeScript-first schema declaration and validation library. I'm using the term "schema" to broadly refer to any data type, from a simple string to a complex nested object.

Zod is designed to be as developer-friendly as possible. The goal is to eliminate duplicative type declarations. With Zod, you declare a validator once and Zod will automatically infer the static TypeScript type. It's easy to compose simpler types into complex data structures.

Some other great aspects:

Zero dependencies
Works in Node.js and all modern browsers
Tiny: 8kb minified + zipped
Immutable: methods (e.g. .optional()) return a new instance
Concise, chainable interface
Functional approach: parse, don't validate
Works with plain JavaScript too! You don't need to use TypeScript.

Sponsors
Sponsorship at any level is appreciated and encouraged. If you built a paid product using Zod, consider one of the corporate tiers.


Platinum
CodeRabbit logo
Cut code review time & bugs in half
coderabbit.ai


Gold
Courier logo
The API platform for sending notifications
courier.com

LibLab
Generate better SDKs for your APIs
liblab.com

Neon
Serverless Postgres — Ship faster
neon.tech

Retool
Build AI apps and workflows with Retool AI
retool.com

stainless
Generate best-in-class SDKs
stainless.com

speakeasy
SDKs & Terraform providers for your API
speakeasy.com


Silver
Nitric
Nitric	PropelAuth
PropelAuth	Cerbos
Cerbos	Scalar.com logo
Scalar
Trigger.dev logo
Trigger.dev	Transloadit logo
Transloadit	Infisical logo
Infisical	Whop logo
Whop
CryptoJobsList logo
CryptoJobsList	Plain logo
Plain.	Inngest logo
Inngest	Storyblok CMS
Storyblok
Mux logo
Mux	Cybozu logo
Cybozu

Bronze
val town logo	route4me logo	Encore.dev logo	Replay.io logo
Numeric logo	Marcato Partners		
Bamboo Creative logo	Jason Laster
Ecosystem
There are a growing number of tools that are built atop or support Zod natively! If you've built a tool or library on top of Zod, tell me about it on Twitter or start a Discussion. I'll add it below and tweet it out.

Resources
Total TypeScript Zod Tutorial by @mattpocockuk
Fixing TypeScript's Blindspot: Runtime Typechecking by @jherr
API libraries
tRPC: Build end-to-end typesafe APIs without GraphQL.
@anatine/zod-nestjs: Helper methods for using Zod in a NestJS project.
zod-endpoints: Contract-first strictly typed endpoints with Zod. OpenAPI compatible.
zhttp: An OpenAPI compatible, strictly typed http library with Zod input and response validation.
domain-functions: Decouple your business logic from your framework using composable functions. With first-class type inference from end to end powered by Zod schemas.
@zodios/core: A typescript API client with runtime and compile time validation backed by axios and zod.
express-zod-api: Build Express-based APIs with I/O schema validation and custom middlewares.
tapiduck: End-to-end typesafe JSON APIs with Zod and Express; a bit like tRPC, but simpler.
koa-zod-router: Create typesafe routes in Koa with I/O validation using Zod.
zod-sockets: Zod-powered Socket.IO microframework with I/O validation and built-in AsyncAPI specs
oas-tszod-gen: Client SDK code generator to convert OpenApi v3 specifications into TS endpoint caller functions with Zod types.
GQLoom: Weave GraphQL schema and resolvers using Zod.
oRPC: Typesafe APIs Made Simple
Form integrations
react-hook-form: A first-party Zod resolver for React Hook Form.
TanStack Form: Headless, performant, and type-safe form state management for TS/JS, React, Vue, Angular, Solid, and Lit
zod-validation-error: Generate user-friendly error messages from ZodErrors.
zod-formik-adapter: A community-maintained Formik adapter for Zod.
react-zorm: Standalone <form> generation and validation for React using Zod.
zodix: Zod utilities for FormData and URLSearchParams in Remix loaders and actions.
conform: A typesafe form validation library for progressive enhancement of HTML forms. Works with Remix and Next.js.
remix-params-helper: Simplify integration of Zod with standard URLSearchParams and FormData for Remix apps.
formik-validator-zod: Formik-compliant validator library that simplifies using Zod with Formik.
zod-i18n-map: Useful for translating Zod error messages.
@modular-forms/solid: Modular form library for SolidJS that supports Zod for validation.
houseform: A React form library that uses Zod for validation.
sveltekit-superforms: Supercharged form library for SvelteKit with Zod validation.
mobx-zod-form: Data-first form builder based on MobX & Zod.
@vee-validate/zod: Form library for Vue.js with Zod schema validation.
zod-form-renderer: Auto-infer form fields from zod schema and render them with react-hook-form with E2E type safety.
antd-zod: Zod adapter for Ant Design form fields validation.
frrm: Tiny 0.5kb Zod-based, HTML form abstraction that goes brr.
Zod to X
zod-to-ts: Generate TypeScript definitions from Zod schemas.
zod-to-json-schema: Convert your Zod schemas into JSON Schemas.
@anatine/zod-openapi: Converts a Zod schema to an OpenAPI v3.x SchemaObject.
zod-fast-check: Generate fast-check arbitraries from Zod schemas.
zod-dto: Generate Nest.js DTOs from a Zod schema.
fastify-type-provider-zod: Create Fastify type providers from Zod schemas.
zod-to-openapi: Generate full OpenAPI (Swagger) docs from Zod, including schemas, endpoints & parameters.
nestjs-graphql-zod: Generates NestJS GraphQL model classes from Zod schemas. Provides GraphQL method decorators working with Zod schemas.
zod-openapi: Create full OpenAPI v3.x documentation from Zod schemas.
fastify-zod-openapi: Fastify type provider, validation, serialization and @fastify/swagger support for Zod schemas.
typeschema: Universal adapter for schema validation.
zodex: (De)serialization for zod schemas
X to Zod
ts-to-zod: Convert TypeScript definitions into Zod schemas.
@runtyping/zod: Generate Zod from static types & JSON schema.
json-schema-to-zod: Convert your JSON Schemas into Zod schemas. Live demo.
json-to-zod: Convert JSON objects into Zod schemas. Live demo.
graphql-codegen-typescript-validation-schema: GraphQL Code Generator plugin to generate form validation schema from your GraphQL schema.
zod-prisma: Generate Zod schemas from your Prisma schema.
Supervillain: Generate Zod schemas from your Go structs.
prisma-zod-generator: Emit Zod schemas from your Prisma schema.
drizzle-zod: Emit Zod schemas from your Drizzle schema.
prisma-trpc-generator: Emit fully implemented tRPC routers and their validation schemas using Zod.
zod-prisma-types Create Zod types from your Prisma models.
quicktype: Convert JSON objects and JSON schemas into Zod schemas.
@sanity-typed/zod: Generate Zod Schemas from Sanity Schemas.
java-to-zod: Convert POJOs to Zod schemas
Orval: Generate Zod schemas from OpenAPI schemas
Kubb: Generate SDKs and Zod schemas from your OpenAPI schemas
Mocking
@anatine/zod-mock: Generate mock data from a Zod schema. Powered by faker.js.
zod-mocking: Generate mock data from your Zod schemas.
zod-fixture: Use your zod schemas to automate the generation of non-relevant test fixtures in a deterministic way.
zocker: Generate plausible mock-data from your schemas.
zodock Generate mock data based on Zod schemas.
zod-schema-faker Generates mock data from Zod schemas. Powered by @faker-js/faker and randexp.js
Powered by Zod
freerstore: Firestore cost optimizer.
slonik: Node.js Postgres client with strong Zod integration.
schemql: Enhances your SQL workflow by combining raw SQL with targeted type safety and schema validation.
soly: Create CLI applications with zod.
pastel: Create CLI applications with react, zod, and ink.
zod-xlsx: A xlsx based resource validator using Zod schemas.
znv: Type-safe environment parsing and validation for Node.js with Zod schemas.
zod-config: Load configurations across multiple sources with flexible adapters, ensuring type safety with Zod.
unplugin-environment: A plugin for loading enviroment variables safely with schema validation, simple with virtual module, type-safe with intellisense, and better DX 🔥 🚀 👷. Powered by Zod.
zod-struct: Create runtime-checked structs with Zod.
zod-csv: Validation helpers for zod for parsing CSV data.
fullproduct.dev: Universal Expo + Next.js App Starter that uses Zod schemas as the single source of truth to keep generated MDX docs, GraphQL, database models, forms, and fetcher functions in sync.
Utilities for Zod
zod_utilz: Framework agnostic utilities for Zod.
zod-playground: A tool for learning and testing Zod schema validation functionalities. Link.
zod-sandbox: Controlled environment for testing zod schemas. Live demo.
zod-dev: Conditionally disables Zod runtime parsing in production.
zod-accelerator: Accelerates Zod's throughput up to ~100x.

Installation
Requirements
TypeScript 4.5+!

You must enable strict mode in your tsconfig.json. This is a best practice for all TypeScript projects.

// tsconfig.json
{
  // ...
  "compilerOptions": {
    // ...
    "strict": true
  }
}
From npm
npm install zod       # npm
deno add npm:zod      # deno
yarn add zod          # yarn
bun add zod           # bun
pnpm add zod          # pnpm
Zod also publishes a canary version on every commit. To install the canary:

npm install zod@canary       # npm
deno add npm:zod@canary      # deno
yarn add zod@canary          # yarn
bun add zod@canary           # bun
pnpm add zod@canary          # pnpm
The rest of this README assumes you are using npm and importing directly from the "zod" package.


Basic usage
Creating a simple string schema

import { z } from "zod";

// creating a schema for strings
const mySchema = z.string();

// parsing
mySchema.parse("tuna"); // => "tuna"
mySchema.parse(12); // => throws ZodError

// "safe" parsing (doesn't throw error if validation fails)
mySchema.safeParse("tuna"); // => { success: true; data: "tuna" }
mySchema.safeParse(12); // => { success: false; error: ZodError }
Creating an object schema

import { z } from "zod";

const User = z.object({
  username: z.string(),
});

User.parse({ username: "Ludwig" });

// extract the inferred type
type User = z.infer<typeof User>;
// { username: string }

Primitives
import { z } from "zod";

// primitive values
z.string();
z.number();
z.bigint();
z.boolean();
z.date();
z.symbol();

// empty types
z.undefined();
z.null();
z.void(); // accepts undefined

// catch-all types
// allows any value
z.any();
z.unknown();

// never type
// allows no values
z.never();

Coercion for primitives
Zod now provides a more convenient way to coerce primitive values.

const schema = z.coerce.string();
schema.parse("tuna"); // => "tuna"
schema.parse(12); // => "12"
During the parsing step, the input is passed through the String() function, which is a JavaScript built-in for coercing data into strings.

schema.parse(12); // => "12"
schema.parse(true); // => "true"
schema.parse(undefined); // => "undefined"
schema.parse(null); // => "null"
The returned schema is a normal ZodString instance so you can use all string methods.

z.coerce.string().email().min(5);
How coercion works

All primitive types support coercion. Zod coerces all inputs using the built-in constructors: String(input), Number(input), new Date(input), etc.

z.coerce.string(); // String(input)
z.coerce.number(); // Number(input)
z.coerce.boolean(); // Boolean(input)
z.coerce.bigint(); // BigInt(input)
z.coerce.date(); // new Date(input)
Note — Boolean coercion with z.coerce.boolean() may not work how you expect. Any truthy value is coerced to true, and any falsy value is coerced to false.

const schema = z.coerce.boolean(); // Boolean(input)

schema.parse("tuna"); // => true
schema.parse("true"); // => true
schema.parse("false"); // => true
schema.parse(1); // => true
schema.parse([]); // => true

schema.parse(0); // => false
schema.parse(""); // => false
schema.parse(undefined); // => false
schema.parse(null); // => false
For more control over coercion logic, consider using z.preprocess or z.pipe().


Literals
Literal schemas represent a literal type, like "hello world" or 5.

const tuna = z.literal("tuna");
const twelve = z.literal(12);
const twobig = z.literal(2n); // bigint literal
const tru = z.literal(true);

const terrificSymbol = Symbol("terrific");
const terrific = z.literal(terrificSymbol);

// retrieve literal value
tuna.value; // "tuna"
Currently there is no support for Date literals in Zod. If you have a use case for this feature, please file an issue.


Strings
Zod includes a handful of string-specific validations.

// validations
z.string().max(5);
z.string().min(5);
z.string().length(5);
z.string().email();
z.string().url();
z.string().emoji();
z.string().uuid();
z.string().nanoid();
z.string().cuid();
z.string().cuid2();
z.string().ulid();
z.string().regex(regex);
z.string().includes(string);
z.string().startsWith(string);
z.string().endsWith(string);
z.string().datetime(); // ISO 8601; by default only `Z` timezone allowed
z.string().ip(); // defaults to allow both IPv4 and IPv6
z.string().cidr(); // defaults to allow both IPv4 and IPv6

// transforms
z.string().trim(); // trim whitespace
z.string().toLowerCase(); // toLowerCase
z.string().toUpperCase(); // toUpperCase

// added in Zod 3.23
z.string().date(); // ISO date format (YYYY-MM-DD)
z.string().time(); // ISO time format (HH:mm:ss[.SSSSSS])
z.string().duration(); // ISO 8601 duration
z.string().base64();
Check out validator.js for a bunch of other useful string validation functions that can be used in conjunction with Refinements.

You can customize some common error messages when creating a string schema.

const name = z.string({
  required_error: "Name is required",
  invalid_type_error: "Name must be a string",
});
When using validation methods, you can pass in an additional argument to provide a custom error message.

z.string().min(5, { message: "Must be 5 or more characters long" });
z.string().max(5, { message: "Must be 5 or fewer characters long" });
z.string().length(5, { message: "Must be exactly 5 characters long" });
z.string().email({ message: "Invalid email address" });
z.string().url({ message: "Invalid url" });
z.string().emoji({ message: "Contains non-emoji characters" });
z.string().uuid({ message: "Invalid UUID" });
z.string().includes("tuna", { message: "Must include tuna" });
z.string().startsWith("https://", { message: "Must provide secure URL" });
z.string().endsWith(".com", { message: "Only .com domains allowed" });
z.string().datetime({ message: "Invalid datetime string! Must be UTC." });
z.string().date({ message: "Invalid date string!" });
z.string().time({ message: "Invalid time string!" });
z.string().ip({ message: "Invalid IP address" });
z.string().cidr({ message: "Invalid CIDR" });
Datetimes
As you may have noticed, Zod string includes a few date/time related validations. These validations are regular expression based, so they are not as strict as a full date/time library. However, they are very convenient for validating user input.

The z.string().datetime() method enforces ISO 8601; default is no timezone offsets and arbitrary sub-second decimal precision.

const datetime = z.string().datetime();

datetime.parse("2020-01-01T00:00:00Z"); // pass
datetime.parse("2020-01-01T00:00:00.123Z"); // pass
datetime.parse("2020-01-01T00:00:00.123456Z"); // pass (arbitrary precision)
datetime.parse("2020-01-01T00:00:00+02:00"); // fail (no offsets allowed)
Timezone offsets can be allowed by setting the offset option to true.

const datetime = z.string().datetime({ offset: true });

datetime.parse("2020-01-01T00:00:00+02:00"); // pass
datetime.parse("2020-01-01T00:00:00.123+02:00"); // pass (millis optional)
datetime.parse("2020-01-01T00:00:00.123+0200"); // pass (millis optional)
datetime.parse("2020-01-01T00:00:00.123+02"); // pass (only offset hours)
datetime.parse("2020-01-01T00:00:00Z"); // pass (Z still supported)
Allow unqualified (timezone-less) datetimes with the local flag.

const schema = z.string().datetime({ local: true });
schema.parse("2020-01-01T00:00:00"); // pass
You can additionally constrain the allowable precision. By default, arbitrary sub-second precision is supported (but optional).

const datetime = z.string().datetime({ precision: 3 });

datetime.parse("2020-01-01T00:00:00.123Z"); // pass
datetime.parse("2020-01-01T00:00:00Z"); // fail
datetime.parse("2020-01-01T00:00:00.123456Z"); // fail
Dates
Added in Zod 3.23

The z.string().date() method validates strings in the format YYYY-MM-DD.

const date = z.string().date();

date.parse("2020-01-01"); // pass
date.parse("2020-1-1"); // fail
date.parse("2020-01-32"); // fail
Times
Added in Zod 3.23

The z.string().time() method validates strings in the format HH:MM:SS[.s+]. The second can include arbitrary decimal precision. It does not allow timezone offsets of any kind.

const time = z.string().time();

time.parse("00:00:00"); // pass
time.parse("09:52:31"); // pass
time.parse("23:59:59.9999999"); // pass (arbitrary precision)

time.parse("00:00:00.123Z"); // fail (no `Z` allowed)
time.parse("00:00:00.123+02:00"); // fail (no offsets allowed)
You can set the precision option to constrain the allowable decimal precision.

const time = z.string().time({ precision: 3 });

time.parse("00:00:00.123"); // pass
time.parse("00:00:00.123456"); // fail
time.parse("00:00:00"); // fail
IP addresses
By default .ip() allows both IPv4 and IPv6.

const ip = z.string().ip();

ip.parse("192.168.1.1"); // pass
ip.parse("84d5:51a0:9114:1855:4cfa:f2d7:1f12:7003"); // pass
ip.parse("84d5:51a0:9114:1855:4cfa:f2d7:1f12:192.168.1.1"); // pass

ip.parse("256.1.1.1"); // fail
ip.parse("84d5:51a0:9114:gggg:4cfa:f2d7:1f12:7003"); // fail
You can additionally set the IP version.

const ipv4 = z.string().ip({ version: "v4" });
ipv4.parse("84d5:51a0:9114:1855:4cfa:f2d7:1f12:7003"); // fail

const ipv6 = z.string().ip({ version: "v6" });
ipv6.parse("192.168.1.1"); // fail
IP ranges (CIDR)
Validate IP address ranges specified with CIDR notation. By default, .cidr() allows both IPv4 and IPv6.

const cidr = z.string().cidr();
cidr.parse("192.168.0.0/24"); // pass
cidr.parse("2001:db8::/32"); // pass
You can specify a version with the version parameter.

const ipv4Cidr = z.string().cidr({ version: "v4" });
ipv4Cidr.parse("84d5:51a0:9114:1855:4cfa:f2d7:1f12:7003"); // fail

const ipv6Cidr = z.string().cidr({ version: "v6" });
ipv6Cidr.parse("192.168.1.1"); // fail

Numbers
You can customize certain error messages when creating a number schema.

const age = z.number({
  required_error: "Age is required",
  invalid_type_error: "Age must be a number",
});
Zod includes a handful of number-specific validations.

z.number().gt(5);
z.number().gte(5); // alias .min(5)
z.number().lt(5);
z.number().lte(5); // alias .max(5)

z.number().int(); // value must be an integer

z.number().positive(); //     > 0
z.number().nonnegative(); //  >= 0
z.number().negative(); //     < 0
z.number().nonpositive(); //  <= 0

z.number().multipleOf(5); // Evenly divisible by 5. Alias .step(5)

z.number().finite(); // value must be finite, not Infinity or -Infinity
z.number().safe(); // value must be between Number.MIN_SAFE_INTEGER and Number.MAX_SAFE_INTEGER
Optionally, you can pass in a second argument to provide a custom error message.

z.number().lte(5, { message: "this👏is👏too👏big" });

BigInts
Zod includes a handful of bigint-specific validations.

z.bigint().gt(5n);
z.bigint().gte(5n); // alias `.min(5n)`
z.bigint().lt(5n);
z.bigint().lte(5n); // alias `.max(5n)`

z.bigint().positive(); // > 0n
z.bigint().nonnegative(); // >= 0n
z.bigint().negative(); // < 0n
z.bigint().nonpositive(); // <= 0n

z.bigint().multipleOf(5n); // Evenly divisible by 5n.

NaNs
You can customize certain error messages when creating a nan schema.

const isNaN = z.nan({
  required_error: "isNaN is required",
  invalid_type_error: "isNaN must be 'not a number'",
});

Booleans
You can customize certain error messages when creating a boolean schema.

const isActive = z.boolean({
  required_error: "isActive is required",
  invalid_type_error: "isActive must be a boolean",
});

Dates
Use z.date() to validate Date instances.

z.date().safeParse(new Date()); // success: true
z.date().safeParse("2022-01-12T00:00:00.000Z"); // success: false
You can customize certain error messages when creating a date schema.

const myDateSchema = z.date({
  required_error: "Please select a date and time",
  invalid_type_error: "That's not a date!",
});
Zod provides a handful of date-specific validations.

z.date().min(new Date("1900-01-01"), { message: "Too old" });
z.date().max(new Date(), { message: "Too young!" });
Coercion to Date

Since zod 3.20, use z.coerce.date() to pass the input through new Date(input).

const dateSchema = z.coerce.date();
type DateSchema = z.infer<typeof dateSchema>;
// type DateSchema = Date

/* valid dates */
console.log(dateSchema.safeParse("2023-01-10T00:00:00.000Z").success); // true
console.log(dateSchema.safeParse("2023-01-10").success); // true
console.log(dateSchema.safeParse("1/10/23").success); // true
console.log(dateSchema.safeParse(new Date("1/10/23")).success); // true

/* invalid dates */
console.log(dateSchema.safeParse("2023-13-10").success); // false
console.log(dateSchema.safeParse("0000-00-00").success); // false
For older zod versions, use z.preprocess like described in this thread.


Zod enums
const FishEnum = z.enum(["Salmon", "Tuna", "Trout"]);
type FishEnum = z.infer<typeof FishEnum>;
// 'Salmon' | 'Tuna' | 'Trout'
z.enum is a Zod-native way to declare a schema with a fixed set of allowable string values. Pass the array of values directly into z.enum(). Alternatively, use as const to define your enum values as a tuple of strings. See the const assertion docs for details.

const VALUES = ["Salmon", "Tuna", "Trout"] as const;
const FishEnum = z.enum(VALUES);
This is not allowed, since Zod isn't able to infer the exact values of each element.

const fish = ["Salmon", "Tuna", "Trout"];
const FishEnum = z.enum(fish);
.enum

To get autocompletion with a Zod enum, use the .enum property of your schema:

FishEnum.enum.Salmon; // => autocompletes

FishEnum.enum;
/*
=> {
  Salmon: "Salmon",
  Tuna: "Tuna",
  Trout: "Trout",
}
*/
You can also retrieve the list of options as a tuple with the .options property:

FishEnum.options; // ["Salmon", "Tuna", "Trout"];
.exclude/.extract()

You can create subsets of a Zod enum with the .exclude and .extract methods.

const FishEnum = z.enum(["Salmon", "Tuna", "Trout"]);
const SalmonAndTrout = FishEnum.extract(["Salmon", "Trout"]);
const TunaOnly = FishEnum.exclude(["Salmon", "Trout"]);

Native enums
Zod enums are the recommended approach to defining and validating enums. But if you need to validate against an enum from a third-party library (or you don't want to rewrite your existing enums) you can use z.nativeEnum().

Numeric enums

enum Fruits {
  Apple,
  Banana,
}

const FruitEnum = z.nativeEnum(Fruits);
type FruitEnum = z.infer<typeof FruitEnum>; // Fruits

FruitEnum.parse(Fruits.Apple); // passes
FruitEnum.parse(Fruits.Banana); // passes
FruitEnum.parse(0); // passes
FruitEnum.parse(1); // passes
FruitEnum.parse(3); // fails
String enums

enum Fruits {
  Apple = "apple",
  Banana = "banana",
  Cantaloupe, // you can mix numerical and string enums
}

const FruitEnum = z.nativeEnum(Fruits);
type FruitEnum = z.infer<typeof FruitEnum>; // Fruits

FruitEnum.parse(Fruits.Apple); // passes
FruitEnum.parse(Fruits.Cantaloupe); // passes
FruitEnum.parse("apple"); // passes
FruitEnum.parse("banana"); // passes
FruitEnum.parse(0); // passes
FruitEnum.parse("Cantaloupe"); // fails
Const enums

The .nativeEnum() function works for as const objects as well. ⚠️ as const requires TypeScript 3.4+!

const Fruits = {
  Apple: "apple",
  Banana: "banana",
  Cantaloupe: 3,
} as const;

const FruitEnum = z.nativeEnum(Fruits);
type FruitEnum = z.infer<typeof FruitEnum>; // "apple" | "banana" | 3

FruitEnum.parse("apple"); // passes
FruitEnum.parse("banana"); // passes
FruitEnum.parse(3); // passes
FruitEnum.parse("Cantaloupe"); // fails
You can access the underlying object with the .enum property:

FruitEnum.enum.Apple; // "apple"

Optionals
You can make any schema optional with z.optional(). This wraps the schema in a ZodOptional instance and returns the result.

const schema = z.optional(z.string());

schema.parse(undefined); // => returns undefined
type A = z.infer<typeof schema>; // string | undefined
For convenience, you can also call the .optional() method on an existing schema.

const user = z.object({
  username: z.string().optional(),
});
type C = z.infer<typeof user>; // { username?: string | undefined };
You can extract the wrapped schema from a ZodOptional instance with .unwrap().

const stringSchema = z.string();
const optionalString = stringSchema.optional();
optionalString.unwrap() === stringSchema; // true

Nullables
Similarly, you can create nullable types with z.nullable().

const nullableString = z.nullable(z.string());
nullableString.parse("asdf"); // => "asdf"
nullableString.parse(null); // => null
Or use the .nullable() method.

const E = z.string().nullable(); // equivalent to nullableString
type E = z.infer<typeof E>; // string | null
Extract the inner schema with .unwrap().

const stringSchema = z.string();
const nullableString = stringSchema.nullable();
nullableString.unwrap() === stringSchema; // true

Objects
// all properties are required by default
const Dog = z.object({
  name: z.string(),
  age: z.number(),
});

// extract the inferred type like this
type Dog = z.infer<typeof Dog>;

// equivalent to:
type Dog = {
  name: string;
  age: number;
};
.shape
Use .shape to access the schemas for a particular key.

Dog.shape.name; // => string schema
Dog.shape.age; // => number schema
.keyof
Use .keyof to create a ZodEnum schema from the keys of an object schema.

const keySchema = Dog.keyof();
keySchema; // ZodEnum<["name", "age"]>
.extend
You can add additional fields to an object schema with the .extend method.

const DogWithBreed = Dog.extend({
  breed: z.string(),
});
You can use .extend to overwrite fields! Be careful with this power!

.merge
Equivalent to A.extend(B.shape).

const BaseTeacher = z.object({ students: z.array(z.string()) });
const HasID = z.object({ id: z.string() });

const Teacher = BaseTeacher.merge(HasID);
type Teacher = z.infer<typeof Teacher>; // => { students: string[], id: string }
If the two schemas share keys, the properties of B overrides the property of A. The returned schema also inherits the "unknownKeys" policy (strip/strict/passthrough) and the catchall schema of B.

.pick/.omit
Inspired by TypeScript's built-in Pick and Omit utility types, all Zod object schemas have .pick and .omit methods that return a modified version. Consider this Recipe schema:

const Recipe = z.object({
  id: z.string(),
  name: z.string(),
  ingredients: z.array(z.string()),
});
To only keep certain keys, use .pick .

const JustTheName = Recipe.pick({ name: true });
type JustTheName = z.infer<typeof JustTheName>;
// => { name: string }
To remove certain keys, use .omit .

const NoIDRecipe = Recipe.omit({ id: true });

type NoIDRecipe = z.infer<typeof NoIDRecipe>;
// => { name: string, ingredients: string[] }
.partial
Inspired by the built-in TypeScript utility type Partial, the .partial method makes all properties optional.

Starting from this object:

const user = z.object({
  email: z.string(),
  username: z.string(),
});
// { email: string; username: string }
We can create a partial version:

const partialUser = user.partial();
// { email?: string | undefined; username?: string | undefined }
You can also specify which properties to make optional:

const optionalEmail = user.partial({
  email: true,
});
/*
{
  email?: string | undefined;
  username: string
}
*/
.deepPartial
The .partial method is shallow — it only applies one level deep. There is also a "deep" version:

const user = z.object({
  username: z.string(),
  location: z.object({
    latitude: z.number(),
    longitude: z.number(),
  }),
  strings: z.array(z.object({ value: z.string() })),
});

const deepPartialUser = user.deepPartial();

/*
{
  username?: string | undefined,
  location?: {
    latitude?: number | undefined;
    longitude?: number | undefined;
  } | undefined,
  strings?: { value?: string}[]
}
*/
Important limitation: deep partials only work as expected in hierarchies of objects, arrays, and tuples.

.required
Contrary to the .partial method, the .required method makes all properties required.

Starting from this object:

const user = z
  .object({
    email: z.string(),
    username: z.string(),
  })
  .partial();
// { email?: string | undefined; username?: string | undefined }
We can create a required version:

const requiredUser = user.required();
// { email: string; username: string }
You can also specify which properties to make required:

const requiredEmail = user.required({
  email: true,
});
/*
{
  email: string;
  username?: string | undefined;
}
*/
.passthrough
By default Zod object schemas strip out unrecognized keys during parsing.

const person = z.object({
  name: z.string(),
});

person.parse({
  name: "bob dylan",
  extraKey: 61,
});
// => { name: "bob dylan" }
// extraKey has been stripped
Instead, if you want to pass through unknown keys, use .passthrough() .

person.passthrough().parse({
  name: "bob dylan",
  extraKey: 61,
});
// => { name: "bob dylan", extraKey: 61 }
.strict
By default Zod object schemas strip out unrecognized keys during parsing. You can disallow unknown keys with .strict() . If there are any unknown keys in the input, Zod will throw an error.

const person = z
  .object({
    name: z.string(),
  })
  .strict();

person.parse({
  name: "bob dylan",
  extraKey: 61,
});
// => throws ZodError
.strip
You can use the .strip method to reset an object schema to the default behavior (stripping unrecognized keys).

.catchall
You can pass a "catchall" schema into an object schema. All unknown keys will be validated against it.

const person = z
  .object({
    name: z.string(),
  })
  .catchall(z.number());

person.parse({
  name: "bob dylan",
  validExtraKey: 61, // works fine
});

person.parse({
  name: "bob dylan",
  validExtraKey: false, // fails
});
// => throws ZodError
Using .catchall() obviates .passthrough() , .strip() , or .strict(). All keys are now considered "known".


Arrays
const stringArray = z.array(z.string());

// equivalent
const stringArray = z.string().array();
Be careful with the .array() method. It returns a new ZodArray instance. This means the order in which you call methods matters. For instance:

z.string().optional().array(); // (string | undefined)[]
z.string().array().optional(); // string[] | undefined
.element
Use .element to access the schema for an element of the array.

stringArray.element; // => string schema
.nonempty
If you want to ensure that an array contains at least one element, use .nonempty().

const nonEmptyStrings = z.string().array().nonempty();
// the inferred type is now
// [string, ...string[]]

nonEmptyStrings.parse([]); // throws: "Array cannot be empty"
nonEmptyStrings.parse(["Ariana Grande"]); // passes
You can optionally specify a custom error message:

// optional custom error message
const nonEmptyStrings = z.string().array().nonempty({
  message: "Can't be empty!",
});
.min/.max/.length
z.string().array().min(5); // must contain 5 or more items
z.string().array().max(5); // must contain 5 or fewer items
z.string().array().length(5); // must contain 5 items exactly
Unlike .nonempty() these methods do not change the inferred type.


Tuples
Unlike arrays, tuples have a fixed number of elements and each element can have a different type.

const athleteSchema = z.tuple([
  z.string(), // name
  z.number(), // jersey number
  z.object({
    pointsScored: z.number(),
  }), // statistics
]);

type Athlete = z.infer<typeof athleteSchema>;
// type Athlete = [string, number, { pointsScored: number }]
A variadic ("rest") argument can be added with the .rest method.

const variadicTuple = z.tuple([z.string()]).rest(z.number());
const result = variadicTuple.parse(["hello", 1, 2, 3]);
// => [string, ...number[]];

Unions
Zod includes a built-in z.union method for composing "OR" types.

const stringOrNumber = z.union([z.string(), z.number()]);

stringOrNumber.parse("foo"); // passes
stringOrNumber.parse(14); // passes
Zod will test the input against each of the "options" in order and return the first value that validates successfully.

For convenience, you can also use the .or method:

const stringOrNumber = z.string().or(z.number());
Optional string validation:

To validate an optional form input, you can union the desired string validation with an empty string literal.

This example validates an input that is optional but needs to contain a valid URL:

const optionalUrl = z.union([z.string().url().nullish(), z.literal("")]);

console.log(optionalUrl.safeParse(undefined).success); // true
console.log(optionalUrl.safeParse(null).success); // true
console.log(optionalUrl.safeParse("").success); // true
console.log(optionalUrl.safeParse("https://zod.dev").success); // true
console.log(optionalUrl.safeParse("not a valid url").success); // false

Discriminated unions
A discriminated union is a union of object schemas that all share a particular key.

type MyUnion =
  | { status: "success"; data: string }
  | { status: "failed"; error: Error };
Such unions can be represented with the z.discriminatedUnion method. This enables faster evaluation, because Zod can check the discriminator key (status in the example above) to determine which schema should be used to parse the input. This makes parsing more efficient and lets Zod report friendlier errors.

With the basic union method, the input is tested against each of the provided "options", and in the case of invalidity, issues for all the "options" are shown in the zod error. On the other hand, the discriminated union allows for selecting just one of the "options", testing against it, and showing only the issues related to this "option".

const myUnion = z.discriminatedUnion("status", [
  z.object({ status: z.literal("success"), data: z.string() }),
  z.object({ status: z.literal("failed"), error: z.instanceof(Error) }),
]);

myUnion.parse({ status: "success", data: "yippie ki yay" });
You can extract a reference to the array of schemas with the .options property.

myUnion.options; // [ZodObject<...>, ZodObject<...>]
To merge two or more discriminated unions, use .options with destructuring.

const A = z.discriminatedUnion("status", [
  /* options */
]);
const B = z.discriminatedUnion("status", [
  /* options */
]);

const AB = z.discriminatedUnion("status", [...A.options, ...B.options]);

Records
Record schemas are used to validate types such as Record<string, number>. This is particularly useful for storing or caching items by ID.

const User = z.object({ name: z.string() });

const UserStore = z.record(z.string(), User);
type UserStore = z.infer<typeof UserStore>;
// => Record<string, { name: string }>
The schema and inferred type can be used like so:

const userStore: UserStore = {};

userStore["77d2586b-9e8e-4ecf-8b21-ea7e0530eadd"] = {
  name: "Carlotta",
}; // passes

userStore["77d2586b-9e8e-4ecf-8b21-ea7e0530eadd"] = {
  whatever: "Ice cream sundae",
}; // TypeError
A note on numerical keys

While z.record(keyType, valueType) is able to accept numerical key types and TypeScript's built-in Record type is Record<KeyType, ValueType>, it's hard to represent the TypeScript type Record<number, any> in Zod.

As it turns out, TypeScript's behavior surrounding [k: number] is a little unintuitive:

const testMap: { [k: number]: string } = {
  1: "one",
};

for (const key in testMap) {
  console.log(`${key}: ${typeof key}`);
}
// prints: `1: string`
As you can see, JavaScript automatically casts all object keys to strings under the hood. Since Zod is trying to bridge the gap between static and runtime types, it doesn't make sense to provide a way of creating a record schema with numerical keys, since there's no such thing as a numerical key in runtime JavaScript.


Maps
const stringNumberMap = z.map(z.string(), z.number());

type StringNumberMap = z.infer<typeof stringNumberMap>;
// type StringNumberMap = Map<string, number>

Sets
const numberSet = z.set(z.number());
type NumberSet = z.infer<typeof numberSet>;
// type NumberSet = Set<number>
Set schemas can be further constrained with the following utility methods.

z.set(z.string()).nonempty(); // must contain at least one item
z.set(z.string()).min(5); // must contain 5 or more items
z.set(z.string()).max(5); // must contain 5 or fewer items
z.set(z.string()).size(5); // must contain 5 items exactly

Intersections
Intersections are useful for creating "logical AND" types. This is useful for intersecting two object types.

const Person = z.object({
  name: z.string(),
});

const Employee = z.object({
  role: z.string(),
});

const EmployedPerson = z.intersection(Person, Employee);

// equivalent to:
const EmployedPerson = Person.and(Employee);
Though in many cases, it is recommended to use A.merge(B) to merge two objects. The .merge method returns a new ZodObject instance, whereas A.and(B) returns a less useful ZodIntersection instance that lacks common object methods like pick and omit.

const a = z.union([z.number(), z.string()]);
const b = z.union([z.number(), z.boolean()]);
const c = z.intersection(a, b);

type c = z.infer<typeof c>; // => number

Recursive types
You can define a recursive schema in Zod, but because of a limitation of TypeScript, their type can't be statically inferred. Instead you'll need to define the type definition manually, and provide it to Zod as a "type hint".

const baseCategorySchema = z.object({
  name: z.string(),
});

type Category = z.infer<typeof baseCategorySchema> & {
  subcategories: Category[];
};

const categorySchema: z.ZodType<Category> = baseCategorySchema.extend({
  subcategories: z.lazy(() => categorySchema.array()),
});

categorySchema.parse({
  name: "People",
  subcategories: [
    {
      name: "Politicians",
      subcategories: [
        {
          name: "Presidents",
          subcategories: [],
        },
      ],
    },
  ],
}); // passes
Thanks to crasite for this example.

ZodType with ZodEffects
When using z.ZodType with z.ZodEffects ( .refine, .transform, preprocess, etc... ), you will need to define the input and output types of the schema. z.ZodType<Output, z.ZodTypeDef, Input>

const isValidId = (id: string): id is `${string}/${string}` =>
  id.split("/").length === 2;

const baseSchema = z.object({
  id: z.string().refine(isValidId),
});

type Input = z.input<typeof baseSchema> & {
  children: Input[];
};

type Output = z.output<typeof baseSchema> & {
  children: Output[];
};

const schema: z.ZodType<Output, z.ZodTypeDef, Input> = baseSchema.extend({
  children: z.lazy(() => schema.array()),
});
Thanks to marcus13371337 and JoelBeeldi for this example.

JSON type
If you want to validate any JSON value, you can use the snippet below.

const literalSchema = z.union([z.string(), z.number(), z.boolean(), z.null()]);
type Literal = z.infer<typeof literalSchema>;
type Json = Literal | { [key: string]: Json } | Json[];
const jsonSchema: z.ZodType<Json> = z.lazy(() =>
  z.union([literalSchema, z.array(jsonSchema), z.record(jsonSchema)])
);

jsonSchema.parse(data);
Thanks to ggoodman for suggesting this.

Cyclical objects
Despite supporting recursive schemas, passing cyclical data into Zod will cause an infinite loop in some cases.

To detect cyclical objects before they cause problems, consider this approach.


Promises
const numberPromise = z.promise(z.number());
"Parsing" works a little differently with promise schemas. Validation happens in two parts:

Zod synchronously checks that the input is an instance of Promise (i.e. an object with .then and .catch methods.).
Zod uses .then to attach an additional validation step onto the existing Promise. You'll have to use .catch on the returned Promise to handle validation failures.
numberPromise.parse("tuna");
// ZodError: Non-Promise type: string

numberPromise.parse(Promise.resolve("tuna"));
// => Promise<number>

const test = async () => {
  await numberPromise.parse(Promise.resolve("tuna"));
  // ZodError: Non-number type: string

  await numberPromise.parse(Promise.resolve(3.14));
  // => 3.14
};

Instanceof
You can use z.instanceof to check that the input is an instance of a class. This is useful to validate inputs against classes that are exported from third-party libraries.

class Test {
  name: string;
}

const TestSchema = z.instanceof(Test);

const blob: any = "whatever";
TestSchema.parse(new Test()); // passes
TestSchema.parse(blob); // throws

Functions
Zod also lets you define "function schemas". This makes it easy to validate the inputs and outputs of a function without intermixing your validation code and "business logic".

You can create a function schema with z.function(args, returnType) .

const myFunction = z.function();

type myFunction = z.infer<typeof myFunction>;
// => ()=>unknown
Define inputs and outputs.

const myFunction = z
  .function()
  .args(z.string(), z.number()) // accepts an arbitrary number of arguments
  .returns(z.boolean());

type myFunction = z.infer<typeof myFunction>;
// => (arg0: string, arg1: number)=>boolean
Function schemas have an .implement() method which accepts a function and returns a new function that automatically validates its inputs and outputs.

const trimmedLength = z
  .function()
  .args(z.string()) // accepts an arbitrary number of arguments
  .returns(z.number())
  .implement((x) => {
    // TypeScript knows x is a string!
    return x.trim().length;
  });

trimmedLength("sandwich"); // => 8
trimmedLength(" asdf "); // => 4
If you only care about validating inputs, just don't call the .returns() method. The output type will be inferred from the implementation.

You can use the special z.void() option if your function doesn't return anything. This will let Zod properly infer the type of void-returning functions. (Void-returning functions actually return undefined.)

const myFunction = z
  .function()
  .args(z.string())
  .implement((arg) => {
    return [arg.length];
  });

myFunction; // (arg: string)=>number[]
Extract the input and output schemas from a function schema.

myFunction.parameters();
// => ZodTuple<[ZodString, ZodNumber]>

myFunction.returnType();
// => ZodBoolean

Preprocess
Zod now supports primitive coercion without the need for .preprocess(). See the coercion docs for more information.

Typically Zod operates under a "parse then transform" paradigm. Zod validates the input first, then passes it through a chain of transformation functions. (For more information about transforms, read the .transform docs.)

But sometimes you want to apply some transform to the input before parsing happens. A common use case: type coercion. Zod enables this with the z.preprocess().

const castToString = z.preprocess((val) => String(val), z.string());
This returns a ZodEffects instance. ZodEffects is a wrapper class that contains all logic pertaining to preprocessing, refinements, and transforms.


Custom schemas
You can create a Zod schema for any TypeScript type by using z.custom(). This is useful for creating schemas for types that are not supported by Zod out of the box, such as template string literals.

const px = z.custom<`${number}px`>((val) => {
  return typeof val === "string" ? /^\d+px$/.test(val) : false;
});

type px = z.infer<typeof px>; // `${number}px`

px.parse("42px"); // "42px"
px.parse("42vw"); // throws;
If you don't provide a validation function, Zod will allow any value. This can be dangerous!

z.custom<{ arg: string }>(); // performs no validation
You can customize the error message and other options by passing a second argument. This parameter works the same way as the params parameter of .refine.

z.custom<...>((val) => ..., "custom error message");

Schema methods
All Zod schemas contain certain methods.

.parse
.parse(data: unknown): T

Given any Zod schema, you can call its .parse method to check data is valid. If it is, a value is returned with full type information! Otherwise, an error is thrown.

IMPORTANT: The value returned by .parse is a deep clone of the variable you passed in.

const stringSchema = z.string();

stringSchema.parse("fish"); // => returns "fish"
stringSchema.parse(12); // throws error
.parseAsync
.parseAsync(data:unknown): Promise<T>

If you use asynchronous refinements or transforms (more on those later), you'll need to use .parseAsync.

const stringSchema = z.string().refine(async (val) => val.length <= 8);

await stringSchema.parseAsync("hello"); // => returns "hello"
await stringSchema.parseAsync("hello world"); // => throws error
.safeParse
.safeParse(data:unknown): { success: true; data: T; } | { success: false; error: ZodError; }

If you don't want Zod to throw errors when validation fails, use .safeParse. This method returns an object containing either the successfully parsed data or a ZodError instance containing detailed information about the validation problems.

stringSchema.safeParse(12);
// => { success: false; error: ZodError }

stringSchema.safeParse("billie");
// => { success: true; data: 'billie' }
The result is a discriminated union, so you can handle errors very conveniently:

const result = stringSchema.safeParse("billie");
if (!result.success) {
  // handle error then return
  result.error;
} else {
  // do something
  result.data;
}
.safeParseAsync
Alias: .spa

An asynchronous version of safeParse.

await stringSchema.safeParseAsync("billie");
For convenience, this has been aliased to .spa:

await stringSchema.spa("billie");
.refine
.refine(validator: (data:T)=>any, params?: RefineParams)

Zod lets you provide custom validation logic via refinements. (For advanced features like creating multiple issues and customizing error codes, see .superRefine.)

Zod was designed to mirror TypeScript as closely as possible. But there are many so-called "refinement types" you may wish to check for that can't be represented in TypeScript's type system. For instance: checking that a number is an integer or that a string is a valid email address.

For example, you can define a custom validation check on any Zod schema with .refine :

const myString = z.string().refine((val) => val.length <= 255, {
  message: "String can't be more than 255 characters",
});
⚠️ Refinement functions should not throw. Instead they should return a falsy value to signal failure.

Arguments
As you can see, .refine takes two arguments.

The first is the validation function. This function takes one input (of type T — the inferred type of the schema) and returns any. Any truthy value will pass validation. (Prior to zod@1.6.2 the validation function had to return a boolean.)
The second argument accepts some options. You can use this to customize certain error-handling behavior:
type RefineParams = {
  // override error message
  message?: string;

  // appended to error path
  path?: (string | number)[];

  // params object you can use to customize message
  // in error map
  params?: object;
};
For advanced cases, the second argument can also be a function that returns RefineParams.

const longString = z.string().refine(
  (val) => val.length > 10,
  (val) => ({ message: `${val} is not more than 10 characters` })
);
Customize error path
const passwordForm = z
  .object({
    password: z.string(),
    confirm: z.string(),
  })
  .refine((data) => data.password === data.confirm, {
    message: "Passwords don't match",
    path: ["confirm"], // path of error
  });

passwordForm.parse({ password: "asdf", confirm: "qwer" });
Because you provided a path parameter, the resulting error will be:

ZodError {
  issues: [{
    "code": "custom",
    "path": [ "confirm" ],
    "message": "Passwords don't match"
  }]
}
Asynchronous refinements
Refinements can also be async:

const userId = z.string().refine(async (id) => {
  // verify that ID exists in database
  return true;
});
⚠️ If you use async refinements, you must use the .parseAsync method to parse data! Otherwise Zod will throw an error.

Relationship to transforms
Transforms and refinements can be interleaved:

z.string()
  .transform((val) => val.length)
  .refine((val) => val > 25);
.superRefine
The .refine method is actually syntactic sugar atop a more versatile (and verbose) method called superRefine. Here's an example:

const Strings = z.array(z.string()).superRefine((val, ctx) => {
  if (val.length > 3) {
    ctx.addIssue({
      code: z.ZodIssueCode.too_big,
      maximum: 3,
      type: "array",
      inclusive: true,
      message: "Too many items 😡",
    });
  }

  if (val.length !== new Set(val).size) {
    ctx.addIssue({
      code: z.ZodIssueCode.custom,
      message: `No duplicates allowed.`,
    });
  }
});
You can add as many issues as you like. If ctx.addIssue is not called during the execution of the function, validation passes.

Normally refinements always create issues with a ZodIssueCode.custom error code, but with superRefine it's possible to throw issues of any ZodIssueCode. Each issue code is described in detail in the Error Handling guide: ERROR_HANDLING.md.

Abort early
By default, parsing will continue even after a refinement check fails. For instance, if you chain together multiple refinements, they will all be executed. However, it may be desirable to abort early to prevent later refinements from being executed. To achieve this, pass the fatal flag to ctx.addIssue and return z.NEVER.

const schema = z.number().superRefine((val, ctx) => {
  if (val < 10) {
    ctx.addIssue({
      code: z.ZodIssueCode.custom,
      message: "should be >= 10",
      fatal: true,
    });

    return z.NEVER;
  }

  if (val !== 12) {
    ctx.addIssue({
      code: z.ZodIssueCode.custom,
      message: "should be twelve",
    });
  }
});
Type refinements
If you provide a type predicate to .refine() or .superRefine(), the resulting type will be narrowed down to your predicate's type. This is useful if you are mixing multiple chained refinements and transformations:

const schema = z
  .object({
    first: z.string(),
    second: z.number(),
  })
  .nullable()
  .superRefine((arg, ctx): arg is { first: string; second: number } => {
    if (!arg) {
      ctx.addIssue({
        code: z.ZodIssueCode.custom, // customize your issue
        message: "object should exist",
      });
    }

    return z.NEVER; // The return value is not used, but we need to return something to satisfy the typing
  })
  // here, TS knows that arg is not null
  .refine((arg) => arg.first === "bob", "`first` is not `bob`!");
⚠️ You must use ctx.addIssue() instead of returning a boolean value to indicate whether the validation passes. If ctx.addIssue is not called during the execution of the function, validation passes.

.transform
To transform data after parsing, use the transform method.

const stringToNumber = z.string().transform((val) => val.length);

stringToNumber.parse("string"); // => 6
Chaining order
Note that stringToNumber above is an instance of the ZodEffects subclass. It is NOT an instance of ZodString. If you want to use the built-in methods of ZodString (e.g. .email()) you must apply those methods before any transforms.

const emailToDomain = z
  .string()
  .email()
  .transform((val) => val.split("@")[1]);

emailToDomain.parse("colinhacks@example.com"); // => example.com
Validating during transform
The .transform method can simultaneously validate and transform the value. This is often simpler and less duplicative than chaining transform and refine.

As with .superRefine, the transform function receives a ctx object with an addIssue method that can be used to register validation issues.

const numberInString = z.string().transform((val, ctx) => {
  const parsed = parseInt(val);
  if (isNaN(parsed)) {
    ctx.addIssue({
      code: z.ZodIssueCode.custom,
      message: "Not a number",
    });

    // This is a special symbol you can use to
    // return early from the transform function.
    // It has type `never` so it does not affect the
    // inferred return type.
    return z.NEVER;
  }
  return parsed;
});
Relationship to refinements
Transforms and refinements can be interleaved. These will be executed in the order they are declared.

const nameToGreeting = z
  .string()
  .transform((val) => val.toUpperCase())
  .refine((val) => val.length > 15)
  .transform((val) => `Hello ${val}`)
  .refine((val) => val.indexOf("!") === -1);
Async transforms
Transforms can also be async.

const IdToUser = z
  .string()
  .uuid()
  .transform(async (id) => {
    return await getUserById(id);
  });
⚠️ If your schema contains asynchronous transforms, you must use .parseAsync() or .safeParseAsync() to parse data. Otherwise Zod will throw an error.

.default
You can use transforms to implement the concept of "default values" in Zod.

const stringWithDefault = z.string().default("tuna");

stringWithDefault.parse(undefined); // => "tuna"
Optionally, you can pass a function into .default that will be re-executed whenever a default value needs to be generated:

const numberWithRandomDefault = z.number().default(Math.random);

numberWithRandomDefault.parse(undefined); // => 0.4413456736055323
numberWithRandomDefault.parse(undefined); // => 0.1871840107401901
numberWithRandomDefault.parse(undefined); // => 0.7223408162401552
Conceptually, this is how Zod processes default values:

If the input is undefined, the default value is returned
Otherwise, the data is parsed using the base schema
.describe
Use .describe() to add a description property to the resulting schema.

const documentedString = z
  .string()
  .describe("A useful bit of text, if you know what to do with it.");
documentedString.description; // A useful bit of text…
This can be useful for documenting a field, for example in a JSON Schema using a library like zod-to-json-schema).

.catch
Use .catch() to provide a "catch value" to be returned in the event of a parsing error.

const numberWithCatch = z.number().catch(42);

numberWithCatch.parse(5); // => 5
numberWithCatch.parse("tuna"); // => 42
Optionally, you can pass a function into .catch that will be re-executed whenever a default value needs to be generated. A ctx object containing the caught error will be passed into this function.

const numberWithRandomCatch = z.number().catch((ctx) => {
  ctx.error; // the caught ZodError
  return Math.random();
});

numberWithRandomCatch.parse("sup"); // => 0.4413456736055323
numberWithRandomCatch.parse("sup"); // => 0.1871840107401901
numberWithRandomCatch.parse("sup"); // => 0.7223408162401552
Conceptually, this is how Zod processes "catch values":

The data is parsed using the base schema
If the parsing fails, the "catch value" is returned
.optional
A convenience method that returns an optional version of a schema.

const optionalString = z.string().optional(); // string | undefined

// equivalent to
z.optional(z.string());
.nullable
A convenience method that returns a nullable version of a schema.

const nullableString = z.string().nullable(); // string | null

// equivalent to
z.nullable(z.string());
.nullish
A convenience method that returns a "nullish" version of a schema. Nullish schemas will accept both undefined and null. Read more about the concept of "nullish" in the TypeScript 3.7 release notes.

const nullishString = z.string().nullish(); // string | null | undefined

// equivalent to
z.string().nullable().optional();
.array
A convenience method that returns an array schema for the given type:

const stringArray = z.string().array(); // string[]

// equivalent to
z.array(z.string());
.promise
A convenience method for promise types:

const stringPromise = z.string().promise(); // Promise<string>

// equivalent to
z.promise(z.string());
.or
A convenience method for union types.

const stringOrNumber = z.string().or(z.number()); // string | number

// equivalent to
z.union([z.string(), z.number()]);
.and
A convenience method for creating intersection types.

const nameAndAge = z
  .object({ name: z.string() })
  .and(z.object({ age: z.number() })); // { name: string } & { age: number }

// equivalent to
z.intersection(z.object({ name: z.string() }), z.object({ age: z.number() }));
.brand
.brand<T>() => ZodBranded<this, B>

TypeScript's type system is structural, which means that any two types that are structurally equivalent are considered the same.

type Cat = { name: string };
type Dog = { name: string };

const petCat = (cat: Cat) => {};
const fido: Dog = { name: "fido" };
petCat(fido); // works fine
In some cases, its can be desirable to simulate nominal typing inside TypeScript. For instance, you may wish to write a function that only accepts an input that has been validated by Zod. This can be achieved with branded types (AKA opaque types).

const Cat = z.object({ name: z.string() }).brand<"Cat">();
type Cat = z.infer<typeof Cat>;

const petCat = (cat: Cat) => {};

// this works
const simba = Cat.parse({ name: "simba" });
petCat(simba);

// this doesn't
petCat({ name: "fido" });
Under the hood, this works by attaching a "brand" to the inferred type using an intersection type. This way, plain/unbranded data structures are no longer assignable to the inferred type of the schema.

const Cat = z.object({ name: z.string() }).brand<"Cat">();
type Cat = z.infer<typeof Cat>;
// {name: string} & {[symbol]: "Cat"}
Note that branded types do not affect the runtime result of .parse. It is a static-only construct.

.readonly
.readonly() => ZodReadonly<this>

This method returns a ZodReadonly schema instance that parses the input using the base schema, then calls Object.freeze() on the result. The inferred type is also marked as readonly.

const schema = z.object({ name: z.string() }).readonly();
type schema = z.infer<typeof schema>;
// Readonly<{name: string}>

const result = schema.parse({ name: "fido" });
result.name = "simba"; // error
The inferred type uses TypeScript's built-in readonly types when relevant.

z.array(z.string()).readonly();
// readonly string[]

z.tuple([z.string(), z.number()]).readonly();
// readonly [string, number]

z.map(z.string(), z.date()).readonly();
// ReadonlyMap<string, Date>

z.set(z.string()).readonly();
// ReadonlySet<string>
.pipe
Schemas can be chained into validation "pipelines". It's useful for easily validating the result after a .transform():

z.string()
  .transform((val) => val.length)
  .pipe(z.number().min(5));
The .pipe() method returns a ZodPipeline instance.


Guides and concepts
Type inference
You can extract the TypeScript type of any schema with z.infer<typeof mySchema> .

const A = z.string();
type A = z.infer<typeof A>; // string

const u: A = 12; // TypeError
const u: A = "asdf"; // compiles
What about transforms?

In reality each Zod schema internally tracks two types: an input and an output. For most schemas (e.g. z.string()) these two are the same. But once you add transforms into the mix, these two values can diverge. For instance z.string().transform(val => val.length) has an input of string and an output of number.

You can separately extract the input and output types like so:

const stringToNumber = z.string().transform((val) => val.length);

// ⚠️ Important: z.infer returns the OUTPUT type!
type input = z.input<typeof stringToNumber>; // string
type output = z.output<typeof stringToNumber>; // number

// equivalent to z.output!
type inferred = z.infer<typeof stringToNumber>; // number
Writing generic functions
With TypeScript generics, you can write reusable functions that accept Zod schemas as parameters. This enables you to create custom validation logic, schema transformations, and more, while maintaining type safety and inference.

When attempting to write a function that accepts a Zod schema as an input, it's tempting to try something like this:

function inferSchema<T>(schema: z.ZodType<T>) {
  return schema;
}
This approach is incorrect, and limits TypeScript's ability to properly infer the argument. No matter what you pass in, the type of schema will be an instance of ZodType.

inferSchema(z.string());
// => ZodType<string>
This approach loses type information, namely which subclass the input actually is (in this case, ZodString). That means you can't call any string-specific methods like .min() on the result of inferSchema.

A better approach is to infer the schema as a whole instead of merely its inferred type. You can do this with a utility type called z.ZodTypeAny.

function inferSchema<T extends z.ZodTypeAny>(schema: T) {
  return schema;
}

inferSchema(z.string());
// => ZodString
ZodTypeAny is just a shorthand for ZodType<any, any, any>, a type that is broad enough to match any Zod schema.

The Result is now fully and properly typed, and the type system can infer the specific subclass of the schema.

Inferring the inferred type
If you follow the best practice of using z.ZodTypeAny as the generic parameter for your schema, you may encounter issues with the parsed data being typed as any instead of the inferred type of the schema.

function parseData<T extends z.ZodTypeAny>(data: unknown, schema: T) {
  return schema.parse(data);
}

parseData("sup", z.string());
// => any
Due to how TypeScript inference works, it is treating schema like a ZodTypeAny instead of the inferred type. You can fix this with a type cast using z.infer.

function parseData<T extends z.ZodTypeAny>(data: unknown, schema: T) {
  return schema.parse(data) as z.infer<T>;
  //                        ^^^^^^^^^^^^^^ <- add this
}

parseData("sup", z.string());
// => string
Constraining allowable inputs
The ZodType class has three generic parameters.

class ZodType<
  Output = any,
  Def extends ZodTypeDef = ZodTypeDef,
  Input = Output
> { ... }
By constraining these in your generic input, you can limit what schemas are allowable as inputs to your function:

function makeSchemaOptional<T extends z.ZodType<string>>(schema: T) {
  return schema.optional();
}

makeSchemaOptional(z.string());
// works fine

makeSchemaOptional(z.number());
// Error: 'ZodNumber' is not assignable to parameter of type 'ZodType<string, ZodTypeDef, string>'
Error handling
Zod provides a subclass of Error called ZodError. ZodErrors contain an issues array containing detailed information about the validation problems.

const result = z
  .object({
    name: z.string(),
  })
  .safeParse({ name: 12 });

if (!result.success) {
  result.error.issues;
  /* [
      {
        "code": "invalid_type",
        "expected": "string",
        "received": "number",
        "path": [ "name" ],
        "message": "Expected string, received number"
      }
  ] */
}
For detailed information about the possible error codes and how to customize error messages, check out the dedicated error handling guide: ERROR_HANDLING.md

Zod's error reporting emphasizes completeness and correctness. If you are looking to present a useful error message to the end user, you should either override Zod's error messages using an error map (described in detail in the Error Handling guide) or use a third-party library like zod-validation-error

Error formatting
You can use the .format() method to convert this error into a nested object.

const result = z
  .object({
    name: z.string(),
  })
  .safeParse({ name: 12 });

if (!result.success) {
  const formatted = result.error.format();
  /* {
    name: { _errors: [ 'Expected string, received number' ] }
  } */

  formatted.name?._errors;
  // => ["Expected string, received number"]
}

Comparison
There are a handful of other widely-used validation libraries, but all of them have certain design limitations that make for a non-ideal developer experience.

Joi
https://github.com/hapijs/joi

Doesn't support static type inference 😕

Yup
https://github.com/jquense/yup

Yup is a full-featured library that was implemented first in vanilla JS, and later rewritten in TypeScript.

Supports casting and transforms
All object fields are optional by default
Missing promise schemas
Missing function schemas
Missing union & intersection schemas
io-ts
https://github.com/gcanti/io-ts

io-ts is an excellent library by gcanti. The API of io-ts heavily inspired the design of Zod.

In our experience, io-ts prioritizes functional programming purity over developer experience in many cases. This is a valid and admirable design goal, but it makes io-ts particularly hard to integrate into an existing codebase with a more procedural or object-oriented bias. For instance, consider how to define an object with optional properties in io-ts:

import * as t from "io-ts";

const A = t.type({
  foo: t.string,
});

const B = t.partial({
  bar: t.number,
});

const C = t.intersection([A, B]);

type C = t.TypeOf<typeof C>;
// returns { foo: string; bar?: number | undefined }
You must define the required and optional props in separate object validators, pass the optionals through t.partial (which marks all properties as optional), then combine them with t.intersection .

Consider the equivalent in Zod:

const C = z.object({
  foo: z.string(),
  bar: z.number().optional(),
});

type C = z.infer<typeof C>;
// returns { foo: string; bar?: number | undefined }
This more declarative API makes schema definitions vastly more concise.

io-ts also requires the use of gcanti's functional programming library fp-ts to parse results and handle errors. This is another fantastic resource for developers looking to keep their codebase strictly functional. But depending on fp-ts necessarily comes with a lot of intellectual overhead; a developer has to be familiar with functional programming concepts and the fp-ts nomenclature to use the library.

Supports codecs with serialization & deserialization transforms
Supports branded types
Supports advanced functional programming, higher-kinded types, fp-ts compatibility
Missing object methods: (pick, omit, partial, deepPartial, merge, extend)
Missing nonempty arrays with proper typing ([T, ...T[]])
Missing promise schemas
Missing function schemas
Runtypes
https://github.com/runtypes/runtypes

Runtypes is focused on ergonomics, with good type inference support.

Supports "pattern matching": computed properties that distribute over unions
Supports branded types
Supports template literals
Supports conformance to predefined static types
Missing object methods: (deepPartial, merge)
Missing promise schemas
Missing error customization
Ow
https://github.com/sindresorhus/ow

Ow is focused on function input validation. It's a library that makes it easy to express complicated assert statements, but it doesn't let you parse untyped data. They support a much wider variety of types; Zod has a nearly one-to-one mapping with TypeScript's type system, whereas ow lets you validate several highly-specific types out of the box (e.g. int32Array , see full list in their README).

If you want to validate function inputs, use function schemas in Zod! It's a much simpler approach that lets you reuse a function type declaration without repeating yourself (namely, copy-pasting a bunch of ow assertions at the beginning of every function). Also Zod lets you validate your return types as well, so you can be sure there won't be any unexpected data passed downstream.


Ask AI
Zod logo
Search
Ctrl
K
Beta
Introducing Zod 4 beta

Migration guide

Documentation
Intro

Basic usage

Defining schemas

Customizing errors

Formatting errors

Metadata and registries

New

JSON Schema

New

Ecosystem

Packages
zod

@zod/mini

New

@zod/core

New


Introducing Zod 4 beta
Refer to the Changelog for a complete list of breaking changes.

Zod 4 is now in beta after over a year of active development. It's faster, slimmer, more tsc-efficient, and implements some long-requested features.

To install the beta:


pnpm upgrade zod@next 
Development will continue on the v4 branch over a 4-6 week beta period as I work with libraries to ensure day-one compatibility with the first stable release.

❤️
Huge thanks to Clerk, who supported my work on Zod 4 through their extremely generous OSS Fellowship. They were an amazing partner throughout the (much longer than anticipated!) development process.

Why a new major version?
Zod v3.0 was released in May 2021 (!). Back then Zod had 2700 stars on GitHub and 600k weekly downloads. Today it has 36.5k stars and 23M weekly downloads. After 24 minor versions, the Zod 3 codebase has hit a ceiling; the most commonly requested features and improvements require breaking changes.

Zod 4 implements all of these in one fell swoop. It uses an entirely new internal architecture that solves some long-standing design limitations, lays the groundwork for some long-requested features, and closes 9 of Zod's 10 most upvoted open issues. With luck, it will serve as the new foundation for many more years to come.

For a scannable breakdown of what's new, see the table of contents. Click on any item to jump to that section.

Benchmarks
You can run these benchmarks yourself in the Zod repo:


$ git clone git@github.com:colinhacks/zod.git
$ cd zod
$ git switch v4
$ pnpm install
Then to run a particular benchmark:


$ pnpm bench <name>
2.6x faster string parsing

$ pnpm bench string
runtime: node v22.13.0 (arm64-darwin)
 
benchmark      time (avg)             (min … max)       p75       p99      p999
------------------------------------------------- -----------------------------
• z.string().parse
------------------------------------------------- -----------------------------
zod3          348 µs/iter       (299 µs … 743 µs)    362 µs    494 µs    634 µs
zod4          132 µs/iter       (108 µs … 348 µs)    162 µs    269 µs    322 µs
 
summary for z.string().parse
  zod4
   2.63x faster than zod3
3x faster array parsing

$ pnpm bench array
runtime: node v22.13.0 (arm64-darwin)
 
benchmark      time (avg)             (min … max)       p75       p99      p999
------------------------------------------------- -----------------------------
• z.array() parsing
------------------------------------------------- -----------------------------
zod3          162 µs/iter       (141 µs … 753 µs)    152 µs    291 µs    513 µs
zod4       54'282 ns/iter    (47'084 ns … 669 µs) 50'833 ns    185 µs    233 µs
 
summary for z.array() parsing
  zod4
   2.98x faster than zod3
7x faster object parsing
This runs the Moltar validation library benchmark.


$ pnpm bench object-moltar
benchmark      time (avg)             (min … max)       p75       p99      p999
------------------------------------------------- -----------------------------
• z.object() safeParse
------------------------------------------------- -----------------------------
zod3          767 µs/iter     (735 µs … 3'136 µs)    775 µs    898 µs  3'136 µs
zod4          110 µs/iter     (102 µs … 1'291 µs)    105 µs    217 µs    566 µs
 
summary for z.object() safeParse
  zod4
   6.98x faster than zod3
20x reduction in tsc instantiations
Consider the following simple file:


import * as z from "zod";
 
export const A = z.object({
  a: z.string(),
  b: z.string(),
  c: z.string(),
  d: z.string(),
  e: z.string(),
});
 
export const B = A.extend({
  f: z.string(),
  g: z.string(),
  h: z.string(),
});
Compiling this file with tsc --extendedDiagnostics using zod@3 results in >25000 type instantiations. With zod@4 it only results in ~1100.

The Zod repo contains a tsc benchmarking playground. Try this for yourself using the compiler benchmarks in packages/tsc. The exact numbers may change as the implementation evolves.


$ cd packages/tsc
$ pnpm bench object-with-extend
More importantly, Zod 4 has redesigned and simplified the generics of ZodObject and other schema classes to avoid some pernicious "instantiation explosions". For instance, chaining .extend() and .omit() repeatedly—something that previously caused compiler issues:


import * as z from "zod";
 
export const a = z.object({
  a: z.string(),
  b: z.string(),
  c: z.string(),
});
 
export const b = a.omit({
  a: true,
  b: true,
  c: true,
});
 
export const c = b.extend({
  a: z.string(),
  b: z.string(),
  c: z.string(),
});
 
export const d = c.omit({
  a: true,
  b: true,
  c: true,
});
 
export const e = d.extend({
  a: z.string(),
  b: z.string(),
  c: z.string(),
});
 
export const f = e.omit({
  a: true,
  b: true,
  c: true,
});
 
export const g = f.extend({
  a: z.string(),
  b: z.string(),
  c: z.string(),
});
 
export const h = g.omit({
  a: true,
  b: true,
  c: true,
});
 
export const i = h.extend({
  a: z.string(),
  b: z.string(),
  c: z.string(),
});
 
export const j = i.omit({
  a: true,
  b: true,
  c: true,
});
 
export const k = j.extend({
  a: z.string(),
  b: z.string(),
  c: z.string(),
});
 
export const l = k.omit({
  a: true,
  b: true,
  c: true,
});
 
export const m = l.extend({
  a: z.string(),
  b: z.string(),
  c: z.string(),
});
 
export const n = m.omit({
  a: true,
  b: true,
  c: true,
});
 
export const o = n.extend({
  a: z.string(),
  b: z.string(),
  c: z.string(),
});
 
export const p = o.omit({
  a: true,
  b: true,
  c: true,
});
 
export const q = p.extend({
  a: z.string(),
  b: z.string(),
  c: z.string(),
});
In Zod 3, this took 4000ms to compile; and adding additional calls to .extend() would trigger a "Possibly infinite" error. In Zod 4, this compiles in 400ms, 10x faster.

Coupled with the upcoming tsgo compiler, Zod 4's editor performance will scale to vastly larger schemas and codebases.

2x reduction in core bundle size
Consider the following simple script.


import * as z from "zod";
 
const schema = z.boolean();
 
schema.parse(true);
It's about as simple as it gets when it comes to validation. That's intentional; it's a good way to measure the core bundle size—the code that will end up in the bundle even in simple cases. We'll bundle this with rollup using both Zod 3 and Zod 4 and compare the final bundles.

Package	Bundle (gzip)
zod@3	12.47kb
zod@4	5.36kb
The core bundle is ~57% smaller in Zod 4 (2.3x). That's good! But we can do a lot better.

Introducing @zod/mini
Zod's method-heavy API is fundamentally difficult to tree-shake. Even our simple z.boolean() script pulls in the implementations of a bunch of methods we didn't use, like .optional(), .array(), etc. Writing slimmer implementations can only get you so far. That's where @zod/mini comes in.


npm install @zod/mini@next
It's a sister library with a functional, tree-shakable API that corresponds one-to-one with zod. Where Zod uses methods, @zod/mini generally uses wrapper functions:

@zod/mini
zod

import * as z from "@zod/mini";
 
z.optional(z.string());
 
z.union([z.string(), z.number()]);
 
z.extend(z.object({ /* ... */ }), { age: z.number() });
Not all methods are gone! The parsing methods are identical in zod and @zod/mini.


import * as z from "@zod/mini";
 
z.string().parse("asdf");
z.string().safeParse("asdf");
await z.string().parseAsync("asdf");
await z.string().safeParseAsync("asdf");
There's also a general-purpose .check() method used to add refinements.

@zod/mini
zod

import * as z from "@zod/mini";
 
z.array(z.number()).check(
  z.minLength(5), 
  z.maxLength(10),
  z.refine(arr => arr.includes(5))
);
The following top-level refinements are available in @zod/mini. It should be fairly self-explanatory which zod methods they correspond to.


import * as z from "@zod/mini";
 
// custom checks
z.refine();
 
// first-class checks
z.lt(value);
z.lte(value); // alias: z.maximum()
z.gt(value);
z.gte(value); // alias: z.minimum()
z.positive();
z.negative();
z.nonpositive();
z.nonnegative();
z.multipleOf(value);
z.maxSize(value);
z.minSize(value);
z.size(value);
z.maxLength(value);
z.minLength(value);
z.length(value);
z.regex(regex);
z.lowercase();
z.uppercase();
z.includes(value);
z.startsWith(value);
z.endsWith(value);
z.property(key, schema); // for object schemas; check `input[key]` against `schema`
z.mime(value); // for file schemas (see below)
 
// overwrites (these *do not* change the inferred type!)
z.overwrite(value => newValue);
z.normalize();
z.trim();
z.toLowerCase();
z.toUpperCase();
This more functional API makes it easier for bundlers to tree-shaking the APIs you don't use. While zod is still recommended for the majority of use cases, any projects with uncommonly strict bundle size constraints should consider @zod/mini.

6.6x reduction in core bundle size
Here's the script from above, updated to use "@zod/mini" instead of "zod".


import * as z from "@zod/mini";
 
const schema = z.boolean();
schema.parse(false);
When we build this with rollup, the gzipped bundle size is 1.88kb. That's an 85% (6.6x) reduction in core bundle size compared to zod@3.

Package	Bundle (gzip)
zod@3	12.47kb
zod@4	5.36kb
@zod/mini	1.88kb
Learn more on the dedicated @zod/mini docs page. Complete API details are mixed into existing documentation pages; code blocks contain separate tabs for zod and @zod/mini wherever their APIs diverge.

Metadata
Zod 4 introduces a new system for adding strongly-typed metadata to your schemas. Metadata isn't stored inside the schema itself; instead it's stored in a "schema registry" that associates a schema with some typed metadata. To create a registry with z.registry():


import * as z from "zod";
 
const myRegistry = z.registry<{ title: string; description: string }>();
To add schemas to your registry:


const emailSchema = z.string().email();
 
myRegistry.add(emailSchema, { title: "Email address", description: "..." });
myRegistry.get(emailSchema);
// => { title: "Email address", ... }
Alternatively, you can use the .register() method on a schema for convenience:


emailSchema.register(myRegistry, { title: "Email address", description: "..." })
// => returns emailSchema
The global registry
Zod also exports a global registry z.globalRegistry that accepts some common JSON Schema-compatible metadata:


z.globalRegistry.add(z.string(), { 
  id: "email_address",
  title: "Email address",
  description: "Provide your email",
  examples: ["naomie@example.com"],
  extraKey: "Additional properties are also allowed"
});
.meta()
To conveniently add a schema to z.globalRegistry, use the .meta() method.


z.string().meta({ 
  id: "email_address",
  title: "Email address",
  description: "Provide your email",
  examples: ["naomie@example.com"],
  // ...
});
For compatibility with Zod 3, .describe() is still available, but .meta() is preferred.


z.string().describe("An email address");
 
// equivalent to
z.string().meta({ description: "An email address" });
JSON Schema conversion
Zod 4 introduces first-party JSON Schema conversion via z.toJSONSchema().


import * as z from "zod";
 
const mySchema = z.object({name: z.string(), points: z.number()});
 
z.toJSONSchema(mySchema);
// => {
//   type: "object",
//   properties: {
//     name: {type: "string"},
//     points: {type: "number"},
//   },
//   required: ["name", "points"],
// }
Any metadata in z.globalRegistry is automatically included in the JSON Schema output.


const mySchema = z.object({
  firstName: z.string().describe("Your first name"),
  lastName: z.string().meta({ title: "last_name" }),
  age: z.number().meta({ examples: [12, 99] }),
});
 
z.toJSONSchema(mySchema);
// => {
//   type: 'object',
//   properties: {
//     firstName: { type: 'string', description: 'Your first name' },
//     lastName: { type: 'string', title: 'last_name' },
//     age: { type: 'number', examples: [ 12, 99 ] }
//   },
//   required: [ 'firstName', 'lastName', 'age' ]
// }
Refer to the JSON Schema docs for information on customizing the generated JSON Schema.

z.interface()
Zod 4 introduces a new API for defining object types: z.interface(). This may seem surprising or confusing, so I'll briefly explain the reasoning here. (A full blog post on this topic is coming soon.)

Exact(er) optional properties
In TypeScript a property can be "optional" in two distinct ways:


type KeyOptional = { prop?: string };
type ValueOptional = { prop: string | undefined };
In KeyOptional, the prop key can be omitted from the object ("key optional"). In ValueOptional, the prop key must be set however it can be set to undefined ("value optional").

Zod 3 cannot represent ValueOptional. Instead, z.object() automatically adds question marks to any key that accepts a value of undefined:


z.object({ name: z.string().optional() }); 
// { name?: string | undefined }
 
z.object({ name: z.union([z.string(), z.undefined()]) }); 
// { name?: string | undefined }
This includes special schema types like z.unknown():


z.object({ name: z.unknown() }); // { name?: unknown } 
z.object({ name: z.any() }); // { name?: any } 
To properly represent "key optionality", Zod needed an object-level API for marking keys as optional, instead of trying to guess based on the value schema.

This is why Zod 4 introduces a new API for defining object types: z.interface().


const ValueOptional = z.interface({ name: z.string().optional()}); 
// { name: string | undefined }
 
const KeyOptional = z.interface({ "name?": z.string() }); 
// { name?: string }
Key optionality is now defined with a ? suffix in the key itself. This way, you have the power to differentiate between key- and value-optionality.

Besides this change to optionality, z.object() and z.interface() are functionality identical. They even use the same parser internally.

The z.object() API is not deprecated; feel free to continue using it if you prefer it! For the sake of backwards compatibility, z.interface() was added as an opt-in API.

True recursive types
But wait there's more! After implementing z.interface(), I had a huge realization. The ?-suffix API in z.interface() lets Zod sidestep a TypeScript limitation that has long prevented Zod from cleanly representing recursive (cyclical) types. Take this example from the old Zod 3 docs:


import * as z from "zod"; // zod@3
 
interface Category  {
  name: string;
  subcategories: Category[];
};
 
const Category: z.ZodType<Category> = z.object({
  name: z.string(),
  subcategories: z.lazy(() => Category.array()),
});
This has been a thorn in my side for years. To define a cyclical object type, you must

define a redundant interface
use z.lazy() to avoid reference errors
cast your schema to z.ZodType
That's terrible.

Here's the same example in Zod 4:


import * as z from "zod"; // zod@4
 
const Category = z.interface({
  name: z.string(),
  get subcategories() {
    return z.array(Category)
  }
});
No casting, no z.lazy(), no redundant type signatures. Just use getters to define any cyclical properties. The resulting instance has all the object methods you expect:


Category.pick({ subcategories: true });
This means Zod can finally represent commonly cyclical data structure like ORM schemas, GraphQL types, etc.

Given its ability to represent both cyclical types and more exact optionality, I recommend always using z.interface() over z.object() without reservation. That said, z.object() will never be deprecated or removed, so feel free to keep using it if you prefer.

File schemas
To validate File instances:


const fileSchema = z.file();
 
fileSchema.min(10_000); // minimum .size (bytes)
fileSchema.max(1_000_000); // maximum .size (bytes)
fileSchema.type("image/png"); // MIME type
Internationalization
Zod 4 introduces a new locales API for globally translating error messages into different languages.


import * as z from "zod";
 
// configure English locale (default)
z.config(z.locales.en());
At the time of this writing only the English locale is available; There will be a call for pull request from the community shortly; this section will be updated with a list of supported languages as they become available.

Error pretty-printing
The success of the zod-validation-error package demonstrates that there's significant demand for an official API for pretty-printing errors. If you are using that package currently, by all means continue using it.

Zod now implements a top-level z.prettifyError function for converting a ZodError to a user-friendly formatted string.


const myError = new z.ZodError([
  {
    code: 'unrecognized_keys',
    keys: [ 'extraField' ],
    path: [],
    message: 'Unrecognized key: "extraField"'
  },
  {
    expected: 'string',
    code: 'invalid_type',
    path: [ 'username' ],
    message: 'Invalid input: expected string, received number'
  },
  {
    origin: 'number',
    code: 'too_small',
    minimum: 0,
    inclusive: true,
    path: [ 'favoriteNumbers', 1 ],
    message: 'Too small: expected number to be >=0'
  }
]);
 
z.prettifyError(myError);
This returns the following pretty-printable multi-line string:


✖ Unrecognized key: "extraField"
✖ Invalid input: expected string, received number
  → at username
✖ Invalid input: expected number, received string
  → at favoriteNumbers[1]
Currently the formatting isn't configurable; this may change in the future.

Top-level string formats
All "string formats" (email, etc.) have been promoted to top-level functions on the z module. This is both more concise and more tree-shakable. The method equivalents (z.string().email(), etc.) are still available but have been deprecated. They'll be removed in the next major version.


z.email();
z.uuidv4();
z.uuidv7();
z.uuidv8();
z.ipv4();
z.ipv6();
z.cidrv4();
z.cidrv6();
z.url();
z.e164();
z.base64();
z.base64url();
z.jwt();
z.ascii();
z.utf8();
z.lowercase();
z.iso.date();
z.iso.datetime();
z.iso.duration();
z.iso.time();
Custom email regex
The z.email() API now supports a custom regular expression. There is no one canonical email regex; different applications may choose to be more or less strict. For convenience Zod exports some common ones.


// Zod's default email regex (Gmail rules)
// see colinhacks.com/essays/reasonable-email-regex
z.email(); // z.regexes.email
 
// the regex used by browsers to validate input[type=email] fields
// https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/email
z.email({ pattern: z.regexes.html5Email });
 
// the classic emailregex.com regex (RFC 5322)
z.email({ pattern: z.regexes.rfc5322Email });
 
// a loose regex that allows Unicode (good for intl emails)
z.email({ pattern: z.regexes.unicodeEmail });
Template literal types
Zod 4 implements z.templateLiteral(). Template literal types are perhaps the biggest feature of TypeScript's type system that wasn't previously representable.


const hello = z.templateLiteral(["hello, ", z.string()]);
// `hello, ${string}`
 
const cssUnits = z.enum(["px", "em", "rem", "%"]);
const css = z.templateLiteral([z.number(), cssUnits]);
// `${number}px` | `${number}em` | `${number}rem` | `${number}%`
 
const email = z.templateLiteral([
  z.string().min(1),
  "@",
  z.string().max(64),
]);
// `${string}@${string}` (the min/max refinements are enforced!)
Every Zod schema type that can be stringified stores an internal regex: strings, string formats like z.email(), numbers, boolean, bigint, enums, literals, undefined/optional, null/nullable, and other template literals. The z.templateLiteral constructor concatenates these into a super-regex, so things like string formats (z.email()) are properly enforced (but custom refinements are not!).

Read the template literal docs for more info.

Number formats
New numeric "formats" have been added for representing fixed-width integer and float types. These return a ZodNumber instance with proper minimum/maximum constraints already added.


z.int();      // [Number.MIN_SAFE_INTEGER, Number.MAX_SAFE_INTEGER],
z.float32();  // [-3.4028234663852886e38, 3.4028234663852886e38]
z.float64();  // [-1.7976931348623157e308, 1.7976931348623157e308]
z.int32();    // [-2147483648, 2147483647]
z.uint32();   // [0, 4294967295]
Similarly the following bigint numeric formats have also been added. These integer types exceed what can be safely represented by a number in JavaScript, so these return a ZodBigInt instance with the proper minimum/maximum constraints already added.


z.int64();    // [-9223372036854775808n, 9223372036854775807n]
z.uint64();   // [0n, 18446744073709551615n]
Stringbool
The existing z.coerce.boolean() API is very simple: falsy values (false, undefined, null, 0, "", NaN etc) become false, truthy values become true.

This is still a good API, and its behavior aligns with the other z.coerce APIs. But some users requested a more sophisticated "env-style" boolean coercion. To support this, Zod 4 introduces z.stringbool():


const strbool = z.stringbool();
 
strbool.parse("true")         // => true
strbool.parse("1")            // => true
strbool.parse("yes")          // => true
strbool.parse("on")           // => true
strbool.parse("y")            // => true
strbool.parse("enable")       // => true
 
strbool.parse("false");       // => false
strbool.parse("0");           // => false
strbool.parse("no");          // => false
strbool.parse("off");         // => false
strbool.parse("n");           // => false
strbool.parse("disabled");    // => false
 
strbool.parse(/* anything else */); // ZodError<[{ code: "invalid_value" }]>
To customize the truthy and falsy values:


z.stringbool({
  truthy: ["yes", "true"],
  falsy: ["no", "false"]
})
Refer to the z.stringbool() docs for more information.

Simplified error customization
The majority of breaking changes in Zod 4 involve the error customization APIs. They were a bit of a mess in Zod 3; Zod 4 makes things significantly more elegant, to the point where I think it's worth highlighting here.

Long story short, there is now a single, unified error parameter for customizing errors, replacing the following APIs:

Replace message with error. (The message parameter is still supported but deprecated.)


- z.string().min(5, { message: "Too short." });
+ z.string().min(5, { error: "Too short." });
Replace invalid_type_error and required_error with error (function syntax):


// Zod 3
- z.string({ 
-   required_error: "This field is required" 
-   invalid_type_error: "Not a string", 
- });
 
// Zod 4 
+ z.string({ error: (issue) => issue.input === undefined ? 
+  "This field is required" :
+  "Not a string" 
+ });
Replace errorMap with error (function syntax):


// Zod 3 
- z.string({
-   errorMap: (issue, ctx) => {
-     if (issue.code === "too_small") {
-       return { message: `Value must be >${issue.minimum}` };
-     }
-     return { message: ctx.defaultError };
-   },
- });
 
// Zod 4
+ z.string({
+   error: (issue) => {
+     if (issue.code === "too_small") {
+       return `Value must be >${issue.minimum}`
+     }
+   },
+ });
Upgraded z.discriminatedUnion()
Discriminated union support has improved in a couple ways. First, you no longer need to specify the discriminator key. Zod now has a robust way to identify the discriminator key automatically. If no shared discriminator key is found, Zod will throw an error at schema initialization time.


// in Zod 4:
const myUnion = z.discriminatedUnion([
  z.object({ type: z.literal("a"), a: z.string() }),
  z.object({ type: z.literal("b"), b: z.number() }),
]);
 
// in Zod 3:
const myUnion = z.discriminatedUnion("type", [
  z.object({ type: z.literal("a"), a: z.string() }),
  z.object({ type: z.literal("b"), b: z.number() }),
]);
Discriminated unions schema now finally compose—you can use one discriminated union as a member of another. Zod determines the optimal discrimination strategy.


const BaseError = z.object({ status: z.literal("failed"), message: z.string() });
const MyErrors = z.discriminatedUnion([
  BaseError.extend({ code: z.literal(400) }),
  BaseError.extend({ code: z.literal(401) }),
  BaseError.extend({ code: z.literal(500) })
]);
 
const MyResult = z.discriminatedUnion([
  z.interface({ status: z.literal("success"), data: z.string() }),
  MyErrors
]);
Multiple values in z.literal()
The z.literal() API now optionally supports multiple values.


const httpCodes = z.literal([ 200, 201, 202, 204, 206, 207, 208, 226 ]);
 
// previously in Zod 3:
const httpCodes = z.union([
  z.literal(200),
  z.literal(201),
  z.literal(202),
  z.literal(204),
  z.literal(206),
  z.literal(207),
  z.literal(208),
  z.literal(226)
]);
Refinements now live inside schemas
In Zod 3, they were stored in a ZodEffects class that wrapped the original schema. This was inconvenient, as it meant you couldn't interleave .refine() with other schema methods like .min().


z.string()
  .refine(val => val.includes("@"))
  .min(5);
// ^ ❌ Property 'min' does not exist on type ZodEffects<ZodString, string, string>
In Zod 4, refinements are stored inside the schemas themselves, so the code above works as expected.


z.string()
  .refine(val => val.includes("@"))
  .min(5); // ✅
.overwrite()
The .transform() method is extremely useful, but it has one major downside: the output type is no longer introspectable at runtime. The transform function is a black box that can return anything. This means (among other things) there's no sound way to convert the schema to JSON Schema.


const Squared = z.number().transform(val => val ** 2);
// => ZodPipe<ZodNumber, ZodTransform>
Zod 4 introduces a new .overwrite() method for representing transforms that don't change the inferred type. Unlike .transform(), this method returns an instance of the original class. The overwrite function is stored as a refinement, so it doesn't (and can't) modify the inferred type.


z.number().overwrite(val => val ** 2).max(100);
// => ZodNumber
The existing .trim(), .toLowerCase() and .toUpperCase() methods have been reimplemented using .overwrite().

An extensible foundation: @zod/core
While this will not be relevant to the majority of Zod users, it's worth highlighting. The addition of @zod/mini necessitated the creation of a third package @zod/core that contains the core functionality shared between zod and @zod/mini.

I was resistant to this at first, but now I see it as one of Zod 4's most important features. It lets Zod level up from a simple library to a fast validation "substrate" that can be sprinkled into other libraries.

If you're building a schema library, refer to the implementations of zod and @zod/mini to see how to build on top of the foundation @zod/core provides. Don't hesitate to get in touch in GitHub discussions or via X/Bluesky for help or feedback.

Wrapping up
I'm planning to write up a series of additional posts explaining the design process and rationale behind some major features like @zod/mini and z.interface(). I'll update this section as those get posted.

Zod 4 will remain in beta for roughly 6 weeks as I work with library authors and major adopters to ensure a smooth day-one transition from Zod 3 to Zod 4. I encourage all users of Zod to upgrade their installation and provide feedback during the beta window.


pnpm upgrade zod@next
Happy parsing!
— Colin McDonnell @colinhacks


Edit on GitHub
Next

Migration guide

On this page
Why a new major version?
Benchmarks
2.6x faster string parsing
3x faster array parsing
7x faster object parsing
20x reduction in tsc instantiations
2x reduction in core bundle size
Introducing @zod/mini
6.6x reduction in core bundle size
Metadata
The global registry
.meta()
JSON Schema conversion
z.interface()
Exact(er) optional properties
True recursive types
File schemas
Internationalization
Error pretty-printing
Top-level string formats
Custom email regex
Template literal types
Number formats
Stringbool
Simplified error customization
Upgraded z.discriminatedUnion()
Multiple values in z.literal()
Refinements now live inside schemas
.overwrite()
An extensible foundation: @zod/core
Wrapping up


