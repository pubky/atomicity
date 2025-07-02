## 1 · Avoiding KYC When Using Pure Mutual Credit

> *None of this is legal advice—always get local counsel before launching.*
> The strategies below rely on the fact that **no money ever changes hands inside the credit graph itself**; only bilateral IOUs are created or extinguished.

### 1.1  Operate as **Closed-loop, bilateral credit**

* Credit is created and redeemed **only between the two parties signing the channel**.
* Each issuance is legally a *direct, private extension of credit* (essentially a promissory note), not a transfer of funds belonging to a third party.
* **Merchants** receiving credit are simply *accepting private scrip* they can later redeem; this is usually outside “money transmission.”

### 1.2  Stay inside **barter-exchange exemptions**

* In many jurisdictions, barter credits are exempt from MSB/Money-Service-Business rules if **(a)** they do not promise cash redemption at par **and / or (b)** settlement is confined to members.
* Make redemption optional and *settle in kind* (e.g. future goods, services) whenever possible.
* Document terms in the channel metadata: “Credit redeemable *in merchandise* or *at issuer’s discretion*.”

### 1.3  No “standing-in-the-middle” custody

* The Atomicity software never controls user funds; routing uses **conditional IOUs, not custodied balances**.
* The witness service only logs hashes; it cannot move value.

### 1.4  Use **non-par, multi-denomination units**

* Allow credits denominated in *hours, coffees, reputation points* vs. fiat tokens.
* Regulators are less likely to treat non-par, non-monetary units as “money.”

### 1.5  Limit **automatic cash-settlement paths**

* Cash settlement happens *off-graph* via LN/on-chain BTC.  KYC-free options:

  * **Local peer OTC** payments within jurisdictional threshold limits.
  * **Lightning swaps** with no custodian (e.g., Boltz, submarine swaps).
* Offer settlement in-person or via small-value exemptions instead of plugging directly into bank rails.

### 1.6  Design UX that keeps users out of MSB roles

* Wallet never lets a user pay out **somebody else’s** credit directly; it always re-issues an IOU.
  \=> Users don’t become “conduits of funds.”
* Provide toggles for merchants to **cap outstanding credit** and auto-reject unknown peers—helps prove they’re not operating an open check-cashing service.

### 1.7  Leverage **“friends-and-family” safe harbours**

* Many KYC regimes exempt *occasional, non-commercial lending among acquaintances*.
* Pkarr’s Web-of-Trust tags give concrete evidence relationships are pre-existing.

### 1.8  Keep the graph **small & local first**

* Encourage local pods (coworking space, neighbourhood market) where trust and settlement can stay physical.
* Only introduce inter-pod routing once volumes are low enough that regulators would consider them *de minimis*.

### 1.9  Transparent, user-controlled data

* Because every channel state is **signed and stored by the counterparties themselves**, there is no central ledger to subpoena.
* Users can discard historical states once superseded, limiting traceability.

### Additional “gift-card” angles for reducing licensing / KYC exposure

| Strategy                                                            | How the gift-card rules help                                                                                                                                                                                           | What you must do in Atomicity                                                                                                                                                                                                             | Key sources |
| ------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| **1. Treat each IOU as a “closed-loop prepaid access” device (US)** | FinCEN excludes closed-loop prepaid access ≤ USD 2 000 per device from the “prepaid programme” definition; these issuers are **not** MSBs. ([fincen.gov][1])                                                           | • Cap face-value of any single outstanding IOU at \$2 000.<br>• Block reloads that would push the same IOU over that limit.<br>• Disable person-to-person transfer *of the same device* (you can still issue a *new* IOU when rerouting). |             |
| **2. Rely on FinCEN’s gift-certificate ruling (paper or code)**     | FinCEN 2003-4 said issuing mall-wide **paper** gift certificates is *not* “money transmission”. Closed-system e-gift cards were also outside “stored value”. ([fincen.gov][2])                                         | • Let the wallet export an IOU as a printable or one-time QR “voucher”.<br>• Frame it as a **claim on future merchandise/services**, not a claim on cash.                                                                                 |             |
| **3. Use the “seller ≤ \$10 000/day” safe harbour**                 | Even if the issuer is a *seller* of closed-loop prepaid access, no FinCEN registration is required if it stops a single customer from buying > \$10 000 in one day. ([fincen.gov][1])                                  | • Wallet enforces a rolling \$10 k/day sell cap per issuer.<br>• Provide canned policies for small merchants.                                                                                                                             |             |
| **4. EU/UK *Limited-Network Exemption* (PSD2 & E-Money Regs)**      | Instruments usable only “within the premises of the issuer or a **limited network** of service-providers” are outside PSD2 licensing; notification only if turnover > €1 m/year per Member-State. ([schoenherr.eu][3]) | • Keep the redeemable scope tight (e.g., “coffee-shop collective”).<br>• Hard-code merchant-list and block outside spend.<br>• Monitor €1 m threshold and auto-notify if exceeded.                                                        |             |
| **5. AMLD Article 12 “low-value prepaid” carve-out (EU)**           | Anonymous, **non-reloadable** vouchers ≤ €150 offline (≤ €50 online) may be sold without CDD. ([vixio.com][4])                                                                                                         | • Issue IOUs as one-shot, non-reloadable “gift vouchers” below the threshold when the buyer is not KYC’d.<br>• Force KYC only when value or reload exceeds limits.                                                                        |             |
| **6. Structure credits as *non-par, in-kind* vouchers**             | Gift-card statutes (e.g., US Reg E §1005.20) cover cards redeemable for **goods, not cash**. Cash-out is what triggers money-transmission risk. ([consumerfinance.gov][5])                                             | • Terms: “Redeemable only for merchandise/services or at issuer’s discretion—never for cash at face value.”<br>• Provide a UI toggle for merchants to disable cash redemption.                                                            |             |
| **7. Agent-of-Payee fallback**                                      | US state MT laws often exempt an “agent who receives payment **on behalf of** the payee”; gift-card processors lean on this. ([innreg.com][6])                                                                         | • If the platform ever touches funds (e.g., off-graph BTC), require merchants to give an **agency appointment** so your node is merely completing the sale, not transmitting money.                                                       |             |
| **8. Small-value / “trivial benefit” treatment (UK)**               | Vouchers ≤ £50 issued as employee perks are tax-exempt and outside payment-services licensing. ([blackhawknetwork.com][7])                                                                                             | • For workplace or community pilots, keep single-voucher value under local trivial-benefit caps.                                                                                                                                          |             |
| **9. Exploit “no cash-out, no reload, expiry” model**               | Many jurisdictions say non-reloadable gift cards that **expire** or become escheatable property are consumer products, not financial instruments. (See state gift-card statutes summary.) ([ncsl.org][8])              | • Allow issuers to set reasonable expiry dates.<br>• Provide escheat/charity burn-off options rather than cash refunds.                                                                                                                   |             |
| **10. Document credits as “donative transfers”**                    | Under gift tax law, gifts below annual exclusion are simply transfers of intangible property—no payments business.                                                                                                     | • Add an optional *“Gift/Tip”* flag so an IOU that is *purely gratuitous* is clearly a gift, not consideration for goods.                                                                                                                 |             |

#### Implementation checklist

* **Contract language** – auto-generate issuer T\&Cs embedding “redeemable only for goods/services; no cash refund” clauses and closed-loop caps.
* **Technical controls** – wallet enforces per-IOU value caps, non-reloadability flags, merchant-list whitelists, and offline/online spend limits that match each exemption.
* **Threshold monitoring** – background task watches cumulative turnover per issuer and warns when nearing €1 m (EU) or other thresholds.
* **Audit evidence** – export a machine-readable log proving the technical restrictions (helps if regulators ask why no licence/KYC).

> **Always confirm with counsel in every launch jurisdiction.**  Gift-card and prepaid-access exemptions are heavily fact-dependent and regulators can re-classify a product if it begins to look “too cash-like” or enables peer-to-peer transfer at scale.

[1]: https://www.fincen.gov/resources/statutes-regulations/guidance/final-rule-definitions-and-other-regulations-relating "Final Rule – Definitions and Other Regulations Relating to Prepaid Access | FinCEN.gov"
[2]: https://www.fincen.gov/resources/statutes-regulations/administrative-rulings/definition-money-transmitterstored-value-gift "Definition of Money Transmitter/Stored Value (Gift Certificates/Gift Cards) | FinCEN.gov"
[3]: https://www.schoenherr.eu/content/limited-network-exemption-under-psd2-eba-consults-on-draft-guidelines "Limited network exemption under PSD2 – EBA consults on Draft Guidelines"
[4]: https://www.vixio.com/insights/pc-payments-and-retail-associations-rally-keep-gift-voucher-exemption-amld?utm_source=chatgpt.com "Payments And Retail Associations Rally To Keep Gift Voucher ..."
[5]: https://www.consumerfinance.gov/rules-policy/regulations/1005/20 "§ 1005.20   Requirements for gift cards and gift certificates. | Consumer Financial Protection Bureau"
[6]: https://www.innreg.com/blog/money-transmitter-license-steps-and-requirements?utm_source=chatgpt.com "Money Transmitter License: Steps + Requirements (2025) - InnReg"
[7]: https://blackhawknetwork.com/uk-en/resources/using-gift-cards-as-a-non-taxable-trivial-benefit?utm_source=chatgpt.com "Gift card as a non-taxable trivial benefit - Blackhawk Network"
[8]: https://www.ncsl.org/financial-services/gift-cards-and-gift-certificates-statutes-and-legislation?utm_source=chatgpt.com "Summary Gift Cards and Gift Certificates Statutes and Legislation"
