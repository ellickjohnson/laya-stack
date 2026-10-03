# laya-stack

Git-backed Portainer stack for [Laya](https://github.com/NandhaKishorM/laya) — the open-source,
Jev-compatible System 1 decision engine — serving the TypeSafe Jev `/v1/systemone` wire protocol
over HTTP (`laya-serve`) with `GET /health`.

- Pinned to upstream commit `fa9a2a7070b1789912a49ae24603bbfb1a78b001` (v0.3.24, 2026-10-02).
- Upstream publishes no container images; the stack builds from the upstream Dockerfile
  (CPU torch 2.14.0). Model weights (~1.7 GB per checkpoint) download from HuggingFace on
  first boot into the `laya_model-cache` volume; `LAYA_REVISION=reviewed` pins loadable
  artifacts to the reviewer-verified commit SHAs.
- `LAYA_API_KEY` (required bearer for `/v1/systemone`; `/health` stays open per upstream
  design) lives in the Portainer stack env, never in this repo.
- Attached to the `proxy-net` external network for nginx-proxy-manager routing.
  Exposes no host ports directly.

## Operations

- Redeploy: Portainer → stacks → `laya` → pull & redeploy.
- Roll forward upstream: bump the `build.context` ref in `docker-compose.yml` and the pin
  note here, redeploy, then `GET /health` and a smoke `POST /v1/systemone`.
- Health: `curl localhost:8000/health` (from the host) or through NPM if routed.
- GPU (optional): the VM has the nvidia runtime; see upstream compose.cuda.yaml if we ever
  need the CUDA fast path — CPU is the supported default for this small model.
