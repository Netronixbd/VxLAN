# 12 — GitHub Repository Setup

[Home](../README.md)

## Repository identity

| Field | Value |
|---|---|
| Repository name / URL slug | `VxLAN` (current repository) |
| README display title | `VXLAN [Huawei & Cisco]` |
| Suggested visibility | Public for sanitized learning material |
| Initial release tag | `v1.0.0` |

The existing GitHub repository is `Netronixbd/VxLAN`. Keep the requested `VXLAN [Huawei & Cisco]` title in the README. Repository names do not accept spaces, square brackets or ampersands.

## Description — copy this

```text
VXLAN and BGP EVPN lab guides for Huawei CloudEngine and Cisco Nexus, including diagrams, static and advanced configurations, interoperability, and troubleshooting.
```

## Suggested topics

```text
vxlan evpn bgp huawei cloudengine cisco nexus nx-os vrp networking network-engineering datacenter troubleshooting homelab
```

## Browser upload

1. Open the existing repository `https://github.com/Netronixbd/VxLAN`.
2. Paste the description above. Choose the visibility you want.
3. Keep one root `README.md`; the package includes the updated file.
4. Extract `VXLAN-Huawei-Cisco-Markdown.zip` on your computer.
5. Open the extracted `VXLAN-Huawei-Cisco` directory inside the package.
6. You can upload the changed Markdown files through GitHub. **To remove the old `.cfg` files automatically**, the Git workflow below is preferred.
7. Commit with message `Add Huawei and Cisco VXLAN lab documentation`.
8. Check README links and rendered Mermaid diagrams. Add topics through the repository About section.

If your browser cannot retain a directory upload, use the Git workflow below.

## Git upload

For the **existing** `Netronixbd/VxLAN` repository, first update your local checkout using `git pull --rebase origin main`; then extract the new ZIP and copy its *contents* into the checkout. Delete the old `.cfg` files before staging. In Git Bash run:

```bash
cd /f/VxLAN/VXLAN-Huawei-Cisco
git pull --rebase origin main
# Next, extract the ZIP and copy its files into this directory.
# Then delete old .cfg files in configs/static and configs/evpn.
git add -A
git status
git commit -m "Convert VXLAN device configs to Markdown guides"
git push origin main
```

Authenticate through your normal GitHub method; no credentials are included in this package. Review `git status` before committing. Do not force-push.

## Optional first release

```bash
git tag -a v1.0.0 -m "Initial VXLAN Huawei and Cisco lab reference"
git push origin v1.0.0
```

## Final repository checks

- README title is `VXLAN [Huawei & Cisco]`.
- Relative links open correctly.
- Configurations are under the correct vendor and lab folders.
- README states that templates are not device-tested.
- No credentials, real customer configuration or vendor images are committed.
- Select a content license deliberately if you want to grant reuse permissions.
