# Chapter 13 TypeScript & React: Components, State, and Safety

This chapter introduces the two technologies that power most modern web applications. TypeScript catches errors before your code runs. React lets you build interfaces from reusable pieces. Together, they form the standard toolkit for Mode 3 development.

## Key Terms

| Term           | What it means                                                                          |
| -------------- | -------------------------------------------------------------------------------------- |
| **TypeScript** | JavaScript with type annotations: the compiler catches errors before runtime           |
| **Type**       | A label that says what kind of value a variable holds (string, number, boolean)        |
| **Interface**  | A TypeScript definition of an object's shape (which properties it has and their types) |
| **Component**  | A reusable piece of UI (a button, a card, a form, a full page)                         |
| **Props**      | Data passed from a parent component to a child component                               |
| **State**      | Data that a component manages and can change over time                                 |
| **Hook**       | A special React function that lets components use state and other features             |

## TypeScript: JavaScript That Catches Mistakes

JavaScript is forgiving. Too forgiving. It lets you add a number to a string, call a function that doesn't exist, or pass the wrong type of data to an API (Chapter 14 covers APIs and HTTP). These mistakes crash at runtime, in production, when a user is trying to check out.

TypeScript adds a layer of checking that catches these mistakes while you're still writing code.

```typescript
// JavaScript: no complaints, crashes later
function calculateTotal(price, quantity) {
  return price * quantity;
}
calculateTotal("ten", 3); // NaN (not a number). No error. Silent failure.

// TypeScript: catches the mistake immediately
function calculateTotal(price: number, quantity: number): number {
  return price * quantity;
}
calculateTotal("ten", 3); // Error: Argument of type 'string' is not assignable to parameter of type 'number'
```

The `: number` annotations are types. They tell TypeScript (and your AI assistant) what kind of data each variable holds.

### Interfaces: Defining Object Shapes

When your data has structure (a review has a rating, comment, and date), TypeScript lets you define that structure:

```typescript
interface Review {
  id: string;
  rating: number;
  comment: string;
  createdAt: Date;
  author: {
    name: string;
    email: string;
  };
}

// TypeScript knows exactly what a Review looks like
function displayReview(review: Review) {
  console.log(`${review.author.name}: ${review.rating}/5`);
  console.log(review.comment);
}

// This works
displayReview({
  id: "r1",
  rating: 5,
  comment: "Brilliant",
  createdAt: new Date(),
  author: { name: "Sofia", email: "sofia@example.com" },
});

// This fails: missing 'author' property
displayReview({
  id: "r2",
  rating: 4,
  comment: "Good",
  createdAt: new Date(),
});
// Error: Property 'author' is missing
```

Interfaces serve double duty: they catch bugs and they document your data. When you read an interface, you know exactly what shape the data takes. AI assistants perform dramatically better when your code has TypeScript types, because the types tell the AI what it can and cannot do.

### JobPilot's User Profile Interface

Here's a simplified version of the interface you'll use in JobPilot to store someone's professional profile:

```typescript
interface UserProfile {
  name: string;
  email: string;
  phone: string;
  location: string;
  currentRole: string;
  linkedIn: string;
  workHistory: WorkEntry[];
  education: EducationEntry[];
  skills: string[];
}

interface WorkEntry {
  company: string;
  title: string;
  startDate: string;
  endDate: string | null; // null means "still working here"
  description: string;
}
```

Every part of JobPilot that handles profile data references `UserProfile`. The popup uses it when saving form input. The service worker uses it when sending data to OpenAI. The content script uses it when filling application fields. If you add a new property (say, a portfolio URL), TypeScript immediately flags every place in the codebase that creates a `UserProfile` without providing the new field. No hunting through 15 files. The compiler tells you.

If you're building a web app instead, the same principle applies. Define your data shapes as interfaces, and TypeScript catches mistakes across your entire project.

> **Try It:** Open any TypeScript file (or create one). Define an interface with three properties. Create an object that matches it, then deliberately misspell a property name. The red underline that appears is TypeScript saving you from a runtime bug.

## React: Build Interfaces From Reusable Pieces

React breaks interfaces into components. Each component is a function that returns a piece of UI.

```tsx
// A simple component: a review card
function ReviewCard({ review }: { review: Review }) {
  return (
    <div className="review-card">
      <div className="rating">{"★".repeat(review.rating)}</div>
      <p>{review.comment}</p>
      <span className="author">{review.author.name}</span>
    </div>
  );
}

// Using the component
function ReviewList({ reviews }: { reviews: Review[] }) {
  return (
    <div>
      <h2>Customer Reviews</h2>
      {reviews.map((review) => (
        <ReviewCard key={review.id} review={review} />
      ))}
    </div>
  );
}
```

`ReviewCard` is a component. `ReviewList` is a component that uses `ReviewCard`. This nesting is how React applications are built: small components compose into larger ones.

### Props: Data Flowing Down

Props are the data you pass to a component. In the example above, `review` is a prop. The parent (`ReviewList`) passes it to the child (`ReviewCard`). Data flows in one direction: parent to child.

```tsx
// Parent decides what to show
<ReviewCard review={myReview} />;

// Child receives and displays it
function ReviewCard({ review }: { review: Review }) {
  // review is available here
}
```

### useState: How Components Remember Things

Components need to track changing data. React provides `useState` for this:

```tsx
function RatingSelector() {
  const [rating, setRating] = useState(0);

  return (
    <div>
      <p>Your rating: {rating}</p>
      {[1, 2, 3, 4, 5].map((n) => (
        <button
          key={n}
          onClick={() => setRating(n)}
          style={{ fontWeight: n <= rating ? "bold" : "normal" }}
        >
          {n}
        </button>
      ))}
    </div>
  );
}
```

`useState(0)` creates a state variable (`rating`) with an initial value of 0 and a function to update it (`setRating`). When the user clicks a button, `setRating` updates the value. React re-renders the component with the new value. The UI stays in sync with the data automatically.

### useEffect: Reacting to Changes

`useEffect` runs code when something changes. The most common use: fetching data when a component first appears.

```tsx
function ReviewDashboard() {
  const [reviews, setReviews] = useState<Review[]>([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function loadReviews() {
      const response = await fetch("/api/reviews");
      const data = await response.json();
      setReviews(data);
      setLoading(false);
    }
    loadReviews();
  }, []); // Empty array: run once when the component mounts

  if (loading) return <p>Loading...</p>;

  return <ReviewList reviews={reviews} />;
}
```

The empty array `[]` at the end means "run this effect once, when the component first renders". If you put a variable in the array, the effect re-runs whenever that variable changes.

## The Ecosystem

React on its own handles structure and behaviour. For a complete application, you'll use additional tools:

**Tailwind CSS:** Write styles directly in your HTML using utility classes.

```tsx
// Traditional CSS: write a separate stylesheet
<div className="review-card">  // .review-card { padding: 16px; border: 1px solid #ccc; }

// Tailwind: style inline with utility classes
<div className="p-4 border border-gray-300 rounded-lg shadow-sm">
```

Tailwind eliminates the need for separate CSS files for most styling. You describe the appearance right where the component is defined.

**shadcn/ui:** Pre-built, customisable components (buttons, forms, modals, tables). Not a dependency you install. A collection of code you copy into your project and own.

**Radix:** The accessibility layer beneath shadcn/ui. Handles keyboard navigation, screen reader support, and focus management.

The typical 2026 React stack: React + TypeScript + Tailwind + shadcn/ui + Vite.

## Common Beginner Mistake

**Mutating state directly instead of using the setter function.** React doesn't detect changes when you modify an object directly. You must create a new object:

```tsx
// Wrong: React doesn't re-render
reviews.push(newReview);
setReviews(reviews);

// Correct: new array triggers a re-render
setReviews([...reviews, newReview]);
```

The spread operator (`...`) creates a copy. React compares the old and new references. Different reference means something changed. Same reference means nothing happened.

## Checklist

- [ ] I understand why TypeScript catches errors that JavaScript misses
- [ ] I can read an interface and know what shape the data takes
- [ ] I understand components as reusable pieces of UI
- [ ] I know the difference between props (data in) and state (data that changes)
- [ ] I can recognise useState (component memory) and useEffect (side effects)
- [ ] I know the modern React stack: React + TypeScript + Tailwind + shadcn/ui + Vite

## Summary

TypeScript adds type checking to JavaScript, catching errors before code runs. Interfaces define the shape of objects, serving as both documentation and validation. React builds interfaces from reusable components. Props pass data from parent to child. useState lets components track changing data. useEffect runs code in response to changes (typically for fetching data). The modern stack combines React with TypeScript for safety, Tailwind for styling, and shadcn/ui for pre-built components.

## Challenge

1. Install a React + TypeScript project: `npm create vite@latest my-app -- --template react-ts`. Open it. Find the `App.tsx` component. Add a counter with `useState` that increments when you click a button.

2. Create a `Review` interface with five properties. Build a `ReviewCard` component that receives a `Review` as a prop and displays it. Create an array of three reviews and render them with a `ReviewList` component.
