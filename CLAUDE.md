Phoenix is an application with a Python backend in `src/` and a TypeScript frontend in `app/`. The frontend queries the backend using GraphQL (using the `strawberry-graphql` framework server-side and Relay client-side). The GraphQL schema in `app/schema.graphql` is compiled using the `strawberry-graphql` library. So anytime you make a change to a `strawberry` type, input type, resolver, or subscription, you must first recompile the GraphQL schema using `tox run -e build_graphql_schema`. You must then recompile Relay types by running `pnpm run build:relay` from the `app/` directory.

To run the backend, use the Python virtual environment located at `.venv`. Spin up the Python backend with `python -m phoenix.server.main serve --dev`. Spin up the frontend with `pnpm run dev:ui` from the `app/` directory.

If you have spun up both the frontend and the backend, you can do end-to-end testing using the Playwright MCP server. By default, the app is served on `http://localhost:6006`.  See `app/src/Routes.tsx` to understand the structure of our routes.


Do not add comments to code unless explicitly instructed to do so. Instead, use self-documenting variable and function names.

When appropriate, you should run unit and integration tests for Python with `tox r -e unit_tests,integration_tests -- -n auto` and for TS with `pnpm run test`. You can also run individual unit tests with `pytest tests -- -k <search-expression>` (also using the `.venv`).
