# 👋 Welcome to GitKraft

GitKraft is a platform for distributing **software as source code** on GitHub, uniquely designed to empower end-users to **easily manage and retain custom, source-level modifications** of software.

With GitKraft's **GitHub extensions**, users can create **private forks** of software, **customize its source code**, and **update** the software to the latest vendor or community release—all **without losing customizations**.

## Why Choose GitKraft?

Large organizations often need to **adapt the source code** of vendor or community software to meet specific requirements while keeping up with the latest updates. However, GitHub has a notable limitation: a fork of a public repository must also be public, as private forks of public repositories are not supported. GitKraft bridges this gap by enabling the creation of **private forks for public repositories**.

While GitHub allows private forks when the source repository is private (with the option to later make the source repo public), this approach introduces a significant security flaw. Users from other organizations can access commits from private repositories within the same fork network, even including commits from deleted private repositories. Truffle Security has highlighted this issue[^Truffle], which poses a severe risk of exposing confidential information. GitKraft's private forks address this problem, ensuring your data remains **secure** and **isolated**.

In addition to secure forks, GitKraft offers a modern and efficient solution for **managing source-code patches**, overcoming challenges associated with traditional approaches. Current patch management methods often obscure the change history, complicating code reviews and collaboration. Some common problems include:

- **`.patch` files in Git repositories**: Storing customizations as `.patch` files feels like using one version control system inside another, making it difficult to understand changes.
- **Merge**: Merging upstream changes into a private branch buries customizations within the branch's history, making it hard to distinguish between official releases and custom modifications.
- **Rebase**: Rebasing a patch branch over upstream changes disrupts workflows for developers who have already checked out the branch, leading to errors when running `git pull`.

To address these challenges, GitKraft introduces a novel approach to patch management with a new operation called _weld merge_. This operation preserves a transparent history of changes, simplifying audits and fostering effective collaboration.

In summary, GitKraft accelerates development and update cycles, enables rapid vulnerability fixes, and maintains clear audit trails. These features empower open-source collaboration, reduce reliance on permanent forks, and streamline contributions back to the community.

## GitKraft for Open Source

A prime example of software distributed as source code is **Helm charts**, commonly used to configure services on the Kubernetes platform. End-users often need to customize Helm templates at the source level, highlighting the need for robust patch management solutions like GitKraft. To benefit the public, GitKraft will distribute popular open-source Helm charts on its platform. This initiative is currently in progress. Stay tuned for updates.

## Stay in Touch

**[⭐ Star this project](https://github.com/gitkraft/gitkraft)**. Highly recommended, starred users may receive support priority over regular users.

**[📲 Follow me on LinkedIn](https://www.linkedin.com/in/akorzy)**. Don't miss out on updates.

**[▶️ Watch a demo](https://www.youtube.com/watch?v=G8VT_YaDY5U)**. See it in action.

**[✉️ Send me a free message on LinkedIn](https://www.linkedin.com/in/akorzy)**. If you're interested in this project, tell me what you need! I promise – no chatbot!

## How to Use GitKraft

When GitKraft is released you'll be able to use it as follows.

| Step No. | Instructions | Screenshot |
|----------|----------|--|
| 1    | Browse the available repositories in the gitkraft organization on GitHub. Click on the repository containing the chart you require.|![browse](https://github.com/user-attachments/assets/cce31d44-cc3b-4bdd-9a43-417a4cf75a30)|
| 2    | Scroll down to the README file. Click on the green **Private Fork** button. |![private fork](https://github.com/user-attachments/assets/2ab87261-3802-4994-99bb-7590edbfc01c)|
| 3    | Choose your organization for the private fork, then click **Create repository**. |![create repository](https://github.com/user-attachments/assets/0c966688-9ad6-4718-8bb0-14d6494eb21a)|
| 4    | A GitHub workflow has now started in the background and will populate the repository with contents. You can click on the green **Watch progress** button, or wait and refresh the page after a couple of minutes.|![watch progress](https://github.com/user-attachments/assets/b310738c-f1da-453c-b10c-511541804779)|
| 5    | Once the repository is ready, commit your customizations to the `main` branch as needed. |![commit](https://github.com/user-attachments/assets/1264aa71-8089-46b7-a3a0-a2369701c14e)|
| 6    | When a new community version is released, click on the **Update** button in the README file, which will take you to a page where you can trigger an update. |![update](https://github.com/user-attachments/assets/bbc24c87-d985-4142-b22b-2c8cd65f66fb)|
| 7    | Click on the dark grey dropdown box **Run workflow** and then on the green **Run workflow** button to import the latest release while preserving your source-level customizations. Optionally, you can also type in another release to import, rather than the latest one – such as an older version to roll back to.|![run workflow](https://github.com/user-attachments/assets/2edaa9ae-eecb-459a-82a7-2e3d2d4e0f4a)|
| 8a   | If there are no conflicts, the workflow completes successfully, merging your customizations with the update. |![update success](https://github.com/user-attachments/assets/69e4dc5f-ec05-430a-a350-d1ed935a2b84)|
| 8b   | If conflicts occur, the workflow provides a button to resolve it and proceed with the update by creating a pull request and accessing the conflict resolution interface. |![create pr to resolve](https://github.com/user-attachments/assets/8d3151f2-eef5-4d0e-949c-de58c88fb685)|

## How GitKraft Works

Selecting **Private Fork** creates a repository based on a GitKraft template. This template uses a GitHub Workflow to download the chart and its dependencies, merging them into `main`.

During updates, the _GitKraft: Update_ workflow cherry-picks your custom patches on top of the latest release from upstream. It then performs a special merge, known as a _weld merge_, which attaches the patched release to `main` in your private repository.

If all customizations cherry-pick successfully, the workflow completes as follows:

```mermaid
%%{
  init: {
    "gitGraph": {
      "mainBranchName": "GitKraft repo with chart"
    },
    "theme": "neutral",
    "themeVariables": {
      "git0": "#187BCD",
      "git1": "#CC4844",
      "git2": "#888888",
      "gitBranchLabel0": "#FAFAFA",
      "gitBranchLabel1": "#FAFAFA",
      "gitBranchLabel2": "#FAFAFA"
    }
  }
}%%
gitGraph
    branch "your private repo" order: 2
    checkout "GitKraft repo with chart"
    commit id:" " tag:"1.0"
    checkout "your private repo"
    commit id:"Initial commit"
    merge "GitKraft repo with chart" id:"weld merge 1.0" type:HIGHLIGHT
    commit id:"your patch (A)"
    commit id:"your patch (B)"
    checkout "GitKraft repo with chart"
    commit id:"NOT conflicting with (A) or (B)" tag:"1.1"
    branch "update to new version" order: 1
    commit id:"cherry-picked (A)"
    commit id:"cherry-picked (B)"
    checkout "your private repo"
    merge "update to new version" id:"weld merge 1.1" type:HIGHLIGHT
```

If conflicts occur, temporary branches `gitkraft/update` and `gitkraft/main` are created, and a link to a pull request (PR) is generated for conflict resolution. Once the user resolves and merges, the _GitKraft: finalize conflict resolution_ workflow updates `main` using the conflict resolution from `gitkraft/main`. Note that due to GitHub’s conflict resolution process, all patches will combine into one commit (a future improvement may address this). The final `main` branch in your private repo looks like this:

```mermaid
%%{
  init: {
    "gitGraph": {
      "mainBranchName": "GitKraft repo with chart"
    },
    "theme": "neutral",
    "themeVariables": {
      "git0": "#187BCD",
      "git1": "#CC4844",
      "git2": "#888888",
      "gitBranchLabel0": "#FAFAFA",
      "gitBranchLabel1": "#FAFAFA",
      "gitBranchLabel2": "#FAFAFA"
    }
  }
}%%
gitGraph
    branch "your private repo" order: 2
    checkout "GitKraft repo with chart"
    commit id:" " tag:"1.0"
    checkout "your private repo"
    commit id:"Initial commit"
    merge "GitKraft repo with chart" id:"weld merge 1.0" type:HIGHLIGHT
    commit id:"your patch (A)"
    commit id:"your patch (B)"
    checkout "GitKraft repo with chart"
    commit id:"conflicting with (A) or (B)" tag:"2.0"
    branch "update to new version" order: 1
    commit id:"combined (A)+(B)"
    checkout "your private repo"
    merge "update to new version" id:"weld merge 2.0" type:HIGHLIGHT
```

[^Truffle]: [Anyone can Access Deleted and Private Repository Data on GitHub](https://trufflesecurity.com/blog/anyone-can-access-deleted-and-private-repo-data-github), Truffle Security, July 24, 2024
