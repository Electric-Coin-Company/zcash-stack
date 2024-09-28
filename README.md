# Zcash-stack Helm Chart - Plug and Play Zcash Ecosystem Deployment

This Helm chart is designed to be a **plug and play solution** for deploying the complete Zcash ecosystem on Kubernetes. With minimal configuration, you can easily deploy `zcashd`, `zebra`, `lightwalletd`, `caddy`, and `dnsseeder` to run a fully functional Zcash infrastructure.

### Caddy as a Frontend

By default, this chart uses **Caddy** as a web frontend for `lightwalletd`, making it simple to expose the `lightwalletd` service securely over HTTPS. Caddy automatically manages SSL certificates and provides a modern, user-friendly configuration for serving HTTP(S) traffic. This setup allows you to quickly expose `lightwalletd` to external clients, such as mobile wallets, without worrying about complex web server configurations.

### Customization Options

Although the default configuration is ready to deploy and run, this Helm chart is highly customizable. You can easily adapt it to fit your specific infrastructure needs:

- **Ingress or Internal Deployment**: If you prefer not to use `Caddy` or want to integrate the deployment with an existing ingress controller (like NGINX, Traefik, etc.), you can disable Caddy and configure your own ingress to expose `lightwalletd` or other services. This makes the chart suitable for use in internal networks or environments where `Caddy` is not needed.
  
- **Internal Infrastructure**: For deployments that don’t require public exposure (e.g., running on internal networks or for development purposes), you can modify the chart to adjust how services are exposed, allowing tighter integration with internal load balancers or private networking configurations.

- **Custom Images and Resources**: All Docker images, resource limits, volume sizes, and other Kubernetes objects are fully customizable. You can override any value in the `values.yaml` file or through the `--set` flags in Helm, making it easy to adapt the deployment to your exact specifications.

In summary, this Helm chart provides a turnkey solution to deploy the Zcash ecosystem quickly and securely. However, it is also flexible enough to be adapted for more advanced use cases, whether for public-facing deployments or internal infrastructures.

## Components Overview

This Helm chart includes several components that work together to create a complete Zcash infrastructure setup. Below is a brief explanation of each component, along with links to their respective GitHub repositories for more information.

### Zcashd
`zcashd` is the core Zcash full node implementation, responsible for maintaining the Zcash blockchain, validating transactions, and participating in the Zcash network. It allows you to operate a Zcash node, enabling features such as transaction relaying and blockchain synchronization. It is highly configurable, allowing deployment in various environments, including testnet and mainnet modes.

- GitHub: [Zcashd Repository](https://github.com/zcash/zcash)

### Zebra
`zebra` is an alternative Zcash full node implementation developed by the Zcash Foundation. It is written in Rust and focuses on security, performance, and modularity. Like `zcashd`, it can operate as a full node, validating blocks and transactions in the Zcash network. Zebra is intended to provide diversity in Zcash node software, making the network more robust and resilient.

- GitHub: [Zebra Repository](https://github.com/ZcashFoundation/zebra)

### Lightwalletd
`lightwalletd` is a lightweight server that acts as a proxy between Zcash light clients (such as mobile wallets) and full Zcash nodes like `zcashd` or `zebra`. It enables light clients to interact with the Zcash blockchain without requiring a full node locally. Lightwalletd processes client requests, such as syncing balances and sending transactions, by forwarding them to a connected full node.

- GitHub: [Lightwalletd Repository](https://github.com/zcash/lightwalletd)

### Caddy
`Caddy` is a modern web server that can be deployed as a frontend for `lightwalletd`. It provides easy HTTPS configuration, automatic certificate management, and other features like routing and reverse proxying. In this setup, Caddy handles incoming HTTP traffic for `lightwalletd`, ensuring secure connections and simplified configuration.

- GitHub: [Caddy Repository](https://github.com/caddyserver/caddy)

### DNSSeeder
`dnsseeder` is a service that provides seed nodes for Zcash clients. When a new Zcash node starts, it needs to find peers to connect to, and the DNSSeeder helps by returning a list of active nodes in the network. This component ensures that new nodes can quickly discover and join the Zcash network without needing a predefined set of peers.

- GitHub: [DNSSeeder Repository](https://github.com/zcash/zcash-seeder)

Each of these components plays a critical role in the functioning of the Zcash network, from full node operations to lightweight client support, and peer discovery. You can visit their respective GitHub repositories for more details about their implementation, development, and configuration.

## Project Structure

- **Chart.yaml**: Metadata of the Helm chart.
- **values.yaml**: Default values for deploying the Helm chart.
- **templates/**: Helm templates that generate Kubernetes manifests.
- **.gitignore**: Specifies files and directories to be ignored by Git.

## Installation

To use this Helm chart, you need to have [Helm](https://helm.sh/docs/intro/install/) installed.

1. Navigate to the chart directory:

   ```bash
   cd zcash-stack
   ```
2. Add the repo

   ```bash
   helm repo add zcash-stack https://Electric-Coin-Company.github.io/zcash-stack/
   ```

3. Install the chart:

   ```bash
   helm install <release-name> zcash-stack/zcash-stack
   ```

4. If you need to override the default values, create a custom `values.yaml` and use the following command:

   ```bash
   helm install <release-name> zcash-stack/zcash-stack --values <custom-values-file.yaml>
   ```

## Configuration

The following table lists the configurable parameters of the Zcashd Helm chart and their default values:

| Parameter                     | Description                                                  | Default                                   | Required | Possible values                          |
|--------------------------------|--------------------------------------------------------------|-------------------------------------------|----------|------------------------------------------|
| `zcashd.enabled`               | Enable Zcashd node deployment                                | `True`                                    | True    | `False`, `True`                          |
| `zcashd.name`                  | Name of the Zcashd instance                                  | `zcashd`                                  | True    | Any string                               |
| `zcashd.testnet`               | Enable testnet mode                                          | `False`                                   | True    | `true`, `True`                           |
| `zcashd.image.repository`      | Zcashd Docker image repository                               | `electriccoinco/zcashd`                   | True    | Any valid image repository               |
| `zcashd.image.tag`             | Zcashd Docker image tag                                      | `latest`                                  | True    | Any valid image tag                      |
| `zcashd.image.pullPolicy`      | Image pull policy                                            | `IfNotPresent`                            | True    | `Always`, `IfNotPresent`, `Never`        |
| `zcashd.replicas`              | Number of Zcashd replicas                                    | `1`                                       | True    | Any integer >= 1                         |
| `zcashd.reindex`               | Enable reindexing                                            | `False`                                   | True    | `False`, `True`                          |
| `zcashd.nodes`                 | Seed nodes to connect to                                     | `[mainnet.z.cash]`                        | True    | List of valid node addresses             |
| `zcashd.volumes.data.size`     | Size of the Zcashd data volume                               | `400Gi`                                   | True    | Any valid size (e.g., `400Gi`)           |
| `zcashd.volumes.data.storageClass` | Storage class for the Zcashd data volume                 | `default`                                 | True    | Any valid storage class                  |
| `zcashd.service.type`          | Service type for Zcashd                                      | `ClusterIP`                               | True    | `ClusterIP`, `NodePort`, `LoadBalancer`  |
| `zcashd.requests.cpu`          | CPU requests for Zcashd                                      | `nil`                                     | True    | Any valid CPU request (e.g., `500m`)     |
| `zcashd.requests.memory`       | Memory requests for Zcashd                                   | `nil`                                     | True    | Any valid memory request (e.g., `512Mi`) |
| `zebra.enabled`                | Enable Zebra node deployment                                 | `True`                                    | True    | `False`, `True`                          |
| `zebra.name`                   | Name of the Zebra instance                                   | `zebra`                                   | True    | Any string                               |
| `zebra.testnet`                | Enable Zebra testnet mode                                    | `False`                                   | True    | `False`, `True`                          |
| `zebra.image.repository`       | Zebra Docker image repository                                | `zfnd/zebra`                              | True    | Any valid image repository               |
| `zebra.image.tag`              | Zebra Docker image tag                                       | `v1.9.0`                                  | True    | Any valid image tag                      |
| `zebra.replicas`               | Number of Zebra replicas                                     | `1`                                       | True    | Any integer >= 1                         |
| `zebra.volumes.data.size`      | Size of the Zebra data volume                                | `400Gi`                                   | True    | Any valid size (e.g., `400Gi`)           |
| `zebra.volumes.data.storageClass` | Storage class for the Zebra data volume                   | `defaut`                                  | True    | Any valid storage class                  |
| `zebra.service.type`           | Service type for Zebra                                       | `ClusterIP`                               | True    | `ClusterIP`, `NodePort`, `LoadBalancer`  |
| `lightwalletd.enabled`         | Enable Lightwalletd deployment                               | `True`                                    | True    | `False`, `True`                          |
| `lightwalletd.zcashNodeType`   | Type of Zcash node (`zcashd` or `zebra`)                     | `zcashd`                                  | True    | `zcashd`, `zebra`                        |
| `lightwalletd.image.repository`| Lightwalletd Docker image repository                         | `electriccoinco/lightwalletd`             | True    | Any valid image repository               |
| `lightwalletd.image.tag`       | Lightwalletd Docker image tag                                | `latest`                                  | True    | Any valid image tag                      |
| `lightwalletd.volumes.data.size`| Size of the Lightwalletd data volume                        | `400Gi`                                   | True    | Any valid size (e.g., `40Gi`)            |
| `lightwalletd.volumes.data.storageClass` | Storage class for the Lightwalletd volume          | `default`                                 | True    | Any valid storage class                  |
| `caddy.enabled`                | Enable Caddy deployment (frontend for Lightwalletd)          | `False`                                   | True    | `true`, `True`                           |
| `caddy.domain`                 | Domain for Caddy                                             | `"lwd.example.com"`                       | True    | Any valid domain                         |
| `caddy.email`                  | Email for SSL certificates                                   | `"admin@example.com"`                     | True    | Any valid email                          |
| `dnsseeder.enabled`            | Enable DNS seeder deployment                                 | `True`                                    | True    | `False`, `True`                          |
| `dnsseeder.name`               | Name of the DNS seeder instance                              | `dnsseeder`                               | True    | Any string                               |
| `dnsseeder.image.repository`   | DNS seeder Docker image repository                           | `electriccoinco/dnsseeder`                | True    | Any valid image repository               |
| `dnsseeder.image.tag`          | DNS seeder Docker image tag                                  | `latest`                                  | True    | Any valid image tag                      |
| `dnsseeder.service.type`       | Service type for DNS seeder                                  | `ClusterIP`                               | True    | `ClusterIP`, `NodePort`, `LoadBalancer`  |
| `dnsseeder.env.seedHostname`   | Hostname for the seeder                                      | `"example.com"`                           | True    | Any valid hostname                       |
| `dnsseeder.env.nodeHostname`   | Hostname for the DNS node                                    | `"zcash.example.com"`                     | True    | Any valid hostname                       |
| `dnsseeder.env.email`          | Admin email for DNS seeder                                   | `"admin@example.com"`                     | True    | Any valid email                          |
| `dnsseeder.env.threads`        | Number of threads for the seeder                             | `384`                                     | True    | Any valid integer >= 1                   |
| `dnsseeder.env.address`        | IP address to bind for the seeder                            | `"0.0.0.0"`                               | True    | Any valid IP address                     |
| `dnsseeder.env.network`        | Network type for the seeder (`mainnet` or `testnet`)         | `mainnet`                                 | True    | `mainnet`, `testnet`                     |
| `rpc.credentials.rpcUser`      | RPC username                                                 | `5s3rn4m3`                                | True    | Any string                               |
| `rpc.credentials.rpcPassword`  | RPC password                                                 | `s3cr3tp4ssw0rd`                          | True    | Any string                               |

## Customizing the Deployment

You can override the default values by creating a custom `values.yaml` or using the `--set` flag. For example:

```bash
helm install <release-name> zcash-stack/zcash-stack
```

