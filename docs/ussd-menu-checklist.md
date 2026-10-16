# USSD Menu Design Checklist

Product and ops checks before you change a live mobile money USSD tree. Replace timeouts and fee examples with your network values.

## Before a menu change goes live

- [ ] Customer and agent entry points still distinct
- [ ] Confirm screen shows destination, amount, and fee before PIN
- [ ] Back (`0`) and home behaviour match what laminated job aids say
- [ ] Deep-link short codes (`*123*2#` style) listed with deprecation dates if renumbered
- [ ] Session timeout tested on the slowest handset class you still support
- [ ] Success screen includes a short reference the SMS will also carry

## After release (first 48 hours)

- [ ] Drop-off by `menu_path` compared to the previous week
- [ ] Bad-PIN and timeout rates by screen_id
- [ ] Agent float cash-in / cash-out completion time (median screens and seconds)
- [ ] Support tickets tagged "wrong menu" or "fee surprise"

## Copy rules that save support time

1. Prefer verbs customers already use in shops ("Send money", "Cash out") over internal product names.
2. Put currency and amount on the same line.
3. Never ask for PIN on a screen that still might change the amount.
