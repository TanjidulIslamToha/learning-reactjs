<!-- React Learning Checklist -->

# React Learning Checklist

## 0. Prep Work (JavaScript Refresh)
- Revisit ES6+ syntax: arrow functions, const/let, template literals, destructuring.
- Practice modules (`import`/`export`) and default vs named exports.
- Brush up on array/object helpers (`map`, `filter`, `reduce`, spread/rest).
- Understand asynchronous JavaScript: Promises, async/await, fetch API.
- Review tooling basics: Node.js, npm, package.json scripts.

## 1. Core React Fundamentals
- Install Node.js LTS and ensure `npm` works.
- Learn JSX fundamentals and how it translates to `React.createElement`.
- Understand components: function components, props, prop types.
- Practice composing components and passing data down the tree.
- Explore conditional rendering and inline styles/className handling.
- Use lists with keys for deterministic rendering.

## 2. State & Side Effects
- Use `useState` for local state management.
- Derive state from props safely; avoid unnecessary duplication.
- Handle forms and controlled inputs.
- Fetch data with `useEffect`, including cleanup logic.
- Understand dependency arrays and common pitfalls (stale closures, infinite loops).

## 3. Component Communication
- Lift state up versus using callbacks to communicate.
- Pass callbacks for child-to-parent communication.
- Explore Context API for avoiding prop drilling.
- Learn when to use custom hooks to reuse logic.

## 4. Routing & Navigation
- Install React Router DOM.
- Configure BrowserRouter, Routes, and Route components.
- Create navigation with `Link`/`NavLink`.
- Handle URL params, nested routes, and 404 fallbacks.

## 5. Styling Options
- Compare CSS modules, Tailwind, styled-components, and inline styles.
- Choose a baseline approach (CSS modules or utility-first) for the starter project.
- Learn responsive design patterns (flexbox, grid, breakpoints).
- Add simple animations with CSS transitions or libraries (Framer Motion).

## 6. Data & Async Patterns
- Manage loading, error, and empty states.
- Explore simple global state patterns (Context, custom hooks).
- Understand how to integrate APIs with fetch or axios.
- Cache data using query libraries (React Query) once comfortable.

## 7. Performance Basics
- Learn how reconciliation works and why keys matter.
- Use `React.memo`, `useMemo`, `useCallback` judiciously.
- Split bundles with lazy loading and `Suspense`.
- Measure performance with React DevTools profiler.

## 8. Testing & Quality
- Install Jest and React Testing Library (via CRA defaults).
- Write tests covering components, hooks, and user interactions.
- Use ESLint + Prettier to keep code consistent.
- Keep docs updated as patterns evolve.

## 9. Deployment & Maintenance
- Build the production bundle and analyze bundle size.
- Deploy to services like Netlify, Vercel, or GitHub Pages.
- Automate formatting/tests via npm scripts or CI.
- Track learnings in `docs/practices/` after each module or project milestone.

---

### Next Steps
- Follow the modules in `learning-modules/` sequentially.
- Update the practices logs with insights, questions, and follow-up items.
- Apply concepts directly in the `projects/basic-react-app/` starter project.

