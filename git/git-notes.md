PART A — Create a “team repo” locally (simulates a shared repo)
1. Create the repo and initial structure
    mkdir -p ~/labs/git/lab3-team-workflow
    cd ~/labs/git/lab3-team-workflow
    git init
   Create Files:
    mkdir -p docs src
    printf "# Lab 3 - Team Git Workflow\n\n" > README.md
    printf "## Unreleased\n\n- Initial project structure\n" > CHANGELOG.md
    printf "print('hello')\n" > src/app.py
   Initial/First Commit
    git add .
    git commit -m "chore: initial repo structure"
   Rename Branch to main
    git branch -M main

2. Create a “remote” repo locally (simulates GitHub/GitLab)
     cd ~/labs/git
     git clone --bare lab3-team-workflow lab3-team-remote.git
    Now set your working repo to use that remote:
      cd ~/labs/git/lab3-team-workflow
      git remote add origin ~/labs/git/lab3-team-remote.git
      git push -u origin main

PART B — Feature branch workflow
3. Create a feature branch (like a real ticket)
    git checkout -b feature/add-healthcheck
   Edit the files
    printf "\nprint('healthcheck: ok')\n" >> src/app.py
    printf "\n## Added\n- Basic healthcheck output\n" >> docs/release-notes.md
   Commit the files
    git add .
    git commit -m "feat: add basic healthcheck output"
   Push the feature branch
    git push -u origin feature/add-healthcheck

PART C — Simulate a Pull Request (PR) + Review
4. Create a “PR description” file (local PR simulation)
    cat > docs/PR-feature-add-healthcheck.md << 'EOF'
    # PR: Add healthcheck output

    ## Why
    Adds a simple healthcheck line to confirm app responsiveness.

    ## What changed
    - Updated src/app.py to print healthcheck
    - Added release notes entry

    ## How to test
    Run:
    python3 src/app.py
    EOF
   Commit it:
    git add docs/PR-feature-add-healthcheck.md
    git commit -m "docs: add PR description for healthcheck feature"
    git push

5. Review checklist (like a team)
    Run these review steps
     git diff main..feature/add-healthcheck
     python3 src/app.py

PART D — Merge to main (with a clean history mindset)
6. Merge feature branch into main
    git checkout main
    git pull
    git merge --no-ff feature/add-healthcheck -m "merge: feature/add-healthcheck"
   Push to main branch
    git push origin main
   Delete the feature branch 
    git branch -d feature/add-healthcheck
    git push origin --delete feature/add-healthcheck

PART E — Tags + Release Notes (Real release workflow)
7. Update CHANGELOG for a release
    Edit CHANGELOG.md to look like:
      ## v0.1.0 - 2026-02-06

      ### Added
      - Basic healthcheck output
    Commit:
      git add CHANGELOG.md
      git commit -m "docs: update changelog for v0.1.0"
      git push
8. Create a tag for the release
    git tag -a v0.1.0 -m "Release v0.1.0"
    git push origin v0.1.0
   Verify tags
    git tag
    git show v0.1.0

