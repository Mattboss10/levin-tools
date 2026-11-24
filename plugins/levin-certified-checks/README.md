# Levin Certified Checks for Claude Code

Levin gives fast, certified answers to formal-verification checks that are too slow for one
machine. CBMC (C, C++) and Kani (Rust) turn a check into a SAT formula; Levin splits that
formula across many machines that solve the pieces at once, checks every piece's answer (a
bug directly, and a "no bug" proof with a formally verified proof checker), and returns a
certified answer. Your checker then prints its own result, as it would with a local solver.

## What this plugin adds

- **The `levin` MCP server** at `https://mcp.commandersolver.com/mcp`, with two tools:
  `levin_setup` (how to use Levin with your checker) and `levin_job` (a job's record: time,
  number of pieces, fingerprints of the checked proofs).
- **A skill** that tells Claude when and how to use Levin: only after a 5-second local try,
  and only after you agree to send the formula. The skill contains, written out in full, a
  9-line shell script, `levin-sat`, that Claude saves in your project folder (and shows you)
  for your checker to call as its external SAT solver. Claude never runs anything the server
  sends back; the server's replies are answers to read.

The plugin itself contains no scripts or programs: only the MCP server's address, the
skill, this README and the licence.

## What it sends, and where

`levin-sat` sends **only the numbered SAT formula file** that your checker hands it, by
HTTPS POST to `https://mcp.commandersolver.com/v1/solve`, and prints the answer for the
checker. CBMC and Kani give an external solver only numbered clauses: no source code, file
names or variable names. Formulas with comment lines are refused, so names cannot leak by
accident. The script also appends the answer's first line (the job id and outcome) to
`levin.log` in the folder you run the check from. Nothing else is read, sent or stored on
your machine. Privacy policy: https://mcp.commandersolver.com/privacy. Usage figures:
https://mcp.commandersolver.com/stats.

Free at launch, within an hourly allowance per client. If Levin is busy or has no answer in
time, the skill has Claude run the check locally instead.

## Requirements

CBMC or Kani installed on your machine, and `curl`.

## Install

```
claude plugin marketplace add Mattboss10/levin-tools
claude plugin install levin-certified-checks@levin-tools
```

## License

MIT, see [LICENSE](LICENSE).
