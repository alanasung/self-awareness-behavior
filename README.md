<p align="center">
  <h1 align="center">Behavioral Tests for Self-Knowledge Claims</h1>
  <p align="center"><strong>Run behavior-based probes of what models can report about their own limits and processes.</strong></p>
</p>

---

## Overview

This repository implements experimental profiles for **Behavioral Tests for Self-Knowledge Claims**. Config, caching, hooks, metrics, ablations, reporting, and CI support local pilots on small open-weight models.

Hypothesis (one line): Run behavior-based probes of what models can report about their own limits and processes.

## Status

Shared infrastructure is in place; domain stages must pass harness validation before any measured claim.

| Command | Purpose |
|---|---|
| `make install-dev` | editable install + pinned requirements |
| `make test` | full unit suite |
| `make ci` | lint + test + typecheck |
| `make pilot` | end-to-end pilot profile |
