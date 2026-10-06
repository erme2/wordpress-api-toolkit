# WordPress API Toolkit

A reusable project for provisioning independent WordPress installations as REST API backends and importing users and content from existing WordPress sites through authenticated REST APIs.

## Status

Project definition only. Provisioning and migration commands are not implemented yet.

## Intended workflow

1. Create a named, fresh WordPress installation with its own database, uploads, configuration, and credentials.
2. Connect that installation to an existing source WordPress site using authenticated REST API access.
3. Run a read-only preflight and dry run to report available content, permissions, unsupported fields, and identity conflicts.
4. Export users and content through the source REST API and import them through the destination REST API.
5. Verify the result and retain a migration report and ID mappings so interrupted jobs can resume and repeated runs avoid duplicates.

Repeat this workflow for any number of independent installations. Birra La Fortezza is the first intended test case, not a dependency or a site-specific product requirement. WordPress Multisite is not assumed.

## Planned scope

- Reproducible provisioning of fresh WordPress backends, retaining WordPress administration and REST API access.
- Separate source and destination configuration for each migration, with credentials supplied outside version control.
- Authenticated export/import of users, posts, pages, categories, tags, and media, plus supported custom post types, taxonomies, and metadata.
- Source-to-destination mappings for users, content, terms, and attachments; preservation of authorship and reconstruction of parent, taxonomy, and featured-image relationships.
- Transfer of media files from source URLs and upload through the destination media REST endpoint, with supported internal links and media references rewritten to destination URLs.
- Paginated reads, resumable imports, explicit conflict handling, dry runs, and reports of skipped or unsupported data.
- Preservation of editable source content rather than substituting rendered HTML where raw content is available.

Provisioning must happen before an installation can expose REST endpoints. REST APIs are the migration interface; they are not a substitute for installing WordPress itself.

## REST API requirements and limits

- Use HTTPS and separate WordPress Application Passwords for source and destination access, with the capabilities required for the selected operations. Public endpoints alone do not provide a complete user or private-content export.
- The standard users API never returns passwords or password hashes. Existing passwords cannot be preserved through core REST endpoints. Imported accounts will require new credentials or a password-reset flow; notification behavior must be explicit.
- Custom post types and taxonomies must be registered on the destination and exposed to REST on the source. Custom metadata must also be explicitly exposed. A preflight must report missing support; some sources may require a companion plugin or adapter.
- Themes, plugins, custom role definitions, arbitrary options, and plugin-specific database tables are not automatically migrated by the standard content APIs. Plugin-dependent content needs compatible destination support.
- Destination IDs may differ from source IDs. Imports must map relationships and explicitly resolve username, email, slug, and role conflicts rather than silently overwriting existing data or elevating permissions.
- This is content and user migration, not a complete site clone. WooCommerce orders, theme settings, and other plugin-specific data require separately scoped adapters.

## Data handling

The repository is public. Credentials, user exports, migration state containing personal information, database dumps, and uploaded site media must remain outside version control. Logs and reports must redact credentials and avoid unnecessary personal data.

## Implementation milestones

1. Define installation configuration and provision two isolated fresh WordPress instances.
2. Implement authenticated capability discovery and export preflight.
3. Implement user, taxonomy, media, and content imports with durable ID mappings.
4. Add resume, conflict handling, verification, and migration reports.
5. Validate with synthetic fixtures, then use Birra La Fortezza as the first real migration test with explicit source and destination configuration.

## References

- [WordPress REST API reference](https://developer.wordpress.org/rest-api/reference/)
- [REST API authentication](https://developer.wordpress.org/rest-api/using-the-rest-api/authentication/)
- [Users API](https://developer.wordpress.org/rest-api/reference/users/)
- [Media API](https://developer.wordpress.org/rest-api/reference/media/)
- [REST support for custom content types](https://developer.wordpress.org/rest-api/extending-the-rest-api/adding-rest-api-support-for-custom-content-types/)
- [Exposing additional fields](https://developer.wordpress.org/rest-api/extending-the-rest-api/modifying-responses/)
