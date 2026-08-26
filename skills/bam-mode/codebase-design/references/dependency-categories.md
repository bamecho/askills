# Dependency categories

Classify every dependency the design crosses. The category decides whether a seam is warranted and how the module is tested through it. Uses the vocabulary in [`design-vocabulary.md`](design-vocabulary.md).

## The four categories

### 1. In-process

Pure computation, in-memory state, no I/O. Always safe to concentrate: merge the logic into one module and test directly through its interface. No adapter, no port, no seam.

### 2. Local-substitutable

A dependency with a real local stand-in (PGLite for Postgres, an in-memory filesystem). Concentrate the logic when the stand-in exists, and run the stand-in in the test suite. The seam is *internal* — it does not belong on the module's external interface.

### 3. Remote but owned

Your own services across a network boundary (internal APIs, queues, microservices). Define a port at the seam. The deep module owns the logic; the transport is injected as an adapter. Tests use an in-memory adapter, production uses the HTTP/gRPC/queue adapter. State it as: *the logic sits in one deep module even though it is deployed across a network.*

### 4. True external

A third party you do not control (payment providers, SMS gateways). The module takes the dependency as an injected port; tests supply a mock adapter. Never let a provider's wire type onto the public interface — parse it into domain types behind the seam.

## Seam discipline

- **Two adapters or no seam.** Production plus test is the usual pair. One adapter is a hypothetical seam, which is just indirection.
- **Internal seams stay internal.** A deep module's private seams are its own business. Do not widen the interface to expose them.
- **Ports point inward.** The domain owner declares the port; infrastructure implements it. An adapter that has to inspect domain state or infer a transition means the port is too generic — name the semantic operation or pass an explicit domain intent instead.

## Testing across the seam

- **The interface is the test surface.** Write tests at the deepened module's interface, asserting observable outcomes rather than internal state.
- **Replace, do not layer.** Unit tests against the old shallow modules become waste once tests exist at the deep interface. Delete them; do not keep both.
- **A test that must change when the implementation changes is testing past the interface.** Tests describing behavior survive internal refactors. That survival is the signal the seam is in the right place.
