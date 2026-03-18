# Detailed Patterns & Examples

Extended examples for each rule when the SKILL.md summary isn't enough.

## Table of Contents

- [Derived State](#derived-state)
- [Expensive Calculations with useMemo](#expensive-calculations-with-usememo)
- [Data Fetching Race Conditions](#data-fetching-race-conditions)
- [Event Handler Extraction](#event-handler-extraction)
- [Conditional Mounting](#conditional-mounting)
- [Key-Based Reset](#key-based-reset)
- [Adjusting State on Prop Change](#adjusting-state-on-prop-change)
- [Effect Chains](#effect-chains)
- [Parent Notification](#parent-notification)
- [External Store Subscription](#external-store-subscription)
- [App Initialization](#app-initialization)

---

## Derived State

### Chained derived values

```tsx
// BAD: effect chain for tax/total
function Cart({ subtotal }) {
  const [tax, setTax] = useState(0);
  const [total, setTotal] = useState(0);

  useEffect(() => { setTax(subtotal * 0.1); }, [subtotal]);
  useEffect(() => { setTotal(subtotal + tax); }, [subtotal, tax]);
}

// GOOD: inline computation
function Cart({ subtotal }) {
  const tax = subtotal * 0.1;
  const total = subtotal + tax;
}
```

### fullName from firstName + lastName

```tsx
// BAD
const [fullName, setFullName] = useState('');
useEffect(() => {
  setFullName(firstName + ' ' + lastName);
}, [firstName, lastName]);

// GOOD
const fullName = firstName + ' ' + lastName;
```

---

## Expensive Calculations with useMemo

```tsx
// BAD
const [visibleTodos, setVisibleTodos] = useState([]);
useEffect(() => {
  setVisibleTodos(getFilteredTodos(todos, filter));
}, [todos, filter]);

// GOOD
const visibleTodos = useMemo(
  () => getFilteredTodos(todos, filter),
  [todos, filter]
);
```

**When is a calculation expensive?** Measure with `console.time`. Generally only worth memoizing if >1ms on throttled CPU.

---

## Data Fetching Race Conditions

### The problem with bare fetch in effects

User types "hello" fast. Requests fire for "h", "he", "hel", "hell", "hello". Response for "hell" may arrive after "hello", showing wrong results.

### If you must use an effect for fetching (no library available)

Encapsulate in a custom hook — never use `useEffect` directly in a component. Always add cleanup:

### Extract into a custom hook

```tsx
function useData(url) {
  const [data, setData] = useState(null);
  useEffect(() => {
    let ignore = false;
    fetch(url)
      .then(r => r.json())
      .then(json => { if (!ignore) setData(json); });
    return () => { ignore = true; };
  }, [url]);
  return data;
}
```

**Note:** `useEffect` inside a custom hook like `useData` is the accepted escape hatch — the rule bans direct `useEffect` in components, not in reusable hooks that encapsulate side effects behind a clean interface.

### Best: use a data-fetching library

React Query, SWR, TanStack Query, or framework-provided fetching. These handle caching, deduplication, cancellation, retries, background refetching, stale-while-revalidate, and SSR hydration.

---

## Event Handler Extraction

### Shared logic between multiple handlers

```tsx
// BAD: effect watches product.isInCart
useEffect(() => {
  if (product.isInCart) {
    showNotification(`Added ${product.name}!`);
  }
}, [product]);

// GOOD: shared function called from handlers
function buyProduct() {
  addToCart(product);
  showNotification(`Added ${product.name}!`);
}

function handleBuyClick() { buyProduct(); }
function handleCheckoutClick() { buyProduct(); navigateTo('/checkout'); }
```

### Form submission

```tsx
// BAD: effect watches jsonToSubmit
const [jsonToSubmit, setJsonToSubmit] = useState(null);
useEffect(() => {
  if (jsonToSubmit !== null) post('/api/register', jsonToSubmit);
}, [jsonToSubmit]);

// GOOD: submit in the handler
function handleSubmit(e) {
  e.preventDefault();
  post('/api/register', { firstName, lastName });
}
```

### Analytics vs. user actions

Analytics that fire "because the component was displayed" are valid for `useMountEffect`. Form submission that fires "because the user clicked submit" belongs in the handler.

---

## Conditional Mounting

Instead of guarding inside an effect, control mounting in the parent:

```tsx
// BAD
function VideoPlayer({ isLoading }) {
  useEffect(() => {
    if (!isLoading) playVideo();
  }, [isLoading]);
}

// GOOD: parent controls when to mount
function VideoPlayerWrapper({ isLoading }) {
  if (isLoading) return <LoadingScreen />;
  return <VideoPlayer />;
}

function VideoPlayer() {
  useMountEffect(() => playVideo());
}
```

### Persistent shell + conditional instance

When you need a persistent container but conditional behavior:

```tsx
function VideoPlayerContainer({ isLoading }) {
  return (
    <>
      <VideoPlayerShell isLoading={isLoading} />
      {!isLoading && <VideoPlayerInstance />}
    </>
  );
}

function VideoPlayerInstance() {
  useMountEffect(() => playVideo());
}
```

---

## Key-Based Reset

### Resetting all state when entity changes

```tsx
// BAD
function ProfilePage({ userId }) {
  const [comment, setComment] = useState('');
  useEffect(() => { setComment(''); }, [userId]);
}

// GOOD: key forces full reset
function ProfilePage({ userId }) {
  return <Profile userId={userId} key={userId} />;
}

function Profile({ userId }) {
  const [comment, setComment] = useState('');
  // Fresh state on each userId change
}
```

### Resetting form state

```tsx
// BAD: effect clears form fields
useEffect(() => {
  setName('');
  setEmail('');
  setNotes('');
}, [contactId]);

// GOOD
<EditForm key={contactId} contact={contact} />
```

---

## Adjusting State on Prop Change

When you only need to adjust some state (not reset everything):

### Best: derive it during render

```tsx
function List({ items }) {
  const [selectedId, setSelectedId] = useState(null);
  // If selected item no longer exists, selection clears automatically
  const selection = items.find(item => item.id === selectedId) ?? null;
}
```

### Acceptable: adjust during render with previous props tracking

```tsx
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selection, setSelection] = useState(null);
  const [prevItems, setPrevItems] = useState(items);

  if (items !== prevItems) {
    setPrevItems(items);
    setSelection(null);
  }
}
```

---

## Effect Chains

### The game card example

```tsx
// BAD: 4 effects chaining state updates = 4 extra renders
useEffect(() => { if (card?.gold) setGoldCardCount(c => c + 1); }, [card]);
useEffect(() => { if (goldCardCount > 3) { setRound(r => r + 1); setGoldCardCount(0); } }, [goldCardCount]);
useEffect(() => { if (round > 5) setIsGameOver(true); }, [round]);
useEffect(() => { if (isGameOver) alert('Good game!'); }, [isGameOver]);

// GOOD: all logic in the event handler, derived state inline
const isGameOver = round > 5;

function handlePlaceCard(nextCard) {
  if (isGameOver) throw Error('Game already ended.');
  setCard(nextCard);
  if (nextCard.gold) {
    if (goldCardCount < 3) {
      setGoldCardCount(goldCardCount + 1);
    } else {
      setGoldCardCount(0);
      setRound(round + 1);
      if (round === 5) alert('Good game!');
    }
  }
}
```

---

## Parent Notification

### Option A: update both in same event

```tsx
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  function updateToggle(nextIsOn) {
    setIsOn(nextIsOn);
    onChange(nextIsOn);
  }

  return <button onClick={() => updateToggle(!isOn)}>Toggle</button>;
}
```

### Option B: fully controlled component (preferred)

```tsx
function Toggle({ isOn, onChange }) {
  return <button onClick={() => onChange(!isOn)}>Toggle</button>;
}
```

### Passing data to parent

```tsx
// BAD: child fetches, then notifies parent via effect
useEffect(() => { if (data) onFetched(data); }, [onFetched, data]);

// GOOD: parent owns the fetch, passes data down
function Parent() {
  const data = useSomeAPI();
  return <Child data={data} />;
}
```

---

## External Store Subscription

```tsx
// BAD: manual subscription
useEffect(() => {
  const handler = () => setIsOnline(navigator.onLine);
  window.addEventListener('online', handler);
  window.addEventListener('offline', handler);
  return () => {
    window.removeEventListener('online', handler);
    window.removeEventListener('offline', handler);
  };
}, []);

// GOOD: useSyncExternalStore
function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

function useOnlineStatus() {
  return useSyncExternalStore(
    subscribe,
    () => navigator.onLine,
    () => true // SSR fallback
  );
}
```

---

## App Initialization

```tsx
// BAD: runs twice in dev, can invalidate tokens
useEffect(() => {
  loadDataFromLocalStorage();
  checkAuthToken();
}, []);

// GOOD option 1: module-level
if (typeof window !== 'undefined') {
  checkAuthToken();
  loadDataFromLocalStorage();
}

// GOOD option 2: guard variable with useMountEffect
let didInit = false;
function App() {
  useMountEffect(() => {
    if (!didInit) {
      didInit = true;
      loadDataFromLocalStorage();
      checkAuthToken();
    }
  });
}
```
