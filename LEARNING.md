# Learning Angular with this project

This app is a small registration form built with modern Angular (v19): standalone components, signals, reactive forms and the new `@if` template syntax. Read the code in the order below, then do the exercises.

## 1. How the app starts
| File | What to notice |
|---|---|
| `src/main.ts` | `bootstrapApplication(AppComponent, appConfig)` starts the app. There is no NgModule. |
| `src/app/app.config.ts` | Global providers. `provideHttpClient()` makes `HttpClient` injectable everywhere. |
| `src/index.html` | Contains `<app-root>`, the element Angular replaces with `AppComponent`. |

## 2. Components
A component = a TypeScript class + a template + styles, tied together by `@Component`.
- `src/app/app.component.ts`: the root component. `imports: [...]` lists the other components its template uses (standalone components have no module).
- `src/app/register/register.component.ts|html|css`: the form.

Key ideas: `selector` (the HTML tag), `imports`, `templateUrl`, `styleUrl` (styles are scoped to this component).

## 3. Reactive forms (the heart of this project)
In `register.component.ts`:
- `FormBuilder.group({...})` builds the form model in code.
- Each field is `[initialValue, validators]`; built-in validators include `Validators.required`, `.email` and `.minLength`.
- `passwordsMatch` is a custom group-level validator.
- `form.invalid`, `control.touched` and `control.dirty` drive when errors appear.

In `register.component.html`:
- `[formGroup]="form"` binds the form; `formControlName="email"` binds each input.
- `(ngSubmit)="submit()"` is event binding.

## 4. Template syntax cheat sheet
| Syntax | Meaning | Example here |
|---|---|---|
| `{{ expr }}` | interpolation | `{{ submitting() ? 'Creating…' : 'Register' }}` |
| `[prop]="expr"` | property binding | `[disabled]="submitting()"` |
| `(event)="fn()"` | event binding | `(ngSubmit)="submit()"` |
| `@if (...) {}` | conditional | error messages |
| `@for (x of xs; track x.id) {}` | loop | (see exercise 3) |

## 5. Signals
`signal(value)` holds state. Read with `submitting()`, write with `.set(...)` or `.update(fn)`. The template re-renders when a signal it reads changes.

## 6. Dependency injection and HTTP
`inject(HttpClient)` asks Angular for a service. `http.post(...)` returns an Observable; nothing is sent until you `.subscribe(...)`. `proxy.conf.json` forwards `/auth` to the API gateway so the browser sees one origin (no CORS setup needed in dev).

## Commands
```
npm start        # dev server on http://localhost:4200 with live reload
npm test         # unit tests (Karma/Jasmine)
npm run build    # production build into dist/
npx ng generate component login   # scaffold a new component
```

## Exercises (in increasing difficulty)
1. **Add a field.** Add an optional `phone` input with a `pattern` validator. Show an error when it is malformed.
2. **Password strength.** Write a custom validator requiring at least one digit; show a specific message.
3. **Use `@for`.** Show a checklist of password rules that turn green as they are satisfied.
4. **Extract a service.** Move the `http.post` into `AuthService` (`ng generate service auth`) with `providedIn: 'root'`, and inject it into the component.
5. **Login page.** Create `LoginComponent` for `POST /auth/login`. Store the returned `token` in `localStorage`.
6. **Routing.** Add `provideRouter(routes)` back to `app.config.ts`, define `/register` and `/login` routes, and add `<router-outlet />` plus `routerLink` navigation.
7. **HTTP interceptor.** Write a functional interceptor that adds `Authorization: Bearer <token>` to requests, then call `GET /users/me` and display the profile.
8. **Test it.** Write a spec that checks the form is invalid when empty and that mismatched passwords set the `mismatch` error.

## Where to read more
- Official tutorial: https://angular.dev/tutorials
- Reactive forms guide: https://angular.dev/guide/forms/reactive-forms
- Signals guide: https://angular.dev/guide/signals
