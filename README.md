# Functional Programming in TypeScript Manifesto

## Core Principles

1. **Simplicity First**
   - Code should be easy to understand, reason about, and maintain
   - Prefer simple solutions over clever ones
   - If you can't explain your code to a colleague in a few sentences, it's probably too complex

2. **Immutability by Default**
   ```typescript
   // ❌ Don't mutate
   const updateUser = (user: User) => {
     user.name = "New Name";
     return user;
   };

   // ✅ Create new instances
   const updateUser = (user: User): User => ({
     ...user,
     name: "New Name"
   });
   ```

3. **Composition Over Inheritance**
   ```typescript
   // ❌ Avoid class hierarchies
   class Animal extends LivingThing {
     makeSound(): void {
       // ...
     }
   }

   // ✅ Compose functions and interfaces
   interface Animal {
     makeSound: () => void;
   }
   
   const withSound = (sound: string) => ({
     makeSound: () => console.log(sound)
   });
   ```

4. **Pure Functions When Possible**
   ```typescript
   // ❌ Avoid side effects
   let total = 0;
   const addToTotal = (n: number) => {
     total += n;
   };

   // ✅ Pure functions
   const add = (a: number, b: number) => a + b;
   ```

## Practical Guidelines

### 1. Library Usage
- Use `fp-ts` for core FP patterns, but only when benefits outweigh complexity
- Prefer native TypeScript solutions when they're clear enough
```typescript
// ❌ Overly complex for simple operations
import { pipe } from 'fp-ts/function'
import { map } from 'fp-ts/Array'

const doubleNumbers = pipe(
  numbers,
  map((n) => n * 2)
)

// ✅ Clear and simple
const doubleNumbers = numbers.map(n => n * 2)
```

### 2. Error Handling
- Use `Either` or `Option` types for explicit error handling
- But prefer simple solutions for straightforward cases
```typescript
// ✅ For complex error handling
import { Either, right, left } from 'fp-ts/Either'

const divide = (a: number, b: number): Either<string, number> =>
  b === 0 ? left('Division by zero') : right(a / b)

// ✅ For simple cases
const parseNumber = (s: string): number | null =>
  isNaN(Number(s)) ? null : Number(s)
```

### 3. Type Safety
- Leverage TypeScript's type system without overcomplicating
```typescript
// ❌ Over-engineered
type UserStatus = 'active' | 'inactive'
type UserRole = 'admin' | 'user'
type User<S extends UserStatus, R extends UserRole> = {
  status: S
  role: R
}

// ✅ Clear and type-safe
type User = {
  status: 'active' | 'inactive'
  role: 'admin' | 'user'
}
```

### 4. Function Composition
- Use composition to build complex behavior from simple parts
```typescript
// ✅ Simple composition
const validateEmail = (email: string): boolean => /* ... */
const normalizeEmail = (email: string): string => /* ... */
const saveEmail = (email: string): Promise<void> => /* ... */

const processEmail = async (email: string): Promise<void> => {
  const normalized = normalizeEmail(email)
  if (!validateEmail(normalized)) {
    throw new Error('Invalid email')
  }
  await saveEmail(normalized)
}
```

### 5. Data Transformation
- Use pipelines for data transformation when it improves readability
```typescript
// ✅ Clear transformation pipeline
const processUserData = (users: User[]): ProcessedUser[] =>
  users
    .filter(user => user.active)
    .map(user => ({
      ...user,
      lastLoginDate: formatDate(user.lastLogin)
    }))
    .sort((a, b) => b.lastLogin - a.lastLogin)
```

## Anti-patterns to Avoid

1. **Excessive Abstraction**
   - Don't create abstractions until they prove necessary
   - Avoid premature optimization

2. **Overuse of FP Concepts**
   - Don't force monads where a simple null check would do
   - Use FP patterns when they make code clearer, not more complex

3. **Type Gymnastics**
   - Avoid complex type manipulations that obscure the code's intent
   - Prefer explicit types over inferred ones when clarity is needed

## Code Organization

1. **Module Structure**
   ```typescript
   // Group related functions together
   // Export interfaces first, then functions
   export interface Config { /* ... */ }
   export const createConfig = (/* ... */): Config => /* ... */
   export const validateConfig = (config: Config): boolean => /* ... */
   ```

2. **Testing**
   ```typescript
   // Pure functions are easy to test
   describe('validateConfig', () => {
     it('should validate correct config', () => {
       const config = createConfig(/* ... */)
       expect(validateConfig(config)).toBe(true)
     })
   })
   ```

Remember: The goal is to write maintainable, reliable code that solves real problems. Use functional programming principles to reduce complexity, not increase it.

