---
description: "This document provides guidelines for working with Lightning Web Components (LWC) in Salesforce projects. It includes best practices, coding standards, and instructions for creating and managing LWC components."
applyTo: '**/lwc/**'
---

# 📝 LWC Code Guidelines

## 1. General
- **Use Lightning Base Components (`lightning-*`) instead of custom HTML** to inherit SLDS styling, accessibility, and built-in optimisations. <!-- :contentReference[oaicite:0]{index=0} -->
- Name component folders and tags in **lower-case kebab-case**; the folder name must match the `<template>` root tag. <!-- :contentReference[oaicite:1]{index=1} -->
- Expose public properties with the `@api` decorator; keep everything else private. <!-- :contentReference[oaicite:2]{index=2} -->
- **Write modern ES6+ JavaScript** (`const` / `let`, arrow functions, modules) to align with the LWC runtime. <!-- :contentReference[oaicite:3]{index=3} -->
- Prefer **Lightning Data Service** or `@AuraEnabled(cacheable=true)` Apex over imperative calls to reduce round-trips and leverage caching. <!-- :contentReference[oaicite:4]{index=4} -->
- Avoid direct DOM manipulation; express UI through template syntax and reactive getters. <!-- :contentReference[oaicite:5]{index=5} -->

## 2. State Management
- Model each component as a finite **state machine** so invalid states are impossible. <!-- :contentReference[oaicite:6]{index=6} -->
- Derive secondary values with getters instead of storing duplicate tracked fields. <!-- :contentReference[oaicite:7]{index=7} -->
- Group related state into single objects to keep updates atomic and predictable. <!-- :contentReference[oaicite:8]{index=8} -->

## 3. Performance
- **Lazy-load** heavy content with `if:true|false`, dynamic imports, or deferred `<lightning-tab>` panels. <!-- :contentReference[oaicite:9]{index=9} -->
- Provide a **stable `key`** for every element rendered with `for:each` to avoid costly re-renders. <!-- :contentReference[oaicite:10]{index=10} -->
- Batch DOM writes and memoise expensive getters to minimise layout thrashing. <!-- :contentReference[oaicite:11]{index=11} -->
- Keep third-party libraries under **30 KB** minified and confirm Locker compatibility before use. <!-- :contentReference[oaicite:12]{index=12} -->
- Use browser and platform caching wisely; favour LDS cache before custom stores. <!-- :contentReference[oaicite:13]{index=13} -->

## 4. Security & Error Handling
- Sanitize dynamic HTML with `lightning-formatted-rich-text` or DOMPurify before rendering. <!-- :contentReference[oaicite:14]{index=14} -->
- Handle wire and promise rejections; wrap imperative Apex in `try / catch` and surface friendly messages with `reduceErrors`. <!-- :contentReference[oaicite:15]{index=15} -->
- Never store secrets or credentials in component code; use Named Credentials or Protected Custom Metadata. <!-- :contentReference[oaicite:16]{index=16} -->

## 5. Code Examples

### 5.1 Lazy-Loaded Tab
```html
<lightning-tabset>
  <lightning-tab label="Details" value="details">
    <!-- always loaded -->
  </lightning-tab>
  <lightning-tab label="Charts" value="charts" if:true={showCharts}>
    <!-- heavy chart code loads only when needed -->
  </lightning-tab>
</lightning-tabset>
```

### 5.2 Stable Keyed List
```html
<template for:each={items} for:item="item">
  <li key={item.id}>{item.label}</li>
</template>
```

### 5.3 Cacheable Apex Wire
```javascript
import getTopAccounts from '@salesforce/apex/AccountController.getTopAccounts';
@wire(getTopAccounts) topAccounts; // Apex is @AuraEnabled(cacheable=true)
```


