# Database Architecture

## Overview

RebrandAI uses PostgreSQL as its primary database, managed through Prisma ORM. The database architecture supports a multi-tenant SaaS platform for creating AI-generated salon and business websites.

**Database Provider:** PostgreSQL  
**ORM:** Prisma Client JS  
**Total Models:** 22

## Core System Modules

### 1. User Management
- **User** - Core user accounts with authentication and authorization
- **UserSession** - Active user sessions with token-based authentication

### 2. Business & Category Management
- **SalonBusiness** - Business information and profile data
- **SalonCategory** - Business category taxonomy

### 3. Theme & Design System
- **Theme** - Website theme templates (free and premium)
- **ThemeCategory** - Many-to-many relationship between themes and categories
- **ThemeConfig** - Theme-specific configuration key-value pairs

### 4. Content Structure System
- **Page** - Page type definitions (home, about, services, etc.)
- **ThemePage** - Pages available in each theme
- **Section** - Reusable page sections
- **Component** - UI components used within sections
- **SectionComponent** - Components within sections (many-to-many)

### 5. Website Management
- **Website** - Generated websites with their configurations
- **WebsitePage** - Pages within a specific website
- **WebsiteSection** - Sections within website pages with content

### 6. Media Management
- **MediaAsset** - Uploaded and managed media files with CDN integration

### 7. AI & Processing Jobs
- **ScrapingJob** - Web scraping operations for existing websites
- **GenerationJob** - AI content generation tasks
- **AiOrchestratorExecution** - Multi-step AI workflow orchestration

### 8. Performance & SEO
- **PagespeedReport** - Performance metrics from Google PageSpeed Insights
- **PerformanceComparison** - Before/after performance comparisons
- **SeoRecommendation** - AI-generated SEO improvement suggestions

### 9. Audit & Compliance
- **AuditLog** - System-wide audit trail for compliance

---

## Entity Relationships

### User Module
```
User (1) ----< (N) UserSession
User (1) ----< (N) SalonBusiness
User (1) ----< (N) Website
User (1) ----< (N) MediaAsset
User (1) ----< (N) AuditLog
```

### Business & Categories
```
SalonCategory (1) ----< (N) SalonBusiness
SalonCategory (1) ----< (N) ThemeCategory
SalonBusiness (1) ----< (N) Website
```

### Theme System
```
Theme (1) ----< (N) ThemeCategory
Theme (1) ----< (N) ThemeConfig
Theme (1) ----< (N) ThemePage
Theme (1) ----< (N) Website

Theme (N) ----< >---- (N) SalonCategory [via ThemeCategory]
Theme (N) ----< >---- (N) Page [via ThemePage]
```

### Content Structure
```
Page (1) ----< (N) ThemePage
Page (1) ----< (N) WebsitePage

Section (1) ----< (N) SectionComponent
Section (1) ----< (N) WebsiteSection

Component (1) ----< (N) SectionComponent

Section (N) ----< >---- (N) Component [via SectionComponent]
```

### Website Hierarchy
```
Website (1) ----< (N) WebsitePage
Website (1) ----< (N) ScrapingJob
Website (1) ----< (N) GenerationJob
Website (1) ----< (N) AiOrchestratorExecution
Website (1) ----< (N) PagespeedReport
Website (1) ----< (N) PerformanceComparison
Website (1) ----< (N) SeoRecommendation

WebsitePage (1) ----< (N) WebsiteSection
```

### Performance Analysis
```
PagespeedReport (1) ----< (N) PerformanceComparison [as oldReport]
PagespeedReport (1) ----< (N) PerformanceComparison [as newReport]
```

---

## Detailed Model Descriptions

### User
**Purpose:** Core authentication and user management

| Field | Type | Description |
|-------|------|-------------|
| userId | UUID | Primary key |
| email | String | Unique email (indexed) |
| passwordHash | String | Bcrypt hashed password |
| fullName | String? | Optional full name |
| phone | String? | Optional contact number |
| role | String | User role (default: "user", indexed) |
| subscriptionTier | String | Subscription level (default: "free") |
| emailVerified | Boolean | Email verification status |
| isActive | Boolean | Account status |
| lastLoginAt | DateTime? | Last login timestamp |

**Relationships:** Has many sessions, businesses, websites, media assets, and audit logs

---

### UserSession
**Purpose:** Manage active authentication sessions with security tracking

| Field | Type | Description |
|-------|------|-------------|
| sessionId | UUID | Primary key |
| userId | UUID | Foreign key to User |
| tokenHash | String | Hashed session token (indexed) |
| expiresAt | DateTime | Session expiration (indexed) |
| ipAddress | String? | Client IP address |
| userAgent | String? | Client user agent |

**Security Features:**
- Token hashing for security
- Automatic cleanup via expiration index
- IP and user agent tracking for audit
- Cascade delete when user is removed

---

### SalonCategory
**Purpose:** Categorize business types (e.g., Hair Salon, Spa, Barbershop)

| Field | Type | Description |
|-------|------|-------------|
| categoryId | Int | Auto-increment primary key |
| name | String | Unique category name |
| slug | String | URL-friendly identifier (indexed) |
| description | String? | Category description |
| iconUrl | String? | Category icon/image |
| displayOrder | Int | Sorting order |
| isActive | Boolean | Visibility flag |

**Relationships:** Links to businesses and themes via junction tables

---

### SalonBusiness
**Purpose:** Store business profile information

| Field | Type | Description |
|-------|------|-------------|
| businessId | UUID | Primary key |
| userId | UUID | Owner reference |
| categoryId | Int | Business category |
| businessName | String | Business display name |
| address, city, state, zipCode, country | String? | Location data |
| phone, email | String? | Contact information |
| businessHours | JSON? | Operating hours structure |
| websiteUrl | String? | Existing website |
| googleBusinessUrl, facebookUrl, instagramUrl, twitterUrl, yelpUrl | String? | Social media profiles |
| slug | String | Unique URL identifier (indexed) |

**Key Features:**
- Multi-channel social media integration
- Flexible business hours via JSON
- Location-based data structure
- Unique slug for public profiles

---

### Theme
**Purpose:** Define website templates with varying types and pricing

| Field | Type | Description |
|-------|------|-------------|
| themeId | UUID | Primary key |
| name | String | Unique theme name |
| slug | String | URL identifier (indexed) |
| themeType | String | Theme category (indexed) |
| previewImageUrl | String? | Theme preview image |
| demoUrl | String? | Live demo link |
| isPremium | Boolean | Free vs paid flag |
| price | Decimal(10,2)? | Premium theme price |
| isActive | Boolean | Availability status |

**Relationships:**
- Many-to-many with SalonCategory
- One-to-many with ThemeConfig, ThemePage, Website

---

### ThemeConfig
**Purpose:** Store theme-specific configuration in key-value pairs

| Field | Type | Description |
|-------|------|-------------|
| configId | UUID | Primary key |
| themeId | UUID | Parent theme |
| configKey | String | Configuration key |
| configValue | String | Configuration value |
| dataType | String | Value type (default: "string") |

**Unique Constraint:** (themeId, configKey) - Prevents duplicate keys per theme

---

### Page
**Purpose:** Define standard page types available across the platform

| Field | Type | Description |
|-------|------|-------------|
| pageId | UUID | Primary key |
| name | String | Page display name |
| slug | String | Unique identifier (indexed) |
| pageType | String | Page category (indexed) |
| defaultTitle | String? | SEO title template |
| defaultMetaDesc | String? | SEO description template |
| isRequired | Boolean | Must-have page flag |
| displayOrder | Int | Navigation order |

**Common Page Types:** home, about, services, contact, gallery, testimonials, etc.

---

### ThemePage
**Purpose:** Define which pages are included in each theme

| Field | Type | Description |
|-------|------|-------------|
| themePageId | UUID | Primary key |
| themeId | UUID | Parent theme |
| pageId | UUID | Page reference |
| isIncluded | Boolean | Page availability |
| displayOrder | Int | Page order |

**Unique Constraint:** (themeId, pageId)

---

### Section
**Purpose:** Reusable page sections (header, hero, services grid, footer, etc.)

| Field | Type | Description |
|-------|------|-------------|
| sectionId | UUID | Primary key |
| name | String | Section name |
| slug | String | Unique identifier (indexed) |
| sectionType | String | Section category (indexed) |
| defaultLayout | JSON? | Default structure |

**Usage:** Sections are composed of multiple components and can be reused across pages

---

### Component
**Purpose:** Smallest reusable UI building blocks (button, card, form, image, etc.)

| Field | Type | Description |
|-------|------|-------------|
| componentId | UUID | Primary key |
| name | String | Component name |
| slug | String | Unique identifier (indexed) |
| componentType | String | Component category |
| defaultProps | JSON? | Default properties |

---

### SectionComponent
**Purpose:** Junction table defining component composition within sections

| Field | Type | Description |
|-------|------|-------------|
| sectionComponentId | UUID | Primary key |
| sectionId | UUID | Parent section |
| componentId | UUID | Component reference |
| displayOrder | Int | Component order |

**Unique Constraint:** (sectionId, componentId)

---

### Website
**Purpose:** Central entity representing a generated website

| Field | Type | Description |
|-------|------|-------------|
| websiteId | UUID | Primary key |
| userId | UUID | Owner |
| businessId | UUID? | Optional business link |
| themeId | UUID | Applied theme |
| salonName | String | Business name |
| slug | String | Unique URL slug (indexed) |
| subdomain | String | Unique subdomain (indexed) |
| flowType | String | Generation flow (AI vs Manual) |
| originalWebsiteUrl | String? | Source for scraping |
| colorPalette | JSON? | Custom colors |
| customCss, customJs | String? | Custom code |
| faviconUrl, logoUrl | String? | Branding assets |
| status | String | Draft/Published state (indexed) |
| isPublished | Boolean | Publication flag |
| publishedAt | DateTime? | Publication timestamp |
| aiProcessingStatus | String? | AI generation status |

**Key Features:**
- Subdomain-based hosting
- Custom branding and styling
- AI-powered content generation tracking
- Publication workflow

---

### WebsitePage
**Purpose:** Instances of pages within a specific website

| Field | Type | Description |
|-------|------|-------------|
| websitePageId | UUID | Primary key |
| websiteId | UUID | Parent website |
| pageId | UUID | Page template |
| slug | String | Page URL path |
| customTitle | String? | Override title |
| metaTitle, metaDescription, metaKeywords | String? | SEO metadata |
| selectionMethod | String | How page was added (default: "user_selected") |
| isVisible | Boolean | Visibility flag |
| displayOrder | Int | Navigation order |
| publishedVersion | Int | Version tracking |

**Unique Constraint:** (websiteId, slug)

---

### WebsiteSection
**Purpose:** Section instances with actual content for website pages

| Field | Type | Description |
|-------|------|-------------|
| websiteSectionId | UUID | Primary key |
| websitePageId | UUID | Parent page |
| sectionId | UUID | Section template |
| content | JSON | Actual content data |
| customCss | String? | Section-specific styles |
| isVisible | Boolean | Visibility toggle |
| displayOrder | Int | Section order |

**Content Structure:** JSON stores actual text, images, links, and component data

---

### MediaAsset
**Purpose:** Centralized media library with CDN integration

| Field | Type | Description |
|-------|------|-------------|
| assetId | UUID | Primary key |
| userId | UUID | Owner |
| fileName | String | Original filename |
| fileType | String | File category (indexed) |
| mimeType | String | MIME type |
| fileSize | Int | Size in bytes |
| cdnUrl | String | CDN public URL |
| cdnPublicId | String? | CDN identifier |
| thumbnailUrl | String? | Thumbnail URL |
| altText | String? | Accessibility text |
| width, height | Int? | Image dimensions |
| uploadSource | String | Manual/AI/Scraped |
| isOptimized | Boolean | Optimization status |

**Features:**
- CDN integration for fast delivery
- Image optimization tracking
- Source attribution
- Accessibility support

---

### ScrapingJob
**Purpose:** Track web scraping operations for existing websites

| Field | Type | Description |
|-------|------|-------------|
| scrapingJobId | UUID | Primary key |
| websiteId | UUID | Target website |
| sourceUrl | String | URL to scrape |
| sourceType | String | Scraping strategy |
| status | String | Job status (indexed) |
| scrapedData | JSON? | Extracted content |
| errorMessage | String? | Error details |
| startedAt, completedAt | DateTime? | Timing |

**Workflow:** pending → in_progress → completed/failed

---

### GenerationJob
**Purpose:** Track AI content generation tasks

| Field | Type | Description |
|-------|------|-------------|
| generationJobId | UUID | Primary key |
| websiteId | UUID | Target website |
| jobType | String | Generation type |
| status | String | Job status (indexed) |
| progressPercentage | Int | Completion progress |
| currentStep | String? | Active step |
| generatedData | JSON? | Generated content |
| errorMessage | String? | Error details |
| tokensUsed | Int | AI token consumption |
| startedAt, completedAt | DateTime? | Timing |

**Features:**
- Real-time progress tracking
- Token usage monitoring
- Step-by-step execution tracking

---

### AiOrchestratorExecution
**Purpose:** Track complex multi-tool AI workflows

| Field | Type | Description |
|-------|------|-------------|
| executionId | UUID | Primary key |
| websiteId | UUID | Target website |
| orchestrationFlowType | String | Workflow type |
| toolsExecuted | JSON | List of tools run |
| toolExecutionLogs | JSON | Detailed execution logs |
| status | String | Execution status (indexed) |
| totalTokensConsumed | Int | Total AI tokens |
| totalExecutionTimeMs | Int | Execution duration |
| errorMessage | String? | Error details |
| startedAt, completedAt | DateTime? | Timing |

**Purpose:** Coordinates complex AI operations involving scraping, analysis, and generation

---

### PagespeedReport
**Purpose:** Store Google PageSpeed Insights performance metrics

| Field | Type | Description |
|-------|------|-------------|
| reportId | UUID | Primary key |
| websiteId | UUID | Analyzed website |
| url | String | Tested URL |
| device | String | Mobile/Desktop |
| performanceScore | Int | 0-100 score |
| accessibilityScore | Int | 0-100 score |
| bestPracticesScore | Int | 0-100 score |
| seoScore | Int | 0-100 score |
| fcp | Int? | First Contentful Paint (ms) |
| lcp | Int? | Largest Contentful Paint (ms) |
| tti | Int? | Time to Interactive (ms) |
| cls | Decimal(10,3)? | Cumulative Layout Shift |
| speedIndex | Int? | Speed Index |
| rawData | JSON? | Full API response |

**Metrics Tracked:**
- Core Web Vitals (FCP, LCP, CLS)
- Lighthouse scores
- Performance benchmarks

---

### PerformanceComparison
**Purpose:** Compare performance before and after website regeneration

| Field | Type | Description |
|-------|------|-------------|
| comparisonId | UUID | Primary key |
| websiteId | UUID | New website |
| oldWebsiteUrl | String | Original URL |
| oldReportId | UUID | Before metrics |
| newReportId | UUID | After metrics |
| improvementSummary | JSON | Comparison data |

**Use Case:** Demonstrate performance improvements to customers

---

### SeoRecommendation
**Purpose:** AI-generated SEO improvement suggestions

| Field | Type | Description |
|-------|------|-------------|
| recommendationId | UUID | Primary key |
| websiteId | UUID | Target website |
| category | String | SEO category |
| priority | String | Importance level |
| title | String | Recommendation title |
| description | String | Detailed explanation |
| actionRequired | String | Implementation steps |
| isApplied | Boolean | Implementation status (indexed) |
| appliedAt | DateTime? | Implementation timestamp |

**Categories:** Meta tags, content, images, structure, mobile, speed, etc.

---

### AuditLog
**Purpose:** System-wide audit trail for compliance and debugging

| Field | Type | Description |
|-------|------|-------------|
| logId | UUID | Primary key |
| userId | UUID | Actor |
| action | String | Action performed |
| resource | String | Resource type (indexed) |
| resourceId | String? | Specific resource |
| changes | JSON? | Change details |
| ipAddress | String? | Request IP |
| userAgent | String? | Client info |
| createdAt | DateTime | Timestamp (indexed) |

**Logged Actions:** create, update, delete, login, logout, publish, etc.

---

## Database Indexes

### Performance Optimization
All models include strategic indexes for query optimization:

**User & Auth:**
- `users.email`, `users.role`
- `user_sessions.userId`, `user_sessions.tokenHash`, `user_sessions.expiresAt`

**Business & Themes:**
- `salon_categories.slug`
- `salon_businesses.userId`, `salon_businesses.categoryId`, `salon_businesses.slug`
- `themes.slug`, `themes.themeType`
- `theme_categories.themeId`, `theme_categories.categoryId`

**Content Structure:**
- `pages.slug`, `pages.pageType`
- `sections.slug`, `sections.sectionType`
- `components.slug`
- All junction tables have indexes on both foreign keys

**Websites:**
- `websites.userId`, `websites.businessId`, `websites.themeId`, `websites.slug`, `websites.status`
- `website_pages.websiteId`, `website_pages.pageId`
- `website_sections.websitePageId`, `website_sections.sectionId`

**Jobs & Processing:**
- `scraping_jobs.websiteId`, `scraping_jobs.status`
- `generation_jobs.websiteId`, `generation_jobs.status`
- `ai_orchestrator_executions.websiteId`, `ai_orchestrator_executions.status`

**Media & Assets:**
- `media_assets.userId`, `media_assets.fileType`

**Performance & SEO:**
- `pagespeed_reports.websiteId`
- `performance_comparisons.websiteId`
- `seo_recommendations.websiteId`, `seo_recommendations.isApplied`

**Audit:**
- `audit_logs.userId`, `audit_logs.resource`, `audit_logs.createdAt`

---

## Cascade Delete Policies

**Cascade on User Delete:**
- UserSession, SalonBusiness, Website, MediaAsset, AuditLog

**Cascade on Website Delete:**
- WebsitePage, ScrapingJob, GenerationJob, AiOrchestratorExecution, PagespeedReport, PerformanceComparison, SeoRecommendation

**Cascade on WebsitePage Delete:**
- WebsiteSection

**Cascade on Theme/Page/Section/Component Delete:**
- Related junction table entries (ThemePage, ThemeCategory, SectionComponent)

---

## Unique Constraints

### Business Logic Constraints
- **users.email** - One account per email
- **salon_categories.name**, **salon_categories.slug** - Unique category identifiers
- **salon_businesses.slug** - Unique business URLs
- **themes.name**, **themes.slug** - Unique theme identifiers
- **pages.slug**, **sections.slug**, **components.slug** - Unique content identifiers
- **websites.slug**, **websites.subdomain** - Unique website URLs
- **(websiteId, slug)** in WebsitePage - Unique page paths per website
- **(themeId, configKey)** - One value per config key per theme
- Junction table composites prevent duplicates

---

## JSON Schema Fields

Several models use JSON for flexible, schema-less data:

| Model | Field | Purpose |
|-------|-------|---------|
| SalonBusiness | businessHours | Operating hours structure |
| Theme | - | (configs in separate table) |
| Section | defaultLayout | Default section structure |
| Component | defaultProps | Default component properties |
| Website | colorPalette | Custom color scheme |
| WebsiteSection | content | Actual content data |
| ScrapingJob | scrapedData | Extracted website data |
| GenerationJob | generatedData | AI-generated content |
| AiOrchestratorExecution | toolsExecuted, toolExecutionLogs | Workflow tracking |
| PagespeedReport | rawData | Full Lighthouse report |
| PerformanceComparison | improvementSummary | Comparison details |
| AuditLog | changes | Changed fields |

---

## Data Flow Examples

### Website Generation Flow
1. User creates **SalonBusiness**
2. User selects **Theme** and **Pages**
3. **Website** record created with status="draft"
4. **ScrapingJob** (optional) scrapes existing site
5. **GenerationJob** creates AI content
6. **AiOrchestratorExecution** coordinates tools
7. **WebsitePage** and **WebsiteSection** populated
8. **Website** status updated to "published"
9. **PagespeedReport** generated for performance
10. **SeoRecommendation** created for improvements

### Media Upload Flow
1. User uploads file
2. File stored in CDN (Cloudinary)
3. **MediaAsset** record created with CDN URL
4. Asset used in **WebsiteSection** content JSON
5. **AuditLog** records the upload action

---

## Scalability Considerations

### Current Design Strengths
- UUID primary keys allow distributed generation
- Indexed foreign keys for efficient joins
- Status fields indexed for job queue queries
- Cascade deletes maintain referential integrity
- JSON fields provide flexibility without schema migrations

### Future Optimization Opportunities
- Partition large tables (audit_logs) by createdAt
- Add materialized views for complex reports
- Consider read replicas for analytics queries
- Archive old job records (retention policy)
- Cache frequently accessed themes and pages

---

## Security Features

1. **Password Security:** Bcrypt hashing via passwordHash
2. **Session Management:** Token hashing, expiration tracking
3. **Audit Trail:** Comprehensive logging with IP/user agent
4. **Soft Deletes:** isActive flags for users, businesses, themes
5. **Cascade Protection:** Proper delete constraints prevent orphaned records
6. **RBAC Support:** Role field in User model for authorization

---

## Maintenance Notes

### Regular Tasks
- Monitor session expiration cleanup
- Archive old audit logs
- Clean up failed job records
- Optimize media asset storage
- Review and update indexes based on query patterns

### Monitoring Queries
```sql
-- Active users by subscription tier
SELECT subscriptionTier, COUNT(*) FROM users WHERE isActive = true GROUP BY subscriptionTier;

-- Website generation success rate
SELECT status, COUNT(*) FROM generation_jobs GROUP BY status;

-- Average tokens per generation
SELECT AVG(tokensUsed) FROM generation_jobs WHERE status = 'completed';

-- Performance score trends
SELECT DATE(createdAt), AVG(performanceScore) FROM pagespeed_reports GROUP BY DATE(createdAt);
```

---

## Version History

**Current Schema Version:** 1.0  
**Last Updated:** 2024  
**Prisma Version:** 5.x  
**PostgreSQL Version:** 14+

---

## Related Documentation

- [API Documentation](./api-docs.md)
- [Frontend Development Plan](./MVP/frontend-development.md)
- [Deployment Guide](./deployment.md)
- [Security Guidelines](./security.md)
