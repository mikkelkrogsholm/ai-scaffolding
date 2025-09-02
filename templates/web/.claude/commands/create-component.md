---
description: Generate a new React/Vue/Angular component with tests
argument-hint: [component-name] [type: functional|class|page]
---

# Create Frontend Component

Generate a complete component with tests, styles, and documentation.

## Component Details

**Name:** $1
**Type:** $2 (functional, class, or page component)

## What Gets Created

Based on the project's framework (detected from package.json):

### React Component
```
src/components/$1/
├── $1.tsx           # Component implementation
├── $1.types.ts      # TypeScript interfaces
├── $1.styles.ts     # Styled components
├── $1.test.tsx      # Unit tests
├── $1.stories.tsx   # Storybook stories
└── index.ts         # Barrel export
```

### Vue Component
```
src/components/$1/
├── $1.vue           # Component implementation
├── $1.spec.ts       # Unit tests
├── $1.stories.ts    # Storybook stories
└── index.ts         # Export
```

### Angular Component
```
src/app/components/$1/
├── $1.component.ts      # Component class
├── $1.component.html    # Template
├── $1.component.scss    # Styles
├── $1.component.spec.ts # Tests
└── $1.module.ts         # Module definition
```

## Component Features

The generated component includes:
- TypeScript types/interfaces
- Props validation
- Default props
- Error boundaries (React)
- Lifecycle hooks
- Event handlers
- Accessibility attributes
- Responsive design
- Theme integration
- Unit tests with coverage
- Storybook stories
- JSDoc documentation

## Patterns Applied

Based on PATTERNS.md:
- Composition over inheritance
- Single responsibility
- Props interface segregation
- Dependency injection for services
- Observer pattern for events

## Testing Coverage

Generated tests include:
- Render tests
- Props validation
- Event handling
- Error states
- Accessibility checks
- Snapshot tests (optional)

## Usage

After generation:
1. Component is auto-registered
2. Import and use:
   ```tsx
   import { $1 } from '@/components/$1';
   
   <$1 prop="value" />
   ```

The component follows all project conventions from BEST-PRACTICES.md!