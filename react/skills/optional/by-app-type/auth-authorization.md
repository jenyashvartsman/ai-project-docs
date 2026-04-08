# React Auth And Authorization Guide

## Purpose

This guide defines a production-grade authentication and authorization strategy for React applications.

The goal is to keep auth concerns centralized, predictable, and easy for both developers and AI agents to apply consistently.

## Core Principle

Keep authentication global and authorization explicit.

- authentication is an app-level concern
- authorization rules should be visible and reusable
- features should consume auth state, not reinvent it

## Authentication Vs Authorization

Authentication answers:

- is the user signed in
- who is the current user
- is the session still valid

Authorization answers:

- can the user access this route
- can the user perform this action
- does the user have the required permission or role

Do not collapse these into one vague boolean when a feature needs both.

## Recommended Structure

```text
src/
  core/
    auth/
      auth-api.ts
      auth-provider.tsx
      auth-context.ts
      use-auth.ts
      permission-service.ts
      types/
        current-user.dto.ts
```

## Ownership

Authentication should be owned in `core/auth` or another app-level boundary.

That boundary should expose:

- current user
- auth status
- auth loading state
- sign-in
- sign-out
- session initialization or refresh if needed

## Auth Provider Example

```tsx
type AuthContextValue = {
  currentUser: CurrentUserDto | null;
  isAuthenticated: boolean;
  isLoading: boolean;
  initialize: () => Promise<void>;
  signIn: (email: string, password: string) => Promise<void>;
  signOut: () => Promise<void>;
};
```

```tsx
export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [currentUser, setCurrentUser] = useState<CurrentUserDto | null>(null);
  const [isLoading, setIsLoading] = useState(false);

  const isAuthenticated = currentUser !== null;

  async function initialize() {
    setIsLoading(true);

    try {
      const user = await getSession();
      setCurrentUser(user);
    } catch {
      setCurrentUser(null);
    } finally {
      setIsLoading(false);
    }
  }

  async function signIn(email: string, password: string) {
    setIsLoading(true);

    try {
      const user = await signInRequest({ email, password });
      setCurrentUser(user);
    } finally {
      setIsLoading(false);
    }
  }

  async function signOut() {
    await signOutRequest();
    setCurrentUser(null);
  }

  return (
    <AuthContext.Provider
      value={{
        currentUser,
        isAuthenticated,
        isLoading,
        initialize,
        signIn,
        signOut,
      }}
    >
      {children}
    </AuthContext.Provider>
  );
}
```

## Route Protection

Protect routes at the router layer.

Good options:

- protected route wrappers
- loader redirects
- router-specific auth middleware

Do not rely only on conditional rendering inside page components.

Example:

```tsx
export function ProtectedRoute({ children }: { children: React.ReactNode }) {
  const { isAuthenticated } = useAuth();

  if (!isAuthenticated) {
    return <Navigate to="/sign-in" replace />;
  }

  return <>{children}</>;
}
```

## Permission Checks

Permission checks should be explicit and reusable.

Example:

```ts
export function hasPermission(
  user: CurrentUserDto | null,
  permission: string
): boolean {
  return !!user?.permissions.includes(permission);
}
```

Use permission checks in:

- route protection
- container components
- feature hooks when action availability depends on auth state

Avoid scattering raw permission string checks across many component files.

## Feature Usage

Features may:

- show the current user name
- hide or disable actions
- shape feature behavior based on roles or permissions

Features should not:

- own their own session store
- call auth APIs directly from presentation components
- implement their own session-expiration flow

## Session Expiration

Expired-session handling should be consistent across the app.

Typical behavior:

- clear auth state
- redirect to sign-in
- optionally preserve a return URL
- show a clear session-expired message when appropriate

## Anti-Patterns

Avoid:

- duplicating auth state in many features
- injecting auth logic into presentation components
- relying only on hidden UI for authorization
- checking raw permission strings across templates everywhere
- letting each feature implement its own auth flow

## AI Agent Rules

When creating or updating React auth code, AI agents must follow these rules:

1. Keep auth state centralized.
2. Protect routes at the router layer.
3. Keep permission checks explicit and reusable.
4. Do not duplicate session state across features.
5. Do not inject auth APIs into presentation components.
6. Keep session-expiration handling consistent across the app.

## Default Standard

Use one central auth provider or auth store, route-level protection, and reusable permission helpers.
