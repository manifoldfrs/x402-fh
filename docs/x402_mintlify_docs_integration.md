

# **X402 Mintlify Docs Integration**

# **Summary**

| Author | [Faris Habib (CTR)](mailto:faris.habib@coinbase.com) |
| :---- | :---- |
| **Date** | Dec 22, 2025 |
| **Go Link** | TBD |
| **Review** | **In progress** |
| **Reviewers** | TBD:		**Not started**        |
| **Additional Links** | [Mintlify Docs](https://mintlify.com/docs) [Mintlify Pricing](https://mintlify.com/pricing) [CDP Mintlify Docs](https://docs.cdp.coinbase.com) [Agent Auto-Update Guide](https://www.mintlify.com/docs/guides/automate-agent) [x402 Current Docs](https://github.com/coinbase/x402/tree/main/docs) |

| Step 1/2 : Design & Development In Progress |
| :---: |

## **Overview**

Technical specification for migrating x402 documentation to Mintlify, hosted at `docs.x402.org`.

Mintlify provides semantic search, AI-assisted doc updates, and Git-based workflows. Coinbase already uses Mintlify for CDP docs, so this extends existing infrastructure.

**Primary driver**: Programmatic documentation updates that stay in sync with code changes across the x402 monorepo (TypeScript, Python, Go, Java SDKs).

## **Use Cases**

* Developers contributing to x402 need documentation that auto-updates when code changes
* Community members searching for x402 concepts using natural language queries
* Maintainers reviewing AI-generated documentation PRs before merging
* SDK users finding language-specific implementation guides

## **Goals**

* Automated doc updates via Mintlify Agent + GitHub Actions
* Semantic search for x402 concepts
* Docs stay in `/docs` folder for unified PRs
* MDX components and theming support
* Reuse Coinbase's existing Mintlify setup from CDP

## **Non-goals**

* Auto-generating API reference docs from docstrings (deferred - test Agent capability first)
* Creating OpenAPI specs for facilitator endpoints (future enhancement)
* Multi-language i18n translations (not needed for initial launch)
* Custom authentication/gated docs (x402 is fully open source)

## **Success Metrics**

* Documentation PRs auto-generated within 24 hours of code changes to main branch
* Semantic search returning relevant results for conceptual queries
* Zero manual intervention needed for routine doc updates
* Docs deploy successfully on every push to main

## **Terminology**

* **Mintlify Agent**: AI system that monitors code repos and proposes documentation updates via PRs
* **AGENTS.md**: Configuration file that customizes agent behavior and code-to-doc mappings
* **Semantic Search**: Vector embedding-based search that understands query intent, not just keywords
* **MDX**: Markdown + JSX - allows embedding React components in documentation
* **i18n**: Internationalization - support for multiple languages (18 letters between "i" and "n")
* **docs.json**: Mintlify configuration file defining navigation, theming, and integrations

## **Summary**

We will integrate Mintlify into the x402 repository using a monorepo configuration pointing to `/docs`. The Mintlify Agent will be configured via GitHub Actions to automatically propose documentation updates when code changes are pushed to main. Semantic search will be enabled automatically. Initial theming will align with x402 branding using the "Maple" or "Mint" theme.

# **Architecture**

## **Design rationales** 

### **Why Mintlify Over Alternatives**

| Feature | **Mintlify** | **GitBook** | **Docusaurus** |
|---------|-------------|-------------|----------------|
| **Pricing** | $0 (Hobby) / $300/mo (Pro) | $65/site + $12/user | Free (OSS, self-hosted) |
| **AI Agent Auto-Updates** | ✅ Built-in (Pro) | ❌ No | ❌ No |
| **Semantic Search** | ✅ Vector embeddings | ✅ Premium only ($65+) | ❌ Manual setup required |
| **Same Repo Support** | ✅ Native monorepo | ✅ Git sync | ✅ Native |
| **Hosting** | Managed | Managed | Self-hosted (requires infra) |
| **Setup Time** | ~1-2 days | ~1-2 days | ~1 week |
| **Coinbase Familiarity** | ✅ Used by CDP | ❌ | ❌ |

**Decision**: Mintlify is selected because:
1. **Programmatic updates**: Only Mintlify offers an AI agent that auto-generates doc PRs from code changes
2. **Existing infrastructure**: Coinbase already uses Mintlify for CDP docs - shared learnings and potential theming reuse
3. **Zero ops burden**: Managed hosting vs. self-hosting Docusaurus
4. **Semantic search included**: No additional setup for AI-powered search

### **Mintlify Agent Workflow**

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Code Change    │────▶│  GitHub Action   │────▶│ Mintlify Agent  │
│  Push to main   │     │  Triggers API    │     │ API Call        │
└─────────────────┘     └──────────────────┘     └────────┬────────┘
                                                          │
                                                          ▼
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Maintainer     │◀────│  PR Created in   │◀────│ Agent Analyzes  │
│  Reviews & Merge│     │  docs repo       │     │ Code + Docs     │
└─────────────────┘     └──────────────────┘     └─────────────────┘
```

### **Semantic Search Architecture**

Mintlify automatically indexes documentation using vector embeddings:

1. **Indexing**: Each doc page → vector embedding (numerical representation of meaning)
2. **Query**: User search → converted to vector
3. **Matching**: Cosine similarity finds semantically related content
4. **Result**: Returns docs matching intent, not just keywords

Example: Query "how to pay for API access" finds "HTTP 402 Payment Flow" even without exact word matches.

### **MDX & i18n Support**

**MDX (Markdown + JSX)**:
- Existing `.md` files work as-is
- Optional rename to `.mdx` enables rich components: `<Callout>`, `<Tabs>`, `<CodeGroup>`, `<Accordion>`
- No migration required for initial launch

**i18n (Internationalization)**:
- Mintlify supports native language switchers via `docs.json`
- **Not needed for x402 initial launch** - English-only documentation
- Can be added later by creating `/es/`, `/zh/` directories

### **Theming**

Mintlify offers 7 pre-built themes configurable in `docs.json`:

| Theme | Style | Best For |
|-------|-------|----------|
| **Mint** | Classic, time-tested | General documentation |
| **Maple** | Modern, clean | AI/SaaS products (recommended for x402) |
| **Palm** | Sophisticated | Fintech/enterprise |
| **Willow** | Minimalist | Distraction-free reading |
| **Linden** | Retro terminal | Developer/hacker aesthetic |
| **Almond** | Card-based | Intuitive navigation |
| **Aspen** | Complex nav | Large documentation sets |

**Recommendation**: Use **Maple** theme for x402.

### **Custom Domain Setup (docs.x402.org)**

The documentation will be hosted at `docs.x402.org`. Setup requires:

1. **Mintlify Dashboard**: Add `docs.x402.org` in Settings → Custom Domain
2. **DNS Configuration**: Add CNAME record on the x402.org domain registrar:
   ```
   CNAME | docs | cname.mintlify-dns.com.
   ```
3. **Propagation**: DNS changes take 1-24 hours. Verify via [DNSChecker](https://dnschecker.org)
4. **TLS**: Mintlify auto-provisions SSL certificate via Let's Encrypt after DNS propagates

If using Cloudflare: Set SSL/TLS to "Full (strict)" and disable "Always Use HTTPS" in Edge Certificates.

### **Configuration Files**

**docs/docs.json** (Mintlify config):
```json
{
  "$schema": "https://mintlify.com/docs.json",
  "theme": "maple",
  "name": "x402",
  "colors": { "primary": "#0052FF" },
  "seo": {
    "metatags": {
      "canonical": "https://docs.x402.org"
    }
  },
  "navigation": {
    "groups": [
      { "group": "Welcome", "pages": ["README", "faq"] },
      { "group": "Getting Started", "pages": ["getting-started/quickstart-for-buyers", "getting-started/quickstart-for-sellers"] },
      { "group": "Core Concepts", "pages": ["core-concepts/http-402", "core-concepts/facilitator", "core-concepts/wallet"] }
    ]
  }
}
```

**docs/AGENTS.md** (Agent behavior config):
```markdown
# Documentation agent instructions

## Code-to-doc mapping
- Changes to `typescript/packages/core/src/` affect Core Concepts docs
- Changes to `python/x402/src/` affect Python SDK references
- Changes to facilitator endpoints affect quickstart guides

## Style guidelines
- Use TypeScript for code examples (primary SDK)
- Include error handling in all API examples
- Write for developers with 2-5 years experience
```

**.github/workflows/update-docs.yml** (GitHub Action):
```yaml
name: Update Docs
on:
  push:
    branches: [main]
jobs:
  update-docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/github-script@v8
        env:
          MINTLIFY_API_KEY: ${{ secrets.MINTLIFY_API_KEY }}
          PROJECT_ID: ${{ secrets.MINTLIFY_PROJECT_ID }}
        with:
          script: |
            const response = await fetch(`https://api.mintlify.com/v1/agent/${process.env.PROJECT_ID}/job`, {
              method: 'POST',
              headers: { 'Authorization': `Bearer ${process.env.MINTLIFY_API_KEY}` },
              body: JSON.stringify({
                branch: `mintlify/docs-update-${Date.now()}`,
                messages: [{ role: 'user', content: `Update docs for ${context.repo.repo}` }]
              })
            });
```

## **Cost Considerations**

| Plan | Monthly Cost | Features | Recommendation |
|------|-------------|----------|----------------|
| **Hobby** | $0 | Full platform, custom domain, API playground, semantic search | Sufficient for static docs without auto-updates |
| **Pro** | $300 | + AI Agent, team members, preview deployments | **Required for programmatic doc updates** |
| **Custom** | Contact Sales | + SSO, 99.999% SLA, custom auth | Not needed for open source project |

**Estimated Annual Cost**: $3,600/year (Pro plan)

**Hidden Costs**:
- Maintainer time: ~2 hrs/week reviewing agent PRs
- Initial setup: ~16 hours (1 engineer, 2 days)
- Ongoing tuning: ~4 hrs/month refining AGENTS.md
- Total Year 1 Cost: ~$3,600 (Mintlify) + ~$8,000 (eng time @ $100/hr) = ~$11,600

**Cost vs. Alternatives**:
- GitBook Premium: $65/site + $12/user/month = ~$924+/year (no auto-updates)
- Docusaurus: $0 software, but requires hosting infrastructure + maintenance time

**Note on Semantic Search**: Semantic search is included in all Mintlify plans including free Hobby tier. The AI Assistant chatbot and Writing Agent require Pro ($300/mo).

## **Reliability & Maintenance**

### **Availability and Scalability**

1. **Availability**: Mintlify provides managed hosting with CDN-backed documentation. Pro plan includes preview deployments for testing changes before production.
2. **Scalability**: Static documentation scales horizontally via Mintlify's CDN. No infrastructure management required.

### **Monitoring, Alerting, and Logging**

- Mintlify Dashboard provides deployment status and build logs
- GitHub Actions logs for agent API calls
- No custom alerting needed - Mintlify handles uptime monitoring

### **Extensibility, Flexibility, and Generalization**

1. **Can this be easily extended to add new capabilities?** Yes - add new pages via markdown files, extend navigation in docs.json, add MDX components for interactive content.
2. **Is this flexible enough to address a change in business needs?** Yes - can add i18n, OpenAPI specs, custom domains, or migrate to different theme without restructuring.
3. **Can/Should this be generalized and be used as repeatable innovation?** Yes - the GitHub Action + AGENTS.md pattern can be reused for any Coinbase project needing auto-updating docs. 

## **Threats and Mitigations**

### **Customer Data**

| Data Description | Operation | Storage Location | Classification | Comply with [DHG](https://confluence.coinbase-corp.com/display/SEC/Data+Handling+Guide) ? |
| :---- | :---- | :---- | :---- | :---- |
| Documentation content | Read/Write | Mintlify hosted infrastructure | Public | Yes - no PII stored |
| API Keys (Mintlify) | Read | GitHub Secrets | Internal | Yes - secrets management |

### **Services Dependencies**

| Service | Service Overview |
| :---- | :---- |
| Mintlify | Documentation hosting and AI agent. Auth: API key (mint_*). Used for: docs hosting, semantic search, auto-updates |
| GitHub | Source code and docs repository. Auth: GitHub App. Used for: version control, PR creation by agent |
| GitHub Actions | CI/CD automation. Auth: Repository secrets. Used for: triggering Mintlify agent on code push |

*Please utilize the Microsof*t [*Threat Modeling*](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats#stride-model) *article to aid in filling out the STRIDE Threat table.* 

| Threat (STRIDE) | Impact | Mitigations | Residual Risk |
| :---: | :---: | ----- | ----- |
| **Mintlify API Key leak** | Unauthorized doc updates | Store in GitHub Secrets only, never in code. Rotate keys periodically. | Low |
| **Agent creates incorrect docs** | Misinformation in docs | Human review required before merge. Agent creates PRs, not direct commits. | Low |
| **Mintlify service outage** | Docs unavailable | Static docs remain cached at CDN. GitHub repo serves as backup source of truth. | Low |
| **Malicious PR from agent** | Code injection in docs | PRs require maintainer approval. Agent only modifies /docs directory. | Low |

## 

| Step 2/2 : Production Readiness Not Started |
| :---: |

# **Pre-Production**

## **Security & Privacy**

### **Security**

Prior to shipping the product(s), the following security related items MUST be addressed.

**Design requirements**  
Review the [security review criteria](https://confluence.coinbase-corp.com/display/SEC/Security+review+criteria+and+engagement) before finalizing this TDD, and evaluate the changes in this TDD against it. When security engagement is needed ensure the TDD is linked to the security review ticket. If no ticket exists please file one. Please file this ticket no later than three weeks prior to your ship date in order to allow Security sufficient time to review your project.    
Is your product/feature in scope for the security compliance checklist?   
Have you submitted any third parties through vendor security review? ( Link to corresponding JIRA tickets here.)  
**Ship requirements**  
All Blocker security issues MUST be addressed prior to shipping/taking   
customer/partner/integration traffic.   
Please ensure your repo is utilizing Salus to perform security checks. 

### **Privacy**

1. **Are you storing private data?** No

## **Regulatory Compliance**

Prior to shipping the product(s), you need to ensure that your product(s) complies with regulations for the countries you are launching in. In order to do so, teams should integrate their product(s) with the Policy Platform. Details of how to integrate with the platform can be found [here](https://docs.google.com/document/d/14Yv6As19qeesPmCzo3q-sz6781l5l1l_z-QixFUXovs/edit?usp=sharing).

## **Testing**

### **Security Invariants**

1. No Mintlify API keys in client bundle or committed code
2. Agent PRs require human review before merge
3. Documentation content is public - no sensitive data

**E2E Test Cases**

N/A - Mintlify is a managed service. Testing consists of:
1. Verify docs deploy successfully after pushing to main
2. Verify semantic search returns relevant results for sample queries
3. Verify agent creates PR when triggered via GitHub Action
4. Verify AGENTS.md customizations are respected by agent

## **Graceful degradation**

- If Mintlify is unavailable, GitHub repository serves as source of truth for documentation
- Cached CDN responses continue serving docs during brief outages
- Agent failures do not block code merges - docs can be updated manually

## **Timeline**

*When does it ship and what are the milestones? Make sure the 1% & 100% tie to the PLC.*

| Date | Milestone | Milestone Description |
| :---: | :---: | ----- |
| **Week 1, Day 1** | Setup & Config | Create docs.json, configure monorepo path in Mintlify dashboard, install GitHub App |
| **Week 1, Day 2** | Migration & Domain | Convert markdown frontmatter, deploy to staging, configure `docs.x402.org` DNS |
| **Week 1, Day 3** | Agent Setup | Create GitHub Action workflow, add AGENTS.md, configure API keys in secrets |
| **Week 1, Day 4** | Testing | Verify agent creates PRs on code push, test semantic search, confirm DNS propagation |
| **Week 2** | Iteration | Refine AGENTS.md based on agent output, adjust code-to-doc mappings |
| **Ongoing** | Maintenance | Review agent PRs, monitor for incorrect suggestions |

# **Post-Production**

## **Support**

1. **How is the rollout being communicated with the support team?**
   - Internal announcement to x402 maintainers with links to new docs URL
   - Update README.md to point to Mintlify-hosted docs
   - No external support team involvement needed (open source project)

2. **What are the risks of a bad rollout? What strategy do you have to mitigate those risks?**
   - **Risk**: Agent generates incorrect documentation
     - **Mitigation**: All agent PRs require human review before merge
   - **Risk**: Mintlify service outage
     - **Mitigation**: GitHub repo remains source of truth; can revert to GitBook if needed
   - **Risk**: API key exposure
     - **Mitigation**: Keys stored in GitHub Secrets only

# **Out of Scope**

1. **OpenAPI Spec Creation**: Creating OpenAPI specs for facilitator API endpoints (future enhancement)
2. **Docstring-to-Doc Pipeline**: Auto-generating SDK reference from TypeDoc/mkdocstrings (test Agent capability first)
3. **i18n/Localization**: Multi-language translations (English-only for initial launch)
4. **Custom Authentication**: Gated/private docs (x402 is fully open source)
5. **CMS Integration**: No headless CMS - docs managed via Git
6. **Analytics Integration**: No Google Analytics/Mixpanel tracking initially (can add via docs.json)
7. **Versioned Docs**: Single version only (no v1/v2 switcher for initial launch)
8. **Versioned API Docs**: No v1/v2/v3 API version switcher (single latest version)

## **Migration Path from Current Docs**

### Current State
- Docs live in `/docs` with GitBook-style SUMMARY.md navigation
- 13 markdown files across 4 sections (Welcome, Getting Started, Core Concepts, Guides)
- No existing CI/CD for doc updates

### Migration Steps
1. Create `docs.json` with navigation structure mirroring current SUMMARY.md
2. Add frontmatter to existing markdown files (title, description)
3. Test local preview with `mint dev` CLI command
4. Configure monorepo path in Mintlify dashboard → Git Settings → `/docs`
5. Deploy to `x402.mintlify.app` for staging review
6. Configure custom domain `docs.x402.org` (see Custom Domain Setup section)
7. Update README.md links to point to `https://docs.x402.org`
8. Delete SUMMARY.md (replaced by docs.json navigation)

### Rollback Strategy
If Mintlify doesn't meet needs after evaluation:
1. Docs remain in Git - no vendor lock-in on content
2. Can export to GitBook, Docusaurus, or any markdown-based platform
3. Remove Mintlify GitHub App and GitHub Action workflow
4. Revert to previous GitBook-hosted docs if needed

