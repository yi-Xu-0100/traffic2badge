## traffic2badge

[![sync2gitee(list)](<https://github.com/yi-Xu-0100/hub-mirror/workflows/sync2gitee(list)/badge.svg>)](https://github.com/yi-Xu-0100/hub-mirror)
[![traffic2badge](https://github.com/yi-Xu-0100/traffic2badge/workflows/traffic2badge/badge.svg)](https://github.com/yi-Xu-0100/traffic2badge/actions?query=workflow%3Atraffic2badge)
[![sync2gitee(list)](<https://github.com/yi-Xu-0100/traffic2badge/workflows/sync2gitee(cached)/badge.svg>)](./.github/workflows/sync2gitee.cached.yml)
[![GitHub last commit](https://img.shields.io/github/last-commit/yi-Xu-0100/traffic2badge)](./)
[![LICENSE](https://img.shields.io/github/license/yi-Xu-0100/traffic2badge)](./LICENSE)

[![GitHub views](https://raw.githubusercontent.com/yi-Xu-0100/traffic2badge/traffic/traffic-traffic2badge/views.svg)](https://github.com/yi-Xu-0100/traffic2badge/tree/traffic)
[![GitHub clones](https://raw.githubusercontent.com/yi-Xu-0100/traffic2badge/traffic/traffic-traffic2badge/clones.svg)](https://github.com/yi-Xu-0100/traffic-to-badge/tree/traffic)

Template usage repository of [GitHub Action traffic-to-badge](https://github.com/marketplace/actions/traffic-to-badge), you can use repositories `Insights/traffic` data to generate badges that include views and clones.

## Table of Contents

- [traffic2badge](#traffic2badge)
- [Table of Contents](#table-of-contents)
  - [usage for repo list](#usage-for-repo-list)
- [CHANGELOG](#changelog)
- [Thanks](#thanks)
- [LICENSE](#license)

### usage for repo list

**Default repository list do not include private and fork repositories.**

```yaml
name: traffic2badge
on:
  push:
    branches:
      - main
  schedule:
    - cron: '1 0 * * *'

jobs:
  run:
    name: Make GitHub Traffic to Badge
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2.3.3

      - name: Get Repo List
        id: repo
        uses: yi-Xu-0100/repo-list-generator@v0.3.0

      - name: Get Commit Message
        id: message
        uses: actions/github-script@v3.0.0
        env:
          FULL_COMMIT_MESSAGE: '${{ github.event.head_commit.message }}'
        with:
          result-encoding: string
          script: |
            var message = `${process.env.FULL_COMMIT_MESSAGE}`;
            core.info(message);
            if (message != '') return message;
            var time = new Date(Date.now()).toISOString();
            core.info(time);
            return `Get traffic data at ${time}`;

      - name: Set traffic
        id: traffic
        uses: yi-Xu-0100/traffic-to-badge@v1.1.0
        with:
          my_token: ${{ secrets.TRAFFIC_TOKEN }}
          static_list: '${{ steps.repo.outputs.repoList }}'
          traffic_branch: traffic
          views_color: brightgreen
          clones_color: brightgreen
          logo: github

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3.7.3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_branch: traffic
          publish_dir: ${{ steps.traffic.outputs.traffic_path }}
          user_name: 'github-actions[bot]'
          user_email: 'github-actions[bot]@users.noreply.github.com'
          full_commit_message: ${{ steps.message.outputs.result }}

      - name: Show traffic data
        run: |
          cd ${{ steps.traffic.outputs.traffic_path }}
          ls -a
```

## CHANGELOG

-[CHANGELOG](./CHANGELOG_CN.md)

## Thanks

- [sangonzal/repository-traffic-action](https://github.com/sangonzal/repository-traffic-action)
- [yi-Xu-0100/traffic-to-badge](https://github.com/yi-Xu-0100/traffic-to-badge)
- [actions/checkout](https://github.com/actions/checkout)
- [actions/github-script](https://github.com/actions/github-script)
- [yi-Xu-0100/repo-list-generator](https://github.com/yi-Xu-0100/repo-list-generator)
- [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)

## LICENSE

[MIT](./LICENSE)
