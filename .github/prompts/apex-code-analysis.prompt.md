---
mode: agent
---

Run the Salesforce Code Analysis tool to analyze the code provided in the current context.
The tool will provide insights into the code quality, potential issues, and suggestions for improvements.

Example:
```bash
$ sf code-analyzer run --rule-selector apex --target <file1> --target <file2> --output-file apex-scan-results.json
```

The analysis will include:

- Code complexity
- Code coverage
- Best practices adherence
- Security vulnerabilities

Read the result scan file `apex-scan-results.json`. The results will be displayed in a structured json format, such as this example

```json
{
  "violations": [
    {
      "rule": "ApexDoc",
      "engine": "pmd",
      "severity": 4,
      "tags": [
        "Recommended",
        "Documentation",
        "Apex"
      ],
      "primaryLocationIndex": 0,
      "locations": [
        {
          "file": "src/core/crm/classes/schemas/OrderEventSchema.cls",
          "startLine": 1,
          "startColumn": 8,
          "endLine": 106,
          "endColumn": 2
        }
      ],
      "message": "Missing ApexDoc comment",
      "resources": [
        "https://docs.pmd-code.org/pmd-doc-7.14.0/pmd_rules_apex_documentation.html#apexdoc"
      ]
    },
    ...
  ]
}
```

It states the rule that was violated, the severity of the violation, and a message describing the issue. It also provides the file and line number where the violation occurred, along with resources for further reading.

The violations will be categorized by severity, for the different severities perform the following actions:

- **Critical (1)**: These are severe issues that needs manual oversight by a developer to ensure they are addressed correctly. Not allowed to suppress the warnings.
- **High (2)**: These are severe issues that needs manual oversight by a developer to ensure they are addressed correctly. Not allowed to suppress the warnings.
- **Moderate (3)**: Attempt to fix simple issues automatically, but if they cannot be fixed automatically, they need manual oversight by a developer to ensure they are addressed correctly. In some cases, you are allowed to suppress the warnings by a @SuppressWarnings('PMD.<rule_name>') annotation.
- **Low (4)**: Attempt to fix these issues automatically, but if they cannot be fixed automatically, you are allowed to suppress the warnings by a @SuppressWarnings('PMD.<rule_name>') annotation
- **Info (5)**: Attempt to fix these issues automatically, but if they cannot be fixed automatically, you are allowed to suppress the warnings by a @SuppressWarnings('PMD.<rule_name>') annotation
