# Nexus Repository Manager setup

## **Overview of Nexus Repository Manager**

{% hint style="info" %}
**Feature availability**\
This feature is available with Enterprise plans. See [pricing plans](https://snyk.io/plans/) for more details.
{% endhint %}

Connecting Nexus Repository Manager enables Snyk to resolve all direct and transitive dependencies of packages hosted on the Nexus registry and calculate a more complete, accurate dependency graph and related vulnerabilities.

{% hint style="info" %}
**Supported projects**

* The integration currently supports [Node.js](../../products/snyk-open-source/language-and-package-manager-support/snyk-for-javascript/) (npm and Yarn) and [Maven](../../products/snyk-open-source/language-and-package-manager-support/snyk-for-java-gradle-maven.md) projects.
* Gradle projects are not currently supported.
{% endhint %}

You can configure these types of Nexus Repository Manager:

* Publicly accessible instances protected by basic authentication
* Instances on a private network accessed via a [Snyk Broker](../snyk-broker/broker-introduction.md) (with or without basic authentication).

{% hint style="info" %}
**Versions supported**

* Nexus Repository Manager version 3.x is fully supported.
* Nexus Repository Manager version 2.15+ is in Beta
{% endhint %}

## Getting started

1. Go to settings <img src="../../.gitbook/assets/cog_icon.png" alt="Settings icon" data-size="line"> > **Integrations > Package Repositories > Nexus**
2. Verify that you see the screen to configure Nexus.

<figure><img src="../../.gitbook/assets/Screenshot 2022-07-15 at 15.15.11.png" alt="Configure Nexus"><figcaption><p>Configure Nexus</p></figcaption></figure>

{% hint style="info" %}
If you do not see the **Snyk Broker** switch, you do not have the necessary permissions and can only add a publicly accessible instance.\
[Contact Snyk Support](https://support.snyk.io/hc/en-us/requests/new) if you want to add a private registry.
{% endhint %}

## Set up publicly accessible instances

{% tabs %}
{% tab title="Nexus 3" %}
* Enter the URL of your Nexus instance;  this **must** end with `/repository`
* Enter Username
* Enter Password
* Click **Save**
{% endtab %}

{% tab title="Nexus 2" %}
* Enter URL of your Nexus instance, this **must** end with `/nexus/content`
* Enter Username
* Enter Password
* Click **Save**
{% endtab %}
{% endtabs %}

## Nexus behind reverse proxy

If your Nexus server is running behind a reverse proxy, for example, Nginx, the URL might not end with the default `/repository` for Nexus 3 or `/nexus/content` for Nexus 2, depending on what routes have been configured in the reverse proxy. If this is the case, make sure to use the URL configured in the reverse proxy.

Example: for Nexus 3: if `http://nexus.company.io/repository` is mapped to `http://nexus.company.io/my-company/my-repository`,  use `http://nexus.company.io/my-company/my-repository`.

Example: for Nexus 2: if `http://nexus.company.io/nexus/content` is mapped to `http://nexus.company.io/my-nexus-content`, use `http://nexus.company.io/my-nexus-content`.

{% hint style="success" %}
A green success message appears if Snyk can contact your repository.

If you see a yellow warning message, check your URL and credentials and try again.
{% endhint %}

## Set up brokered instances

<figure><img src="../../.gitbook/assets/Screenshot 2022-07-15 at 15.22.58.png" alt="Set up Nexus with Snyk Broker"><figcaption><p>Set up Nexus with Snyk Broker</p></figcaption></figure>

1. Toggle **Snyk Broker on/off** switch to open a form for generating a.. Snyk Broker token.
2. Click on **Generate and Save** button.
3. Copy the token that was generated for you; it is needed to set up a new Broker Client.
4. Set up a new Broker Client in your prod environment:

{% tabs %}
{% tab title="Nexus 3" %}
*   Pull Broker Artifactory image from Dockerhub:

    ```
    docker pull snyk/broker:nexus
    ```
*   Run docker image and provide [broker variables](nexus-repo-manager-setup.md#broker-variables):

    ```
    docker run --restart=always \
      -p 7341:7341 \
      -e BROKER_TOKEN=secret-broker-token \
      -e BASE_NEXUS_URL=https://[username:password]@acme.com \
      -e RES_BODY_URL_SUB=https://acme.com/repository \
      -e BROKER_CLIENT_VALIDATION_URL=https://[username:password]@acme.com/service/rest/v1/status[/check] / 
      snyk/broker:nexus
    ```
{% endtab %}

{% tab title="Nexus 2" %}
*   Pull Broker Artifactory image from Dockerhub:

    ```
    docker pull snyk/broker:nexus2
    ```
*   Run docker image and provide [broker variables](nexus-repo-manager-setup.md#broker-variables)

    ```
    docker run --restart=always \
      -p 7341:7341 \
      -e BROKER_TOKEN=<secret-broker-token> \
      -e BASE_NEXUS_URL=https://[username:password]@acme.com \
      -e RES_BODY_URL_SUB=https://acme.com/nexus/content/(groups|repositories) \ 
      snyk/broker:nexus2
    ```
{% endtab %}
{% endtabs %}

### Checking connection with Broker

Check connection status by making a request to your Nexus broker client `/systemcheck` endpoint.

Example: `curl http://172.17.0.2:7341/systemcheck`

You see success output in the following form:

`{"brokerClientValidationUrl":"https://acme.com/service/rest/v1/status","brokerClientValidationMethod":"GET","brokerClientValidationTimeoutMs":5000,"brokerClientValidationUrlStatusCode":200,"ok":true}`

Or failure output in the following form:

`{"brokerClientValidationUrl":"https://acme.com/service/rest/v1/status","brokerClientValidationMethod":"GET","brokerClientValidationTimeoutMs":5000,"ok":false,"error":"ETIMEDOUT"}`

### Broker variables

{% tabs %}
{% tab title="Nexus 3" %}
| Variable                       | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `BROKER_TOKEN`                 | The token generated in settings <img src="../../.gitbook/assets/cog_icon.png" alt="Settings icon" data-size="line">**> Integrations > Nexus**                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `BASE_NEXUS_URL`               | <p>The URL to your Nexus instance in the format:<br><code>BASE_NEXUS_URL=https://[username_or_token:password_or_token]@acme.com</code></p><p>Must not end with a forward slash.</p><p><strong>Optional field</strong></p><p><em>Auth</em>: Omit if no auth required.<br>Can either be plain text or a two-part token (Nexus Pro)<br>URL encode both username, password and tokens to avoid errors that may prevent authentication.</p><p><strong>Minimal example</strong><br><code>acme.com</code></p><p><br><strong>Complex example</strong><br><code>https://alice:mypassword@acme.com</code></p> |
| `RES_BODY_URL_SUB`             | <p>The URL of the Nexus instance, including <code>https://</code> and <code>/repository</code> and without basic auth credentials.</p><p><strong>Required for npm/Yarn integrations only.</strong></p><p>Must not end with a forward slash.</p><p><br><strong>Example:</strong><br><code>https://acme.com/repository</code></p>                                                                                                                                                                                                                                                                     |
| `BROKER_CLIENT_VALIDATION_URL` | <p>Either be one of:</p><ul><li><code>$BASE_NEXUS_URL/service/rest/v1/status/check</code> (if your Nexus user requires authentication)</li><li><code>$BASE_NEXUS_URL/service/rest/v1/status</code> (if your Nexus user requires NO authentication)</li></ul><p><strong>Example:</strong></p><ul><li><code>https://username:password@acme.com/service/rest/v1/status/check</code></li><li><code>https://acme.com/service/rest/v1/status</code></li></ul>                                                                                                                                             |
{% endtab %}

{% tab title="Nexus 2" %}
| Variable           | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `BROKER_TOKEN`     | The token generated in settings <img src="../../.gitbook/assets/cog_icon.png" alt="Settings icon" data-size="line">**> Integrations > Nexus**                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `BASE_NEXUS_URL`   | <p>format:<br><code>BASE_NEXUS_URL=https://[username_or_token:password_or_token]@acme.com</code></p><p><em>Must not end with a forward slash.</em></p><p><strong>Optional field</strong></p><p><em>Auth</em>: Omit if no auth required.<br>Can either be plain text or a two-part token (Nexus Pro)<br>URL encode both username, password and tokens to avoid errors that may prevent authentication.</p><p><strong>Minimal example:</strong><br><code>https://acme.com</code></p><p><br><strong>Complex example:</strong><br><code>https://alice:mypassword@acme.com:</code></p> |
| `RES_BODY_URL_SUB` | <p>The URL of the Nexus instance, including <code>https://</code> and <code>/nexus/content</code> and without basic auth credentials.</p><p><strong>Required for npm/Yarn integrations only.</strong></p><p>Must not end with a forward slash.<br><br><strong>Example:</strong><br><code>https://acme.com/nexus/content/groups</code><br><br><code>https://acme.com/nexus/content/repositories</code></p>                                                                                                                                                                         |
{% endtab %}
{% endtabs %}

## Nexus user permissions

The Nexus user needs the following privileges, either as part of Role or added individually:

{% tabs %}
{% tab title="Nexus 3" %}
* `nx-metrics-all` (for the [system status check endpoint](https://support.sonatype.com/hc/en-us/articles/226254487-System-Status-and-Metrics-REST-API))
* `nx-repository-view-[*-* | <ecosystem-repo-name>]-read`
* `nx-repository-view-[*-* | <ecosystem-repo-name>]-browse`
{% endtab %}

{% tab title="Nexus 2" %}
* `Status - Read`
* `All [<ecosystem>] Repositories - (read)`
* `[All Repositories | <repoName>] - (view)`
{% endtab %}
{% endtabs %}
