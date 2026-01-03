# GitHub Copilot Instructions for Microsoft Service Tags Repository

## Repository Overview

This repository automatically downloads and tracks Microsoft Azure Service Tags IP ranges for both Public (Commercial) and US Government clouds. Service Tags are updated weekly via GitHub Actions workflow.

## Project Structure

- **Root JSON Files**: `ServiceTags_Public.json` and `ServiceTags_Gov.json` contain IP ranges for Azure services
- **GitHub Actions**: `.github/workflows/serviceTagsUpdate.yml` handles automated weekly downloads and commits
- **PowerShell**: Primary scripting language for automation tasks

## Coding Standards

### PowerShell

- Use `pwsh` (PowerShell 7+) for cross-platform compatibility
- Prefer explicit parameter names over positional arguments
- Use `-UseBasicParsing` with web requests to avoid IE dependency
- Use `Write-Host` for console output
- Always include error handling for web requests and file operations
- Use proper variable scoping with `$()` for command substitution

### Script Headers

All PowerShell scripts should include a standardized header:

```powershell
# .Synopsis
#     Brief description of the script
#
# .NOTES
#     Author     : Your First and Last Name
#     Version    : 1.0.YYMMDD
#
#             _
#         .__(.)<  (MEOW)
#          \___)
#  ~~~~~~~~~~~~~~~~~~~~~~~~
```

### Date Formats

- Version numbers: `1.0.YYMMDD` (e.g., `1.0.250525`)
- Commit messages: `dd.MM.yyyy` (e.g., `Service Tags Update 03.01.2026`)

## GitHub Actions Workflows

### Naming Conventions

- Use descriptive camelCase names for workflow files
- Jobs and steps should have clear, descriptive names

### Workflow Patterns

- Use `workflow_dispatch` to allow manual triggering
- Use cron schedules for regular automation (e.g., weekly updates on Sunday: `"0 0 * * 0"`)
- Always checkout with `fetch-depth: 0` for full git history
- Use environment variables for reusable values
- Always check for changes before committing: `git diff`

### Git Operations

When committing changes in workflows:

```powershell
git config --global user.name  "Your First and Last Name"
git config --global user.email "your.email@example.com"
git add .
git commit -m "Message $(Get-Date -Format dd.MM.yyyy)"
git -c http.extraheader="AUTHORIZATION: bearer ${GITHUB_TOKEN}" push
```

## Microsoft Service Tags Specifics

### Download Sources

- **Public Cloud**: `https://www.microsoft.com/en-us/download/details.aspx?id=56519`
- **Government Cloud**: `https://www.microsoft.com/en-us/download/details.aspx?id=57063`

### Download Pattern

1. Fetch the download page
2. Parse HTML to find the latest download URL
3. For Public Cloud, use regex to match versioned files and sort by date
4. Download to standardized filenames: `ServiceTags_Public.json` and `ServiceTags_Gov.json`

### JSON Structure

Service Tags JSON files contain:
- `changeNumber`: Version/revision number
- `cloud`: Cloud type ("Public" or "AzureGovernment")
- `values`: Array of service tag objects with IP ranges

## Best Practices

1. **Always validate downloads**: Ensure JSON files are valid before committing
2. **Check for changes**: Only commit when files have actually changed
3. **Use consistent formatting**: Maintain existing code style
4. **Keep workflows efficient**: Minimize API calls and network requests
5. **Handle errors gracefully**: Provide clear error messages for troubleshooting
6. **Use `shell: pwsh`**: Explicitly specify PowerShell for cross-platform compatibility

## Common Tasks

### Adding a new workflow

- Place in `.github/workflows/` directory
- Include standardized header with author and version
- Use descriptive name following camelCase convention
- Add workflow status badge to README.md

### Modifying download logic

- Test regex patterns against actual Microsoft download pages
- Handle both versioned and non-versioned file URLs
- Sort by date to ensure latest version is downloaded
- Use `Invoke-WebRequest` with `-OutFile` and `-UseBasicParsing`

### Updating documentation

- Keep README.md concise with essential information
- Add workflow badges for visibility
- Document any new workflows or significant changes

## Tools and Dependencies

- **PowerShell 7+**: For cross-platform scripting
- **GitHub Actions**: For automation (ubuntu-latest or windows-latest runners)
- **Git**: For version control operations
- No external PowerShell modules required (uses built-in cmdlets)

## Maintenance Notes

- Service Tags are updated by Microsoft regularly
- Workflow runs weekly (Sunday at midnight UTC)
- Commit messages include the date of update
- Check GitHub Actions for failed runs if updates are missing
