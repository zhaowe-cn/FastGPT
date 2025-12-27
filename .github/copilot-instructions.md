# GitHub Copilot Instructions for FastGPT

## Project Overview

FastGPT is an AI Agent building platform that provides out-of-the-box capabilities for data processing, model invocation, and workflow orchestration through Flow visualization. The platform enables complex application scenarios through visual workflow composition.

## Technology Stack

- **Frontend**: Next.js 14.x, React 18.x, TypeScript 5.x
- **UI Framework**: Chakra UI 2.x
- **State Management**: Zustand, React Query (TanStack Query)
- **Database**: MongoDB, PostgreSQL (with PG Vector plugin), Milvus
- **Package Manager**: pnpm 9.15.9
- **Node Version**: >= 20
- **Internationalization**: next-i18next, i18next

## Architecture

This is a **monorepo** project managed with pnpm workspace:

### Packages (shared libraries):
- `@fastgpt/global` - Global types, constants, and utilities
- `@fastgpt/service` - Backend services and business logic
- `@fastgpt/web` - Shared web components and UI utilities

### Projects (applications):
- `app` - Main FastGPT application
- `marketplace` - Plugin marketplace
- `mcp_server` - Model Context Protocol server
- `sandbox` - Code execution sandbox

## Code Style and Conventions

### TypeScript

1. **Type Imports**: Always use `import type` for type-only imports
   ```typescript
   import type { UserType } from '@fastgpt/global/support/user/type';
   ```

2. **Consistent Type Imports**: Enforced by ESLint rule `@typescript-eslint/consistent-type-imports`

3. **Path Aliases**:
   - `@fastgpt/*` maps to `packages/*`
   - `@test` maps to `test/*`
   - Use `@/` for relative imports within projects

### Formatting

- **Print Width**: 100 characters
- **Indentation**: 2 spaces (no tabs)
- **Quotes**: Single quotes for strings, double quotes for JSX
- **Semicolons**: Always required
- **Trailing Commas**: None
- **Line Endings**: LF (Unix-style)

### Code Organization

1. **API Files**: Follow RESTful naming patterns
   - Use `GET`, `POST`, `PUT`, `DELETE` functions from `@/web/common/api/request`
   - Export functions with descriptive names like `postCreateAppFolder`, `getAppFolderPath`

2. **Constants**: Keep constants in separate `constants.ts` files
   - Organize by feature/module
   - Use TypeScript enums for related constants

3. **Types**: Define types in `.d.ts` files or with `type` keyword
   - Prefer `type` over `interface` for object shapes
   - Use `interface` for extensible contracts

## Internationalization (i18n)

### Translation Keys

- Use lowercase with underscores: `common.close`, `app.template.simple_robot`
- Format: `namespace:key` (e.g., `t('common:close')`)
- Namespaces: `common`, `publish`, `user`, `app`, etc.

### In Components

```typescript
import { useTranslation } from 'next-i18next';

const Component = () => {
  const { t } = useTranslation();
  return <Button>{t('common:close')}</Button>;
};
```

### In Server-Side Props

```typescript
export async function getServerSideProps(context: any) {
  return {
    props: {
      ...(await serverSideTranslations(context.locale, ['publish', 'user']))
    }
  };
}
```

### In Static Files

```typescript
import { i18nT } from '@fastgpt/web/i18n/utils';

const staticContent = {
  name: i18nT('app:template.simple_robot')
};
```

## Development Workflow

### Installation

```bash
# Give script execution permissions (Linux/Mac)
chmod -R +x ./scripts/

# Install dependencies
pnpm i

# For Node >= 20, use:
NODE_OPTIONS=--no-node-snapshot pnpm i
```

### Running Projects

```bash
# Using make (recommended)
make dev name=app

# Or directly with pnpm
cd projects/app
pnpm dev
```

### Building

```bash
# Build Docker image
make build name=app image=registry.cn-hangzhou.aliyuncs.com/fastgpt/fastgpt:v4.8.1

# With proxy
make build name=app image=registry.cn-hangzhou.aliyuncs.com/fastgpt/fastgpt:v4.8.1 proxy=taobao
```

### Testing

```bash
# Run all tests
pnpm test

# Run workflow tests
pnpm test:workflow
```

### Code Quality

```bash
# Format code
pnpm format-code

# Lint TypeScript files
pnpm lint

# Format documentation
pnpm format-doc
```

## Audit Logging

When adding features that require audit logging:

1. Add event type to `AuditEventEnum`
2. Add corresponding i18n translation
3. Use `addOperationLog` function at the appropriate location in code

## Common Patterns

### API Calls

```typescript
import { GET, POST, DELETE } from '@/web/common/api/request';

export const getAppDetail = (id: string) => 
  GET<AppDetailType>('/core/app/detail', { id });

export const postCreateApp = (data: CreateAppBody) => 
  POST<CreateAppResponse>('/core/app/create', data);
```

### React Hooks

- Use `ahooks` for common hooks utilities
- Use `react-hook-form` version 7.43.1 for form management
- Use `@tanstack/react-query` for server state management

### Styling with Chakra UI

- Use Chakra UI components and theming system
- Theme customization in `packages/web/styles/theme.ts`
- Generate theme typings with: `pnpm gen:theme-typings`

## File Structure Best Practices

1. **Group by feature**, not by type
2. Keep related components, hooks, and utilities together
3. Use index files for clean imports
4. Separate business logic from UI components

## Git Workflow

- Pre-commit hooks run formatting and linting via husky
- Lint-staged configuration:
  - TypeScript/TSX files: formatted and linted
  - Documentation: formatted, timestamps updated, TOC generated

## Environment Requirements

- **Node.js**: >= 20
- **pnpm**: 9.15.9
- **Make**: For convenient dev/build commands

## Additional Notes

- The project uses ChakraUI v2 with Next.js 14
- Monaco Editor is integrated for code editing features
- Lexical is used for rich text editing
- React Beautiful DnD for drag-and-drop functionality
- The codebase supports multiple languages with i18next

## When Writing Code

1. **Always** use TypeScript with strict type checking
2. **Import types** using `import type` syntax
3. **Follow** the existing project structure and naming conventions
4. **Use** shared packages from `@fastgpt/*` when available
5. **Add** i18n support for all user-facing strings
6. **Write** tests for new functionality
7. **Keep** components small and focused
8. **Extract** reusable logic into custom hooks
9. **Document** complex logic with comments
10. **Ensure** code passes linting and formatting checks before committing
