# For Maintainers
This is the documentation that will be used by the maintainers. If you have any better idea on how to do things better, please let us know by creating an issue or PR 😉.

- [Labels](#labels)
- [Releasing a new version](#releasing-a-new-version)
- [Generating CHANGELOG.md](#generating-changelogmd)

## Labels
We have several labels that we can use to organize the issues and PRs on this repo, and also to make the changelog generation easier. All the labels and their descriptions can be [read here](https://github.com/materializecss/materialize/issues/labels).

But there are important labels that are related to [changelog generation](#generating-changelogmd), which will determine where all the merged PRs end up. Any other PRs that hasn't labeled with one of these will not be shown on the changelog. Please take a look at the table below

| Label         | Section                  |
|---------------|--------------------------|
| enhancement   | Implemented enhancements |
| bug           | Fixed bugs               |
| documentation | Documentation changes    |
| meta          | Meta changes             |

## Releasing a new version
To fully release a new version, you need to have access to the @materializecss organization on the npmjs. Then, please follow the steps below:
1. Run `npm run release -- --oldver=<current_version> --newver=<new_version>`
   > What this command does is that it will replace any occurrences of "<current_version>" with the "<new_version>". So for example, if the current release is `1.0.0`, and then the planned release is `1.1.0`, the command is
   > ```
   > npm run release -- --oldver=1.0.0 --newver=1.1.0
   > ```
2. Verify that version in package.json is correctly replaced
3. [Then generate the CHANGELOG.md](#generating-changelogmd)
4. Create a PR ([example](https://github.com/materializecss/materialize/pull/258) so that we can verify nothing goes wrong, address the feedback from the reviewers if there is any
5. Merge the PR after most (hopefully everyone) happy with the planned release PR
6. Create a new release on GitHub
   * With release notes from the generated CHANGELOG.md
   * Upload the `materialize-v<new_vversion>.zip` from the bin folder
   * Don't forget to create a new tag together with the release
7. Publish the release on npmjs
   * If you never logged in on npm, please do `npm login` first and enter your credentials
   * Then run `npm publish` and follow the instructions there
8. Done! Yay new version 🥳

## Generating CHANGELOG.md
To generate the CHANGELOG.md, we will use [github-changelog-generator](https://github.com/github-changelog-generator/github-changelog-generator) to automatically generate it. The changelog will be generated based on merged PRs. So if you have any changes that need to be mentioned on release notes, please avoid commit it directly to the main branch, and do create a PR for it.

Also it's important that if you want to merge the PR, **please use "merge and commit", do not squash**. Because for some reason the generator can't recognize the squashed PRs.

> I think this is noteworthy to mention it on their repo, maybe also fix it?

To make it easier, you can use Docker to run it. You can use the command below to get started.

```
docker run -it --rm \
  -v "$(pwd)":/usr/local/src/your-app \
  githubchangeloggenerator/github-changelog-generator \
  -u materializecss \
  -p materialize \
  --base dontusehistorypls.md \
  --since-tag 1.0.0 \
  --token <your_GitHub_token> \
  --no-issues \
  --future-release 1.1.0 \
  --bug-labels bug,bugfix \
  --breaking-labels "breaking changes" \
  --add-section '{"docs":{"labels":["documentation"],"prefix":"**Documentation changes:**"},"meta":{"labels":["build","meta"],"prefix":"**Meta changes:**"}}'
```

Btw, the `--token` param is optional. But since GitHub API has rate limitations for unauthenticated requests, I recommend you to use the token. [You can follow the instructions from their repo.](https://github.com/github-changelog-generator/github-changelog-generator#github-token)
