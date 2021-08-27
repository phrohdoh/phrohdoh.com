# https://phrohdoh.com

A [`zola`]-based static site deployed to [GitHub Pages] via [GitHub Actions].

Use `fd | entr -c zola serve` to serve the site locally, restarting the server
when any file changes. A build failure normally stops the server, so this will
restart it (presumably upon resolution of build issue(s)).

Given the relevant GitHub Personal Access Token is in the system clipboard, use
`act -s TOKEN=$(pbpaste)` ([`act`]) to locally run the `main` Actions workflow.

[`zola`]: https://www.getzola.org/
[GitHub Pages]: https://pages.github.com/
[GitHub Actions]: https://github.com/features/actions
[`act`]: https://github.com/nektos/act
