---
title: Agent Changelog
---

# ngrok Agent Changelog

### ngrok Agent 3.1.0 - \[2022-09-14\]

* Started signing Windows executables with a code signing certificate. Includes polish for Windows executable properties like description and icon.
* Updated the root CA recognized by new agent versions. Improves incident recovery and mitigation options.

### ngrok Agent 3.0.7 - \[2022-08-17\]

* Added `--region` flag to the `ngrok diagnose` command. Including support for `--region all` to diagnose all regions.
* Ignore the `--config` flag when passed to a command that does not support it.

### ngrok Agent 3.0.6 - \[2022-06-30\]

* Fixed issues with replay not displaying requests in the ngrok Agent web UI, console UI, and API.
* Fixed null pointer issues with `ngrok diagnose` command.

### ngrok Agent 3.0.5 - \[2022-06-22\]

* Fixed a formatting issue in the inspector web UI for the header navigation.
* Updated the start tunnel API to allow starting labeled tunnels using a list of strings as labels.

### ngrok Agent 3.0.4 - \[2022-05-27\]

* Added the ability to provide messages to users via the agent console.
* `ngrok config edit` will now allow you to edit an invalid configuration file.
* Updated the display of the latency number in the console UI to be more human readable.

### ngrok Agent 3.0.3 - \[2022-04-26\]

* Fixed a bug on Windows where running the agent from explorer did not open a cmd prompt.

### ngrok Agent 3.0.2 - \[2022-04-11\]

* Fixed a bug that was not allowing users to start tunnels via the API.

### ngrok Agent 3.0.1 - \[2022-04-04\]

* Added `ngrok config edit` command to open the config file in your default editor.
* Removed invalid `--proxy-proto` flag from the `ngrok start` command.
* Added a migration to the `ngrok config upgrade` command to convert basic auth config parameters.
* The ngrok agent API will now error if invalid values are passed to it. Previously it ignored them.

### ngrok Agent 3.0.0 - \[2022-03-28\]

For more information about upgrading from previous versions of the agent to v3.0, see our [upgrade guide](/docs/guides/upgrade-v2-v3).

* Fixed an issue where an agent would not reconnect after removing a reserved domain from your account.
* Added `ngrok completion` to enable autocomplete for the cli.
* Added `proxy_proto` option for enabling and specifying the [HAProxy's PROXY protocol version](https://www.haproxy.com/blog/haproxy/proxy-protocol/).
* Added the ability to set the `connect_interface` for the agent.
* Renamed `bind_tls` to `schemes`.
* The agent UI now display ping time.
* Added the `ngrok diagnose` command to the agent for troubleshooting connection issues.
* Added the `ngrok config` command for managing the configuration file.
* Added the ability to call the ngrok API from the agent using `ngrok api`.
* Added the `ngrok service` command to manage the ngrok agent as a service.
* All ngrok agent configuration files must now include a version number to indicate the format.
* Added the ability to start Labeled Tunnels to the agent that work with the new Tunnel Group backend.
* Renamed the "Clear" button to "Clear Requests" in the inspect UI to make it clear as to what it's doing.
* Fixed an issue where running `ngrok authtoken` would make changes to your tunnel definitions.
* The inspect UI will now pretty-print content types ending in +json.
* Fixed an issue where the inspect UI was adding unwanted HTML tags to JSON bodies when replayed.
* The ngrok agent now logs to stdout by default when `console_ui: false`.
* Command line arguments must use single hyphen for single letter options, and double hyphen for longer names.
* The ngrok agent now fails when there are keys in the config file it doesn't understand. Previously it ignored unknown options.
* Using the `--host-header rewrite` option puts the original host header value into the `X-Forwarded-Host` header field instead of the `X-Original-Host` header in an attempt to be more standard.
* Combined `http_proxy` and `socks5_proxy` configuration options into a single option (

### Changes in 2.3

If asked to forward to port 443, ngrok will now automatically forward HTTPS traffic instead of HTTP. This change would only affect you if you previously ran a server accepting unencrypted HTTP on port 443. To workaround this, you may specify an explicit http URL if you need the old behavior: `ngrok http http://localhost:443`.

If run under sudo, the ngrok agent previously consulted the sudo-ing user's home directory file when looking for its default configuration file. It now consults the home directory of the assumed user. To workaround this, you may specify an explicit configuration file location with the `-config` option.

### Changes in 2.2

The ngrok agent API no longer accepts `application/x-www-form-urlencoded` request bodies. In practice, this only affects the `/api/requests/http/:id` endpoint because posting to the `/api/tunnels` endpoint with this type of request body previously caused ngrok to crash.

This change was made to help protect against maliciously crafted web pages that could cause a user to inadvertently interact with their local ngrok API.

### Changes in 2.1

Behavior changes for `http` and `tls` tunnels defined in the configuration file or started via the API that do not have a `subdomain` or `hostname` property.

    tunnels:
      webapp:
      proto: http
      addr: 80

Given this example tunnel configuration, behavior will change in the following ways.

##### Old Behavior

Starts a tunnel using the name of the tunnel as the subdomain resulting in the URL `http://webapp.ngrok.io`

##### New Behavior

Starts a tunnel with a random subdomain, for example a URL like `http://d95211d2.ngrok.io`

##### How to keep the old behavior

Add a `subdomain` property with the same name as the tunnel:

    tunnels:
      webapp:
      proto: http
      addr: 80
      subdomain: webapp

This behavior changed in order to make it possible to launch tunnels with random domains. This was preventing the use of the configuration file and agent API to free tier users.