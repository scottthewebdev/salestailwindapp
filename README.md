# SalesTailwindApp
A small demo storefront built for learning and demos. Demonstrates AI assistant, client-side caching, dynamic form generation, product, shopping cart, and checkout flows. Leverages server-side rendering, signals, routes, services, states, client-side caching, and dynamic forms as a service driven by schemas, where data is returned as models. Styled using Tailwind CSS and DaisyUI.

This project was generated using [Angular CLI](https://github.com/angular/angular-cli) version 20.2.2.

## Client side caching
Going from O(u(n+x)) to O(u(n)) means you have successfully eliminated \(x\) as an independent scaling bottleneck for your system.

## Development server

To start a local development server, run:

```bash
ng serve
```

Once the server is running, open your browser and navigate to `http://localhost:4200/`. The application will automatically reload whenever you modify any of the source files.

## Code scaffolding

Angular CLI includes powerful code scaffolding tools. To generate a new component, run:

```bash
ng generate component component-name
```

For a complete list of available schematics (such as `components`, `directives`, or `pipes`), run:

```bash
ng generate --help
```

## Building

To build the project run:

```bash
ng build
```

This will compile your project and store the build artifacts in the `dist/` directory. By default, the production build optimizes your application for performance and speed.

## Running unit tests

To execute unit tests with the [Karma](https://karma-runner.github.io) test runner, use the following command:

```bash
ng test
```

## Running end-to-end tests

For end-to-end (e2e) testing, run:

```bash
ng e2e
```

Angular CLI does not come with an end-to-end testing framework by default. You can choose one that suits your needs.

## Additional Resources

For more information on using the Angular CLI, including detailed command references, visit the [Angular CLI Overview and Command Reference](https://angular.dev/tools/cli) page.

## Services

- **Location:** src/services
- This project uses Angular services to encapsulate API calls and business logic (for example, `product-service.ts`, `image-service.ts`, and `cart-service.ts`).
- Services are singletons by default (provided in root) and are intended to be injected into components or stores to retrieve or update data.

- **AI service:** `ai-service.ts` — provides conversational AI helpers used by features that need chat or assistant-style interactions. Typical responsibilities:
	- managing conversation sessions and message formatting
	- calling backend LLM endpoints or server-side AI adapters
	- returning structured responses (text, suggested actions, or cards)
	- handling streaming or incremental responses when supported

	Example usage (inject into a component or store):

	```ts
	// constructor(private ai: AiService) {}
	const reply = await this.ai.sendMessage({ conversationId, text: 'Hello' });
	```

## Stores

- **Location:** src/stores
- Stores provide a lightweight, local state management layer and are used for client-side caching and sharing state between components without a full global store solution.
- Examples: `product.store.ts` caches product lists and details; `image.store.ts` caches images and related metadata to avoid repeated network requests.

- **AI conversation store:** `ai-conversation.store.ts` — maintains conversational state for AI features. Responsibilities and API (typical):
	- persist conversation history (messages, roles, timestamps)
	- expose observables/selectors for UI binding (e.g., `messages$`)
	- helpers: `startConversation()`, `appendMessage(msg)`, `getConversation(id)`, `clearConversation(id)`

	Example usage:

	```ts
	// constructor(private aiConvoStore: AiConversationStore) {}
	this.aiConvoStore.startConversation('checkout-help');
	this.aiConvoStore.appendMessage({ role: 'user', text: 'How do I apply a discount?' });
	this.aiConvoStore.messages$.subscribe(messages => { /* render chat */ });
	```

## Client-side caching (via stores)

- Stores in this repo act as in-memory caches. Typical pattern:

```ts
// write to store after fetching from a service
await this.productStore.loadProducts();

// read from store in components
const products = this.productStore.getAll();
```

- Use stores to reduce duplicate HTTP requests and to keep UI responsive while data is reused across routes or components.

## Passing state via the router

- Angular's Router supports passing arbitrary state when navigating. This is useful for sending small payloads (like a selected product) between routes without putting them in a global store.

Example: navigate with state

```ts
// sender.component.ts
this.router.navigate(['/product-details'], { state: { product } });
```

Example: read state in the destination

```ts
// product-details.component.ts
ngOnInit() {
	const nav = this.router.getCurrentNavigation();
	const product = nav?.extras?.state?.product ?? history.state.product;
	// fallback: fetch from store/service by id if not provided
}
```

- Note: router state is not persisted across full page reloads; for persistent or large state prefer stores or URL parameters.

## NgRx (optional larger-scale state management)

- For apps that require robust global state with time-travel debugging, strict immutability, effects, and clear separation of concerns, consider using NgRx.
- Quick start:

```bash
ng add @ngrx/store@latest
ng add @ngrx/effects@latest
```

- This repo uses lightweight stores for caching; if you migrate to NgRx, move shared state into `Store` slices, implement `Actions`, `Reducers`, and `Effects` for side effects (API calls), and use `@ngrx/entity` for collections.

