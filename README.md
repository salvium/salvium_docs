# Salvium Docs Website

This repository contains the official documentation for [Salvium](https://salvium.io), a privacy-focused cryptocurrency. It is built using [MkDocs](https://www.mkdocs.org/) with the [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme.

## Local Development

### Prerequisites

- Python 3.x
- pip

### Setup

```bash
git clone https://github.com/salvium/salvium_docs.git
cd salvium_docs
pip install -r requirements.txt
mkdocs serve
```

Then visit `http://127.0.0.1:8000` in your browser to preview the site.

## Building

To generate the static site:

```bash
mkdocs build
```

The output will be in the `site/` directory.

## Deployment

The site is deployed via GitHub Pages at:

🔗 https://docs.salvium.io

Ensure your `mkdocs.yml` file is configured with the correct `site_url` and theme settings.

## Contributing

1. Fork this repository  
2. Create a new branch (`git checkout -b feature/your-feature`)  
3. Make your changes  
4. Commit (`git commit -m "Describe your changes"`)  
5. Push and open a Pull Request

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
