# ansible_project_github_issue_management

This repository contains an Ansible playbook and role to manage GitHub issue creation for two issue categories:

- `REGEX_UPDATE`
- `NEW_AUTOMATION`

The role always performs duplicate detection across open and closed issues before attempting to create a new issue.

## Structure

- `playbooks/github_issue_management.yml`
- `roles/github_issue_manager/defaults/main.yml`
- `roles/github_issue_manager/tasks/main.yml`
- `vars/regex_update_example.yml`
- `vars/new_automation_example.yml`

## Behavior

1. Runs on `localhost`.
2. Calls the same role with conditional flow based on `issue_category`:
	- `REGEX_UPDATE`
	- `NEW_AUTOMATION`
3. Masks sensitive values (IP addresses and hostnames) from relevant input fields.
4. Fetches all open and closed issues before any create action.
5. Checks whether any existing issue already contains the same `Alert Description` and `Alert Key`.
6. If duplicate exists, returns issue details and does not create a new issue.
7. If duplicate does not exist, creates a new issue unless `prevent_issue_creation: true`.

## Required Inputs

Common:

- `github_repo_owner`
- `github_repo_name`
- `github_token` (recommended via environment variable `GITHUB_TOKEN`)
- `issue_category` (`REGEX_UPDATE` or `NEW_AUTOMATION`)
- `alert_description`
- `alert_key`

For `REGEX_UPDATE`:

- `suggested_regex`
- `reasoning`
- `confidence_score`

For `NEW_AUTOMATION`:

- `development_approach`
- `automation_type` (`Diagnostics` or `Remediation`)

## Run

Set token:

```powershell
$env:GITHUB_TOKEN = "<your_github_token>"
```

Run for `REGEX_UPDATE`:

```powershell
ansible-playbook playbooks/github_issue_management.yml -e @vars/regex_update_example.yml
```

Run for `NEW_AUTOMATION`:

```powershell
ansible-playbook playbooks/github_issue_management.yml -e @vars/new_automation_example.yml
```

Optional dry-run style behavior (never create issue):

```powershell
ansible-playbook playbooks/github_issue_management.yml -e @vars/regex_update_example.yml -e "prevent_issue_creation=true"
```