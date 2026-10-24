# eiwa-i18n

Minimal YAML-backed i18n for Eiwa. No dependencies (std only) —
works standalone; framework wiring is just string plumbing.

## Usage

```ei
import { I18n } from "i18n.i18n"

I18n.loadDir("locales")

I18n.t("auth.login.title", "pt-BR")      // "Entrar"
I18n.tp("hello", "es", ["name" of "Leo"]) // "Hola, Leo"
I18n.plural("items", "pt-PT", 3)         // "3 convites" (via pt)
```

## Dictionary schema (`locales/<tag>.yaml`)

```yaml
auth:
  login:
    title: "Entrar"          # t("auth.login.title", "pt-BR")
items:
  one: "{count} convite"     # plural("items", locale, 1)
  other: "{count} convites"  # plural("items", locale, 5)
```

## Lookup fallback

Regional → base → English: `pt-PT` → `pt` → `en`.
Missing keys return the key itself (never breaks rendering).

## Locale resolution

```ei
// Priority: explicit choice > account > cookie > Accept-Language > default.
// Every source is a plain String; pass "" to skip one.
I18n.resolveLocale(queryLang, accountLang, cookieLang, acceptHeader, ["pt-BR", "pt", "en", "es"], "en")
```

### Appendix: arest wiring (optional)

```ei
fun ApplicationCall.locale(accountLocale: String = ""): String {
    return I18n.resolveLocale(query("lang") ?: "", accountLocale, cookie("lang") ?: "", header("Accept-Language") ?: "", ["pt-BR", "pt", "en", "es"], "en")
}
// usage: I18n.t("auth.login.title", call.locale())
```

## Test

```sh
eiwa test
```
