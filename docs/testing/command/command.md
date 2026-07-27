# Command Testing

Tests for CLI commands.

---

## Base class

`{command test base class}` — provides:
- a helper to execute the command and capture its tester/output
- assertions for success/failure exit codes
- a hook to override input parameters (arguments/options)

---

## Examples

### Minimal — success only

```
class ImportSomethingCommandTest extends {BaseCommandTestCase}
{
    protected function getCommandName(): string
    {
        return 'app:import:something';
    }
}
```

### With input parameters and custom assertions

```
class GenerateReportCommandTest extends {CommandTestCase}
{
    protected function getCommandName(): string
    {
        return 'app:report:generate';
    }

    protected function getInputParameters(): array
    {
        return ['--date' => '2024-01-01'];
    }

    public function testSuccessCase(): void
    {
        $tester = $this->executeCommand();
        // assert success + expected output
    }
}
```

---

## Location

```
{tests root}/Command/
├── {CommandTestCase}                ← base
├── {BaseCommandTestCase}            ← shortcut for simple cases
└── {Module}/
```
