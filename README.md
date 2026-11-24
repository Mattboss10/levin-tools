# levin-tools

A Claude Code plugin marketplace with one plugin, **levin-certified-checks**: fast, certified answers for
CBMC (C, C++) and Kani (Rust) checks that are too slow for one machine. The plugin sends
only the numbered SAT formula your checker produces to Levin, which splits it across many
machines, checks every piece's proof, and returns a certified answer. See
[plugins/levin-certified-checks/README.md](plugins/levin-certified-checks/README.md) for what it adds, what it sends and
where, and its privacy policy.

```
claude plugin marketplace add Mattboss10/levin-tools
claude plugin install levin-certified-checks@levin-tools
```

MIT licensed.
