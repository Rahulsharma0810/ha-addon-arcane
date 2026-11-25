# Changelog

## 1.1.1
- Fix docs: default admin username is `arcane`, not `admin`.
- Document that initial admin options apply only on the first start, with a password reset recipe.
- Document API key rules: `arc_` prefix plus 8 or more characters, `X-Api-Key` header, tied to `default_admin_username`.
- Encryption key: must be 32 or more characters and must not change after first start. The add-on now refuses to start on a mismatch instead of failing at login.

## 1.1.0
- Add Arcane icon and logo.
- Add configuration descriptions and examples to the add-on Configuration tab.
- Timezone now follows the Home Assistant timezone unless overridden.
- Expanded documentation.

## 1.0.0
- Initial release, packaging Arcane v3.1.1.
