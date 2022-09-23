# How to transfer your Momentum project repos to your personal GitHub

Your homework repos live in your team’s organization on GitHub. We use this as a protected space for works-in-progress and beginner projects. This also gives you control over what you show to employers, and when you show it.

To transfer a single repo, follow these steps:

1. Go to your Github homepage (e.g., `github.com/amygori`)
2. On your homepage, create a new repo on GitHub using the ➕ menu in the upper right corner. ([GitHub documentation: Creating a new repo](https://help.github.com/en/github/getting-started-with-github/create-a-repo))
	- Give it a name that is clear and informative.
	- Check the Public option during setup.
	- Do NOT initialize the repo with a README.md (you will want to create README for your projects, but not at this step).
3. Copy the url that is shown after you create the repo.
4. In the terminal on your computer, go to the local project repo containing the code that you want to put in this newly created repo on GitHub.
5. Run  `git remote set-url origin <your-GitHub-repo-url-that-you-just-copied>`
    - [GitHub documentation: changing a remote's URL](https://help.github.com/en/github/using-git/changing-a-remotes-url)
6. Run `git remote -v`  to see that the remote url was changed successfully. It should be labeled `origin` and match the url of the repo you created on GitHub.
7. Run `git push origin main` to push your existing repo to that remote url.
8. Go to GitHub and refresh the page of the repo you created to see your code. You can continue to work in this repo, committing and pushing as usual.
9. Consider writing an informative README for your project to let employers know what they are looking at! This should replace the README containing assignment instructions that was in the repo that you originally cloned.
