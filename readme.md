# SIMPLE MOCK – test environment switching with terraform init only (no AWS)

No cloud, no provider, no resources: `main.tf` only has outputs. Each run does
`terraform init` with that environment's backend file, then plan or apply prints the environment.

- **PR opened / updated / label changed** → `tf-plan`: init + plan for the labelled environment
- **PR merged to main** → `tf-apply`: init + apply, outputs show the environment
- Environment = PR label `sandbox` or `production` (exactly one, otherwise the run fails)

## Setup (2 minutes)
1. New **public** GitHub repo (Environments on private repos need a paid plan). Unzip this into it, push to `main`.
2. Settings → Environments → create `sandbox` and `production` (no variables needed).
3. Issues → Labels → create `sandbox` and `production`.

## Test
1. New branch, change anything (e.g. this README), push, open a PR into `main`.
2. PR sidebar → **Labels** → `sandbox`. Actions → **tf-plan** runs once:
   - step "terraform init (plan → sandbox)" uses `envs/sandbox/backend.hcl`
   - plan shows `environment = "sandbox"`
3. Swap the label to `production` → tf-plan runs again, now everything says `production`.
4. Merge → **tf-apply** runs once. Its apply step (and the job Summary) shows:
   ```
   environment        = "production"
   github_environment = "production"
   message            = "Terraform ran in the PRODUCTION environment"
   ```
5. No label, or both labels → the `resolve` job fails with "Add exactly one PR label".

`environment` comes from `envs/<env>/terraform.tfvars`; `github_environment` comes from the
GitHub Environment the job ran in. If they ever differ, the wiring is wrong.

test
Production test
No lable test
