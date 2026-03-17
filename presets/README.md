# Community Presets

These are community-maintained NemoClaw policy presets.

Important:

- These files are example baselines, not audited production policies.
- Replace placeholders like `your-subdomain` and `your-bucket` before use.
- Keep permissions least-privilege for your environment.

Each preset file follows the NemoClaw preset shape:

- `preset.name` and `preset.description`
- `network_policies` entries with endpoint rules
- optional `binaries` restrictions for process-level scoping

Usage options:

1. Copy a preset into your local NemoClaw presets directory, then apply via `nemoclaw <sandbox> policy-add`.
2. Merge endpoint groups into your own policy file and apply with `openshell policy set`.

Recommended hardening checklist:

1. Scope hosts to your tenant where possible.
2. Restrict paths to known API prefixes.
3. Remove methods you do not need (`PATCH`, `PUT`, `DELETE`).
4. Restrict `binaries` to only required executables.
5. Test in staging before production rollout.
