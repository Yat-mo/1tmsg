# Deployment record

## Service

- URL: https://1tmsg.w4y2jbxdpx.workers.dev/
- GitHub fork: https://github.com/Yat-mo/1tmsg
- Cloudflare Worker: `1tmsg`
- Deployment method: Cloudflare Workers Builds connected to GitHub
- Production branch: `main`
- Root directory: `/`
- Build command: `npm run build`
- Deploy command: `npm run deploy`
- Wrangler configuration: `wrangler.jsonc`
- Build credential: Cloudflare-managed `1tmsg build token`; no credential is stored in this repository.

This deployment uses the default text-only configuration. No R2 bucket, custom domain, or paid-plan upgrade was configured. The site uses the upstream Chinese locale (`zh`); users can switch to English in the interface.

## Source and dependency repair

- Upstream: https://github.com/CJSen/1tmsg
- Upstream revision: `01335d473f3ea1266c4061fa8b0725d71b678467`
- Patched dependency-lock revision: `f94bd3efd6f6c4edc1504a3cc75b889adec92f52`
- Only `package-lock.json` was changed for the dependency repair. Application behavior and configuration were not changed.

## Verified on 2026-10-12

- `npm audit`: zero reported vulnerabilities after the dependency repair.
- `npm run typecheck`: passed.
- `npm test`: 50 passed, zero failed.
- `npm run deploy -- --dry-run`: passed.
- Cloudflare build `85b82372-b7b2-410f-aea3-25707858553f`: successful.
- Initial deployed version: `a4932d21-d5db-46ed-8545-96fc231b01ee`.
- Live homepage: HTTP 200 with CSP, `no-referrer`, `nosniff`, and frame denial headers.
- Live `assets/create.js`: byte-identical to the tested local build.
- Browser-created, non-secret test message: HTTP 201.
- Observed create request contained ciphertext, not the test plaintext or a fragment key.
- First browser visit decrypted the test content and removed the URL fragment.
- Message metadata became `destroyed`; a second consume request returned HTTP 410 (`gone`) without ciphertext.
- Temporary browser verification state was removed after testing.

The tests verify this deployment's working create/read/burn flow; they are not a comprehensive security audit.

## Updates

Push changes to `main` in the fork to trigger Cloudflare's production build. To incorporate upstream changes, review and merge them into the fork, retaining the dependency-lock repair where still needed. Do not rely on upstream repository changes alone to update this fork.

Cloudflare deployment history and logs:
https://dash.cloudflare.com/ca3e135dcda29789323b236401bb919a/workers/services/view/1tmsg/production/deployments

## Remaining configuration

`vars.ABUSE_CONTACT` is empty, so the footer currently has no abuse-report contact. Before promoting this as a public service, set a real monitored email address or HTTPS contact URL in the deployment configuration and redeploy. No placeholder contact has been invented.

Image support is not enabled. Enabling it requires separate R2 activation, a private bucket, lifecycle configuration, and deployment with `wrangler.images.jsonc`; do not make the bucket public.
