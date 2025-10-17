# Thomas Borg Salling - Personal Website

This is the source repository for [tbsalling.github.io](https://tbsalling.github.io), a personal website built with Jekyll and hosted on GitHub Pages.

## About

Personal website and blog for Thomas Borg Salling - a freelance programmer and remote worker specializing in Java and open source programming.

## Development

### Prerequisites

- Ruby 3.x
- Bundler

### Local Setup

1. Install dependencies:
   ```bash
   bundle install
   ```

2. Run the site locally:
   ```bash
   bundle exec jekyll serve
   ```
   
   For local development with faster builds and development-specific settings:
   ```bash
   bundle exec jekyll serve --config _config.yml,_config_dev.yml
   ```

3. Open your browser to `http://localhost:4000`

### Building

To build the site without running a server:

```bash
bundle exec jekyll build
```

The built site will be in the `_site` directory.

## Technology Stack

- **Jekyll**: Static site generator
- **GitHub Pages**: Hosting platform
- **Cayman Theme**: Jekyll theme
- **Font Awesome**: Icons
- **Google Analytics & Microsoft Clarity**: Analytics

## Content

The site includes:
- Personal portfolio and CV information
- Blog posts about AIS (Automatic Identification System) and maritime software
- Featured client list
- Open source project information

## License

Content © Thomas Borg Salling. All rights reserved.

Website code licensed under MIT License - see [LICENSE](LICENSE) file for details.
