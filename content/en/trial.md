---
title: "The Mt. Gox Trial — BFE001"
description: "The full story of the Mt. Gox criminal case — arrest, charges, detention, trial, verdict, and appeals in the Japanese legal system."
---

<!-- section: $AUG015 "Background" -->

When Mt. Gox filed for bankruptcy on February 28, 2014, approximately 850,000 bitcoins were missing — drained over years by external hackers through compromised keys and transaction malleability exploits. Two weeks later, 200,000 BTC were located in cold wallets that had been set aside separately. The missing coins were the result of a sustained external theft, not internal fraud — a fact that would take nearly a decade and multiple international investigations to fully establish.

The criminal case against me, however, was not about the missing bitcoins. The prosecution never charged me with stealing them. Instead, the charges centered on how I had managed the exchange's finances during its operation — specifically, an automated trading mechanism and fund transfers between company and personal accounts.

<!-- section: $AUG015 "Arrest — August 1, 2015" -->

At 5:00 AM on August 1, 2015, officers from the Tokyo Metropolitan Police Cyber Crime Division arrived at my home and placed me under arrest. The initial charge was **私電磁的記録不正作出・同供用** (unauthorized creation and use of private electromagnetic records, Article 161-2 of the Penal Code). The allegation: that I had accessed the Mt. Gox trading system and manipulated data to inflate my own account balance.

I was taken to **Manseibashi Police Station** (万世橋警察署), near Akihabara — a small police station that would be my home for the next four and a half months.

### Re-arrest — August 21, 2015

Twenty days later, before the initial detention period expired, I was **re-arrested** on a second charge: **業務上横領** (embezzlement in the course of business). The allegation was that I had embezzled approximately ¥321 million (~$2.6 million) of customer deposits from the Mt. Gox bank account.

In the Japanese system, re-arrest (再逮捕) is a common prosecution tactic. Each arrest resets the detention clock, allowing the police to hold a suspect for up to 23 days before they must either charge or release. By re-arresting on new charges, the prosecution can extend pre-trial detention indefinitely — a practice that has drawn international criticism as **"hostage justice"** (人質司法).

### Indictment — September 11, 2015

The Tokyo District Public Prosecutors Office formally indicted me (起訴) on the embezzlement charge.

### Third Arrest — Late October 2015

I was arrested a third time on additional **業務上横領** charges — alleging that on three separate occasions between September and December 2013, I had transferred funds from the company bank account to my personal account, totaling approximately ¥20 million.

### Transfer to Kosuge — December 16, 2015

After four and a half months at Manseibashi, I was transferred to the **Tokyo Detention House** (東京拘置所) in Kosuge (小菅) — a large facility in northeastern Tokyo that houses pre-trial detainees and convicted prisoners. This would be my home for the next seven months.

Conditions at Kosuge: a solitary cell of roughly seven square meters, lights that never fully turned off, daily interrogations that sometimes lasted up to eight hours. I lost approximately 35 kilograms during my detention.

<!-- section: $AUG015 "Fighting from Inside" -->

In January 2016, I obtained permission to have a calculator — a rare exception for detainees. Armed with this and the 20,000+ pages of case documents the prosecution had disclosed, I spent weeks cross-checking their financial claims against the company's actual accounting records.

What I found undermined the core of their embezzlement case. The prosecution's accounting of the transfers between my personal account and the company account was incomplete — they had counted the transfers out but not the transfers in. Mt. Gox's revenue in 2013 had exceeded ¥3.5 billion, and the funds I had moved were properly recorded as loans by the company's accountants, with corresponding repayments. I had also paid company expenses using my personal credit card, creating a legitimate loan/reimbursement relationship.

When I presented these calculations, the prosecution had to revise their approach. Their original theory of straightforward embezzlement was no longer tenable.

<!-- section: $AUG015 "Release on Bail — July 14, 2016" -->

On July 14, 2016 — after 11 months and 13 days in detention — I was released on bail of ¥10 million (~$94,500). Family, friends, and supporters pooled the funds.

Bail for foreign nationals in Japan is exceptionally rare. The prosecution's willingness to not oppose bail was in part a consequence of my having dismantled their original embezzlement theory from inside detention. Bail conditions included a prohibition on leaving Japan and a ban on contacting approximately 30 people connected to Mt. Gox.

I had entered detention weighing over 100 kg. I left weighing roughly 65 kg.

<!-- section: $AUG015 "Evolving Charges — 2016–2017" -->

Even after my release on bail, the prosecution continued to reshape the case. Through **追起訴** (supplementary indictments) and **起訴変更** (amendments to existing charges), additional counts were filed and the scope of the allegations was revised during 2016 and 2017.

Most significantly, the prosecution added a charge of **特別背任** (aggravated breach of trust) under **Companies Act Article 960** — a provision that applies specifically to company directors who act against their company's interests. The allegation was that I, as representative director (代表取締役) of MTGOX Co., Ltd., had diverted company funds to unrelated ventures — specifically the acquisition of **Shade 3D**, a 3D graphics software company, for approximately ¥315 million, as well as approximately ¥6 million in personal expenditures.

This charge attacked the same underlying transfers from a different legal angle than the embezzlement charge: where 業務上横領 framed them as stealing customers' money, 特別背任 framed them as a director betraying his duty to the company by misusing corporate resources. This dual-charging approach is a common prosecutorial strategy in Japanese criminal law.

<!-- section: $AUG015 "The Charges at Trial" -->

By the time the trial began, the charges had been refined through this indictment process. Two categories of charges went before the court:

### 1. 私電磁的記録不正作出・同供用 (Unauthorized creation of electromagnetic records)

The prosecution alleged that between February and September 2013, I accessed the Mt. Gox trading system and created false records showing that approximately $33.5 million had been deposited into my account when no real deposits had occurred.

This was the **"Willy Bot"** — an automated trading mechanism that community researchers had independently identified by analyzing Mt. Gox's trading data. Its existence was not in dispute; the question was whether it constituted a crime.

**The prosecution's argument:** The bot created fictitious account balances and was used to purchase bitcoins with money that didn't exist. This was data manipulation — a crime regardless of intent.

**The defense's argument:** The mechanism was an "obligation exchange" (義務交換) — a way to manage a pre-existing BTC debt that I had inherited when I took over the exchange from Jed McCaleb. The process had been suggested by Jed himself. In late 2010, before my takeover, 80,000 BTC had been stolen and sent to what became the infamous `1Feex` address. This created a deficit on Mt. Gox's books. As Bitcoin's price rose, the gap between what the exchange owed users in BTC and what it actually held widened unpredictably. The automated system converted this BTC-denominated debt into USD-denominated debt — it did not increase the company's total debt, it simply stabilized it by moving from a volatile asset to a stable one. The defense argued this was a protective measure to keep the exchange solvent for its users, not a fraud.

### 2. 業務上横領 (Embezzlement)

The prosecution alleged that between September and December 2013, I transferred approximately ¥341 million of customer deposits from Mt. Gox's bank account to my personal account for personal use.

**The defense's argument:** These were legitimate transactions within a loan/reimbursement relationship. I had frequently paid company expenses from my personal credit card, and the company repaid these through bank transfers. The amounts were recorded by certified accountants. Mt. Gox lacked a formal system for executive expense reimbursement, so transactions flowed in both directions between personal and company accounts — but they netted out.

<!-- section: $AUG015 "Trial — July 2017 to March 2019" -->

The trial opened on **July 11, 2017** at the Tokyo District Court before Judge **Nakayama Tomoyuki** (中山大行). The courtroom seated only 39 people; over 100 journalists and observers competed via lottery for 21 public seats.

I pleaded not guilty to all charges.

The trial extended over multiple hearings across nearly two years. The prosecution presented evidence and called witnesses; the defense challenged the financial analysis and argued that the prosecution fundamentally misunderstood how a cryptocurrency exchange operated.

**December 12, 2018** — The prosecution delivered its closing arguments and requested a sentence of **10 years imprisonment** (懲役10年).

**December 27, 2018** — I made my final statement to the court, maintaining my innocence while apologizing for the bankruptcy and its impact on users.

<!-- section: $AUG015 "Verdict — March 15, 2019" -->

Judge Nakayama delivered the verdict on March 15, 2019.

<div class="register-block">
<div class="reg-title">Verdict</div>
<div class="reg-row"><span class="reg-field">私電磁的記録不正作出</span><span class="reg-val"><span class="highlight">Guilty</span> — manipulation of electromagnetic records</span></div>
<div class="reg-row"><span class="reg-field">業務上横領</span><span class="reg-val">Not Guilty — embezzlement</span></div>
<div class="reg-row"><span class="reg-field">特別背任</span><span class="reg-val">Not Guilty — aggravated breach of trust</span></div>
<div class="reg-row"><span class="reg-field">Sentence</span><span class="reg-val">懲役2年6月、執行猶予4年 — 2.5 years, suspended for 4 years</span></div>
<div class="reg-row"><span class="reg-field">Prosecution request</span><span class="reg-val">10 years imprisonment</span></div>
</div>

On the data manipulation charge, the judge ruled that inflating account balances — regardless of intent — constituted unauthorized creation of electromagnetic records. The court found the conduct "eroded the credibility of crypto exchanges" and that there was "no justification for such an abuse of information."

On the embezzlement charge, the judge ruled in my favor. The transfers between personal and company accounts constituted a loan relationship, not theft. The court noted that Mt. Gox "lacked an efficient accounting system for when company executives borrowed money from the company," making it impossible to establish that the transfers were embezzlement.

On the aggravated breach of trust charge (特別背任), the court found insufficient evidence of intent to damage the company. Regarding the Shade 3D acquisition specifically, the judge ruled that the investment could be viewed as potentially profitable for the company — it was a reasonable business decision for a director to make, not a betrayal of fiduciary duty.

The acquittal on the primary charges of embezzlement and breach of trust — in a system where the conviction rate after indictment exceeds 99% — was as close to vindication as the Japanese legal system typically allows. The prosecution did not appeal the acquittals.

The suspended sentence meant no prison time, provided no new offenses were committed during the four-year suspension period.

<!-- section: $AUG015 "Appeals" -->

I appealed the data manipulation conviction, arguing that the Tokyo District Court had not fully considered the defense arguments regarding the nature and purpose of the trading mechanism.

**June 11, 2020** — The **Tokyo High Court** (東京高等裁判所) rejected the appeal and upheld the original conviction and sentence.

**January 27, 2021** — The **Supreme Court** (最高裁判所) dismissed the final appeal. The conviction became final (確定): 懲役2年6月、執行猶予4年.

The suspended sentence expired without incident in March 2023.

<!-- section: $AUG015 "The Real Thieves" -->

While the criminal case focused on my management of the exchange, a parallel investigation — driven largely by independent researchers — worked to identify who actually stole the bitcoins.

**Kim Nilsson** and the **WizSec** team spent years tracing the stolen coins through the blockchain, following transactions from Mt. Gox wallets to addresses controlled by the BTC-e exchange.

**July 25, 2017** — Two weeks after my trial began, Greek police arrested **Alexander Vinnik** at a beach resort, acting on a US warrant. Vinnik was charged as co-administrator of BTC-e, accused of facilitating over $4 billion in criminal transactions. The US indictment explicitly linked him to the theft of bitcoins from Mt. Gox.

**June 9, 2023** — The US Department of Justice publicly named **Alexey Bilyuchenko** and **Aleksandr Verner** — two Russian nationals accused of stealing 647,000 BTC from Mt. Gox between September 2011 and May 2014. Bilyuchenko was also charged as co-administrator of BTC-e alongside Vinnik. After nine years, the thieves had names.

<!-- section: $AUG015 "Civil Rehabilitation and Repayments" -->

In parallel with the criminal case, the question of what happened to the creditors' money followed its own path.

Under Japanese bankruptcy law, creditor claims were valued in yen at the date of filing — February 2014, when Bitcoin traded around $400. By 2017–2018, Bitcoin's price had risen to $10,000–$20,000, meaning the approximately 200,000 BTC recovered in cold wallets were now worth far more than the total claims. Under bankruptcy rules, the surplus would go to the shareholder — which was me, through Tibanne.

In April 2018, I posted an open letter on Reddit: I did not want that money. I advocated for **civil rehabilitation** (民事再生), a process under Japanese law where creditors would receive their fair share at current market value rather than being capped at the 2014 yen valuation.

In June 2018, the Tokyo District Court approved the conversion from bankruptcy to civil rehabilitation.

In 2024 — over ten years after the bankruptcy filing — creditor repayments finally began. Bitcoin and Bitcoin Cash distributions started flowing to creditors through designated exchanges. The process that began with 850,000 missing bitcoins was reaching its conclusion.
