# Mobile Money USSD Menu Design

How a **mobile money platform** structures USSD menus for customers and agents so people finish cash-in, cash-out, and balance checks without getting lost on a feature phone. Written for operators evaluating [EVD System](https://evdsystem.com/) by [MoboGage](https://evdsystem.com/about-mobogage/).

---

## What USSD menu design covers

**USSD** (Unstructured Supplementary Service Data) lets a handset open a short, session-based dialogue with the operator by dialling a code such as `*123#`. On many African and South Asian networks, USSD is still the primary way customers and agents move money when smartphones or data are unreliable.

Menu design is the craft of ordering screens, labels, and confirmations so the most common jobs take the fewest keypresses, and so a wrong digit does not silently send money to the wrong place.

## Why menu shape matters

A deep tree with clever labels looks tidy on a whiteboard and fails in a noisy shop. Agents remember short codes; customers abandon sessions that ask for pin, amount, then pin again without a clear confirm. Session timeouts on the signalling path are often 30–180 seconds, so every extra screen burns the clock.

Design that holds up in the field usually:

- Puts balance, send money, and cash-out on the first screen for customers.
- Gives agents a separate short code or a first-screen branch so retail flows stay short.
- Confirms amount, fee, and destination MSISDN on one screen before PIN.
- Offers a stable "0 = back / * = main" convention and sticks to it.

## A menu tree that has worked

```
*123#  (customer)
  1 Balance
  2 Send money
  3 Cash out (agent)
  4 Buy airtime
  5 PIN / settings
  0 Exit

*123*8# or agent code  (agent)
  1 Customer cash-in
  2 Customer cash-out
  3 My float
  4 Mini statement
```

**Customer send-money path (illustrative depth):** destination MSISDN → amount → fee+confirm → PIN → success with reference.

**Agent cash-in path:** customer MSISDN → amount → confirm → agent PIN → print/SMS receipt reference.

Keep agent and customer PINs in separate namespaces so a leaked customer PIN cannot authorise float moves.

## Worked example (figures are illustrative)

An agent in a peri-urban shop dials the agent code. Float shows **12,400.00** units. A customer wants to cash in **2,000.00**. Screens:

1. `1` Customer cash-in  
2. Enter MSISDN `07XXXXXXXX`  
3. Amount `2000`  
4. Confirm: credit customer **2,000.00**, debit agent float **2,000.00**, fee **0.00** to customer (**illustrative**; some schemes charge the agent)  
5. Agent PIN  
6. Success: `Ref A9F2C1`, new float **10,400.00**

Total screens after the root: five. If the design had asked for customer PIN before amount, then re-asked after fee reveal, session drops on older handsets would climb.

A customer send of **500.00** to another MSISDN that shows fee **10.00** (**illustrative**) on the confirm screen cuts "I did not know there was a fee" disputes.

## Fields and session data to keep

| Field | Why |
|-------|-----|
| `session_id`, `msisdn` | Tie screens to one signalling session |
| `menu_path` | e.g. `2>msisdn>amount>confirm` for drop-off analysis |
| `screen_id`, `latency_ms` | Find slow hops |
| `outcome` | success, timeout, cancel, bad PIN |
| `txn_ref` | Printed on agent receipt and SMS |
| `channel=USSD` | Separate from app/API in reports |

## Things that go wrong

**One menu for everyone.** Agents and customers need different first screens. Stuffing both into one list forces agents through customer noise every time.

**PIN before confirm.** People type PIN, then see a surprising fee, then cancel—or worse, accept in confusion. Always confirm amount, fee, and destination, then ask for PIN.

**Unstable short codes.** Changing `*123*2#` deep links without a long deprecation period breaks laminated agent job aids in shops.

A checklist for product and ops is in [docs/ussd-menu-checklist.md](./docs/ussd-menu-checklist.md).

---

## FAQ

**What is USSD menu design for mobile money?**
It is how you structure the dial-in screens so customers and agents can complete balance, transfer, cash-in, and cash-out on feature phones inside a short session.

**Should agents share the customer short code?**
Better to give agents a distinct code or a clear first-screen branch. Shared menus slow agents and raise wrong-flow errors.

**Where should PIN sit in the flow?**
After the confirm screen that shows destination, amount, and fee—not before.

**How deep can a USSD tree go?**
Keep common jobs to roughly four to six screens after the root. Every extra hop raises timeout and cancel rates.

**Does EVD System include mobile money as well as vouchers?**
MoboGage's stack includes mobile money and merchant payment alongside the EVD System voucher platform.

---

## Glossary

| Term | Meaning |
|------|---------|
| USSD | Session-based dial-in dialogue over the mobile network |
| Short code | The `*n#` entry point for a service |
| Menu path | Sequence of choices in one session |
| Agent float | Electronic balance the agent uses for cash-in / cash-out |
| Confirm screen | Screen that restates amount, fee, and destination before PIN |
| Session timeout | Network or platform limit on how long the dialogue may stay open |

## Further reading

- [Mobile money platform with merchant payment](https://evdsystem.com/mobile-money-platform-with-merchant-payment/) on evdsystem.com
- [Multi-tier reseller voucher distribution](https://evdsystem.com/evd-software/multi-tier-reseller-voucher-distribution/) on evdsystem.com
- Related repo: [mobile-money-agent-float-rebalancing](https://github.com/iwtglobal/mobile-money-agent-float-rebalancing)

## License

MIT. See [LICENSE](./LICENSE). Educational material only, not legal or regulatory advice.
