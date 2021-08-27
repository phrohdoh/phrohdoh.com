# https://phrohdoh.com

A [`zola`]-based static website deployed to [GitHub Pages] via [GitHub Actions].

Use `fd | entr -c zola serve` to serve the site locally, restarting the server
when any file changes. A build failure normally stops the server, so this will
restart it (presumably upon resolution of build issue(s)).

Given the relevant GitHub Personal Access Token is in the system clipboard, use
`act -s TOKEN=$(pbpaste)` to run the [`main` Actions workflow] locally ( without
having to push to GitHub).

- [`fd`] - _find entries in your filesystem_
- [`entr`] - _run a command when a file changes_
- [`act`] - _run an Actions workflow_

[`zola`]: https://www.getzola.org/
[`fd`]: https://github.com/sharkdp/fd
[`entr`]: https://github.com/eradman/entr
[GitHub Pages]: https://pages.github.com/
[GitHub Actions]: https://github.com/features/actions
[`act`]: https://github.com/nektos/act
[`main` Actions workflow]: ./.github/workflows/main.yml
