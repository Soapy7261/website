# Fusequota

## What is Fusequota?

[Fusequota](https://github.com/calagopus/fusequota) is a tool that allows you to set disk quotas for individual game servers, preventing them from consuming all available disk space and affecting other servers on the same node. It works by creating a virtual filesystem using FUSE (Filesystem in Userspace) that enforces the specified disk quotas for each server. This way, even if a malicious user tries to fill up the disk, they will only be able to consume the amount of space allocated to their server, and other servers on the same node will not be affected.

> This sounds great, how do I use it?

Slow down there! This is not something you should use without knowing the downsides. While Fusequota can be a great tool to prevent disk space abuse, it does come with some downsides. Since FUSE is in User-space, it can have performance implications, especially for disk-intensive games. Additionally, it may not be compatible with all games (though rarely any game should have issues with it), and it can add complexity to your setup. It's important to weigh the benefits against the potential downsides before deciding to use Fusequota in your setup. If you do decide to use it, make sure to thoroughly test it with your specific games and workloads to ensure that it works well for your use case.

## Downsides

- Performance: Since FUSE operates in user-space, it can introduce performance overhead, especially for disk-intensive games. This may lead to increased latency and reduced performance for affected servers. We do try to minimize this by using high write-caches and - for fds that are read-only - the backing fs system to completely bypass the fuse layer for pure-reads, but it is still something to be aware of.
- Compatibility: While Fusequota should work with most games, there may be some compatibility issues with certain games or workloads. It's important to thoroughly test it with your specific games and workloads to ensure that it works well for your use case.
- Complexity: Using Fusequota adds an additional layer of complexity to your setup, which may require additional maintenance and troubleshooting. It's important to be comfortable with managing and troubleshooting FUSE filesystems before implementing Fusequota in your setup.
- Stability: While we have tested Fusequota extensively, there may still be edge cases or bugs that could lead to instability or crashes. It's important to monitor your servers closely after implementing Fusequota and be prepared to troubleshoot any issues that may arise. In general, FUSE is not the most stable technology, and while we have done our best to make it as stable as possible, it is still something to be aware of.
- Not a true quota system: Fusequota is not a true quota system, it is a workaround that allows you to set disk quotas for individual game servers. It may not be as robust or reliable as a true quota system (like the available btrfs, zfs and xfs options), and it may not be suitable for all use cases.

## So, when should I use it?

Well, it highly depends. Do only you and some friends use you panel? Fusequota is not worth the hassle and speed decrease.

Do you have literally any other native quota system available? Then dont use fusequota, the native quota systems are much more efficient and stable than fusequota, and they do not have the same downsides.

Do you host free servers? Then it might be worth it, as you can have a lot of malicious users that try to fill up the disk, and it's better to have a slow server than no server at all.

Do you host high-performance servers? Then it might not be worth it, as the performance decrease might be too much for your users. But it **depends**! Do not make decisions purely on this guide, do your testing and due diligence before making a decision. If you are unsure, you can always ask for help in our [Discord](https://discord.gg/uSM8tvTxBV), we are happy to help you make the best decision for your use case!

## How do I use it?

Using Fusequota is relatively straightforward, it does not require any additional software or similar.

To use Fusequota, simply set the `disk_limiter_mode` option in your wings configuration to `fuse_quota`:

```yaml
system:
  disk_limiter_mode: fuse_quota
```

Then, simply restart wings and fusequota will be used when a server restarts.
