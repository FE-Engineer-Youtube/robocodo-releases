# Screenshot capture checklist

No product screenshots are included yet. The [public README](../../README.md) uses Mermaid diagrams until safe captures are available.

Capture these views in the real Windows app using a dedicated, sanitized demo environment:

- [ ] Tasks/project overview showing a neutral demo project and task list.
- [ ] Machine setup page showing the connection fields and preparation controls.
- [ ] Isolated worktree/task view showing a task's workspace and progress.
- [ ] Git change review and publishing view showing a harmless documentation diff.

Before adding any image:

1. Remove usernames, hostnames, IP addresses, local filesystem paths, private repository names, tokens, keys, personal account details, and unrelated desktop content from the visible UI. Include sidebars, terminal output, tooltips, dialogs, title bars, and notifications in the review.
2. Prefer a demo state with neutral labels; crop or fully cover sensitive fields if the real UI must display them. Review the final exported image at full size and remove identifying metadata.
3. Capture actual product behavior. Do not fabricate screens or imply controls exist by drawing them into an image.
4. Use a descriptive filename and concise alt text. Add a relative Markdown image link only after the reviewed image exists in this directory.
5. Recheck that the screenshot matches the public build and reveals no personal or private information before committing.
