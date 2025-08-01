![](https://i.imgur.com/waxVImv.png)
### [View all Roadmaps](https://github.com/nholuongut/all-roadmaps) &nbsp;&middot;&nbsp; [Best Practices](https://github.com/nholuongut/all-roadmaps/blob/main/public/best-practices/) &nbsp;&middot;&nbsp; [Questions](https://www.linkedin.com/in/nholuong/)
<br/>

[![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&weight=500&size=24&pause=1000&color=F7931E&width=435&lines=Hello%2C+I'm+Nho+Luong🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻🇳🇻)](https://git.io/typing-svg)

# **About Me🇻🇳**
- ✍️ Blogger
- ⚽ Football Player
- ♾️ DevOps Engineer
- ⭐ Open-source Contributor
- 😄 Pronouns: Mr. Nho Luong
- 📚 Lifelong Learner | Always exploring something new
- 📫 How to reach me: luongutnho@hotmail.com

![GitHub Grade](https://img.shields.io/badge/GitHub%20Grade-A%2B-brightgreen?style=for-the-badge&logo=github)
<p align="left"> <img src="https://komarev.com/ghpvc/?username=amanpathak-devops&label=Profile%20views&color=0e75b6&style=flat" alt="amanpathak-devops" /> </p>

# Helm Charts

The canonical source for Helm charts is the [Artifact Hub](https://artifacthub.io), an aggregator for distributed chart repos.

This GitHub project is the source for Helm `stable` and `incubator` [Helm chart repositories](https://v3.helm.sh/docs/topics/chart_repository/). This is now an archive no longer under active development.

For more information about installing and using Helm, see the
[Helm Docs](https://helm.sh/docs/). For a quick introduction to Charts, see the [Chart Guide](https://helm.sh/docs/topics/charts/).

## ⚠️ Deprecation and Archive Notice

Similar to the [Helm 2 Support Plan](https://helm.sh/blog/2019-10-22-helm-2150-released/#helm-2-support-plan), this GitHub project has begun transition to a 1 year "maintenance mode" (see [Deprecation Timeline](#deprecation-timeline) below). Given the deprecation plan, this project is intended for [apiVersion: v1](https://helm.sh/docs/topics/charts/#the-apiversion-field) Charts (installable by both Helm 2 and 3), and not for `apiVersion: v2` charts (installable by Helm 3 only).

### Deprecation Timeline

| | |
| - | - |
| **Nov 13, 2019** | At [Helm 3's public release](https://helm.sh/blog/helm-3-released/), new charts are no longer accepted to `stable` or `incubator`. Patches to existing charts may continue to be submitted by the community, and (time permitting) reviewed by chart OWNERS for acceptance |
| **Aug 13, 2020** | At 9 months – when Helm v2 goes security fix only – the `stable` and `incubator` repos will be de-listed from the Helm Hub. Chart OWNERS are encouraged to accept security fixes only. ℹ️ _Note: the original date has been extended 3 months to match Helm v2 support. See [COVID-19: Extending Helm v2 Bug Fixes](https://helm.sh/blog/covid-19-extending-helm-v2-bug-fixes/)_ |
| **Nov 13, 2020** | At 1 year, support for this project will formally end, at which point the `stable` and `incubator` chart repos will be marked obsolete. At that time these chart repos will likely be garbage collected and no longer available. This git repository will remain as an archive. |

This timeline gives the community (chart OWNERS, organizations, groups or individuals who want to host charts) 9 months to move charts to new Helm repos, and list these new repos on the Helm Hub before `stable` and `incubator` are de-listed.

Note that this project has been under active development for some time, so you might run into [issues](https://github.com/nholuongut/Helmcharts/issues). If you do, please don't be shy about letting us know, or better yet, contribute a fix or feature (within the deprecation timeline of course). Also be aware the repo and chart OWNERS are volunteers so reviews are as time allows, and acceptance is up to the chart OWNERS - you may [reach out](#where-to-find-us) but please be patient and courteous.

## Where to Find Us

For general Helm Chart discussions join the Helm Charts (#charts) room in the [Kubernetes Slack instance](http://slack.kubernetes.io/).

For issues and support for Helm and Charts see [Support Channels](CONTRIBUTING.md#support-channels).

## How Do I Install These Charts?

Just `helm install stable/<chart>`. This is the default repository for Helm v2 which is located at https://charts.helm.sh/stable/ and was installed by default prior to Helm v3.

For more information on using Helm, refer to the [Helm documentation](https://github.com/kubernetes/helm#docs).

## How Do I Enable the Stable Repository for Helm 3?

To add the Helm Stable Charts for your local client, run `helm repo add`:

```
$ helm repo add stable https://charts.helm.sh/stable
"stable" has been added to your repositories
```

## How Do I Enable the Incubator Repository?

To add the Incubator charts for your local client, run `helm repo add`:

```
$ helm repo add incubator https://charts.helm.sh/incubator
"incubator" has been added to your repositories
```

You can then run `helm search incubator` to see the charts.

## Chart Format

Take a look at the [alpine example chart](https://github.com/helm/helm/tree/master/cmd/helm/testdata/testcharts/alpine) for reference when you're writing your first few charts.

Before contributing a Chart, become familiar with the format. Note that the project is still under active development and the format may still evolve a bit.

## Repository Structure

This GitHub repository contains the source for the packaged and versioned charts released using [GitHub pages](https://github.com/nholuongut/Helmcharts/tree/gh-pages/stable) (the Chart Repository).

The Charts in the `stable/` directory in the master branch of this repository match the latest packaged Chart in the Chart Repository, though there may be previous versions of a Chart available in that Chart Repository.

The purpose of this repository is to provide a place for maintaining and contributing official Charts, with CI processes in place for managing the releasing of Charts into the Chart Repository.

The Charts in this repository are organized into two folders:

* stable
* incubator

Stable Charts meet the criteria in the [technical requirements](CONTRIBUTING.md#technical-requirements).

Incubator Charts are those that do not meet these criteria. Having the incubator folder allows charts to be shared and improved on until they are ready to be moved into the stable folder. The charts in the `incubator/` directory can be found on the [`gh-pages` branch of the repository](https://github.com/nholuongut/Helmcharts/tree/gh-pages/incubator).

In order to get a Chart from incubator to stable, Chart maintainers should open a pull request that moves the chart folder.

## Contributing to an Existing Chart

We'd love for you to contribute to an existing Chart that you find provides a useful application or service for Kubernetes. Please read our [Contribution Guide](CONTRIBUTING.md) for more information on how you can contribute Charts.

Note: We use the same [workflow](https://github.com/kubernetes/community/blob/master/contributors/devel/development.md#workflow),
[License](LICENSE) and [Contributor License Agreement](CONTRIBUTING.md) as the main Kubernetes repository.

## Owning and Maintaining A Chart

Individual charts can be maintained by one or more users of GitHub. When someone maintains a chart they have the access to merge changes to that chart. To have merge access to a chart someone needs to:

1. Be listed on the chart, in the `Chart.yaml` file, as a maintainer. If you need sponsors and have contributed to the chart, please reach out to the existing maintainers, or if you are having trouble connecting with them, please reach out to one of the [OWNERS](OWNERS) of the charts repository.
1. Be invited (and accept your invite) as a read-only collaborator on [this repo](https://github.com/nholuongut/Helmcharts). This is required for @k8s-ci-robot [PR comment interaction](https://github.com/kubernetes/community/blob/master/contributors/guide/pull-requests.md).
1. An OWNERS file needs to be added to a chart. That OWNERS file should list the maintainers' GitHub login names for both the reviewers and approvers sections. For an example see the [Drupal chart](stable/drupal/OWNERS). The `OWNERS` file should also be appended to the `.helmignore` file.

Once these three steps are done a chart approver can merge pull requests following the directions in the [REVIEW_GUIDELINES.md](REVIEW_GUIDELINES.md) file.

## Trusted Collaborator

The `pull-charts-e2e` test run, that installs a chart to test it, is required before a pull request can be merged. These tests run automatically for members of the Helm Org and for chart [repository collaborators](https://help.github.com/articles/adding-outside-collaborators-to-repositories-in-your-organization/). For regular contributors who are trusted, in a manner similar to Kubernetes community members, we have trusted collaborators. These individuals can have their tests run automatically as well as mark other pull requests as ok to test by adding a comment of `/ok-to-test` on pull requests.

There are two paths to becoming a trusted collaborator. One only needs follow one of them.

1. If you are a Kubernetes GitHub org member and have your Kubernetes org membership public you can become a trusted collaborator for Helm Charts
2. Get sponsorship from one of the Charts Maintainers listed in the OWNERS file at the root of this repository

The process to get added is:

* File an issue asking to be a trusted collaborator
* A Helm Chart Maintainer can then add the user as a read only collaborator to the repository

## Review Process

For information related to the review procedure used by the Chart repository maintainers, see [Merge approval and release process](CONTRIBUTING.md#merge-approval-and-release-process).

### Stale Pull Requests and Issues

Pull Requests and Issues that have no activity for 30 days automatically become stale. After 30 days of being stale, without activity, they become rotten. Pull Requests and Issues can rot for 30 days and then they are automatically closed. This is the standard stale process handling for all repositories on the Kubernetes GitHub organization.

## Supported Kubernetes Versions

This chart repository supports the latest and previous minor versions of Kubernetes. For example, if the latest minor release of Kubernetes is 1.8 then 1.7 and 1.8 are supported. Charts may still work on previous versions of Kubernertes even though they are outside the target supported window.

To provide that support the API versions of objects should be those that work for both the latest minor release and the previous one.

## Happy Helming in China

If you are in China, there are some problems to use upstream Helm Charts directly (e.g. images hosted on `gcr.io`, `quay.io`, and Charts hosted on `googleapis.com` etc), you can use this mirror repo at https://github.com/cloudnativeapp/charts which automatically sync & replace unavailable image & repo URLs in every Chart.

![](https://i.imgur.com/waxVImv.png)
# I'm are always open to your feedback🚀
# **[Contact Me🇻]**
* [Name: Nho Luong]
* [Telegram](+84983630781)
* [WhatsApp](+84983630781)
* [PayPal.Me](https://www.paypal.com/paypalme/nholuongut)
* [Linkedin](https://www.linkedin.com/in/nholuong/)

![](https://i.imgur.com/waxVImv.png)
![](Donate.jpg)
