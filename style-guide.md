# Salvium Documentation Style Guide

## Overview

This style guide establishes standards for creating and maintaining documentation for the Salvium project. Consistent documentation improves readability and helps users find information quickly. All contributors should follow these guidelines when creating or updating documentation.

## Document Structure

### File Naming
- Use descriptive names that clearly indicate content
- Separate words with spaces (e.g., `Mining and Emissions.md`)
- Use title case for filenames
- Group related files in appropriate folders

### Headers
- Use a single level 1 heading (`#`) for the page title
- Structure content using level 2 (`##`) and level 3 (`###`) headings
- Avoid skipping heading levels (e.g., don't go from level 2 to level 4)
- Keep headings concise and descriptive

```markdown
# Main Title
## Section Heading
### Subsection
```

### Content Organization
- Begin each page with a brief introduction (1-2 paragraphs)
- Follow a logical progression from basic to advanced concepts
- Use sections and subsections to organize related information
- For tutorials or guides, present information in the order users will need it

## Formatting

### Text Formatting
- Use **bold** (`**text**`) for emphasis and UI elements
- Use *italics* (`*text*`) for introducing new terms or for light emphasis
- Use `code formatting` for command names, file paths, and code snippets
- Use > blockquotes for important notes or quotes

### Lists
- Use unordered lists (with `-`) for items without sequence
- Use ordered lists (with `1.`, `2.`, etc.) for sequential steps
- Maintain consistent indentation for nested lists (2 spaces)
- Keep list items parallel in structure

```markdown
- Unordered item
  - Nested item
    - Further nested item

1. First step
2. Second step
   1. Substep one
   2. Substep two
```

### Code Blocks
- Use fenced code blocks with language specification
- Include comments for complex code examples
- For command line examples, indicate user input vs. system output

````markdown
```javascript
// Example code
function example() {
  return "This is an example";
}
```

```bash
# User command
$ salvium-wallet-cli --wallet-file my-wallet

# Expected output
This is Salvium CLI wallet v1.0.0
```
````

### Tables
- Use tables for structured data comparison
- Include header row and alignment formatting
- Keep tables simple and scannable

```markdown
| Feature | Description | Default Value |
|---------|-------------|:-------------:|
| Privacy | Ring signatures | 11 |
| Staking | Lock period | 21,600 blocks |
```

## Language and Style

### Tone and Voice
- Use a professional, helpful tone
- Write in second person ("you") when addressing the reader
- Be concise but thorough
- Avoid jargon; when using technical terms, define them on first use

### Technical Writing Guidelines
- Use active voice over passive voice
- Write in present tense when possible
- Be specific and precise with technical details
- Avoid ambiguous pronouns (this, that, it, they) without clear antecedents

### Consistency
- Maintain consistent terminology throughout documentation
- Use the same terms for the same concepts
- Capitalize product names consistently (Salvium, SAL)
- Follow established conventions for technical terms

## Special Elements

### Notes and Warnings
Use admonition blocks for important information:

```markdown
!!! note
    This is additional information users might find helpful.

!!! warning
    This highlights potential problems or important considerations.

!!! tip
    This provides helpful advice for better usage.
```

### Links
- Use descriptive link text that indicates the destination
- Link to other documentation pages using relative paths
- Use absolute URLs for external resources
- Check links regularly to ensure they're not broken

```markdown
[GUI Wallet Guide](../WALLETS/Salvium%20GUI%20Wallet%20Guide.md)
[Official Website](https://salvium.io)
```

### Images
- Include descriptive alt text for all images
- Use screenshots to illustrate UI elements and processes
- Optimize images for web viewing (reasonable file size)
- Place images near the relevant text
- Use consistent formatting and sizing

```markdown
![Salvium GUI Wallet main screen](../assets/images/gui-wallet-main.png)
```

## Technical Content

### Command Line Examples
- Prefix commands with appropriate prompt symbols ($ for user, # for root)
- Indicate placeholders with angle brackets (e.g., `<filename>`)
- Include expected output when helpful
- Explain command options and parameters

```markdown
$ salvium-wallet-cli --wallet-file <filename>
```

### API Documentation
- Use consistent formatting for API endpoints
- Include parameters, return values, and example responses
- Document error codes and messages
- Provide example requests and responses

### Configuration Examples
- Include annotated examples of configuration files
- Highlight required vs. optional parameters
- Provide examples for common use cases

## Best Practices

### Keep Documentation Current
- Update documentation when features change
- Add documentation for new features
- Remove documentation for deprecated features
- Regularly review documentation for accuracy

### Make Documentation Accessible
- Write clear, simple sentences
- Avoid unnecessarily complex language
- Structure content with clear headings and sections
- Provide examples for complex concepts

### Include Context
- Explain why, not just how
- Provide use cases for features
- Link to related documentation
- Include troubleshooting information

## Review Process

Before submitting documentation:
1. Verify technical accuracy
2. Check for adherence to this style guide
3. Proofread for spelling, grammar, and clarity
4. Test all procedures, code examples, and links

## Contribution Workflow

1. For new documentation, create an issue describing the proposed content
2. Fork the repository and create a branch for your changes
3. Write documentation following this style guide
4. Submit a pull request referencing the issue
5. Address feedback from reviewers
6. Once approved, documentation will be merged

By following these guidelines, we can maintain high-quality, consistent documentation that helps users effectively understand and use Salvium.
