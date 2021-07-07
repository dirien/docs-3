# Cloud Native Tunnel

You can use inlets to connect HTTP and TCP services between networks securely. Through an encrypted websocket, inlets can penetrate firewalls, NAT, captive portals, and other restrictive networks lowering the barrier to entry.

VPNs traditionally require up-front configuration like subnet assignment and ports to be opened in firewalls. A tunnel with inlets can provide an easy-to-use, low-maintenance alternative to VPNs and other site-to-site networking solutions. 

You can run inlets as a stand-alone binary, in Docker, integrated into [Kubernetes](https://kubernetes.io) for [Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/), or with cloud APIs. All services can be kept private in the target network, or exposed publicly.

> Inlets and [inlets PRO](https://inlets.dev/) are both available for MacOS, Linux (including ARM), Windows, FreeBSD, and as Docker containers.

## Use-cases

You can use inlets as a stand-alone networking tool, or integrate it into a platform to extend functionality for your users.

### For companies - hybrid cloud, multi-cluster and partner access

* Low-maintenance, secure, and quick alternative to a VPN
* To build a hybrid cloud between existing servers and public cloud for: CI, e2e testing, or for accessing legacy databases and IT systems
* To migrate on-premises databases and APIs to public cloud
* To connect to the private environments of your customers in a SaaS product
* For command and control of: services within private VPCs, IoT devices, and Point of Sale (PoS)
* Exposing private API endpoints to third-parties and partners
* As a cheaper, easier alternative to a data-center uplink or managed product like [AWS Direct Connect](https://aws.amazon.com/directconnect/) or [Azure Express Route](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction)

### For teams and individuals - for public tunnels and Kubernetes connectivity

* As a zero-touch VPN - penetrating NAT, firewalls, captive portals and hotel WiFi
* When integrating with API that use webhooks such as Stripe, PayPal or GitHub, you can test live with your local machine
* To get a public IP address for your IngressController or services on a Kubernetes cluster
* To access your homelab, Owncloud instance, FreeNAS, or Raspberry Pi cluster using SSH and HTTPS
* As a freelancer, you can share a blog or website with a client or with your team
* As an alternative to SaaS and or proprietary offerings such as [Ngrok](https://ngrok.io) and [Argo Tunnel](https://www.cloudflare.com/en-gb/products/argo-tunnel/) where you can use your own DNS, and decide your own rate-limits

## Concept

[Inlets PRO](https://inlets.dev/) provides HTTPS and TCP tunnels with built-in TLS encryption.

You can start a HTTPS or TCP tunnel:

* TCP (L4) tunnels can be used to connect TCP services such as a database, a reverse proxy, RDP, Kubernetes or SSH to the Internet. A single tunnel can expose multiple ports on an exit-server
* HTTPS (L7) tunnels can be used to connect one or more HTTP endpoints from one network to another. A single tunnel can expose multiple websites or hosts, including LoadBalancing and multiple clients to one server.

inlets PRO has secure defaults and includes additional documentation, examples, and integrations to make it easier to use. It also has a commercial license and support available.

In the diagram we can see a developer has exposed a Node.js website on his or her laptop through the use of inlets and a server that has a public IPv4 address.

![Conceptual diagram for inlets](images/conceptual.png)

The remote server is called an "exit-node" or "exit-server" because that is where traffic from the private network appears. The user's laptop has gained a "VirtualIP" and users on the Internet can now connect to it using that IP.

## Exit-servers

An exit-server is a host that runs the `inlets-pro server` command to expose its control-plane (websocket) to tunnel clients. The tunnel client can then connect to the exit-server and make services within its local network available remotely.

Services are tunnelled through the exit-server, however unlike SaaS products such as Ngrok and Argo Tunnels, they do not need to be exposed on the public Internet. 

Exit servers can be set up manually, or you can use tooling like [Terraform](https://www.terraform.io), bash, cloud-init or additional inlets community projects:

* [inletsctl](https://github.com/inlets/inletsctl)  - create individual exit-servers
* [inlets-operator](https://github.com/inlets/inlets-operator) - get an exit-server for each LoadBalancer service in your Kubernetes cluster
* [inlets PRO helm charts](https://github.com/inlets/inlets-pro/tree/master/chart) - deploy one or more inlets PRO clients or servers via helm

> These share the same provisioning code and create exit-servers on a range of cloud platforms like: DigitalOcean, Packet, Scaleway, Hetzner, AWS EC2, Azure, and GCP.

## Reference documentation

### inletsctl reference documentation

* [inletsctl documentation](/tools/inletsctl?id=inletsctl-reference-documentation)

### inlets-operator reference documentation

* [inlets-operator documentation](/tools/inlets-operator?id=inlets-operator-reference-documentation)

### inlets PRO reference documentation

* [inlets PRO CLI reference guide](https://github.com/inlets/inlets-pro/blob/master/docs/cli-reference.md)

## Tutorials

### Getting started with your free trial

We created a page to help you get started once you have your free trial license:

* [Get started with your free trial](/get-started/free-trial)

### inlets PRO

* [Quick-start: Tunnel a private SSH server over inlets PRO](/get-started/quickstart-tcp-ssh)
* [Expose a local HTTP server with a Let's Encrypt certificate](/get-started/quickstart-http)
* [Quick-start: Tunnel a private database over inlets PRO](/get-started/quickstart-tcp-database)

* [Quick-start: Expose one or more websites with HTTPS using Caddy](/get-started/quickstart-caddy)
* [Expose Apache Cassandra running on your local machine, out to another network with TLS encryption](https://github.com/inlets/inlets-pro/blob/master/docs/cassandra-tutorial.md)
* [Expose your private Grafana devops dashboards with Caddy and TLS](https://blog.alexellis.io/expose-grafana-dashboards/)
* [Case-study: The Simple Way To Connect Existing Apps to Public Cloud](https://inlets.dev/blog/2021/04/07/simple-hybrid-cloud.html)

### inlets PRO with Kubernetes

* [Case-study: Reliable local port-forwarding from Kubernetes](https://inlets.dev/blog/2021/04/13/local-port-forwarding-kubernetes.html)
* [Quick-start: Expose Your IngressController and get TLS from LetsEncrypt and cert-manager](/get-started/quickstart-ingresscontroller-cert-manager?id=expose-your-ingresscontroller-and-get-tls-from-letsencrypt)
* [Expose your local OpenFaaS functions to the Internet](https://inlets.dev/blog/2020/10/15/openfaas-public-endpoints.html)
* [Get kubectl access to your private cluster from anywhere](https://blog.alexellis.io/get-private-kubectl-access-anywhere/)
* [Get a private Docker registry with auth and TLS](https://blog.alexellis.io/get-a-tls-enabled-docker-registry-in-5-minutes/)
* [Quick-start: Expose a Pod from your Kubernetes cluster with KinD](/get-started/quickstart-k8s-pod)

### inlets PRO Community blog posts

* [Inlets Pro Homelab Awesomeness by Matt Brewster](https://blog.brewsterops.dev/post/inlets-pro-homelab/)
* [Save Money by Connecting Your Local Database to the Public Cloud by Burton Rheutan](https://medium.com/@burtonr/local-database-for-the-cloud-with-inlets-pro-ac0488cc54e0)
* [Exploring NAT Traversal and Tunnels with Inlets and Inlets Pro by Alistair Hey](https://blog.heyal.co.uk/inlets-pro/)

### inlets OSS examples
* [Advanced Cloud Patterns with inlets and inlets-cloud](https://inlets.dev/blog/2020/10/08/advanced-cloud-patterns.html)
* [Video: Get public endpoints in seconds with inlets and create-react-app](https://www.youtube.com/watch?v=jrAqqe8N3q4&feature=youtu.be)

## Pricing

### inlets PRO

Buy an inlets PRO license for personal or commercial use:

* [inlets PRO pricing](https://inlets.dev/pricing)

### inlets OSS

Perhaps the open source version of inlets is for you, feel free to reach out to us at [sales@openfaas.com](mailto:sales@openfaas.com) for a quote on commercial support, training, and custom solutions for any of the inlets projects.

Do you need to connect many clients to your inlets servers? Ask us about inlets-cloud, which can support thousands of tunnels and clients using inlets OSS or inlets PRO. inlets-cloud has its own REST API, metrics, authorization and CLI and is ideal for companies.

Alternatively, if you're a home user or a small company using inlets OSS, then you can support the project [via GitHub Sponsors](https://github.com/sponsors/inlets).

## Connect with the community

Follow [@inletsdev](https://twitter.com/inletsdev) on Twitter.

Join the `#inlets` channel on [OpenFaaS Slack](https://slack.openfaas.io/)

[Contribute to this documentation on GitHub](https://github.com/inlets/docs/)

### Featured on

inlets is proud to be featured on the Cloud Native Landscape in the Service Proxy category.

<p><a href="https://landscape.cncf.io"><img width="200px" src="/images/cncf-landscape-left-logo.svg" alt="Cloud Native Landscape"></a></p>
