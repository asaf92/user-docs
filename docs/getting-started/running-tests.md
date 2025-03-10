# Running tests

## Introduction

You can use Snyk to test your code in different ways:

* [Run tests manually](running-tests.md#run-tests-manually): using the Snyk CLI, the Snyk Web UI, and the Snyk API.
* [Run tests automatically](running-tests.md#run-tests-automatically): after Project import, using the `snyk monitor` CLI command, or using PR Checks to scan new PRs.

{% hint style="info" %}
Tests may be limited on your account; see [What counts as a test?](https://support.snyk.io/hc/en-us/articles/360000925418-What-counts-as-a-test-) for more information.
{% endhint %}

### Run tests manually

#### Run tests with the CLI

With the Snyk [CLI](../snyk-cli/cli-reference.md) you can use the following commands:

* Scan open-source code with `snyk test`.
* Scan application code with `snyk code test`.
* Scan container images with `snyk container test`.
* Scan Infrastructure as Code (IaC) files with `snyk iac test`.

See [Getting started with the CLI](../snyk-cli/getting-started-with-the-cli.md) for details.

#### Run tests with the Snyk Web UI

A test is run when you import a Snyk Project (see [Import a Project](quickstart/import-a-project.md)), or click the **Retest now** button on a Project in the Overview tab.

See [Exploring the Snyk Web UI](../snyk-web-ui/getting-started-with-the-snyk-web-ui.md) for details.

#### Run tests with the API

Tests are counted when calls are made to the [**https://snyk.io/api/v1/test**](https://snyk.io/api/v1/test) endpoint.

See [API documentation](https://snyk.docs.apiary.io) for details.

### Run tests automatically

#### After Project import

After you [import a Project](quickstart/import-a-project.md), Snyk automatically runs periodic scans on that Project, to see if your code is affected by newly disclosed vulnerabilities.

{% hint style="info" %}
Test frequency is set to daily by default. To change frequency, go to either the **Usage** page (see [Usage page details](../snyk-admin/managing-settings/usage-page-details.md)) or the project **Settings** page (see [View project settings](../manage-issues/introduction-to-snyk-projects/view-project-settings.md)).
{% endhint %}

#### Use Snyk monitor

Use the `snyk monitor` CLI command to create a snapshot of a project on the Snyk website that will be continuously monitored for new vulnerabilities.

See [Monitor your projects at regular intervals](../snyk-cli/test-for-vulnerabilities/monitor-your-projects-at-regular-intervals.md) for details.

#### Use PR Checks

Snyk can scan every new Pull Request (PR) submitted on your monitored repositories, to help prevent new vulnerabilities from being added to your codebase.

See [Run PR Checks](../scan-application-code/run-pr-checks/) for details.
