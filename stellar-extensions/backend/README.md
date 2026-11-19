# Puzzle submission throttle extension

This NestJS extension is intentionally kept separate from `backend/`. Import
`PuzzleSubmissionThrottleModule` in the host application, then attach
`PuzzleSubmissionThrottleGuard` and `PuzzleSubmissionThrottleInterceptor` to
the puzzle submission route:

```ts
@UseGuards(PuzzleSubmissionThrottleGuard)
@UseInterceptors(PuzzleSubmissionThrottleInterceptor)
```

The guard resolves difficulty from the persisted puzzle record; it does not
trust difficulty supplied by the caller. The interceptor records the
submission result so incorrect answers receive escalating backoff and correct
answers clear that backoff.

Administrators can read and update the live settings at
`/admin/settings/puzzle-submission-throttle`. Updates are in-memory for the
current application process. The route uses the host application's
`admin-jwt` strategy and allows `ADMIN` and `SUPERADMIN` roles.

Run the extension unit tests from the repository root with:

```sh
./node_modules/.bin/jest --config stellar-extensions/backend/jest.config.js --runInBand
```
