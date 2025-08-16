# 🌐 Conduit Matrix Server

A modular Docker Compose configuration system for Conduit Matrix homeserver with support for multiple environments and OIDC integration capabilities.

## 🏗️ Project Structure

```sh
src/conduit/
├── components/                              # Source compose components
│   ├── base/                               # Base components
│   │   ├── docker-compose.yml              # Main Conduit service
│   │   └── .env.example                    # Base environment variables
│   ├── environments/                       # Environment components
│   │   ├── devcontainer/
│   │   │   └── docker-compose.yml          # DevContainer environment
│   │   ├── forwarding/
│   │   │   └── docker-compose.yml          # Development with port forwarding
│   │   ├── letsencrypt/
│   │   │   ├── docker-compose.yml          # Let's Encrypt SSL
│   │   │   └── .env.example                # Let's Encrypt variables
│   │   └── step-ca/
│   │       ├── docker-compose.yml          # Step CA SSL
│   │       └── .env.example                # Step CA variables
│   └── extensions/                         # Extension components
│       ├── oidc/                           # OIDC authentication extension
│       │   ├── docker-compose.yml          # OIDC configuration
│       │   ├── .env.example                # OIDC variables
│       │   └── Dockerfile                  # Custom Conduit image with OIDC
│       └── step-ca-trust/                  # Step CA certificate trust
│           ├── docker-compose.yml          # Step CA trust configuration
│           └── .env.example                # Step CA trust variables
├── extensions.yml                          # Extension compatibility configuration
├── build/                        # Generated configurations (auto-generated)
│   ├── devcontainer/
│   │   ├── base/                 # DevContainer + base
│   │   ├── oidc/                 # DevContainer + base + OIDC
│   │   ├── step-ca-trust/        # DevContainer + base + Step CA trust
│   │   └── oidc+step-ca-trust/   # DevContainer + base + OIDC + Step CA trust
│   ├── forwarding/
│   │   ├── base/                 # Development + base
│   │   ├── oidc/                 # Development + base + OIDC
│   │   ├── step-ca-trust/        # Development + base + Step CA trust
│   │   └── oidc+step-ca-trust/   # Development + base + OIDC + Step CA trust
│   ├── letsencrypt/
│   │   ├── base/                 # Let's Encrypt + base
│   │   ├── oidc/                 # Let's Encrypt + base + OIDC
│   │   ├── step-ca-trust/        # Let's Encrypt + base + Step CA trust
│   │   └── oidc+step-ca-trust/   # Let's Encrypt + base + OIDC + Step CA trust
│   └── step-ca/
│       ├── base/                 # Step CA + base
│       ├── oidc/                 # Step CA + base + OIDC
│       ├── step-ca-trust/        # Step CA + base + Step CA trust
│       └── oidc+step-ca-trust/   # Step CA + base + OIDC + Step CA trust
├── build.sh                      # Build script
└── README.md                     # This file
```

## 🚀 Quick Start

### 1. Build Configurations

Run the build script to generate all possible combinations:

```bash
./build.sh
```

This will create all combinations in the `build/` directory.

### 2. Choose Your Configuration

Navigate to the desired configuration directory:

```bash
# For development with port forwarding
cd build/forwarding/base/

# For development with port forwarding and OIDC
cd build/forwarding/oidc/

# For DevContainer environment
cd build/devcontainer/base/

# For DevContainer environment with OIDC
cd build/devcontainer/oidc/

# For production with Let's Encrypt SSL
cd build/letsencrypt/base/

# For production with Let's Encrypt SSL and OIDC
cd build/letsencrypt/oidc/

# For production with Step CA SSL
cd build/step-ca/base/

# For production with Step CA SSL and OIDC
cd build/step-ca/oidc/

# For production with Step CA SSL, OIDC and Step CA trust
cd build/step-ca/oidc+step-ca-trust/
```

### 3. Configure Environment

Copy and edit the environment file:

```bash
cp .env.example .env
# Edit .env with your values
```

### 4. Deploy

Start the services:

```bash
docker-compose up -d
```

Access: `http://localhost:6167` (for forwarding mode)

## 🔧 Available Configurations

### Environments

- **devcontainer**: Development environment with workspace network
- **forwarding**: Development environment with port forwarding (6167:6167)
- **letsencrypt**: Production with Let's Encrypt SSL certificates
- **step-ca**: Production with Step CA SSL certificates

### Extensions

- **oidc**: OIDC authentication integration for enhanced authentication
- **step-ca-trust**: Step CA certificate trust integration

### Generated Combinations

Each environment can be combined with extensions to provide complete Conduit deployments:

**Base configurations:**

- `devcontainer/base` - DevContainer development setup
- `forwarding/base` - Development with port forwarding
- `letsencrypt/base` - Production with Let's Encrypt SSL
- `step-ca/base` - Production with Step CA SSL

**Single extension configurations:**

- `devcontainer/oidc` - DevContainer development with OIDC authentication
- `devcontainer/step-ca-trust` - DevContainer development with Step CA certificate trust
- `forwarding/oidc` - Development with port forwarding and OIDC authentication
- `forwarding/step-ca-trust` - Development with port forwarding and Step CA certificate trust
- `letsencrypt/oidc` - Production with Let's Encrypt SSL and OIDC authentication
- `letsencrypt/step-ca-trust` - Production with Let's Encrypt SSL and Step CA certificate trust
- `step-ca/oidc` - Production with Step CA SSL and OIDC authentication
- `step-ca/step-ca-trust` - Production with Step CA SSL and Step CA certificate trust

**Combined extension configurations:**

When [`extensions.yml`](extensions.yml) is present, additional combinations are generated:

- `devcontainer/oidc+step-ca-trust` - DevContainer development with OIDC + Step CA trust
- `forwarding/oidc+step-ca-trust` - Development with port forwarding + OIDC + Step CA trust
- `letsencrypt/oidc+step-ca-trust` - Production with Let's Encrypt + OIDC + Step CA trust
- `step-ca/oidc+step-ca-trust` - Production with Step CA + OIDC + Step CA trust

## 🔧 Environment Variables

### Base Configuration

- `COMPOSE_PROJECT_NAME`: Project name for Docker Compose
- `CONDUIT_SERVER_NAME`: Matrix server name (e.g., matrix.example.com)
- `CONDUIT_DATABASE_PATH`: Database storage path (default: /var/lib/matrix-conduit/)
- `CONDUIT_DATABASE_BACKEND`: Database backend (default: rocksdb)
- `CONDUIT_PORT`: Conduit service port (default: 6167)
- `CONDUIT_MAX_REQUEST_SIZE`: Maximum request size in bytes (default: 20000000)
- `CONDUIT_ALLOW_REGISTRATION`: Allow user registration (default: false)
- `CONDUIT_REGISTRATION_TOKEN`: Registration token for user signup
- `CONDUIT_ALLOW_FEDERATION`: Enable Matrix federation (default: true)
- `CONDUIT_ALLOW_CHECK_FOR_UPDATES`: Allow update checks (default: true)
- `CONDUIT_TRUSTED_SERVERS`: List of trusted Matrix servers (default: ["matrix.org"])
- `CONDUIT_ADDRESS`: Bind address (default: 0.0.0.0)
- `CONDUIT_CONFIG`: Path to custom configuration file

### Let's Encrypt Configuration

- `VIRTUAL_PORT`: Port for nginx-proxy (default: 6167)
- `VIRTUAL_HOST`: Domain for nginx-proxy (e.g., matrix.example.com)
- `LETSENCRYPT_HOST`: Domain for SSL certificate
- `LETSENCRYPT_EMAIL`: Email for certificate registration
- `WELL_KNOWN_VIRTUAL_PORT`: Port for well-known service (default: 80)
- `WELL_KNOWN_VIRTUAL_HOST`: Domain for well-known service (e.g., example.com)
- `WELL_KNOWN_LETSENCRYPT_HOST`: Domain for well-known SSL certificate
- `WELL_KNOWN_LETSENCRYPT_EMAIL`: Email for well-known certificate registration

### Step CA Configuration

- `VIRTUAL_PORT`: Port for nginx-proxy (default: 6167)
- `VIRTUAL_HOST`: Domain for nginx-proxy (e.g., matrix.example.local)
- `LETSENCRYPT_HOST`: Domain for SSL certificate
- `LETSENCRYPT_EMAIL`: Email for certificate registration
- `WELL_KNOWN_VIRTUAL_PORT`: Port for well-known service (default: 80)
- `WELL_KNOWN_VIRTUAL_HOST`: Domain for well-known service (e.g., example.com)
- `WELL_KNOWN_LETSENCRYPT_HOST`: Domain for well-known SSL certificate
- `WELL_KNOWN_LETSENCRYPT_EMAIL`: Email for well-known certificate registration

### OIDC Configuration

- `CONDUIT_CONFIG`: Path to custom configuration file (default: /matrix-conduit/conduit.toml)
- `CONDUIT_IDPS_SSO_ISSUER`: OIDC issuer URL (e.g., <https://sso.example.com/realms/matrix>)
- `CONDUIT_IDPS_SSO_NAME`: SSO provider display name (default: SSO)
- `CONDUIT_IDPS_SSO_ICON`: SSO provider icon URL
- `CONDUIT_IDPS_SSO_SCOPES`: OIDC scopes (default: ["openid", "profile", "email", "groups"])
- `CONDUIT_IDPS_SSO_CLIENT_ID`: OIDC client ID for Conduit
- `CONDUIT_IDPS_SSO_CLIENT_SECRET`: OIDC client secret
- `CONDUIT_IDPS_SSO_AUTH_METHOD`: Authentication method (default: basic)
- `CONDUIT_IDPS_SSO_REDIRECT_URL`: OAuth callback URL (optional)

### Step CA Trust Configuration

- `STEP_CA_TRUST`: Enable Step CA certificate trust (default: true)
- `STEP_CA_TRUST_RESTART`: Restart container when trust configuration changes (default: true)

The step-ca-trust extension automatically configures containers to trust certificates issued by Step CA, enabling secure communication with Step CA-protected services.

## 🔐 OIDC Integration

The OIDC extension provides integration with external identity providers for enhanced authentication and user management.

### Using OIDC Extension

1. **Build with OIDC extension:**

   ```bash
   ./build.sh
   ```

2. **Choose OIDC-enabled configuration:**

   ```bash
   # For development with OIDC
   cd build/forwarding/oidc/
   
   # For production with Let's Encrypt and OIDC
   cd build/letsencrypt/oidc/
   ```

3. **Configure identity provider:**

   ```bash
   cp .env.example .env
   # Edit .env with your identity provider details
   ```

4. **Example OIDC configuration:**

   ```bash
   CONDUIT_SERVER_NAME=matrix.example.com
   CONDUIT_IDPS_SSO_ISSUER=https://sso.example.com/realms/matrix
   CONDUIT_IDPS_SSO_CLIENT_ID=conduit
   CONDUIT_IDPS_SSO_CLIENT_SECRET=your-client-secret
   ```

5. **Deploy:**

   ```bash
   docker-compose up -d
   ```

## 🛠️ Development

### Adding New Environments

1. Create directory in `components/environments/` with `docker-compose.yml` and optional `.env.example` file
2. Run `./build.sh` to generate new combinations

### Adding New Extensions

1. Create directory in `components/extensions/` with `docker-compose.yml` and optional `.env.example` file
2. Update [`extensions.yml`](extensions.yml) if the extension should be part of combinations
3. Run `./build.sh` to generate new combinations with all environments

### File Naming Convention

All component files follow the standard Docker Compose naming convention (`docker-compose.yml`) for:

- **VS Code compatibility**: Full support for Docker Compose language features and IntelliSense
- **IDE integration**: Proper syntax highlighting and validation in all major editors
- **Tool compatibility**: Works with Docker Compose plugins and extensions
- **Standard compliance**: Follows official Docker Compose file naming patterns

### Modifying Existing Components

1. Edit the component files in `components/`
2. Run `./build.sh` to regenerate configurations
3. The `build/` directory will be completely recreated

## 🌐 Networks

- **Development**: `matrix-network` (internal)
- **DevContainer**: `matrix-workspace-network` (external)
- **Let's Encrypt**: `letsencrypt-network` (external)
- **Step CA**: `step-ca-network` (external)

## 🔗 Extension Combinations

### Configuration File

Extension combinations are configured via [`extensions.yml`](extensions.yml). This file defines:

- **Groups**: Extensions that conflict with each other
- **Combinations**: Valid extension combinations to generate
- **Compatibility**: Rules for which extensions can work together

### Example Configuration

```yaml
# Extension compatibility configuration
version: "1.0"

# Extension groups - extensions in same group conflict with each other
groups:
  auth:
    description: "Authentication services"
    extensions:
      - oidc

  step-ca:
    description: "smallstep-ca"
    extensions:
      - step-ca-trust

# Valid combinations
combinations:
  - name: "oidc+step-ca-trust"
    extensions: ["oidc", "step-ca-trust"]
    description: "Authentication with Step CA trust"
```

### Backward Compatibility

- **Without `extensions.yml`**: Only single extensions are generated (legacy behavior)
- **With `extensions.yml`**: Both single extensions and combinations are generated
- **Existing configurations**: Remain unchanged and fully compatible

## 🔒 Security

⚠️ **Production Checklist:**

- Configure proper server name and federation settings
- Set up proper firewall rules
- Regular security updates
- Configure rate limiting
- Set up proper logging
- Validate SSL certificates
- Secure registration tokens
- Review federation settings

## 🆘 Troubleshooting

**Build Issues:**

- Ensure `yq` is installed: <https://github.com/mikefarah/yq#install>
- Check component file syntax
- Verify all required files exist

**Conduit Issues:**

- Check configuration syntax
- Verify database permissions
- Review container logs: `docker logs conduit`
- Check network connectivity

**SSL Issues:**

- **Let's Encrypt**: Verify domain DNS and letsencrypt-manager
- **Step CA**: Check step-ca-manager and virtual network config

**Federation Issues:**

- Verify server name configuration
- Check well-known delegation setup
- Validate SSL certificates
- Check firewall and port configuration

**OIDC Issues:**

- Verify OIDC provider configuration
- Check client ID and secret
- Validate redirect URLs
- Review OIDC provider logs

## 📝 Notes

- The `build/` directory is automatically generated and should not be edited manually
- Environment variables in generated files use `$VARIABLE_NAME` format for proper interpolation
- Each generated configuration includes a complete `docker-compose.yml` and `.env.example`
- Missing `.env.*` files for components are handled gracefully by the build script
- Extension combinations are only generated when [`extensions.yml`](extensions.yml) exists
- The build script maintains full backward compatibility - existing workflows continue to work unchanged
- Extension conflicts are automatically validated during the build process
- User `.env` files are preserved during rebuilds

## 🔄 Configuration Management

The build system automatically:

- Merges base and environment configurations
- Copies additional files and configurations
- Generates complete deployment configurations
- Preserves user `.env` files during rebuilds

**Build approach:**

```bash
./build.sh
cd build/forwarding/base/
cp .env.example .env
# Edit .env with your values
docker-compose up -d
```

## 🎯 Conduit Features

The Conduit configuration includes:

- **Lightweight**: Rust-based Matrix homeserver with minimal resource usage
- **Federation**: Full Matrix federation support
- **Database**: RocksDB backend for optimal performance
- **Registration**: Configurable user registration with token support
- **OIDC**: External authentication provider integration
- **Well-known**: Automatic Matrix well-known delegation setup
- **SSL**: Full SSL/TLS support with Let's Encrypt and Step CA
- **Updates**: Automatic update checking capability
- **Trusted Servers**: Configurable trusted server list

## ⚙️ Advanced Configuration

For detailed configuration options of the Conduit Matrix homeserver, including federation settings, database configuration, and all available environment variables, see the official Conduit documentation:

📖 **[Conduit Configuration Guide](https://gitlab.com/famedly/conduit/-/blob/next/README.md)**

This comprehensive guide covers:

- Server configuration options
- Database backend selection
- Federation and well-known setup
- Registration and authentication
- Performance tuning
- Logging and monitoring
- Security considerations

## 🔗 Integration

Conduit works seamlessly with:

- **Matrix Clients**: Element, Cinny, FluffyChat, and other Matrix clients
- **Identity Providers**: Keycloak, Kanidm, and other OIDC providers
- **Bridges**: Matrix bridges for Telegram, Discord, and other platforms
- **Bots**: Maubot and other Matrix bot frameworks
- **Monitoring**: Prometheus and Grafana for metrics and monitoring
