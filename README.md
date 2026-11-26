# Spacekoncept Core

Business logic for spacekoncept.com: hardware quotations with AI company check, delivery fees, enquiry forms, product data and admin settings. Works with any theme; the Spacekoncept theme provides the design.

- **Version:** 0.6.3
- **Requires:** WordPress 6.6+, PHP 8.2+, WooCommerce
- **Text domain:** `spacekoncept`

The theme and this plugin are separate on purpose: the theme decides how things look, the plugin decides how the shop works. Switching theme does not lose quotations, prices, settings or product data.

---

## Features

### Hardware by quotation
- Products in the **Hardware** category (and its sub-categories) cannot be added to the cart. Their prices are hidden and the button reads **Request a Quote**. This is enforced on the server, not only hidden on screen.
- All other products (Murphy beds, tables, mattresses, storage and so on) stay normal shop products with visible prices and checkout.
- Hardware product pages show a quote form protected by Cloudflare Turnstile.
- Each request is saved as a **Hardware quotation** (private, staff only).

### Quote pricing (Individual / Company)
- Every hardware product and variation has two private prices: **Individual** and **Company**.
- Two editable email templates, one per pricing type, with placeholders such as `{customer_name}`, `{product}` and `{payment_link}` (HitPay pay-now link).
- Each quotation can be edited and re-sent from its screen.
- **Send quote automatically:** when a price is set for the customer's pricing type, the quote is emailed right after they submit. Otherwise the team gets a "needs price" email.

### AI company website check
The customer can enter their company website on the quote form. The plugin checks it in the background and picks the pricing type:

| Situation | Result |
|---|---|
| No website given | Individual price |
| Website is a trade business (interior design, renovation, contractor, carpentry or furniture maker, construction) and the company name matches | Company price |
| Website is not a trade business | Individual price |
| Website unreachable, social media or marketplace link, name does not match, same website used by another customer email in 30 days, AI error, not set up, or daily limit (150) reached | **Needs review**: nothing is sent to the customer, the team is emailed |

- Runs through OpenRouter. Default model: `openai/gpt-6-luna`, with reasoning off.
- Results are cached per website for 30 days.
- Staff can re-run the check from the quotation screen.
- The website text is treated as untrusted: the AI is told to ignore any instructions inside it.

### Product add-ons
- Optional extras on simple and variable products, which can be switched on or off per product.
- Hardware add-ons have Individual and Company prices. Furniture add-ons have one price and are added to the cart.

### Delivery fees
- A WooCommerce shipping method: $50 under $500, free from $500, and $100 per Murphy wall bed. Amounts are editable in settings.

### Forms
- Shortcode `[spacekoncept_form]` with four forms:
  - Contact
  - Showroom appointment
  - Customization enquiry
  - Newsletter
- Submissions are saved privately in **Form submissions** and emailed to the team.
- Hidden spam trap and a per-visitor rate limit.

### Trade buyers
- Trade access and trade application pages for carpenters, contractors and designers.
- Applications are private and reviewed by staff.

### Product data and content
- Extra product fields (**Spacekoncept Details**):
  - Width, height, depth and unit
  - Specification notes and image
  - Good to know
  - Recommended mattress size
  - Assembly / installation
  - Delivery note
  - Warranty duration, coverage, exclusions and policy link
- **Material** taxonomy for products.
- **Hero slides** for the homepage: image or video, poster image, text and buttons.
- Business contact details with shortcode `[spacekoncept_business_contact]`.
- Showroom booking rules and notification settings.

### SEO and housekeeping
- Product schema:
  - Hardware (quote-only) products and products with no price or reviews get no Product schema, because Google rejects a Product with no price, rating or review.
  - This also applies to Rank Math's schema output.
- Privacy links fall back to `/privacy-policy/` if the page set in Settings → Privacy is missing.
- The old bilingual (Chinese) URLs with `?lang=` redirect to the English page.

### Developer tools
- WP-CLI: `wp spacekoncept migrate catalog` (controlled catalog and media migration).

---

## Admin screens

| Where | What |
|---|---|
| WooCommerce → Hardware quote settings | Team notification emails, auto-send, email templates, AI check status |
| Hardware quotations | All quote requests, pricing, website check, re-check, send quote |
| Products → edit product | Individual and Company prices, add-ons, Spacekoncept Details |
| Hero slides | Homepage slider content |
| Settings → Spacekoncept contact / forms / shipping / showroom booking | Business details, form recipients, delivery fees, booking rules |
| WooCommerce → V3 commerce candidate | Hardware quote switch and Turnstile site key |
| WooCommerce → COM store direction | Trade access setup and trade application review |

---

## Configuration (wp-config.php)

Secrets live only in `wp-config.php`, never in the database or admin screens. Add these above `/* That's all, stop editing! */`:

```php
// Required for the quote form and trade application (Cloudflare Turnstile secret key).
define('SPACEKONCEPT_COM_TURNSTILE_SECRET', 'your-turnstile-secret');

// Required for the AI company website check (OpenRouter API key).
define('SPACEKONCEPT_OPENROUTER_KEY', 'sk-or-...');

// Optional. Default: openai/gpt-6-luna
define('SPACEKONCEPT_OPENROUTER_MODEL', 'openai/gpt-6-luna');

// Optional. none (default) | minimal | low | medium
define('SPACEKONCEPT_OPENROUTER_REASONING', 'none');
```

- **Turnstile site key** (the public one) is entered in WooCommerce → V3 commerce candidate.
- **Without the OpenRouter key,** every quote with a website is held for review.

---

## Installation

1. Plugins → Add New → Upload Plugin → choose `spacekoncept-core-x.x.x.zip`.
2. If the plugin is already installed, choose **Replace current with uploaded**.
3. Activate. WooCommerce must be active first.
4. Add the `wp-config.php` constants above.
5. Check WooCommerce → Hardware quote settings: the team emails must be correct, and the AI check should show "set up".
6. Clear the page cache.

## Testing a release

- Send one quote from a hardware product page with a trade company website, and one with a shop or blog website. Check both verdicts in Hardware quotations.
- Add a furniture product to the cart and check the delivery fee at checkout. Do not pay.
- Send the contact form and check the team email arrives.

---

## Changelog (recent)

| Version | Changes |
|---|---|
| 0.6.3 | AI check default model changed to GPT-6 Luna with reasoning off; automatic retry with low reasoning; clear error when an AI answer is cut off |
| 0.6.2 | Company field label "Company name (optional)" |
| 0.6.1 | Product schema removed for hardware and for products with no price or reviews, including Rank Math output |
| 0.6.0 | AI company website check replaces the customer-type dropdown; company website field on the quote form |
| 0.5.5 | Privacy Policy link fallback |
| 0.5.4 | Product add-ons for simple and variable products |

## Credits

Developed by Anis Abulkhaer for FJ & Joseph Pte Ltd (Spacekoncept), Singapore.
