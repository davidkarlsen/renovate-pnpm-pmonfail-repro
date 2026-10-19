# Renovate drops `packageManagerDependencies` from `pnpm-lock.yaml` on lock file maintenance

Minimal reproduction. `package.json` pins `"packageManager": "pnpm@12.14.0"`; `pnpm-lock.yaml` was generated with `pnpm install --lockfile-only --config.pmOnFail=download` and contains a `packageManagerDependencies` section in its first (`---`) document.

Renovate (`ghcr.io/renovatebot/renovate:44-full`, 44.168.0) regenerates the lockfile with

    pnpm install --lockfile-only --ignore-scripts --ignore-pnpmfile --no-frozen-lockfile

which writes a lockfile without `packageManagerDependencies`. Adding `--config.pmOnFail=download` keeps it. Setting `pmOnFail: download` in `pnpm-workspace.yaml` or `pnpm_config_pm_on_fail=download` in the environment does not help.

    docker run --rm -v "$PWD:/w" -w /w ghcr.io/renovatebot/renovate:44-full sh -c '
      rm pnpm-lock.yaml
      pnpm install --lockfile-only --ignore-scripts --ignore-pnpmfile --no-frozen-lockfile; grep -c packageManagerDependencies pnpm-lock.yaml   # 0
      rm pnpm-lock.yaml
      pnpm install --lockfile-only --ignore-scripts --ignore-pnpmfile --no-frozen-lockfile --config.pmOnFail=download; grep -c packageManagerDependencies pnpm-lock.yaml   # 1'
