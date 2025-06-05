1. GitHub Actions must be enabled
If you see a section named “Actions” under your organization or repo, that means GitHub Actions is available, but:

It still might be restricted to only GitHub-hosted runners

You might not have permission to add self-hosted runners unless your org allows it

✅ 2. Permissions You Need
Ask your GitHub admin to verify the following:

Permission / Setting	Required For
GitHub Actions is enabled org-wide	Run any workflows
You can create self-hosted runners	Required for ARC
You can generate a PAT with repo, workflow, admin:org scopes (if using org-level runners)	Needed to install ARC

✅ 3. Where ARC Will Register Runners
Depending on your deployment, you can target:

A specific repository:

yaml
Copy
Edit
spec:
  template:
    spec:
      repository: your-org/your-repo
Or an entire organization (needs higher privileges):

yaml
Copy
Edit
spec:
  template:
    spec:
      organization: your-org
Using organization-wide runners requires admin:org access and enabling self-hosted runners at the org level.

