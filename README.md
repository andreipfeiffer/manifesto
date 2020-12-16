# Software Development Manifesto

This document contains **Guidelines** and **Good Practices** applied by the AE Mobile Team at [\[e-spres-oh\]](https://e-spres-oh.com/). Some of them are very general (applicable on any project), others are specific to our team structure and technology stack. Avoid the [Cargo Cult](https://en.wikipedia.org/wiki/Cargo_cult_programming) before applying them on your projects.

❗️ **IMPORTANT**:  
- this document includes _Guidelines, not Dogmas_;
- motivated exceptions are always understandable;
- this is a living document, so any piece of content is subject to change for the better.

<br />

**Table of contents:**

1. 🧐 [Pragmatism](#-pragmatism)
2. 💄 [Code style](#-code-style)
3. 🗃 [Folder structure](#-folder-structure)
4. 📦 [Component structure](#-component-structure)
5. 💬 [Code Reviews and Feedback](#-code-reviews-and-feedback)
6. 🛎 [Issues](#-issues)
7. 🔨 [Commits](#-commits)
8. 📝 [Code Comments](#-code-comments)
9. 🔁 [Refactoring](#-refactoring)
10. ✅ [Tests](#-tests)
11. 🧩 [Types](#-types)
12. 🎖 [Leadership](#-leadership)
13. 🧠 [Mindset](#-mindset)

<br />

## 🧐 Pragmatism

Don't overthink. Don't predict the future. Don't over-engineer.

1. 🧬 **Avoid creating the wrong abstractions**  
   Evaluate only the current needs when implementing a new feature. Create an abstraction only when you have more than one use-case. Let the [Desire Lines](https://en.wikipedia.org/wiki/Desire_path) unravel themselves.

2. 🎳 **Re-evaluate all use-cases when changing an existing abstraction, or feature**  
   Always question the high inertia of abstractions. When you need to extend existing abstractions or features with additional functionality, don't focus only on your specific use-case. Consider all the use-cases and re-think the implementation if needed.

3. 🐸 **Avoid [boiling frogs](https://en.wikipedia.org/wiki/Boiling_frog) and fix [broken windows](https://en.wikipedia.org/wiki/Broken_windows_theory)**  
   Always [refactor](#-refactoring) when something doesn't feel right. Keep the [entropy](https://en.wikipedia.org/wiki/Software_entropy) in check.

4. ⚖️ **Improve constantly, but not indefinitely**  
   Keep the right balance between [Kaizen](https://en.wikipedia.org/wiki/Kaizen) and [Wabi-sabi](https://en.wikipedia.org/wiki/Wabi-sabi).

<br />

**IMPORTANT:**
If adapting a new use-case would require bigger rewrites that are cumbersome, add a **`// @todo`** comment + an [Issue](#-issues) with all the details, and reference the `Issue` in the `@todo`. Discuss it and plan it accordingly.

<br />

## 💄 Code style

Coding style is (and will always be) a strongly debatable subject, in contrast with its usefulness. No matter what the style is, it should be 100% consistent across the entire team.

- **Use [Prettier](https://prettier.io/) for code formatting**  
  No more manual formatting, no more debates on _"how the code should look"_.

- **Set your editor to Format on Save**  
  Unformatted code should never reach the remote repository. One way to prevent that is to format the code before you save. Having your editor/IDE do that for you is infallible.

- **Make sure your editor/IDE is using the local `.prettierrc` settings**  
  We all must use the same formatting rules. Saving an unmodified file should make no changes to it.

<br />

## 🗃 Folder structure

These guidelines are influenced by our technology choices.

<br />

**1. 📂 Each Screen should have its folder**

- Each `Screen` represents a `Route` in the application.
- Each `Screen` has its separate folder, that contains any `SubComponents` used by that `Screen`.
- Screens are **flattened, not nested** and they should not reflect the routing. Routing is often not a tree, but a graph. Changing the routing should not require you to change the folder structure.

  <br />

**2. 🗂 Reusable components should be placed in `src/components` folder**

A reusable component is any component that is used in _more than one Screen_. They are split into 2 categories:

- **Generic components**, that don't know anything about the business domain.  
  Examples: `Text`, `Button`, `Input`.

- **Business components**, that contain some knowledge about the business domain.  
  Examples: `ReservationHeader`, `CarCard`, `PriceButton`.

  <br />

**3. 🔖 Use file suffixes**

Specific files such as **`Services`**, **`Stores`**, **`Screens`** should have the proper suffix appended to their filename.

This communicates better the role of a file, especially when you have different files with the same name. It also helps to distinguish the opened files in your editor/IDE or when you're searching for them.

```gn
src/
├── services/
│   └── AuthService.ts
├── stores/
│   ├── index.ts
│   └── UserStore.ts
└── screens/
    └── User/
        ├── UserScreen.tsx
        └── Profile.tsx
```

<br />

- The `Screen` suffix helps identify which is the **top-level Component** and which are the **SubComponents**, from a Screen folder.

- The `Service` or `Store` suffix helps differentiate between components and other business logic files, ie: `User.tsx` component and `UserStore.ts`.

<br />

## 📦 Component structure

All components should follow a basic structure, as suggested below:

```js
// imports
import React from 'react'
import { useTranslation } from 'react-i18next'
import Text from '../../components/Text';

// constants
const HEIGHT = 56;

// interface
type Props = {
  title: string;
  icon?: IconEnum;
}

// named export
export function Component(props: Props) {

  // declarations
  const { title, icon } = props;
  const [count, setCount] = useState(1);
  const navigation = useNavigation();

  // effects
  useEffect(() => {
    increment(count);
  }, [count]);

  // return render content
  return (
    <View style={styles.container}>
      <View>{renderHeader()}</View>
      <View>{renderBody()}</View>
    </View>
  )

  // partial renderers
  function renderHeader() {}
  function renderBody() {}

  // local functions (closures)
  function callSomeAction() { // button handler }
  function getSomeValue() {}

// end component
}

// styles
const styles = StyleSheet.create({
  container: {...}
})

// local functions (pure)
function getSomePureValue() {}
```

<br />

Some things to keep in mind:

- **Avoid verbose Component `interface`**:

  - use **`Props`** if the interface is **private**. There's no need to use a different name for each component. One file should contain only one component and `Props` communicates perfectly its purpose.
  - prepend the component name, like **`ButtonProps`**, if the interface is **exported**, because it will be imported in other components that have their own `Props` interface.
  - avoid the [Hungarian notation](https://en.wikipedia.org/wiki/Hungarian_notation), like `IProps` or `IButtonProps`.

- **Prefer function declarations** over expressions:  
  Use`function increment() {}` instead of `const increment = () => {}`. This better communicates that it's a `function` and has the benefit of being hoisted, so you can call it before you define it.

- **Return component structure as soon as possible**:  
  The primary purpose of a component is its markup & structure. That should be visible as soon as possible when you open the file.

- **Prefix `render` to partial rendering functions**:  
  Functions that return `JSX` should be prefixed with `render` to better communicate their purpose. They should also be placed **right below the return** statement, and before other functions that don't return `JSX`.

- **Place closure functions at the end** of the component:  
  Functions that close over props or state are less important, so they should be placed at the end of the component.

- **Place pure functions outside** the component:  
  Pure functions, can easily be moved outside the component, making extraction and testing much easier.

- **Extract any `magic values` as constants**:  
  Magic `numbers` and `strings` should be extracted as `named constants`. If they're used in other files as well, debate whether to `export` it, or to `move` it somewhere else.

- **Don't prefix functions with `_`**:  
  In function components, everything declared inside the component is **private**. Only **`exported`** expressions become **public**. Prefixing functions with `"_"` is useless.

<br />

## 💬 Code Reviews and Feedback

🔬 Code Reviews can be performed in 2 ways:

1. **On request**, by creating a **Pull Request**, or
2. **Unrequested**, by checking the git history.

<br />

🙊 The Feedback you give could fall into 2 categories and should be treated accordingly:

1. **Objective** feedback should always be accompanied by explanations;
2. **Subjective** feedback should be highlighted (ie. _"If I were you, I would do this..."_, _"My opinion is that..."_, etc).

<br />

> It's important __what__ you say, but equally important __how__ you say it.

<br />

📤 Guidelines for giving feedback:

- **Be polite** when you give feedback, especially when it's not requested. Choose your words carefully.

- **Don't ask absurd things** during code reviews. Ask only for meaningful changes that you would do yourself.

- **Try to understand** why a weird specific approach was taken, before trashing it.

- **Ask questions** when a piece of code looks weird. You might not understand the bigger picture.

- **Give props** when you encounter any piece of code that tingle your heart, or mind.

<br />

📥 Guidelines for receiving feedback:

- **Be polite** when you receive feedback, especially when you asked for it. Accept the feedback with an open mind and an open heart.

- **Don't be defensive** when you receive feedback you don't like. Don't take it personally. You got feedback for the code that you wrote, not for the person that you are.

- **Explain when you don't agree** with the feedback you receive. It's OK to disagree. It's NOT OK to be disagreeable.

- **Show your appreciation** when your reviewer finds a bug, or a problem with the code you wrote.

<br />

## 🛎 Issues

When you add a new Issue, you make it public for everyone on the team. That's why it should contain all the knowledge and the information that somebody else would require to understand what the problem is.

- 🎯 **Short and concise title**  
  Treat Issues like functions or [commits](#-commits). Their title should clearly and concisely communicate the problem. You have plenty of space to add lengthy descriptions and explanations in the body.

- 🏷 **Use labels for categorization**  
  Labels are a great way to add predefined context to Issues, to keep the title concise.

- 🐞 **Bugs should have a test case**  
  Before you create a bug issue, make sure that bug is reproducible in certain situations. When you report a bug, it's necessary to include all the details required for somebody else to be able to reproduce it:

  - **iOS**-specific, **Android**-specific or **both**;
  - **simulator** or **real device**;
  - **relevant application state**: guest or user, filled in profile or not, online or offline, search location, etc.

- 🎨 **Format the description**  
  Plain text is harder to read than formatted text. Use [markdown](https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax) to format your content accordingly. You won't receive any praise for formatting the content, but you will annoy somebody if you didn't.

  - use **[bold](https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax#styling-text)** to highlight important content;
  - use **[lists](https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax#lists)** whenever you have to enumerate more than 1 item;
  - use **[quotes](https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax#quoting-text)** when you cite something;
  - use **[inline or block code](https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax#quoting-code)** to format code-related content;
  - use **[links](https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax#links)** to refer to external sources;
  - use **[mentions](https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax#mentioning-people-and-teams)** to ping other team members;
  - reference other **[issues](https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax#referencing-issues-and-pull-requests)**;
  - use **[task lists](https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax#task-lists)** to track progress, etc.

<br />

## 🔨 Commits

- **Commits should be self-contained and highly cohesive**  
  All changes in a commit should be related to the message of the commit. Avoid adding unrelated changes. If you revert a commit, it should only undo what the commit message describes.

- **Avoid merge commits on `develop`**  
  Fetch/pull before making a commit. After committing, push immediately to remote. Merge commits are accepted on branches, because we're squashing them anyway.

- **Avoid temporary commits on `develop`**  
  If you feel like committing, but the only message you can think of is _"Temp commit"_ or _"WIP some feature"_, just don't. Commit when you have a stable and clear implementation, or create a branch + Pull Request.

<br />

📜 **Commit messages**

Commit subject should be **short** and **to the point**. Don't explain what the problem was; explain what the commit is doing.

- **Use the imperative mood in the message**  
  Commit subjects should complete the sentence:

  > _"If applied, this commit will **your commit subject here**"_

  Examples: _"Replace icons file"_ or _"Prevent location search when offline"_, etc.

- **Commit subject should only explain the `what`**  
  Avoid explaining **`why`** or **`how`** in the commit subject. It should only describe **what** the commit is doing.

- **Commit body can explain the `why`**  
  Sometimes it helps to highlight **why** we created the commit. This can be described in the commit body.

- **Don't explain the `how` in the commit message**  
  You should never describe **how** you implemented a specific commit. You have the code diff for that.

<br />

## 📝 Code Comments

👎 Comments in code should generally be avoided. Usually, there are better alternatives:

- **What a function/constant does**  
  Instead of explaining the name of an identifier, better rename it to be self-explanatory. Yes, naming things is difficult, but the exercise pays off.

- **Magic values**  
  Instead of explaining what a magic number or string is, extract it to a constant and give it a self-explanatory name.

- **Commented out code**  
  Instead of commenting code, just remove it. You won't need it. And if you do, you can write it again, even better than the first time. And if you can't write it again, you have the git history.

<br />

👍 Comments could help in some situations:

- **Explaining the "why"**  
  Sometimes we have to implement **strange or weird code**, **intentionally bad code**, or take some **unorthodox approaches** that are not intuitive, nor self-explanatory. In such situations, comments help to document why such decisions were taken. Think of it like a written [Mea culpa](https://en.wikipedia.org/wiki/Mea_culpa).

  If possible, add a [test](#-tests) to ensure it doesn't get unfixed by mistake.

- **Regular expressions**  
  Regexes are inherently obscure and difficult to understand. Breaking them down and describing what they do using comments, might help when you need to touch that part of code.

<br />

## 🔁 Refactoring

Consider refactoring as a normal routine, like cleaning or maintenance:

- ⏰ **Every day**  
  Refactorings like **Renaming**, **Extracting**, **Inlining**, **Moving**, **Deleting** should be performed every time you encounter a better approach. They should be applied to `constants`, `variables`, `props`, `types`, `interfaces`, `functions`, `arguments`, `components`, `files`, `folders`, etc.

- 🗓 **Every sprint**  
  Whenever something _feels wrong_ or it becomes _difficult to explain_ to someone else, that's a code smell. Discuss it and plan the proper refactoring.

- ✂️ **Remove unused code**  
  You won't need it later. You might think you will, but **foreseeing** hasn't been proven yet as a human skill. Even [Wikipedia](https://en.wikipedia.org/wiki/Foresight_(psychology)) has little information about it.

<br />

## ✅ Tests

Put all the necessary effort to make your tests stupidly simple, that anybody can understand without reading the source of the system under test.

- **Don't test the tools**  
  Be [pragmatic](#-pragmatism), test only your own code, not the tools, frameworks, and libraries that you use.

- **Make sure you cover all code branches**  
  Usually you'll need at least one test for each `if` statement, `ternary` or `logical` operators like `&&` or `||`. Additional tests might be required to cover some combinations.

- **Use code coverage only as a double check**  
  A high coverage doesn't really mean anything. A low coverage, on the other hand, means that you don't have enough tests. Use coverage only to identify **what you've missed**. Don't use it as a metric for _how good your tests are_.

- **When testing collections, 2 items are enough**  
  Usually, if a collection implementation works with 2 items, it will work with more than 2 as well. If your logic doesn't specifically expect more than 2 items, there's no need to go above and beyond.

- **Use dummy data when input is irrelevant**  
  When you pass some input or stub something, and you don't care what that data is, use something as close to "nothing", such as: `0` for numbers, `""` for strings, `[]` for Arrays or `{}` for Objects.

- **Avoid concrete input, if not used in asserts, or not relevant for the test**  
  Passing `admin123` as a username, or `United States` as a country, could raise the question: _"Would the test pass if I put a different value?"_. Avoid this question by making them explicit.

  ```js
  // ❌ don't
  expect(getProfile({ country: 'United States', username: 'admin123' })).toEqual(...)

  // ✅ do
  expect(getProfile({ country: 'Any Country', username: 'any_username' })).toEqual(...)
  ```

  <br />

- **Describe test input & stubs**  
   Similar to _magic values_ extract hard-coded values to constants that describe what they are:

  ```js
  // ❌ don't
  expect(getCountry('40')).toEqual('Romania');

  // ✅ do
  const ROMANIA_COUNTRY_CODE = '40';
  expect(getCountry(ROMANIA_COUNTRY_CODE)).toEqual('Romania');
  ```

  ```js
  // ❌ don't
  expect(login('admin', '123456')).toEqual(true);

  // ✅ do
  const VALID_CREDENTIALS = { user: 'admin', pass: '123456' };
  expect(login(VALID_CREDENTIALS.user, VALID_CREDENTIALS.pass)).toEqual(true);
  ```

  ```js
  // ❌ don't
  expect(login('asdfghjkl', 'zxcvbnm')).toEqual(false);

  // ✅ do
  expect(login('invalid_user', 'invalid_pass')).toEqual(false);
  ```

<br />

## 🧩 Types

We use `TypeScript` because of two primary reasons:

1. **Communicates structure & design**  
   Very useful for deeply nested data structures like `Arrays` and `Objects`, to have IntelliSense on what properties are available and what their type is. It also forces you to think more deeply about code design and domain modelling.
   
2. **Documents function contracts**  
   You get out-of-the-box documentation on `function arguments` and their `return type`, easily finding out how to call a function/method and what to expect from it.

3. **Verifies that contracts are respected**  
   Let the type checker verify that you don't have any compile errors. Extremely useful in [refactorings](#-refactoring) to not miss any potential errors.

<br />

🚦 Type everything as strictly as possible:

- **Convert `Strings` to `Enums`**  
  Strings are difficult to change, refactor, and discover their accepted values. In isolated situations, where you don't need to re-use it in other files, you could also go for a `Union`. Otherwise, use `Enum`.

  ```js
  // ❌ don't
  function setDirection(direction: string) {}
  setDirection('up')

  // 👍 better
  function setDirection(direction: 'up' | 'down') {}
  setDirection('up')

  // ✅ best
  enum Direction {
    UP = 'up',
    DOWN = 'down',
  }

  function setDirection(direction: Direction) {}
  setDirection(Direction.UP)
  ```

<br />

- **Define `types` for `Objects`**  
  An arbitrary object structure doesn't communicate anything. You can't re-use it, you don't know what it represents. Putting the extra effort to define a `type` for your object, forces you to deeply think about code design & structure. You might realize that you end up having multiple definitions for the same concept.

  ```js
  // ❌ don't
  const user = {...}

  // ✅ do
  type User = {...}
  const user: User = {...}
  ```

    <br />

- **Specify items type for `Arrays`**  
  An array is usually a collection of a certain type. Specify what that type is.

  ```js
  // ❌ don't
  const usersList = []

  // ✅ do
  const usersList: User[] = []
  // or
  const usersList: Array<User> = []
  ```

<br />

- **Define boundary `types` for `responses` & `payloads`**  
   When fetching data from a server or sending data to a server, TS cannot infer the types. That's the **boundary** of your system. To have a `type-safe` system you need to ensure that your system starts and ends with the right types.

  Add suffixes for types used for boundary requests:

  - for data on `GET` requests, add the `Response` suffix, ie. `LocationsResponse`;
  - for payload on `POST`/`PUT`/`DELETE` requests, add the `Payload` suffix, ie. `ReservationPayload`.

<br />

- **Avoid type casting**  
   Forcing something to be something else is a _code smell_ and it should trigger the alarm that there might be better alternatives. However, at the boundaries of the application like `HTTP requests` or `3rd party libraries`, where proper typing is not available, casting might be necessary.

  ```js
  // ❌ this might be a lie & not type-checked
  const style = {...} as ViewStyle

  // ✅ this will be properly type-checked
  const style: ViewStyle = {...}
  ```

  ```js
  // ❌ don't
  const user = {
    address: {} as Address
  }

  // ✅ do
  const EMPTY_ADDRESS: Address: {...}
  const user = {
    address: EMPTY_ADDRESS
  }
  ```

  ```js
  // ❌ don't
  const [item, setItem] = useState(Enum.FIRST as string);

  // ✅ do
  const [item, setItem] = useState<Enum>(Enum.FIRST);
  ```

<br />

## 🎖 Leadership

There is no designated Team, or Tech Lead as a _role_. Leaders exist only as a _trait_. This enables the _team_ mindset and avoids _pulling ranks_.

> It's nice to be important, but it's more important to be nice.

- **Responsibility lies on the team**  
  We are a _self-organizing agile team_. If somebody fucks up, the team fucks up. You don't have a specific person to blame. It's every member's responsibility to make the necessary efforts that the team doesn't fuck up: **do, learn, and help**.

- **Everybody has something to say**  
  Without a hierarchy, we are all equal. Anybody can make mistakes, anybody can (and should) point out these mistakes. If there is something you believe that needs to change, bring it up, and let's talk about it.

- **Decisions are driven by reason**  
  Nobody can _impose_ anything. Anybody can bring proposals to the table. When you don't agree with something, motivate it.

<br />

## 🧠 Mindset

Software development goes beyond frameworks, libraries, programming paradigms, and technical challenges. The right mindset is a valuable trait, that will benefit you and your teammates at any stage of the project, and on any project.

- 🎓 **Be responsible**  
  Taking responsibility for your actions and your code doesn't mean that _"others will have someone to blame"_. It means that _"you don't have someone else to blame"_.

  Ignorance (as in _not knowing about something_) can be understandable, but it's not an excuse. Consider this when:

  - you re-use or copy-paste someone else's code, that turns out to be _bad_;
  - you follow someone's suggestion and it turns out it's not a good idea;
  - you get feedback after your PR was approved;
  - you install a dependency that might be harmful, etc.

<br />

- 🤸‍♀️ **Be agile**  
  Nothing is set in stone. Anything can change. _Resistance is futile_. Be comfortable with changing features, even late in the development process. Don't get emotionally attached to your code. For this you need to:
  - write your code **easy to change**;
  - make sure your code is **easy to remove**.

<br />

- 🎩 **Understand your managers, clients & users**  
  Depeche Mode puts it perfectly in [Walking In My Shoes](https://www.youtube.com/watch?v=GrC_yuzO-Ss). Before you judge and trash a decision from your _Client or Manager_, or feedback from your _Users_, try walking in their shoes. Understand their context and the reason for their decisions or feedback. If you think they're wrong, prove them wrong.
  - **being a manager yourself** and having to talk directly to clients (pushing back, contradicting them, or negotiating features and deadlines) will help you enjoy the comfort of not having to wear their shoes;
  - **being a client yourself** and witnessing what it takes to communicate your needs to a development team, will help you better understand _the other side_;
  - **being a user** of the software you're building, will help you better understand its flaws.

<br />

- 🏋️ **Get involved**  
  Replace **_"Let's do that"_** with **_"Let me do that"_**. When you want to propose something new: take the lead, be the driver, show the others how, why, and what to do. Don't just launch ideas.

<br />

- 🌞 **Be reasonable**  
  No one is perfect, and that's perfectly fine.
  - **be reasonable with yourself**: you will make mistakes and those mistakes will and should be highlighted by your teammates;
  - **be reasonable with others**: they will also make mistakes and you should highlight those mistakes in the most **friendly** possible way.
