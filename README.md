# MOS Hub Template Builder

A web-based form builder for creating manifest files (`template.json`) for the MOS Hub ecosystem. This tool provides an intuitive interface for generating configuration manifests for three types of deployments:

- **Compose**: Docker Compose configurations with metadata (name, categories, description, URLs)
- **Docker**: Full Docker container specifications including environment variables, ports, volumes, devices, labels, and advanced options
- **Plugin**: Plugin metadata and configuration details

The application generates properly formatted JSON output that can be directly used in MOS Hub repositories. It also supports creating separate `compose.yaml` files for Compose configurations.

### Features

- Multi-form interface for different manifest types
- Real-time JSON preview and validation
- Copy and download functionality
- Support for complex configurations (paths, ports, variables, devices, labels)
- Advanced options for GPU and CPU settings

---

## License

GPL-3.0, like the other MOS plugins in this ecosystem.
