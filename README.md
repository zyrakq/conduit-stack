# 🌐 Conduit Matrix Stack

Complete Docker-based Conduit Matrix homeserver deployment with SSL certificate management and identity integration for production and development environments.

## 🧩 Components

### 🔐 SSL Automation

#### [🔒 Let's Encrypt Manager](src/ssl-automation/letsencrypt-manager)

Automatic SSL certificate management from Let's Encrypt for production deployments. Provides seamless HTTPS integration for Docker containers using nginx-proxy and acme-companion.
[Learn more about Let's Encrypt Manager configuration](src/ssl-automation/letsencrypt-manager/README.md).

#### [🏠 Step CA Manager](src/ssl-automation/step-ca-manager)

Local domain stack with trusted self-signed certificates for virtual network deployments. Includes private CA management and local DNS resolution for development environments.
[Learn more about Step CA Manager configuration](src/ssl-automation/step-ca-manager/README.md).

### 🔑 Identity Management

#### [🔐 Keycloak](src/identity-management/keycloak)

Enterprise-grade identity and access management solution. Provides authentication, authorization, and user management for secure application access.
[Learn more about Keycloak configuration](src/identity-management/keycloak/README.md).

For Conduit integration, see: [Conduit OIDC Configuration](src/conduit/README.md#oidc-integration)

#### [🔐 Kanidm](src/identity-management/kanidm)

Modern identity and access management server with comprehensive authentication capabilities. Provides secure identity management with modular configuration system and multiple deployment modes.
[Learn more about Kanidm configuration](src/identity-management/kanidm/README.md).

## 🌐 Services

### 🌐 [Conduit Matrix Server](src/conduit/)

Modular Docker Compose configuration system for Conduit Matrix homeserver with support for multiple environments and OIDC integration capabilities. Provides lightweight, Rust-based Matrix homeserver deployment with customizable configurations for development and production.
[Learn more about Conduit configuration](src/conduit/README.md).

## 🚀 Quick Start

Each component has its own README with detailed setup instructions. Choose the certificate management solution and identity provider that fits your deployment scenario.

### Basic Setup

1. **Choose SSL Management:**
   - Production: Use Let's Encrypt Manager
   - Development: Use Step CA Manager

2. **Configure Identity (Optional):**
   - Enterprise: Use Keycloak
   - Modern: Use Kanidm

3. **Deploy Conduit Matrix Server:**
   - Configure Conduit homeserver with desired environment and extensions

### Example Deployment

```bash
# 1. Build Conduit configurations
cd src/conduit/
./build.sh

# 2. Choose your deployment scenario
# For development with OIDC
cd build/forwarding/oidc/

# For production with Let's Encrypt and OIDC
cd build/letsencrypt/oidc/

# For production with Step CA, OIDC and Step CA trust
cd build/step-ca/oidc+step-ca-trust/

# 3. Configure environment
cp .env.example .env
# Edit .env with your values

# 4. Deploy
docker-compose up -d
```

## 🏗️ Architecture

```sh
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Matrix Client  │────│  Conduit Matrix │────│    RocksDB      │
│ (Element/Cinny) │    │   Homeserver    │    │   (Database)    │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │
         │                       │
┌─────────────────┐    ┌─────────────────┐
│ Identity Server │    │  SSL Manager    │
│ (Keycloak/      │    │ (Let's Encrypt/ │
│  Kanidm)        │    │  Step CA)       │
└─────────────────┘    └─────────────────┘
```

## 📋 Requirements

- Docker & Docker Compose
- Domain name (for production deployments)
- Email address (for Let's Encrypt)
- `yq` tool for configuration building

## 🔧 Configuration

All services use modular Docker Compose configurations with:

- **Base components**: Core service definitions
- **Environment components**: Development, production, SSL configurations
- **Extension components**: OIDC, identity integration, Step CA trust
- **Build system**: Automatic generation of deployment combinations

## 🌍 Deployment Scenarios

### Development Environment

```bash
# Conduit with port forwarding
cd src/conduit/build/forwarding/base/
docker-compose up -d

# Conduit with port forwarding and OIDC
cd src/conduit/build/forwarding/oidc/
docker-compose up -d
```

### Production Environment

```bash
# Conduit with Let's Encrypt SSL
cd src/conduit/build/letsencrypt/base/
docker-compose up -d

# Conduit with Let's Encrypt SSL and OIDC
cd src/conduit/build/letsencrypt/oidc/
docker-compose up -d

# Conduit with Step CA SSL, OIDC and Step CA trust
cd src/conduit/build/step-ca/oidc+step-ca-trust/
docker-compose up -d
```

### DevContainer Environment

```bash
# Conduit in DevContainer
cd src/conduit/build/devcontainer/base/
docker-compose up -d

# Conduit in DevContainer with OIDC and Step CA trust
cd src/conduit/build/devcontainer/oidc+step-ca-trust/
docker-compose up -d
```

## 🔐 Security Features

- **SSL/TLS Encryption**: Automatic certificate management
- **Identity Integration**: OIDC authentication
- **Network Isolation**: Docker network segmentation
- **Secret Management**: Environment-based configuration
- **Access Control**: Registration tokens and federation controls
- **Step CA Trust**: Automatic certificate trust for internal services

## 🎯 Conduit Features

- **Lightweight**: Rust-based Matrix homeserver with minimal resource usage
- **Performance**: RocksDB backend for optimal performance
- **Federation**: Full Matrix federation support
- **OIDC**: External authentication provider integration
- **Well-known**: Automatic Matrix well-known delegation setup
- **Registration**: Configurable user registration with token support

## 🆘 Troubleshooting

### Common Issues

- **SSL Certificate Issues**: Check Let's Encrypt/Step CA configuration
- **Identity Integration**: Verify OIDC provider settings
- **Network Connectivity**: Ensure proper Docker network configuration
- **Federation Issues**: Check server name and well-known configuration
- **Database Issues**: Verify RocksDB permissions and storage

### Logs

```bash
# Conduit logs
docker logs conduit

# Identity provider logs
docker logs keycloak  # or kanidm

# SSL automation logs
docker logs nginx-proxy
docker logs letsencrypt-companion  # or step-ca-manager
```

## 📚 Documentation

- [Conduit Matrix Server Configuration](src/conduit/README.md)
- [SSL Automation](src/ssl-automation/)
- [Identity Management](src/identity-management/)
- [Official Conduit Documentation](https://gitlab.com/famedly/conduit/-/blob/next/README.md)

## 🔗 Matrix Ecosystem

### Compatible Clients

- **Element**: Full-featured Matrix client
- **Cinny**: Modern Matrix client with clean UI
- **FluffyChat**: Cross-platform Matrix client
- **Nheko**: Desktop Matrix client

### Integration Services

- **Matrix Bridges**: Connect to Telegram, Discord, and other platforms
- **Maubot**: Matrix bot framework
- **Matrix Widgets**: Embedded applications in Matrix rooms

## 🌐 Federation

Conduit supports full Matrix federation, allowing communication with:

- **matrix.org**: The flagship Matrix homeserver
- **Other Conduit instances**: Lightweight Matrix homeservers
- **Synapse instances**: Reference Matrix homeserver implementation
- **Dendrite instances**: Next-generation Matrix homeserver

## 🔧 Advanced Configuration

### Custom Configurations

Each service supports extensive customization through:

- Environment variables
- Configuration files
- Docker Compose overrides
- Extension combinations

### Monitoring and Observability

- Container health checks
- Log aggregation
- Metrics collection (when integrated with monitoring stack)
- Performance monitoring

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test configurations
5. Submit a pull request

## 📄 License

This project is dual-licensed under:

- [Apache License 2.0](LICENSE-APACHE)
- [MIT License](LICENSE-MIT)

## 🔗 Related Projects

- [Matrix.org](https://matrix.org/) - Open network for secure, decentralized communication
- [Conduit](https://conduit.rs/) - Lightweight Matrix homeserver written in Rust
- [Element.io](https://element.io/) - Secure collaboration and messaging
- [Keycloak](https://www.keycloak.org/) - Identity and access management
- [Kanidm](https://kanidm.com/) - Modern identity management
- [Let's Encrypt](https://letsencrypt.org/) - Free SSL certificates
- [Smallstep](https://smallstep.com/) - Private certificate authority
