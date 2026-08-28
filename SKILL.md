---
name: lazycat-app-feasibility
description: Assess whether open-source, Docker, Web, or feature projects can ship on Lazycat MicroServer as LPKs, covering platform fit, required adaptations, permissions, and POC planning.
---

# Lazycat MicroServer Application Feasibility Assessment and Delivery Guide

## Objective

Use this Skill to determine how feasible it is to implement a project or product requirement on Lazycat MicroServer, and turn the conclusion into an executable LPK, code adaptations, and an on-device POC.

Each assessment must answer at least the following questions:

- Can the target functionality be implemented with Lazycat’s public capabilities?
- Which parts can be reused directly, and which parts must be adapted?
- Which permissions must be requested, and are they only declarations or has runtime capability been verified?
- Should the application use a single instance or multiple instances, and how should user data be isolated?
- Should data be written to `/lzcapp/var`, Lazycat drive storage, media directories, or external storage?
- Does it depend on OIDC, entry identity headers, system APIs, device permissions, or special mounts?
- Is a POC required first, and what are the pass criteria?
- What are the confidence level of the final conclusion and the remaining unknowns?

## Basic Principles

### Evidence Priority

Judge platform capabilities in the following order:

1. Public specifications in the Lazycat developer manual.
2. The officially documented Go and JavaScript/TypeScript SDKs, package source, and type definitions.
3. The official documentation source repository and system changelog.
4. Current on-device POC results on LzcOS.
5. Lazycat’s official app store pages and observed behavior of official applications.
6. Community Skills, third-party SDK mirrors, and other open-source projects.

Every final capability conclusion must include at least one official specification or official SDK evidence item. For mounts, permissions injection, container differences, user isolation, and background auto-start, add an on-device POC.

### Do Not Infer Capabilities from Names

- A permission name only proves that the platform defines an authorization semantic; it does not automatically prove that a corresponding SDK method, directory, or file stream exists.
- Declaring permissions in `package.yml` only means that the application is requesting them. Whether they are granted at runtime and whether a mount or API appears must also be checked after installation and on the device.
- The fact that the SDK lacks a method does not directly prove that the platform has no filesystem projection. Continue checking the official documentation and the container runtime.
- Internal paths shown on an app store page can only be clues; they must not be written as stable contracts for third-party applications on their own.

### Distinguish Four Types of Content

Separate these in every assessment:

- Confirmed facts.
- Evidence-based inferences.
- Unconfirmed assumptions.
- Items that require POC validation.

Do not write an assumption as an implementation path that is already available.

### Version Sensitivity

Permissions, route fields, SDK methods, mount behavior, and system capabilities may change with LzcOS updates. During an assessment, check the current official documentation and changelog, and record the target device version. Do not rely only on old documentation or historical code.

### Security Boundaries

- Do not use host-internal paths such as `/lzcsys` or `/data/appvar` as production implementations for ordinary LPKs.
- Do not bypass the platform security model through `compose.override`, block-device permissions, or high-risk host mounts unless the requirement itself clearly needs them and they are officially allowed.
- Do not treat `WithRealUID`, a UID supplied by the browser, or a custom Header as an independent authorization boundary.
- An administrator role does not automatically grant permission to read other users’ data. Cross-user capabilities must have public permissions, platform-level isolation, auditing, and on-device verification.
- Do not put target application data, file contents, tokens, cookies, or database contents into ordinary logs or diagnostic bundles.

## Scope of Triggers

Use this Skill for the following tasks:

- Porting a GitHub, Gitee, Docker Hub, or existing Docker Compose project to Lazycat MicroServer.
- Assessing whether a new product is suitable to become a Lazycat App.
- Assessing OIDC, multiple instances, background jobs, drive storage, appvar, file access, databases, GPU, USB, KVM, LAN, and port-forwarding capabilities.
- Assessing the architecture, permissions, and data-isolation design of an existing Lazycat App.
- Writing or reviewing `package.yml`, `lzc-manifest.yml`, and `lzc-build.yml`.
- Determining whether a feature is blocked by code, a platform contract, a system version, or permission approval.
- Designing an on-device POC, acceptance matrix, and release gates.

Do not trigger this Skill for ordinary Docker or Web technology questions that do not involve Lazycat MicroServer.

## Input Information

Prefer to obtain the following:

- The project repository, Docker image, or product requirements.
- Functionality that must be retained and functionality that may be removed.
- The frontend, backend, database, queue, cache, and external services.
- Port, protocol, WebSocket, TCP/UDP, and LAN access requirements.
- Locations of persistent data, uploaded files, configuration, indexes, and databases.
- Data boundaries for single users, multiple users, administrators, and family members.
- The target LzcOS version and device architecture.
- Whether publication to the app store is planned.

When information is missing but can be inferred from the repository, investigate directly and list the assumptions in the conclusion. Ask the user only when the missing item would change the final feasibility result.

## Required Sources

### Main Entry Point and Agent-Oriented Documentation

- Developer manual: <https://developer.lazycat.cloud/>
- Documentation index: <https://developer.lazycat.cloud/llms.txt>
- Full documentation: <https://developer.lazycat.cloud/llms-full.txt>
- SDK overview: <https://developer.lazycat.cloud/introduction.html>

At the start of an assessment, read `llms.txt`. Load `llms-full.txt` only when multiple domains are involved or offline full-text search is needed.

### LPK Specifications

- `package.yml`: <https://developer.lazycat.cloud/spec/package.html>
- `lzc-manifest.yml`: <https://developer.lazycat.cloud/spec/manifest.html>
- `lzc-build.yml`: <https://developer.lazycat.cloud/spec/build.html>
- LPK format: <https://developer.lazycat.cloud/spec/lpk-format.html>

Use these to confirm package metadata, permissions, runtime structure, routes, environment variables, build artifacts, and minimum system versions.

### Runtime and Platform Capabilities

- File access: <https://developer.lazycat.cloud/advanced-file.html>
- Multiple instances: <https://developer.lazycat.cloud/advanced-multi-instance.html>
- Environment variables: <https://developer.lazycat.cloud/advanced-envs.html>
- HTTP Headers: <https://developer.lazycat.cloud/http-request-headers.html>
- Routing: <https://developer.lazycat.cloud/advanced-route.html>
- Advanced routing: <https://developer.lazycat.cloud/advanced-routes.html>
- Background residency: <https://developer.lazycat.cloud/advanced-background.html>
- Database services: <https://developer.lazycat.cloud/advanced-db.html>
- OIDC: <https://developer.lazycat.cloud/advanced-oidc.html>
- API Auth Token: <https://developer.lazycat.cloud/advanced-api-auth-token.html>
- System changelog: <https://developer.lazycat.cloud/changelog.html>

### Official SDKs and Tools

- Go SDK: <https://gitee.com/linakesi/lzc-sdk>
- Go API: <https://pkg.go.dev/gitee.com/linakesi/lzc-sdk/lang/go>
- Official Go API page: <https://developer.lazycat.cloud/api/golang.html>
- JavaScript/TypeScript SDK: <https://www.npmjs.com/package/@lazycatcloud/sdk>
- JavaScript API page: <https://developer.lazycat.cloud/api/javascript.html>
- `lzc-cli`: <https://www.npmjs.com/package/@lazycatcloud/lzc-cli>
- CLI documentation: <https://developer.lazycat.cloud/lzc-cli.html>
- File picker: <https://www.npmjs.com/package/@lazycatcloud/lzc-file-pickers>
- Drive mount SDK: <https://www.npmjs.com/package/@lazycatcloud/lzc-file-mount-sdk>
- WebDAV client: <https://www.npmjs.com/package/@lazycatcloud/webdav>

When checking SDK capabilities, use the current source, generated types, and method signatures as the authority. Do not infer an API that does not exist from old examples or permission names.

### Official Source and Templates

- Developer documentation source: <https://github.com/lazycatapps/lzc-developer-doc>
- Official project templates: <https://github.com/lazycatapps/hack>

Use web documentation to determine the public contract; use the source repository for full-text search, commit history, and version comparison; use templates for project structure and CI, not as proof of runtime permissions.

### Auxiliary Proto and Community Sources

- Rust SDK and Proto mirror: <https://github.com/lib-x/lzc-sdk-rs>
- Lazycat Skills: <https://github.com/whoamihappyhacking/lazycat-skills>
- App store: <https://lazycat.cloud/appstore>

Use Proto mirrors and community Skills to accelerate research. The final conclusion must still return to official documentation, official SDKs, or on-device results. App store pages may only be used to discover similar products and runtime clues.

## Standard Assessment Process

### Step 1: Decompose the Target Project

Check the following in the repository or requirements:

- Project type: frontend-only, monolith, multiple services, desktop application, or system service.
- Build method: build from source, prebuilt binary, Docker image, or Compose.
- CPU architectures: amd64, arm64, or other architectures.
- Entry protocols: HTTP, HTTPS, WebSocket, TCP, UDP, or VNC.
- Number of services, startup order, and health checks.
- Runtime user, file permissions, and whether running as root is explicitly forbidden.
- Persistent, cache, and temporary directories.
- Database, message queue, cache, and object storage.
- Background jobs, Cron, downloads, transcoding, and long-lived connections.
- Requirements for GPU, USB, KVM, FUSE, host network, and NET_ADMIN.
- User system, OIDC, administrator permissions, and multi-tenant model.
- External APIs, LAN devices, domains, and certificate dependencies.
- Open-source license and restrictions on app-store distribution.

Check the README, Dockerfile, Compose file, environment-variable examples, startup scripts, database migrations, CI, Issues, and Releases. Do not give a feasibility conclusion after looking only at the README.

### Step 2: Map Lazycat Capabilities

Create a mapping table for each dimension:

| Dimension | Project requirement | Lazycat mechanism | Evidence | Status | POC |
| --- | --- | --- | --- | --- | --- |
| Package and build |  | LPK V2, build, embed image |  |  |  |
| Container topology |  | application, services |  |  |  |
| Routing |  | routes, upstreams, exec, ingress |  |  |  |
| Persistence |  | var, cache, document, media |  |  |  |
| Identity |  | OIDC, Header, runtime UID |  |  |  |
| Multiple instances |  | Single instance or per-user instance |  |  |  |
| Permissions |  | package permissions, compatibility permissions |  |  |  |
| Background execution |  | background_task, catch-up |  |  |  |
| Database |  | built-in service, external service, backup method |  |  |  |
| Network |  | internet, LAN, host, TCP/UDP |  |  |  |
| Devices |  | GPU, USB, KVM, block |  |  |  |
| App-store release |  | architecture, permissions, review, images |  |  |  |

Use only these statuses:

- `SUPPORTED`: The public contract is sufficient; normal implementation is enough.
- `ADAPTATION_REQUIRED`: It can be implemented, but the project structure or code must be adapted.
- `POC_REQUIRED`: Public material is insufficient, or runtime behavior depends on the version and requires an on-device POC.
- `PLATFORM_BLOCKED`: The platform capability or authorization contract is missing; the core functionality cannot currently be completed.
- `OUT_OF_SCOPE`: The user explicitly does not need it, or it is excluded from the current version.

### Step 3: Identify Hard Blockers

Check these blockers first:

- There is no image or binary for the target architecture, and building from source is not possible.
- The application depends on a host kernel module, Docker socket, block device, or an unopened system capability.
- It must access another application’s or another user’s data, but there is no formal permission or platform isolation.
- Data can only exist in the container’s ephemeral layer, with no adjustable persistent path.
- It must copy the data directory of a running MySQL, PostgreSQL, MongoDB, Redis, or similar service, but has no native database backup mechanism.
- It depends on a fixed public domain, callback address, or LAN broadcast, but the Lazycat network model cannot satisfy it.
- The user boundaries of multiple instances, OIDC, and data directories cannot be made consistent.
- It needs unattended scheduled tasks, but multi-instance auto-start and restart catch-up have not been verified.
- A commercial or open-source license prohibits redistributing the image or application package.

After finding a hard blocker, still provide alternatives, such as an export initiated by the target application, a standalone system component, an official API, a reduced feature scope, or waiting for platform support.

### Step 4: Design the Minimum POC

The POC should validate only capabilities that would change the feasibility conclusion; do not develop the complete product in advance.

Common POCs:

- Whether the LPK can be installed, started, and pass its health check.
- Whether HTTP, WebSocket, TCP, or UDP routing works.
- Whether the target persistent directory can be read and written, and remains after a restart.
- Whether runtime authorization is actually granted after declaring a permission.
- Whether an SDK method returns the expected fields.
- Whether a special mount appears in the `application` or a specific `service` container.
- Whether the OIDC UID, entry Header, and runtime instance UID are consistent.
- Whether user A cannot access user B’s data.
- Whether SQLite WAL, file locks, and random reads/writes meet the requirement.
- Whether `background_task`, auto-start, and missed-run compensation meet the product promise.
- Whether large files, file counts, and concurrent tasks fit within the device’s resource limits.

At minimum, an on-device POC must record:

```text
Test date
Full LzcOS version
MicroServer model and CPU architecture
LPK version and SHA-256
package.yml permission declarations
Whether runtime permissions were granted
Key configuration in lzc-manifest.yml
Container hosting the backend
API Gateway call results
mountinfo / findmnt / stat evidence
User A/B isolation results
Successful output and failure error codes
Complete reproduction commands
```

Before performing installation, uninstallation, permission changes, data cleanup, or device-configuration changes, obtain user confirmation. Use non-production devices and test data without sensitive information for POCs.

### Step 5: Provide the Conclusion

Use only the following five conclusion categories:

#### Ready to Deliver Directly

The public contract, SDK, images, and runtime conditions are complete; changes are limited to LPK configuration and a small amount of adaptation.

#### Deliverable with Adaptation

Platform capabilities are sufficient, but the project must adjust storage, identity, routing, architecture images, background jobs, or database deployment.

#### Decision After a Passing POC

There are public clues for the core path, but permissions injection, mounts, user isolation, system version, or performance still require on-device evidence.

#### Currently Blocked by Platform Capabilities

The core functionality depends on a platform contract that is not public or authorized. Explain the specific missing capability and the non-blocked parts that can continue.

#### Not Recommended for Delivery

Implementation effort, resource consumption, security risk, or maintenance cost clearly exceeds the product value. Give specific reasons and alternatives.

Attach the following to every conclusion:

- Confidence: high, medium, or low.
- Direct evidence.
- Unconfirmed items.
- Next action.

## Specialized Decision Rules

### `package.yml` Permissions

- Use only permission IDs that currently exist in the official specification.
- Distinguish required from optional permissions.
- Record the minimum LzcOS version.
- Continue verifying whether runtime authorization is granted after declaring a permission.
- An unpublished `PERM_*` compatibility permission is only POC evidence for a specific version; do not automatically treat it as a public LPK contract.

### `application` and `services`

The main `application` container and auxiliary `services` containers may have different mounts, environments, and permissions. When files, devices, API sockets, or identity information are involved, verify the actual runtime container separately. Do not infer that the entire application lacks a capability because one container failed.

### Files and Storage

- Put the current application’s internal state in `/lzcapp/var` by default.
- Put caches and rebuildable data in `/lzcapp/cache`.
- For files that users can understand and need to manage in drive storage, use the document directories and permissions specified by the current version of the official documentation.
- Paths may differ between old and new versions; verify the current specification and the device before writing documentation or code.
- `/lzcapp/pkg/content` is read-only package content; do not use it for runtime-modifiable configuration.
- To read another application’s appvar, verify permissions, the formal access contract, and user isolation together.

### OIDC, Headers, and Tenants

- Use Lazycat OIDC when the application needs independent sessions, callbacks, or role information.
- An entry-injected user Header can serve as a platform identity signal; the backend should check consistency with the OIDC Session and runtime instance UID.
- A UID submitted by the frontend does not participate in authorization.
- An administrator role affects only confirmed administrative capabilities. Without evidence of cross-user permission and isolation, still process an administrator within the current-user scope.
- Verify with two ordinary users on a real device whether a multi-instance application can see only the current user’s data.

### `appvar.other.read`

Assess this capability separately in the following steps:

1. Whether the permission is defined in `package.yml`.
2. Whether the permission is actually granted after installation.
3. Whether `QueryApplication` can return the target `appid`, `deploy_id`, owner, and multiple-instance information.
4. Whether data content is accessed through the SDK, a formal mount, or another versioned contract.
5. Which container contains the data entry point.
6. Whether the platform restricts access to the current user under multiple instances.
7. Whether the source data is read-only, or whether the application at least enforces strict read-only behavior at the service layer.
8. Whether target files are written to a legal drive-storage directory for the current user.

`QueryApplication` provides only application-instance metadata; it cannot automatically be treated as a file-reading API. Directories shown on app store pages can only help locate clues. Host directories must not be used as a production fallback.

When a project has completed a POC, record the exact LzcOS version, package SHA, compatibility permissions, container, paths, directory layout, and isolation scope. The result applies only to the verified combination; do not generalize it to all versions and all applications without testing.

### Databases

- Back up a running SQLite database with the SQLite Online Backup API or an equivalent consistency mechanism.
- Do not directly copy an active SQLite main file while ignoring WAL/SHM.
- Use the database’s official backup mechanism for MySQL, MariaDB, PostgreSQL, MongoDB, Redis, ClickHouse, Elasticsearch, and similar systems.
- Without a database account, export API, maintenance window, or official snapshot mechanism, do not label copying the data directory as a reliable backup.
- For embedded databases such as RocksDB, LevelDB, LMDB, bbolt, Badger, and DuckDB, also check for a dedicated checkpoint or backup API.

### Background Jobs and Scheduled Plans

- Use `background_task` to prevent an application from being stopped because it is inactive.
- Background residency does not automatically mean the application will auto-start after a device restart.
- Test restart, auto-start, lease recovery, and missed-run compensation for multi-instance schedules.
- When the product cannot guarantee auto-start, clearly document the catch-up behavior on the page and in the documentation.

### Routing and Networking

- Prefer `file://` for static frontends.
- A backend in the same container can use `exec://` or the main-container process.
- Forward auxiliary services through `http://service` or a complete lzcapp domain.
- Verify path preservation, Host, WebSocket, CORS, and multiple domains according to the routes/upstreams documentation.
- Use ingress for TCP/UDP, confirming ports, authentication, and exposure scope.
- Explain the security impact of LAN access, host networking, and NET_ADMIN.

### Images, Architecture, and Builds

- Check image manifests for amd64 and arm64.
- When there is no multi-architecture image, confirm whether the project can be built from source or generated with Buildx.
- Do not download dependencies or compile the frontend when the container starts.
- A release package should include images or build artifacts at deterministic versions.
- Check health checks, initialization scripts, read-only package directories, and writable configuration directories.

### App-Store Release

- Verify the license and rights to redistribute images.
- Explain the user value and risks of high-privilege, device, LAN, and cross-application data permissions.
- Set `min_os_version` according to the latest necessary capability used.
- Include on-device POCs, amd64/arm64, upgrades, data retention after uninstall, and permission changes in release acceptance.

## Output Format

### Quick Assessment

When the user only asks “Can it be done?”, use this structure:

```markdown
## Conclusion
Deliverable with adaptation, confidence: medium.

## Confirmed
- ...

## Main Adaptations
- ...

## Blockers and POC
- ...

## Next Steps
- ...
```

### Complete Assessment Report

```markdown
# Lazycat MicroServer Application Feasibility Assessment

## 1. Conclusion
- Assessment level:
- Confidence:
- One-sentence judgment:

## 2. Current State of the Target Project
- Technology stack:
- Deployment topology:
- Data and external dependencies:

## 3. Lazycat Capability Mapping
| Dimension | Project requirement | Lazycat mechanism | Evidence | Status | POC |

## 4. Necessary Adaptations

## 5. Permissions, Identity, and Data Boundaries

## 6. Persistence and Databases

## 7. LPK Implementation Recommendations
- package.yml
- lzc-manifest.yml
- lzc-build.yml

## 8. POC Plan and Pass Criteria

## 9. Risks and Alternatives

## 10. Unconfirmed Items

## 11. Implementation Order
```

Attach official links or source locations to important facts. When analyzing a repository, cite specific files, configurations, or code locations.

## Deliverables

As requested by the user, continue to produce:

- Feasibility assessment reports.
- PRDs, PDDs, or technical proposals.
- Minimal POC implementation documentation.
- Examples of `package.yml`, `lzc-manifest.yml`, and `lzc-build.yml`.
- Docker Compose to LPK migration mappings.
- Permission lists and OIDC, multiple-instance designs.
- On-device test checklists, release acceptance tables, and issue tickets.

When generating configuration, use only fields and paths that have already been confirmed. When something is uncertain, use a placeholder explanation and POC gate; do not invent a platform interface.

## Common Errors

The following practices are prohibited:

- Seeing that a Docker image starts and directly concluding that the application can be fully delivered.
- Looking only at the README without checking the Dockerfile, Compose file, data directories, and database.
- Inferring an SDK method or fixed mount path from a permission name.
- Writing a permission declaration as if it were already granted.
- Treating app store copy as a public development contract.
- Writing host-internal paths into an ordinary LPK implementation.
- Treating `QueryApplication` as a cross-application file-reading interface.
- Assuming that an administrator can back up other users’ data by default.
- Replacing platform-level user-isolation verification with backend owner filtering.
- Copying a live database directory and labeling it a consistent backup.
- Inferring from `background_task` that the application will definitely auto-start after a device restart.
- Claiming that the on-device platform path has run successfully based only on `go test`, a frontend build, or a fixture.
- Seeing no mount in one container and asserting that no other container has one either.
- Citing old-version documentation without recording the target LzcOS version.

## Completion Criteria

A qualified assessment must satisfy all of the following:

- The target functionality scope is clear.
- The project source or deployment configuration has been checked.
- Official documentation and SDK evidence are complete.
- The platform version and architecture are recorded.
- Permission declarations and runtime authorization are distinguished.
- Single-instance, multiple-instance, OIDC, and data ownership are explained.
- Database consistency and persistence paths are explained.
- Every unknown is turned into an executable POC.
- The conclusion includes an assessment level, confidence, and next step.
- No unconfirmed host paths or platform interfaces are used.
