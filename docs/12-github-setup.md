# 12 — GitHub Repository Setup

[Home](../README.md)

## Repository identity

| Field | Value |
|---|---|
| Repository name / URL slug | `VXLAN-Huawei-Cisco` |
| README display title | `VXLAN [Huawei & Cisco]` |
| Suggested visibility | Public for sanitized learning material |
| Initial release tag | `v1.0.0` |

GitHub repository names do not accept spaces, square brackets or ampersands. Use the requested title in the README and the valid slug for the repository URL.

## Description — copy this

```text
VXLAN and BGP EVPN lab guides for Huawei CloudEngine and Cisco Nexus, including diagrams, static and advanced configurations, interoperability, and troubleshooting.
```

## Suggested topics

```text
vxlan evpn bgp huawei cloudengine cisco nexus nx-os vrp networking network-engineering datacenter troubleshooting homelab
```

## Browser upload

1. Create a new GitHub repository named `VXLAN-Huawei-Cisco`.
2. Paste the description above. Choose the visibility you want.
3. Do not auto-create another README; the package already includes one.
4. Extract `VXLAN-Huawei-Cisco.zip` on your computer.
5. Open the extracted `VXLAN-Huawei-Cisco` directory.
6. Use GitHub **Add file → Upload files** and upload its contents, including the `docs`, `configs` and `diagrams` folders. Do not upload only the ZIP or an extra parent folder.
7. Commit with message `Add Huawei and Cisco VXLAN lab documentation`.
8. Check README links and rendered Mermaid diagrams. Add topics through the repository About section.

If your browser cannot retain a directory upload, use the Git workflow below.

## Git upload

After creating an empty repository, run from the extracted project directory. Replace `YOUR_GITHUB_USERNAME` first.

```bash
git init
git add .
git commit -m "Add Huawei and Cisco VXLAN lab documentation"
git branch -M main
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/VXLAN-Huawei-Cisco.git
git push -u origin main
```

Authenticate through your normal GitHub method; no credentials are included in this package. For an existing repository, inspect its current history/remotes and integrate these files normally; do not force-push.

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
