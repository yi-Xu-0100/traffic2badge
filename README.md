## ⚡️ traffic2badge

[![sync2gitee(list)](<https://github.com/yi-Xu-0100/hub-mirror/workflows/sync2gitee(list)/badge.svg>)](https://github.com/yi-Xu-0100/hub-mirror)
[![traffic2badge](https://github.com/yi-Xu-0100/traffic2badge/workflows/traffic2badge/badge.svg)](https://github.com/yi-Xu-0100/traffic2badge/actions?query=workflow%3Atraffic2badge)
[![GitHub last commit](https://img.shields.io/github/last-commit/yi-Xu-0100/traffic2badge)](./)
[![LICENSE](https://img.shields.io/github/license/yi-Xu-0100/traffic2badge)](./LICENSE)

[![GitHub views](https://raw.githubusercontent.com/yi-Xu-0100/traffic2badge/traffic/traffic-traffic2badge/views.svg)](https://github.com/yi-Xu-0100/traffic2badge/tree/traffic)
[![GitHub clones](https://raw.githubusercontent.com/yi-Xu-0100/traffic2badge/traffic/traffic-traffic2badge/clones.svg)](https://github.com/yi-Xu-0100/traffic-to-badge/tree/traffic)

Usage template repository of [traffic-to-badge GitHub Action](https://github.com/marketplace/actions/traffic-to-badge), you can use repositories `Insights/traffic` data to generate badges that include views and clones.

[**English**](./README.md) | [简体中文](./README_CN.md)

## 🎨 Table of contents

- [⚡️ traffic2badge](#️-traffic2badge)
- [🎨 Table of contents](#-table-of-contents)
- [🚀 Configuration](#-configuration)
  - [📝 Usage for `static_list`](#-usage-for-static_list)
- [🎉 Thanks](#-thanks)
- [📄 LICENSE](#-license)

## 🚀 Configuration

There are **only one input must be set up** and four inputs can be set to default without config. The default setting only supports running repository, and if repositories support is required, please refer to [Usage for `static_list`](#-usage-for-static_list).

|     input      |                            description                             | required |      default      |
| :------------: | :----------------------------------------------------------------: | :------: | :---------------: |
|    my_token    | Personal access token to obtain the secret repository traffic data |    ✔     |                   |
|  static_list   |             List of repositories which want to be got              |    ✖     | github.repository |
| traffic_branch |                Branch used to back up traffic data                 |    ✖     |      traffic      |
|  views_color   |             Color value for the views badge background             |    ✖     |    brightgreen    |
|  clones_color  |            Color value for the clones badge background             |    ✖     |    brightgreen    |
|      logo      |            logo or simple-icon to the left of the label            |    ✖     |      github       |

### 📝 Usage for `static_list`

**[`repo-list-generator`](https://github.com/marketplace/actions/repo-list-generator): Default output `repoList` do not include `private` and `fork` repositories.**

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
        uses: yi-Xu-0100/traffic-to-badge@v1.1.4
        with:
          my_token: ${{ secrets.TRAFFIC_TOKEN }}
          static_list: '${{ steps.repo.outputs.repoList }}'
          #(default) traffic_branch: traffic
          #(default) views_color: brightgreen
          #(default) clones_color: brightgreen
          #(default) logo: github

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3.7.3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_branch: ${{ steps.traffic.outputs.traffic_branch }}
          publish_dir: ${{ steps.traffic.outputs.traffic_path }}
          user_name: 'github-actions[bot]'
          user_email: 'github-actions[bot]@users.noreply.github.com'
          full_commit_message: ${{ steps.message.outputs.result }}

      - name: Show traffic data
        run: |
          cd ${{ steps.traffic.outputs.traffic_path }}
          ls -a
```

**Explanation:**

1. The [`repo-list-generator`](https://github.com/marketplace/actions/repo-list-generator) generates `repoList` for `static_list` usage.
2. THe [`github-script`](https://github.com/marketplace/actions/github-script) generates message for commit.
3. The [`actions-gh-pages`](https://github.com/marketplace/actions/github-pages-action) publish traffic data to `traffic_branch`.
4. THe `TRAFFIC_TOKEN` needs to be generated, the guild in [Generate `my_token`](https://github.com/yi-Xu-0100/traffic-to-badge#-generate-my_token) .
5. The `GITHUB_TOKEN` does not need to be generated，only reference it in your workflow file，and the guide in [Using the `GITHUB_TOKEN` in a workflow](https://docs.github.com/en/free-pro-team@latest/actions/reference/authentication-in-a-workflow#using-the-github_token-in-a-workflow) .

## 🎉 Thanks

- [sangonzal/repository-traffic-action](https://github.com/sangonzal/repository-traffic-action)
- [yi-Xu-0100/traffic-to-badge](https://github.com/yi-Xu-0100/traffic-to-badge)
- [actions/checkout](https://github.com/actions/checkout)
- [actions/github-script](https://github.com/actions/github-script)
- [yi-Xu-0100/repo-list-generator](https://github.com/yi-Xu-0100/repo-list-generator)
- [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)

## 📄 LICENSE

- [MIT](./LICENSE)
