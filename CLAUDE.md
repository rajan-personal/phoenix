Phoenix is an application with a Python backend in `src/` and a TypeScript frontend in `app/`. The frontend queries the backend using GraphQL (using the `strawberry-graphql` framework server-side and Relay client-side). The GraphQL schema in `app/schema.graphql` is compiled using the `strawberry-graphql` library. So anytime you make a change to a `strawberry` type, input type, resolver, or subscription, you must first recompile the GraphQL schema using `tox run -e build_graphql_schema`. You must then recompile Relay types by running `pnpm run build:relay` from the `app/` directory.

To run the backend, use the Python virtual environment located at `.venv`. Spin up the Python backend with `python -m phoenix.server.main serve --dev`.

Spin up the frontend with `pnpm run dev:ui` from the `app/` directory.

Do not add comments to code unless explicitly instructed to do so. Instead, use self-documenting variable and function names.
