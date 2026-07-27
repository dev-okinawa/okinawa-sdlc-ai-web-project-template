# Message Handler Testing

Tests for async message/event handlers, invoked directly via the container rather than through the queue transport.

---

## What to cover

- Handler behavior given a constructed message/event object
- Side effects (entity state changes, follow-up dispatches, notifications)
- Error/retry behavior on handler failure

---

## Location

```
{tests root}/Command/{Module}/{Name}HandlerTest.php
```

---

## Guidelines

- Invoke the handler directly (no need to run the actual queue/worker)
- Assert on the resulting state, not on internal implementation details
