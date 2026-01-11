# Git Procedures

## Basic Workflow for Feature Additions

### 1. Create a Feature Branch
Before making any changes, checkout a new branch for your feature:
```bash
git checkout -b feature-name
```

### 2. Make Your Changes
Implement your feature or changes in the new branch.

### 3. Test for Functionality
Thoroughly test your changes to ensure everything works as expected.

### 4. Commit Your Changes
Once testing is complete, commit your changes to the branch:
```bash
git add .
git commit -m "Description of your changes"
```

### 5. Merge into Main Branch
After your feature is complete and tested, merge it into the main branch:
```bash
git checkout main
git merge feature-name
```

### 6. Clean Up (Optional)
Delete the feature branch after merging:
```bash
git branch -d feature-name
```
