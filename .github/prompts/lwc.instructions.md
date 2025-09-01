---
description: "Concise, actionable guidelines for building, reviewing, and tuning Lightning Web Components (LWC). Optimized for Copilot use during coding and PR reviews."
applyTo: "**/lwc/**"
---

# 📝 LWC Code Guidelines (Compact)

> Use this as a “while-coding” checklist. Defaults: modern JS, properties-down/events-up, LDS/UI API first, Apex cacheable, progressive disclosure.

## 1) Structure & Conventions
- **Prefer Lightning Base Components** (`lightning-*`) for SLDS, a11y, and platform optimizations over hand-rolled HTML.  
- **Folder/tag naming:** kebab-case; folder name = component tag root. Keep public API minimal; expose only with `@api`. (Properties down, events up.)  
- **Modern JS only:** `const/let`, arrow functions, modules, Promises, classes, array/object methods.  
- **Imports hygiene:** group/import platform features (Apex, labels, static resources, permissions) consistently; consider a `labels.js`.  

## 2) Data Access & Caching
- **LDS/UI API before Apex.** Fetch only required fields; avoid `getRecordUi` unless metadata is truly needed (payload can be 100–1000× larger).  
- **Cache when safe:** `@AuraEnabled(cacheable=true)` on idempotent Apex; leverage LDS client cache across components.  
- **Minimize round-trips:** share data via service components; pass data between components rather than re-querying.  
- **Queries:** select only used fields, set `LIMIT`, add pagination for large sets.  

## 3) Rendering & Performance
- **Progressive disclosure:** lazy-instantiate with App Builder tabs/utility bar/actions when possible; otherwise use `if:true|false`.  
- **Lists:** always provide a stable `key` for `for:each`/`iterator`; paginate or virtualize—no “infinite” unbounded lists.  
- **Events:** keep handlers minimal; attach only what you need (each handler has overhead). Use `CustomEvent` for child→parent.  
- **Base components & images:** prefer base components; optimize images and avoid oversized static assets.  
- **Debug mode:** enable in dev to get framework warnings (slower!)—never on in production.  

## 4) State, Mutations & DOM
- **Make invalid state hard to represent:** model components as small state machines; derive with getters instead of duplicating flags.  
- **Don’t mutate read-only data** from `@api` or `@wire` (LDS returns shared, frozen objects). Instead shallow-copy via spread or `Object.assign`. Avoid `JSON.parse(JSON.stringify(...))`.  
- **Avoid manual DOM manipulation** (use template bindings/getters).  

## 5) Third-Party Libraries
- **Challenge the need first** (modern JS may replace it). If needed, prefer **ESM** modules over UMD; otherwise load via static resource with `platformResourceLoader`.  
- **Size guardrail:** if a lib is > ~30 KB min+gzip, think twice; check Locker compatibility (test in Locker Console).  

## 6) Security & Communication
- **Parent↔child:** parent sets child `@api` props or calls child `@api` methods; child dispatches `CustomEvent` up. Use **Lightning Message Service** for cross-DOM, not ad-hoc pub/sub.  
- **Sanitize HTML:** render dynamic HTML with safe components (e.g., `lightning-formatted-rich-text`) or a vetted sanitizer. (Locker constraints apply.)  

## 7) Testing & Review
- **Jest black-box tests:** don’t create `@api` just for tests; test via inputs (props/events) and observable outputs (DOM).  
- **Use a lightweight, repeatable code review checklist** (info block, security, performance, limits, migration to LWC, etc.).  

---

## Quick Patterns (copy-ready)

**Wire LDS with minimal fields**
```js
import { getRecord, getFieldValue } from 'lightning/uiRecordApi';
import NAME_FIELD from '@salesforce/schema/Account.Name';

@wire(getRecord, { recordId: '$recordId', fields: [NAME_FIELD] })
account;

get name() { return getFieldValue(this.account.data, NAME_FIELD); }
```

**Cacheable Apex**
```apex
public with sharing class AccountCtl {
  @AuraEnabled(cacheable=true)
  public static List<Account> top(Int limitSize) {
    return [SELECT Id, Name FROM Account ORDER BY Rating NULLS LAST LIMIT :Math.min(limitSize, 200)];
  }
}
```
```js
import top from '@salesforce/apex/AccountCtl.top';
@wire(top, { limitSize: 50 }) accounts;
```

**Lazy instantiate heavy tab**
```html
<lightning-tabset>
  <lightning-tab label="Details">...</lightning-tab>
  <lightning-tab label="Charts" if:true={showCharts}>...</lightning-tab>
</lightning-tabset>
```

**Stable keyed list + pagination hint**
```html
<template for:each={rows} for:item="r">
  <c-row key={r.id} row={r}></c-row>
</template>
<!-- load-more or paginator here -->
```

**Child → Parent event**
```js
// child.js
this.dispatchEvent(new CustomEvent('select', { detail: { id } }));
```
```html
<!-- parent.html -->
<c-child onselect={handleSelect}></c-child>
```

---

## 📚 References
- [Drive Consistency and Grow Developer Skills with a Developer Best Practices Checklist (2022)](https://developer.salesforce.com/blogs/2022/01/drive-consistency-and-grow-developer-skills-with-a-developer-best-practices-checklist)  
- [Lightning Web Components Performance Best Practices (2020)](https://developer.salesforce.com/blogs/2020/06/lightning-web-components-performance-best-practices)  
- [Step Up Your LWC Skills – Part 1 (2020)](https://developer.salesforce.com/blogs/2020/10/step-up-your-lwc-skills-part-1)  
- [Step Up Your LWC Skills – Part 2 (2020)](https://developer.salesforce.com/blogs/2020/10/step-up-your-lwc-skills-part-2)  
