# Exposing Wings in a Homelab

Let's say you just finished setting up your Calagopus Panel and Wings in your homelab, and you want to access it from the internet. This guide will cover all the ways you can expose your Wings machine to the internet, and the pros and cons of each method.

## Prerequisites

Before we start, make sure you have the following:

- A working Calagopus Panel
- A working Calagopus Wings machine (accessible by the Panel)
- A domain name (optional, but recommended for easier access and SSL certificate generation)

## Methods

:::: tabs
=== Reverse Proxy
The most common way to expose your Wings machine to the internet is by using a reverse proxy. A reverse proxy is a server that sits in front of your Wings machine and forwards requests to it. This is the recommended method for most users, as it allows you to easily manage SSL certificates and provides an additional layer of security.

| Pros | Cons |
| --- | --- |
| Easy to set up with tools like Nginx or Caddy | Requires additional configuration and maintenance |
| Allows for easy SSL certificate management | May introduce additional latency |
| Provides an additional layer of security | Requires a domain name for best results |
| Can be used to expose multiple services on the same domain | May require additional resources on your server |
| | No support for SFTP (but you can still use SFTP by connecting to the Wings machine directly on the local network) |

For a detailed guide on setting up a reverse proxy, please see our [Reverse Proxies documentation](./reverse-proxies.md).

Whats important to note for Wings is how you configure the reverse proxy, since Wings uses both HTTP and WebSocket connections, you need to make sure to properly configure your reverse proxy to support both types of connections. When entering the URL into the panel, you should make sure you enter the Reverse Proxy URL without a Port (well, usually). For example, if your reverse proxy is accessible at `https://wings.example.com`, you should enter `https://wings.example.com` in the panel, and not `https://wings.example.com:8080` or any other port.

=== Reverse Proxy + Wings Proxy Mode
This method is similar to the Reverse Proxy method, but it also involves setting up the Panel to support Wings Proxy Mode. This allows the Panel to proxy HTTP/WS connections going to wings through the panel itself, which can simplify the reverse proxy configuration and also allows you to use the same domain and port for both the panel and wings (e.g. `https://panel.example.com` for both the panel and wings). This method is recommended for users who want to simplify their reverse proxy configuration and are okay with having the panel proxying connections to wings.

| Pros | Cons |
| --- | --- |
| Simplifies reverse proxy configuration | Requires additional configuration in the panel |
| Allows using the same domain and port for both panel and wings | May introduce additional latency due to the panel proxying connections |
| Provides an additional layer of security | Requires a domain name for best results |
| | Limits Bandwidth since all connections to wings are proxied through the panel, so if you have a lot of traffic going to wings, it may put a lot of load on the panel |
| | No support for SFTP (but you can still use SFTP by connecting to the Wings machine directly on the local network) |

For the Reverse Proxy Part, you can refer to the [Reverse Proxies documentation](./reverse-proxies.md) for a detailed guide on setting up a reverse proxy.

### Enabling Wings Proxy Mode

To enable Wings Proxy Mode, you need to edit your panel's `.env` file and set the [`APP_ENABLE_WINGS_PROXY`](../panel/environment#app-enable-wings-proxy) variable to `true`, then restart your panel. After that, you can enter the same URL for both the panel and wings in the panel's node configuration, and the panel will automatically proxy connections to wings.

If you installed the Panel via Docker, that means editing your `compose.yml` file and adding the following environment variable to the `web` service, then running `docker compose up -d` to apply the changes:

```yaml
services:
  web:
    environment:
      - APP_ENABLE_WINGS_PROXY=true
```

### Switching the Node to Proxy Mode

After enabling Wings Proxy Mode in the panel, you need to switch your node to proxy mode as well. To do this, go to your node's configuration page on the panel, and take a look at the "Public URL" field of your node. This field should have a globe button at its right side, click on it and it should automatically fill the field with the correct URL for proxy mode. After that, save the changes and your node should now be using the proxy mode URL.

=== Port Forwarding
Another way to expose your Wings machine to the internet is by using port forwarding. This method involves forwarding the wings port(s) (default is 8080) from your router to your Wings machine. This method is less secure than using a reverse proxy, as it exposes your Wings machine directly to the internet, but it is simpler to set up and does not require a domain name.

| Pros | Cons |
| --- | --- |
| Simple to set up | Exposes your Wings machine directly to the internet |
| Does not require a domain name | More difficult to manage SSL certificates |
| No additional resources required on your server | May introduce security risks if not properly configured |
| | Usually also requires a static IP or Dynamic DNS setup for best results |

For a detailed guide on setting up port forwarding, please refer to your router's documentation, as the steps can vary greatly depending on the make and model of your router.

Whats important to note for Wings is that if you choose to use port forwarding, your Wings URL in the panel should include the port number. For example, if you forwarded port 8080 to your Wings machine, and your public IP is `217.33.3.3`, you should enter `http://217.33.3.3:8080` in the panel. (Or your DynDNS domain if you have one set up, like `http://mywings.dyndns.org:8080`)

::::
