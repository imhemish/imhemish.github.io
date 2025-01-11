+++
title = "Mitmproxy"
date = 2022-09-20
[taxonomies]
tags=['linux', 'network']
+++

Today, I came across this wonderful tool called [mitmproxy](https://mitmproxy.org/) which is a TLS capable HTTP and HTTPS proxy and can be used by pen-testers and enthusiasts.
<!-- more -->

Their documentation provides an easy way to install CA trust certificates on each operating system so that browser or any application does not show security errors making it unable to connect to required site.

It provides three tools in single package:

* mitmdump: This is a CLI tool which can be used to dump http(s) requests. It is like tcpdump tool, but just for HTTP and capable of decoding TLS requests.

* mitmproxy: This is a TUI interface which provides convenient way of monitoring the requests.

* mitmweb: is a web interface (the interface is rendered in your browser) which provides a chromium-devtools-like interface for monitoring and editing requests.

Mitmweb is most convenient as it provides a GUI interface. Though, BurpSuite may be beneficial for advance usecases; but for a random hobbyist, mitmproxy is enough for just intercepting requests.
