# Contributing to Release Plugin

Thanks for your interest in contributing! This plugin helps automate release workflows for Claude Code users.

## Ways to Contribute

- **Bug reports** - Found something broken? Open an issue
- **Feature requests** - Ideas for new project types or commands
- **Documentation** - Improvements to README or command docs
- **Code** - Bug fixes or new features

## Development Setup

1. Clone the repo:
   ```bash
   git clone https://github.com/scarrillo/release.git
   cd release
   ```

2. Test locally with Claude Code:
   ```bash
   /plugin install ./
   ```

3. Commands are in `commands/*.md` - edit and test interactively

## Pull Request Process

1. Fork the repo and create a branch
2. Make your changes
3. Test with Claude Code locally
4. Submit PR with clear description of changes

## Adding Project Type Support

Want to add support for a new project type (Next.js, Android, etc.)?

1. Update `commands/release.md`:
   - Add detection logic in "Detect Project Type" section
   - Add version reading instructions
   - Add version update instructions
   - Add project-specific notes

2. Update `README.md`:
   - Add to "Supported Project Types" table

3. Test the full release flow with a sample project

## Code Style

- Commands use Markdown with clear section headers
- Keep instructions concise and actionable
- Follow existing patterns in command files

## Questions?

Open an issue or reach out at release@shawn.co
