# React Development

Modern React development guidance for building production-ready applications within DBCA. This guide provides recommended patterns and tooling for frontend development, with emphasis on type safety, maintainability, and developer experience.

This represents a recommended approach based on industry standards rather than strict requirements. Teams should evaluate these patterns against their specific project needs and constraints. For simple applications, Django templates may be sufficient; this guide addresses scenarios where interactive, complex frontends provide clear value.

**Note**: All code snippets in this guide are examples to illustrate patterns and concepts. Adapt them to your specific requirements.

## Why Frontend/Backend Separation

Modern web applications benefit from separating frontend and backend concerns. This architectural approach has become industry standard for applications requiring rich interactivity, complex state management, or independent scaling.

**Independent Scaling**: Frontend and backend scale separately based on load patterns. Read-heavy applications deploy more frontend instances, write-heavy applications scale the backend. This flexibility reduces infrastructure costs and improves performance.

**Technology Flexibility**: Each layer adopts new technologies independently. Upgrading React doesn't require touching Django, and vice versa.

**Team Specialisation**: Clear API contracts enable parallel development, faster iteration, and better utilisation of specialised skills.

**Better Testing**: Frontend tests mock API responses, backend tests verify business logic independently, and integration tests validate the contract between them.

**Deployment Flexibility**: Frontend and backend deploy independently with potential for separate release cycles. Frontend updates don't require backend coordination, enabling faster iteration on user-facing features and polish. This separation allows frontend design improvements to ship without requiring approvals for the backend as business logic remains unchanged.

**Monorepo Structure**: Keeping frontend and backend in the same repository provides practical benefits for security teams, while maintaining architectural separation. Both applications share version control, issue tracking, and CI/CD pipelines. Developers make coordinated changes across the stack via pull requests, ensuring API contracts and implementations stay synchronised.

Each application builds its own Docker image, deploys independently, and has the potential to scale separately. Docker's official documentation explicitly recommends this separation: 
- ["It's best practice to separate areas of concern by using one service per container"](https://docs.docker.com/engine/containers/multi-service_container/)
- ["Each container should have only one concern"](https://docs.docker.com/build/building/best-practices/).

**Build Optimisation**: Separate Docker images enable intelligent build strategies. CI/CD pipelines detect changes and build only affected services. Frontend changes trigger frontend builds, backend changes trigger backend builds. Multi-stage builds separate build-time dependencies from runtime requirements, producing smaller images (often 10x reduction) with improved security.

**Trade-offs**: Separation introduces deployment coordination overhead (for large teams) and an additional Docker image. The benefits—security through multi-stage builds, independent scaling, and clear boundaries—outweigh the initial setup complexity. Multi-stage frontend builds produce lean images with negligible storage costs; these tradeoffs are acceptable for most applications.

## Technology Stack

The recommended stack balances developer experience, performance, and maintainability. Each technology solves specific problems while integrating naturally with the others. These choices reflect current industry standards.

**React** provides the foundation for building interactive user interfaces. As the most widely adopted frontend framework, it offers excellent hiring pipelines, extensive ecosystem support, and mature tooling. React's concurrent features enable sophisticated performance optimisations, while its component model promotes reusable, testable code. Compared to Vue's smaller ecosystem or Angular's more opinionated structure, React provides flexibility without sacrificing community support.

**TypeScript** catches errors at compile time rather than runtime, dramatically improving code reliability. Type definitions serve as living documentation, making codebases easier to understand and refactor. Modern frontend development without TypeScript means accepting preventable bugs and reduced IDE support. The initial learning curve pays dividends through fewer production issues and better developer experience.

**Vite** replaces older build tools like Webpack with significantly faster development and build times. Native ES modules support eliminates bundling during development, providing instant server starts and sub-100ms hot module replacement. Excellent TypeScript support works out of the box without configuration. Create React App has been deprecated, and Webpack's slower build times make Vite the clear choice for new projects.

**Bun** serves as a drop-in Node.js replacement with dramatically faster package installation and script execution. Installation times drop from minutes to seconds, reducing development friction and CI/CD costs. While npm and yarn remain viable, Bun's performance improvements justify adoption for teams prioritising developer velocity.

**TanStack Query** eliminates server state management boilerplate through intelligent caching, automatic refetching, and built-in loading/error states. Compared to manual fetch with useState (excessive boilerplate) or Redux (overkill for server state), TanStack Query provides the right abstraction level. It handles cache invalidation, request deduplication, and background updates automatically, letting developers focus on business logic rather than data fetching mechanics.

**MobX** manages client-side state with minimal boilerplate and excellent TypeScript integration. Automatic reactivity eliminates manual subscription management, while the observable pattern provides intuitive state updates. MobX requires significantly less code than Redux (no actions, reducers, or dispatch boilerplate). For teams working with complex domain models, MobX's class-based stores scale well and have proven themselves in production for titans like Microsoft (Outlook) and Valve (Steam). The `makeAutoObservable` decorator eliminates configuration overhead, and the `observer` wrapper provides surgical re-renders without manual optimisation. Alternative libraries like Zustand offer simpler APIs for basic use cases—choose based on your team's preferences and complexity requirements. 

**React Hook Form** delivers best-in-class form performance through minimal re-renders and intuitive API design. Seamless Zod integration provides type-safe validation with excellent error messages. Compared to Formik's more frequent re-renders or manual state management's boilerplate, React Hook Form provides the optimal developer experience for complex forms.

**Zod** enables runtime type validation with TypeScript integration, ensuring data conforms to expected shapes at runtime. Type inference from schemas eliminates duplicate type definitions, while clear error messages simplify debugging. Compared to Yup's weaker TypeScript support or manual validation's error-prone nature, Zod provides the most robust validation solution for TypeScript projects.

**Tailwind CSS** enables rapid development without context-switching between files. Utility classes provide consistent design systems while excellent tree-shaking ensures production bundles contain only used styles. Compared to CSS Modules' additional file management or Styled Components' runtime costs, Tailwind offers the best balance of developer experience and performance.

**Shadcn** takes a fundamentally different approach to component libraries by copying source code directly into your project rather than installing external dependencies. This means you own the code completely—no lock-in, no version conflicts, and updates can't break your application since components are versioned with your codebase. You can modify components directly without fighting framework constraints or waiting for upstream changes. Shadcn establishes base patterns using TypeScript and Tailwind, reducing technical debt by ensuring new developers only need to understand your existing stack. Compared to Material-UI/Chakra UI's external dependencies and customisation challenges or Ant Design's opinionated styling, Shadcn provides complete control while eliminating the need to build everything from scratch.

**Docker** ensures consistent development and production environments, simplifying onboarding and eliminating "works on my machine" issues. Reproducible deployments reduce configuration drift and deployment failures. Docker is standard infrastructure at DBCA, making it the natural choice for containerisation.

## Project Structure

A monorepo approach keeps frontend and backend together while maintaining clear boundaries. At the repository level, applications remain independent with separate Docker images. Within the frontend, feature modules group related functionality while shared resources remain accessible.

```
project_root/
├── backend/              # Django application
├── frontend/             # React application
├── kustomize/            # Kubernetes deployment configs
├── .github/              # CI/CD workflows
└── docker-compose.yml    # Local development orchestration
```

```
frontend/src/
├── app/                  # Application-wide configuration
│   ├── router/           # React Router setup
│   └── stores/           # MobX root store
├── features/             # Feature modules (domain-driven)
│   ├── auth/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── types/
│   │   └── schemas/
│   └── [feature]/        # Additional features follow same pattern
├── pages/                # Route page components
├── shared/               # Shared resources between features
│   ├── components/
│   │   ├── users/        # User-based components that are cross-cutting (used by other domains)
│   │   └── ui/           # Shadcn components
│   ├── lib/
│   ├── types/
│   └── styles/
└── main.tsx
```

Feature modules group related code by domain. Each feature contains its own components, hooks, services, types, and schemas, creating clear boundaries that prevent unintended coupling. The app directory handles routing and global state. Pages compose feature components. Shared resources include Shadcn components in `shared/components/ui/` and any cross-cutting features domains may use.

**Scaling Considerations**: For much larger applications, micro-frontend architectures may be considered to enable independent team ownership of features. However, this introduces significant complexity—coordinated deployments, shared dependencies, and potential for developers stepping over each other when modifying shared infrastructure. The monorepo structure described here scales effectively for most applications without these trade-offs.

## Getting Started

Prerequisites include Bun (or Node.js), Docker, Git, and a code editor. VS Code provides the best TypeScript experience with appropriate extensions. Installation steps vary by operating system—macOS users typically use Homebrew, Windows users use installers or WSL2, and Linux users use package managers.

```bash
# Create new React application with Vite
bun create vite my-app --template react-ts
cd my-app

# Install dependencies
bun install

# Add recommended dependencies
bun add @tanstack/react-query mobx mobx-react-lite react-hook-form zod @hookform/resolvers
bun add -d @types/node

# Start development server
bun run dev
```

Recommended VS Code extensions improve development experience: ESLint for linting, Prettier for formatting, Tailwind CSS IntelliSense for utility class completion, and Error Lens for inline error display. TypeScript and React extensions come built-in with modern VS Code installations.

## State Management

Modern React applications manage two distinct types of state: server state (data from APIs) and client state (UI and application state). TanStack Query handles server state with intelligent caching and automatic refetching. MobX manages client state with minimal configuration and automatic reactivity.

Server state represents backend data—projects, users, settings. Client state represents frontend concerns—UI toggles, form inputs, navigation state.

```typescript
// TanStack Query for server state
import { useQuery } from '@tanstack/react-query';

const { data, isLoading, error } = useQuery({
  queryKey: ['projects'],
  queryFn: fetchProjects,
  staleTime: 5 * 60 * 1000,
});
```

```typescript
// MobX for client state
import { makeAutoObservable } from 'mobx';

class UIStore {
  sidebarOpen = true;
  
  constructor() {
    makeAutoObservable(this);
  }
  
  toggleSidebar() {
    this.sidebarOpen = !this.sidebarOpen;
  }
}
```

TanStack Query handles caching automatically. Data remains fresh for the configured `staleTime`, then refetches in the background. Mutations invalidate related queries, triggering automatic refetches.

MobX observables track dependencies automatically. The `observer` wrapper makes components reactive to observable changes, while `makeAutoObservable` eliminates boilerplate.

## API Integration

Axios interceptors handle authentication tokens, request/response transformation, and error processing. Service modules organise API calls by feature, providing clean interfaces for TanStack Query hooks.

```typescript
// Axios instance with interceptors
import axios from 'axios';

const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_URL,
});

apiClient.interceptors.request.use((config) => {
  const token = localStorage.getItem('authToken');
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});
```

```typescript
// API service pattern
export const projectsApi = {
  getAll: async () => {
    const { data } = await apiClient.get('/projects/');
    return data;
  },
  create: async (input) => {
    const { data } = await apiClient.post('/projects/', input);
    return data;
  },
};
```

Query keys follow a hierarchical pattern: `['projects']` for all projects, `['projects', id]` for a specific project, `['projects', id, 'tasks']` for project tasks. This enables precise cache invalidation.

Error handling occurs at multiple levels. Axios interceptors catch authentication errors globally. TanStack Query provides error states per query. Service functions can transform API errors into user-friendly messages.

## Form Handling

React Hook Form minimises re-renders while providing intuitive form state management. Zod schemas define validation rules and generate TypeScript types automatically. The zodResolver bridges React Hook Form and Zod, enabling type-safe validation.

```typescript
// Zod schema with type inference
import { z } from 'zod';

export const projectSchema = z.object({
  name: z.string().min(1, 'Name is required'),
  description: z.string().min(10, 'Description must be at least 10 characters'),
  status: z.enum(['active', 'completed', 'archived']),
});

export type ProjectFormData = z.infer<typeof projectSchema>;
```

```typescript
// React Hook Form with Zod validation
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';

const { register, handleSubmit, formState: { errors } } = useForm({
  resolver: zodResolver(projectSchema),
});

const onSubmit = async (data) => {
  await projectsApi.create(data);
};
```

React Hook Form tracks field-level changes, only re-rendering when specific fields update. Zod validation runs on submit by default, with optional onChange or onBlur validation for immediate feedback.

## Routing and Navigation

React Router manages client-side routing with nested routes, route protection, and layout composition. Outlet components enable nested layouts that share common UI elements.

```typescript
// Router configuration
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

const router = createBrowserRouter([
  {
    path: '/',
    element: <RootLayout />,
    children: [
      { index: true, element: <HomePage /> },
      { path: 'login', element: <LoginPage /> },
      { path: 'projects', element: <ProtectedRoute><ProjectsPage /></ProtectedRoute> },
    ],
  },
]);

export const App = () => <RouterProvider router={router} />;
```

Route organisation follows the feature module pattern. Route definitions live in `app/router/`, page components in `pages/`, and feature-specific routing logic within feature directories.

## Component Patterns

Shadcn components copy directly into your project rather than installing as external dependencies. You own the code completely—no lock-in, no version conflicts, and updates can't break your application. Components establish base patterns using TypeScript and Tailwind, ensuring new developers only need to understand your existing stack and do not need to reinvent the wheel.

```bash
# Install Shadcn CLI and add components
bunx shadcn@latest init
bunx shadcn@latest add button dialog form
```

After installation and minor adjustments to components.json, components live in `src/shared/components/ui/` as regular TypeScript files. Modify them directly to match your design system.

```typescript
// Using Shadcn components
import { Button } from '@/shared/components/ui/button';
import { Dialog, DialogContent, DialogTitle } from '@/shared/components/ui/dialog';

export const ProjectActions = () => {
  return (
    <>
      <Button onClick={handleCreate}>Create Project</Button>
      <Button variant="outline">Cancel</Button>
    </>
  );
};
```

Tailwind utility classes enable rapid styling without context-switching. The `cn` utility (from `clsx` and `tailwind-merge`) combines class names intelligently.

```typescript
// Component composition with Tailwind
export const ProjectCard = ({ project, featured }) => {
  return (
    <div className={cn(
      'rounded-lg border p-6',
      featured && 'border-primary ring-2'
    )}>
      <h3>{project.name}</h3>
      <p className="text-muted-foreground">{project.description}</p>
    </div>
  );
};
```

Component organisation separates shared components from feature-specific components. Shadcn components live in `shared/components/ui/`, reusable business components in `shared/components/`, and feature-specific components within their feature directories.

## TypeScript Configuration

TypeScript configuration balances strictness with developer experience. Strict mode catches errors at compile time, while path aliases simplify imports. Type inference reduces boilerplate, but explicit types improve clarity for complex structures.

```json
// tsconfig.json - recommended settings
{
  "compilerOptions": {
    "target": "ES2020",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "bundler",
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

Path aliases eliminate relative import chains like `../../../shared/components`. Configure in both `tsconfig.json` and `vite.config.ts`.

```typescript
// Type inference from Zod schemas
const projectSchema = z.object({
  name: z.string(),
  status: z.enum(['active', 'completed']),
});

type Project = z.infer<typeof projectSchema>;
```

Strict mode enables all strict type-checking options, catching potential runtime errors at compile time. Use explicit types for function parameters, return values, and complex data structures. Rely on inference for simple cases.

## Testing Approach

Vitest provides fast test execution with excellent TypeScript support and React Testing Library integration. Focus testing on user behaviour—test what users see and do, not internal component state.

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    environment: 'jsdom',
    globals: true,
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html', 'lcov'],
      thresholds: {
        lines: 40,
        functions: 40,
        branches: 40,
        statements: 40,
      },
    },
  },
});
```

```typescript
// Component testing example
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

it('submits form with valid data', async () => {
  render(<ProjectForm onSubmit={onSubmit} />);
  
  await userEvent.type(screen.getByLabelText(/name/i), 'New Project');
  await userEvent.click(screen.getByRole('button', { name: /create/i }));
  
  expect(onSubmit).toHaveBeenCalled();
});
```

Test user-facing behaviour: form submissions, button clicks, navigation, data display. Avoid testing implementation details. Tests should remain valid when refactoring components.

**Coverage Targets**: Aim for 40% coverage as a baseline when sharding tests (ensures the CI/CD does not fail), focusing on critical paths—authentication, data submission, error handling. Complex business logic deserves thorough testing, while simple presentational components may not require dedicated tests. Coverage thresholds provide guardrails without becoming obstacles to shipping features.

## Docker Configuration

Multi-stage Docker builds separate build-time dependencies from runtime requirements, producing smaller images with improved security. The build stage compiles the application with Bun, while the production stage serves compiled assets using a lightweight Bun server.

```dockerfile
# Build stage
FROM oven/bun:1.3.9-alpine AS build_image
WORKDIR /app

COPY package.json bun.lock* ./
RUN bun install --frozen-lockfile

COPY . .

# Build arguments for environment variables
ARG VITE_PRODUCTION_BACKEND_API_URL=http://127.0.0.1:8000/api/v1/
ARG VITE_VERSION=0.0.1

ENV VITE_PRODUCTION_BACKEND_API_URL=$VITE_PRODUCTION_BACKEND_API_URL
ENV VITE_VERSION=$VITE_VERSION

RUN bun run build

# Production stage
FROM oven/bun:1.3.9-slim AS production_image
WORKDIR /client

COPY --from=build_image /app/dist/ /client/dist/
COPY --from=build_image /app/server.js /client/

USER bun
EXPOSE 3000
CMD ["bun", "run", "server.js"]
```

```javascript
// server.js - Bun server for SPA routing
import { serve } from "bun";

serve({
  port: 3000,
  async fetch(req) {
    const url = new URL(req.url);
    let filePath = url.pathname === "/" || !url.pathname.includes(".") 
      ? "/index.html" 
      : url.pathname;

    const file = Bun.file(`./dist${filePath}`);
    return (await file.exists()) 
      ? new Response(file) 
      : new Response(Bun.file("./dist/index.html"));
  },
});
```

Environment variables configure the application for different environments. Vite requires the `VITE_` prefix for variables accessible in client code. Build arguments in the Dockerfile allow CI/CD pipelines to inject environment-specific values. Never commit `.env.local` files containing sensitive data.

The Bun server handles SPA routing by serving `index.html` for all non-file requests, enabling client-side routing to work correctly.

## Performance Optimisation

Performance optimisation balances user experience with development velocity. Start with measurement using browser DevTools and React DevTools Profiler to identify actual bottlenecks before optimising. You may also choose to use Lighthouse to assess SEO.

**Code Splitting and Lazy Loading**: Route-based code splitting reduces initial bundle size by loading pages on demand. React's `lazy` function combined with dynamic imports creates separate chunks for each route.

```typescript
// routes.config.ts - Lazy load route components
import { lazy } from 'react';

const Dashboard = lazy(() => import('@/pages/dash/Dashboard'));
const ProjectListPage = lazy(() => import('@/pages/projects/ProjectListPage'));
const UserDetailPage = lazy(() => import('@/pages/users/UserDetailPage'));

export const ROUTES: RouteConfig[] = [
  { path: '/', component: Dashboard, requiresAuth: true },
  { path: '/projects', component: ProjectListPage, requiresAuth: true },
  { path: '/users/:id', component: UserDetailPage, requiresAuth: true },
];
```

```typescript
// Router setup with Suspense boundary
import { Suspense } from 'react';
import { RouteLoader } from '@/shared/components/RouteLoader';

const element = (
  <Suspense fallback={<RouteLoader />}>
    <Component />
  </Suspense>
);
```

**Manual Chunk Configuration**: Vite's `manualChunks` separates vendor libraries into dedicated bundles, improving caching and parallel loading.

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          'vendor-react': ['react', 'react-dom', 'react-router'],
          'vendor-query': ['@tanstack/react-query'],
          'vendor-mobx': ['mobx', 'mobx-react-lite'],
          'vendor-ui': ['lucide-react', 'sonner', 'framer-motion'],
        },
      },
    },
  },
});
```

**Component-Level Optimisation**: Use `React.memo` for expensive components that receive the same props frequently. Measure impact before applying—premature optimisation adds complexity without guaranteed benefit.

```typescript
// Memoize expensive list items
const ProjectCard = React.memo(({ project }) => {
  return <div>{/* Complex rendering logic */}</div>;
});
```

**Bundle Analysis**: Regularly analyse bundle size to identify optimisation opportunities. Large dependencies or duplicate code indicate areas for improvement.

```bash
# Analyse production bundle
bun run build
npx vite-bundle-visualizer
```

Performance optimisation requires continuous measurement. Monitor Core Web Vitals (LCP, FID, CLS) in production using tools like Sentry or browser DevTools to validate improvements.

## Best Practices

Component organisation follows the feature module pattern, grouping related functionality by domain. Keep components focused on single responsibilities. Prefer composition over inheritance.

**Collaboration with Infrastructure Team**: Frontend development at DBCA involves coordination with the Office of Information Management (OIM) for infrastructure concerns. OIM primarily manages edge caching (Fastly/Varnish), CDN configuration, Kubernetes clusters, Azure resources and overall infrastructure provisioning. Developers focus on application code while infrastructure changes follow the RFC (Request for Change) process via Freshservice. For production deployments, submit an RFC detailing the change, implementation plan, rollback strategy, and testing approach. This separation enables developers to concentrate on features while infrastructure specialists handle platform-level concerns.

**User Acceptance Testing**: Deploy features to UAT (User Acceptance Testing) environments to gather stakeholder feedback before production release. UAT enables real users to validate functionality, identify usability issues, and confirm requirements are met. For experimental features, UAT provides a controlled environment for A/B testing different approaches with actual users, informing product decisions before wider rollout.

Security practices include input sanitisation, HTTPS enforcement, and secure token storage. Validate and sanitise on both client and server. Store authentication tokens in httpOnly cookies when possible. Use environment variables for sensitive configuration.

Error handling occurs at multiple levels. Error boundaries catch rendering errors. API error handling provides user-friendly messages. Form validation prevents invalid submissions.

File naming conventions use PascalCase for components (`ProjectCard.tsx`), camelCase for utilities (`formatDate.ts`), and kebab-case for directories (`project-details/`).

Accessibility fundamentals ensure applications work for all users. Use semantic HTML elements (`<button>`, `<nav>`, `<main>`) rather than generic `<div>` elements with click handlers. Semantic elements provide built-in keyboard navigation and screen reader support.

Ensure all interactive elements are keyboard accessible. Test by navigating with Tab/Tab+Shift keys alone. Focus indicators should be visible.

[WCAG 2.2 AA](https://www.w3.org/WAI/WCAG22/quickref/?currentsidebar=%23col_customize&levels=aa) requires 4.5:1 contrast ratio for normal text, 3:1 for large text. Use browser DevTools or [contrast checkers](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html) to verify ratios. Don't rely solely on colour to convey information—use text labels, icons, or patterns as well.

**Automated Accessibility Testing**: Integrate automated accessibility testing into your test suite using jest-axe. While automated tests catch common issues (missing alt text, insufficient contrast, invalid ARIA), they complement rather than replace manual testing with assistive technologies.

```typescript
// test/setup.ts - Configure jest-axe
import { toHaveNoViolations } from 'jest-axe';
expect.extend(toHaveNoViolations);
```

```typescript
// test/axe-utils.ts - WCAG 2.2 AA configuration
import { configureAxe } from 'jest-axe';

export const axe = configureAxe({
  runOnly: {
    type: 'tag',
    values: ['wcag2a', 'wcag2aa', 'wcag21a', 'wcag21aa', 'wcag22aa'],
  },
  rules: {
    'color-contrast': { enabled: true },
    'valid-lang': { enabled: true },
    'html-has-lang': { enabled: true },
    'landmark-one-main': { enabled: true },
    'page-has-heading-one': { enabled: true },
    region: { enabled: true },
    bypass: { enabled: true },
    'target-size': { enabled: true }, // WCAG 2.2
  },
});
```

```typescript
// Component.a11y.test.tsx - Accessibility test pattern
import { renderPage, testAccessibility } from '@/test/page-test-utils';

describe('Dashboard - Accessibility', () => {
  it('should have no accessibility violations', async () => {
    const { container } = renderPage(<Dashboard />);
    const results = await testAccessibility(container);
    expect(results).toHaveNoViolations();
  });
});
```

**Shadcn Accessibility Foundation**: Shadcn components build on Radix UI primitives, which provide robust accessibility features out of the box—proper ARIA attributes, keyboard navigation, focus management, and screen reader support. This foundation reduces the accessibility work required for common UI patterns. However, you remain responsible for accessible implementation: meaningful labels, sufficient contrast, logical heading hierarchy, and testing with real assistive technologies.

Create dedicated `.a11y.test.tsx` or `.accessibility.test.tsx` files for each page or complex component. Run accessibility tests alongside functional tests in CI/CD pipelines to catch regressions early. Automated tests provide a safety net, but manual testing with screen readers (NVDA, JAWS, VoiceOver) remains essential for validating real user experience.

## Error Tracking and Monitoring

Sentry provides error tracking, performance monitoring, and session replay for production debugging. Initialize Sentry before React renders to capture all errors, including those during application bootstrap.

```typescript
// app/sentry.ts
import * as Sentry from "@sentry/react";

export function initSentry() {
  const sentryDsn = import.meta.env.VITE_SENTRY_DSN;
  
  if (!sentryDsn) {
    console.warn("Sentry DSN not configured - error tracking disabled");
    return;
  }

  Sentry.init({
    dsn: sentryDsn,
    environment: import.meta.env.VITE_SENTRY_ENVIRONMENT || "development",
    integrations: [
      Sentry.reactRouterV7BrowserTracingIntegration({
        useEffect,
        useLocation,
        useNavigationType,
        createRoutesFromChildren,
        matchRoutes,
      }),
      Sentry.replayIntegration({
        maskAllText: true,
        blockAllMedia: true,
      }),
    ],
    tracesSampleRate: import.meta.env.PROD ? 0.1 : 1.0,
    replaysSessionSampleRate: 0.1,
    replaysOnErrorSampleRate: 1.0,
  });
}
```

```typescript
// main.tsx - Initialize before React
import { initSentry } from "./app/sentry";
initSentry();

// Then render React app
```

Configure Sentry DSN through environment variables. The integration automatically captures unhandled errors, React component errors via ErrorBoundary, and performance metrics. Session replay records user interactions leading to errors, invaluable for debugging production issues. Sample rates balance monitoring coverage with performance impact—10% of normal sessions, 100% of error sessions.

Infrastructure monitoring (logs, metrics, container health) is handled by the operations team through Rancher and Azure. Sentry focuses on application-level errors and user experience issues that developers need to address.

## External Resources

### Core Technologies
- [React Documentation](https://react.dev/) - Official React documentation with guides and API reference
- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html) - Comprehensive TypeScript language guide
- [Vite Guide](https://vitejs.dev/guide/) - Build tool documentation and configuration
- [Bun Documentation](https://bun.sh/docs) - JavaScript runtime and package manager

### State Management
- [TanStack Query](https://tanstack.com/query/latest) - Server state management with caching and synchronisation
- [MobX](https://mobx.js.org/) - Client state management with automatic reactivity

### Forms and Validation
- [React Hook Form](https://react-hook-form.com/) - Performant form handling with minimal re-renders
- [Zod](https://zod.dev/) - TypeScript-first schema validation with type inference

### Styling and Components
- [Tailwind CSS](https://tailwindcss.com/) - Utility-first CSS framework
- [Shadcn/ui](https://ui.shadcn.com/) - Copy-paste component library with full code ownership

### Testing
- [Vitest](https://vitest.dev/) - Fast unit test framework with Vite integration
- [React Testing Library](https://testing-library.com/react) - User-centric component testing
- [jest-axe](https://github.com/nickcolley/jest-axe) - Accessibility testing utilities

### Monitoring and Observability
- [Sentry](https://docs.sentry.io/platforms/javascript/guides/react/) - Error tracking and performance monitoring
- [Sentry Session Replay](https://docs.sentry.io/platforms/javascript/session-replay/) - Debug production issues with user session recordings

### Deployment
- [Docker Documentation](https://docs.docker.com/) - Containerisation and deployment
- [Docker Best Practices](https://docs.docker.com/build/building/best-practices/) - Multi-stage builds and optimisation

### Worked Example

The [Science Projects Management System](https://github.com/dbca-wa/science-projects) demonstrates these patterns in a production application. The project uses React 19, TypeScript, TanStack Query, MobX, and Shadcn/ui with a Django REST Framework backend, following the monorepo structure and architectural patterns described in this guide.
