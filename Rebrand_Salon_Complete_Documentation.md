# Rebrand.Salon - Complete Platform Documentation

## Table of Contents
1. [Platform Overview](#platform-overview)
2. [Platform Architecture](#platform-architecture)
3. [User Experience Flows](#user-experience-flows)
4. [Technical Implementation](#technical-implementation)
5. [Deployment Process](#deployment-process)

---

## Platform Overview

### Vision
Rebrand.Salon revolutionizes the salon website creation process through a single orchestrated AI system that blends modular design systems, automation, and intelligence — enabling salon owners to launch stunning, SEO-optimized websites in just minutes.

### What is Rebrand.Salon?
An AI-driven website builder designed specifically for the beauty and wellness industry. The platform empowers salon owners to instantly generate professional, SEO-optimized websites tailored to their business type — powered by prebuilt themes, reusable components, and intelligent automation through a single, multi-functional AI agent.

### Supported Business Categories
The platform supports multiple beauty and wellness business categories, each identified by a unique `salonCategory_id`:
* Hair Salon
* Beauty Salon
* Hair & Beauty Salon
* Barber Shop
* Nail Salon
* Spa & Sauna
* MedSpa
* Lashes & Eyebrows Studio
* Massage Center
* Others

---

## Platform Architecture

### Themes Architecture

#### Theme Composition
Every theme includes all essential pages and their variants:
* Home
* Services
* Contact Us
* Gift Cards
* Offers & Promotions
* Careers / We're Hiring
* Bookings / Appointments
* About / Team
* Gallery / Portfolio

Each page is built from **reusable, modular sections and components**, each assigned a unique ID for efficient management, reusability, and customization.

#### Theme Structure
Each theme includes:
* A unique identifier (`theme_id`)
* One or more associated `salonCategory_id`
* A structured hierarchy of pages, sections, and components

#### Theme Types
Two theme types are maintained for flexibility and performance:

1. **Theme_FullPagesAndSections**
   * Contains all available pages, sections, and components
   * Serves as the master design library for AI-driven customization

2. **Theme_Default**
   * A simplified, lightweight version
   * Includes only core pages and components for fast, ready-to-deploy website generation

### Component Architecture

#### Key Concept
The AI agent doesn't create new components. It decides **WHAT to use** from the master branch, then the generation script copies only those files and populates them with database content.

#### Separation of Concerns
```
Master Branch = Component Library
Database = Content & Decisions
Customer Branch = Compiled Site (components + content)
```

This is a **"smart copy + populate"** approach, not code generation.

---

## User Experience Flows

### Entry Point: Rebrand.salon Landing Page
Users land on **Rebrand.salon** and are presented with two options:
> ✅ Option A: "I already have a website"  
> 🆕 Option B: "I want to create a new website"

---

## Flow A: Existing Website (Rebranding)

### Step 1: Enter Existing Website URL
* User enters their salon's current website URL
* AI automatically analyzes and extracts:
  - Text & content
  - Images and assets
  - Menus and navigation
  - Contact & business info
  - SEO metadata

### Step 2: AI Insight Scan
AI connects to **Google PageSpeed Insights** to generate a detailed performance report:

| Metric | Current Score |
|--------|--------------|
| Page Speed (Mobile/Desktop) | 65 / 78 |
| SEO | 72 |
| Accessibility | 81 |
| Best Practices | 80 |

> "✨ Your website is cooking... Meanwhile, let me generate your performance report so you can compare the Before & After results!"

⏳ Progress animation plays while the report is generated.

### Step 3: Choose Color Preference
> "Would you like to keep your existing website colors or explore a new brand palette?"

**Options:**
- ✅ Keep existing colors
- 🎨 Choose new AI-powered palette (via **TweakCN**)

### Step 4: Select Theme
AI shows themes based on the salon category (Hair, Spa, Nail Studio, Barbershop).  
Each has live previews, SEO optimization, and mobile-first design.

### Step 5: AI Content Mapping & UX Enhancement
AI maps and enhances the current content:
- Suggests new pages (Booking, Reviews, Gallery, etc.)
- Rewrites text for SEO and clarity
- Improves layout & user experience

💡 Users can accept or skip suggestions.

### Step 6: AI Builds the New Website
AI compiles:
- Color palette
- Theme layout
- Existing content
- New suggestions

> "Your new salon website is being crafted — this won't take long! 💅"

### Step 7: AI Comparison Report
Before preview, users see **Before vs After** metrics:

| Metric | Old Website | New Website |
|--------|-------------|-------------|
| Speed (Mobile/Desktop) | 65 / 78 | 95 / 98 |
| SEO Score | 72 | 96 |
| Accessibility | 81 | 98 |
| Load Time | 4.2s | 1.8s |

🎉 This gives a "wow" effect showing instant improvement.

### Step 8: Website Preview + Final CTA
> 🧠 "AI has done its magic! Want a human touch?"  
> Our pro designer can polish your site and make it live.  
> 👉 Chat with us | 📞 Call Now

### Step 9: Publish or Customize
User can:
- 🚀 Publish instantly
- 💬 Chat with designer
- ✏️ Continue editing

---

## Flow B: New Website Creation (No Existing Website)

### Multi-Step Form Process

#### Screen 1: Salon Name
> "Let's start with your salon's name 💇‍♀️"  
> **Field:** Salon Name  
> 👉 **Next**

#### Screen 2: Address & Contact Info
> "Where is your salon located? Let's make sure your customers can find you 📍"  
> **Fields:** Address, City, Contact Number  
> 👉 **Next**

#### Screen 3: Choose Color Palette
> "Would you like to use your own colors or let AI pick the perfect ones?"  
> **Options:**
> - 🎨 Let AI choose
> - 🎯 I'll pick my own (color picker appears)  
> 👉 **Next**

#### Screen 4: Select Theme
> "Choose a theme that fits your salon's vibe 💅"  
> Gallery includes: Hair Salon, Spa, Nail Studio, Barbershop themes  
> 👉 **Next**

#### Screen 5: Choose Pages
> "Which pages do you want on your website?"  
> **Options:**
> - ✅ Home
> - ✅ About
> - ✅ Services
> - ✅ Gallery
> - ✅ Contact
> - 🗓️ Booking (Optional)
> - 💬 Reviews (Optional)  
> 👉 **Next**

### Step 6: AI Builds the Website
> "✨ Please wait while we're cooking your website... This won't take long 💇‍♀️💅"  
> AI generates content, layout, and SEO automatically.

### Step 7: Website Preview
> "🎉 Your new salon website is ready!"  
> Preview window with full site demo.  
> **CTA:** View Full Website

### Step 8: Offer Human Assistance
> 🧠 "AI has done its magic! Want a human touch?"  
> Our designer can polish and finalize your website.

**Buttons:**
- 💬 Chat with Designer
- 🚀 Publish My Website

### Step 9: Publish or Customize
User can:
- Publish instantly
- Save & continue editing

---

## Technical Implementation

### Two-Phase Process

#### Phase 1: AI Agent Decision Making
The AI agent populates the database with decisions.

#### Phase 2: Static Site Generation
Generation script reads database and creates customer branch.

---

## Phase 1: AI Agent

### Input
```javascript
{
  websiteId: "uuid",
  flowType: "rebranding",
  businessInfo: { name, category, services, ... },
  themeId: "modern-minimalist-uuid",
  selectedPages: ["home", "services", "gallery", "contact"]
}
```

### AI Agent Tool Execution

**Tool 1-3:** Scrape, Extract, Select Theme
- Determines theme: "modern-minimalist"

**Tool 4: populate_content** (Most Important)
```javascript
// For each page
for (const page of selectedPages) {
  // AI decides which sections to use
  const sections = decideSectionsForPage(page, theme, businessInfo);
  
  // For home page, AI might decide:
  sections = [
    { type: "hero", component: "HeroModern" },
    { type: "services", component: "ServicesGrid" },
    { type: "testimonials", component: "TestimonialsSlider" },
    { type: "cta", component: "CTABanner" }
  ];
  
  // Create WebsitePage record
  const websitePage = await prisma.websitePage.create({
    data: {
      websiteId,
      pageId: homePageId,
      slug: "index",
      metaTitle: "Glamour Hair Salon - Premium Hair Services"
    }
  });
  
  // For each section
  for (const section of sections) {
    // AI generates content using Claude
    const content = await generateContentWithClaude(
      section.type,
      businessInfo
    );
    
    // Example content for hero section:
    content = {
      heading: "Transform Your Look at Glamour Hair",
      subheading: "Premium Hair & Beauty Services",
      cta_text: "Book Appointment",
      cta_link: "/contact",
      background_image: "hero-bg.jpg",
      overlay_opacity: 0.6
    };
    
    // Store in database
    await prisma.websiteSection.create({
      data: {
        websitePageId: websitePage.websitePageId,
        sectionId: heroSectionId,  // From sections table
        content: content,           // JSON content
        displayOrder: 1
      }
    });
  }
}
```

### Database State After AI Agent

**websites table:**
```sql
{
  website_id: "uuid",
  theme_id: "modern-minimalist-uuid",
  salon_name: "Glamour Hair Salon",
  slug: "glamour-hair",
  status: "draft",
  ai_processing_status: "completed"
}
```

**website_pages table:**
```sql
{
  website_page_id: "page-uuid-1",
  website_id: "uuid",
  page_id: "home-page-uuid",
  slug: "index",
  meta_title: "Glamour Hair Salon - Premium Hair Services"
}
```

**website_sections table (multiple records):**
```sql
[
  {
    website_section_id: "section-uuid-1",
    website_page_id: "page-uuid-1",
    section_id: "hero-modern-uuid",  -- Links to sections table
    content: {
      heading: "Transform Your Look at Glamour Hair",
      subheading: "Premium Hair & Beauty Services",
      cta_text: "Book Appointment",
      cta_link: "/contact",
      background_image: "hero-bg.jpg"
    },
    display_order: 1
  },
  {
    website_section_id: "section-uuid-2",
    website_page_id: "page-uuid-1",
    section_id: "services-grid-uuid",
    content: {
      heading: "Our Services",
      services: [
        { name: "Haircut", price: "$45", description: "..." }
      ]
    },
    display_order: 2
  }
]
```

**sections table (reference data):**
```sql
{
  section_id: "hero-modern-uuid",
  slug: "hero-modern",
  section_type: "hero",
  template_component: "HeroModern",  -- ⭐ Component name
  default_layout: {...}
}
```

---

## Phase 2: Static Site Generation Script

### Entry Point
```javascript
// Backend service triggers this after AI completes
await staticSiteBuilderService.generateCustomerBranch(websiteId);
```

### Generation Process Steps

#### 1. Fetch All Data from Database
```javascript
const website = await prisma.website.findUnique({
  where: { websiteId },
  include: {
    theme: true,
    pages: {
      include: {
        page: true,
        sections: {
          include: {
            section: true  // ⭐ This has template_component field
          },
          orderBy: { displayOrder: 'asc' }
        }
      }
    },
    business: true
  }
});
```

#### 2. Load Registry
```javascript
const registry = {
  sections: require('../registry/sections.json').sections,
  themes: require('../registry/themes.json').themes
};
```

#### 3. Determine Components to Copy
```javascript
const componentsNeeded = new Set();
const sectionsNeeded = new Set();

for (const page of pages) {
  for (const section of page.sections) {
    // Get component name from sections table
    const componentName = section.section.templateComponent;
    sectionsNeeded.add(componentName);
    
    // Look up in registry to get file path and dependencies
    const registryEntry = registry.sections.find(
      s => s.database_id === section.sectionId
    );
    
    if (registryEntry) {
      componentsNeeded.add(registryEntry.component_path);
      
      // Add dependencies
      if (registryEntry.dependencies) {
        registryEntry.dependencies.forEach(dep => {
          componentsNeeded.add(dep);
        });
      }
    }
  }
}
```

#### 4. Setup Git Branch
```javascript
const tempDir = `/tmp/build-${websiteId}`;
const repoUrl = process.env.GIT_REPO_URL;

// Clone master branch
await git.clone(repoUrl, tempDir);
const customerGit = simpleGit(tempDir);

// Create new branch
await customerGit.checkoutLocalBranch(slug);
```

#### 5. Clean Everything First
```javascript
// Remove everything except what we'll keep
await fs.emptyDir(path.join(tempDir, 'components'));
await fs.emptyDir(path.join(tempDir, 'pages'));
await fs.remove(path.join(tempDir, 'registry'));
await fs.remove(path.join(tempDir, 'themes'));
```

#### 6. Copy Base Template
```javascript
await fs.copy(
  path.join(tempDir, 'templates/base'),
  tempDir,
  { overwrite: true }
);
```

#### 7. Copy Only Needed Components
```javascript
for (const componentPath of componentsNeeded) {
  const sourcePath = path.join(tempDir, componentPath);
  const targetPath = path.join(tempDir, componentPath);
  
  if (await fs.pathExists(sourcePath)) {
    await fs.copy(sourcePath, targetPath, { overwrite: true });
    console.log(`  ✓ Copied ${componentPath}`);
  }
}
```

#### 8. Generate data.json
```javascript
const dataJson = {
  site: {
    name: website.salonName,
    slug: website.slug,
    domain: website.subdomain
  },
  theme: {
    name: theme.name,
    colors: website.colorPalette || theme.colors
  },
  business: {
    name: website.business.businessName,
    phone: website.business.phone,
    email: website.business.email,
    address: {
      street: website.business.address,
      city: website.business.city,
      state: website.business.state,
      zip: website.business.zipCode
    }
  },
  pages: pages.map(page => ({
    id: page.pageId,
    type: page.page.pageType,
    slug: page.slug,
    title: page.customTitle,
    seo: {
      metaTitle: page.metaTitle,
      metaDescription: page.metaDescription
    },
    sections: page.sections.map(section => ({
      id: section.websiteSectionId,
      type: section.section.sectionType,
      component: section.section.templateComponent,  // ⭐ Component name
      content: section.content,                       // ⭐ Content from database
      order: section.displayOrder
    }))
  }))
};

await fs.writeJson(
  path.join(tempDir, 'data.json'),
  dataJson,
  { spaces: 2 }
);
```

#### 9. Generate Page Files
```javascript
async function generatePageFile(tempDir, page, dataJson) {
  const pageType = page.page.pageType;
  const templatePath = path.join(
    tempDir,
    'pages/templates',
    `${pageType}.template.tsx`
  );
  
  // Read template
  let template = await fs.readFile(templatePath, 'utf-8');
  
  // Generate imports for sections used on this page
  const imports = [];
  const renderCases = [];
  
  for (const section of page.sections) {
    const componentName = section.section.templateComponent;
    const registryEntry = registry.sections.find(
      s => s.id === section.section.slug
    );
    
    if (registryEntry) {
      imports.push(
        `import ${componentName} from '@/${registryEntry.component_path}';`
      );
      
      renderCases.push(`
        case '${componentName}':
          return <${componentName} key={section.id} content={section.content} />;
      `);
    }
  }
  
  // Replace placeholders
  template = template.replace(
    '// {{SECTION_IMPORTS}}',
    imports.join('\n')
  );
  
  template = template.replace(
    '// {{RENDER_SECTIONS}}',
    `
    switch (section.component) {
      ${renderCases.join('\n')}
      default:
        return null;
    }
    `
  );
  
  // Write page file
  const targetPath = path.join(
    tempDir,
    'pages',
    page.slug === 'index' ? 'index.tsx' : `${page.slug}.tsx`
  );
  
  await fs.writeFile(targetPath, template);
}
```

#### 10. Apply Theme Styles
```javascript
async function generateThemeCSS(tempDir, website, theme) {
  const colors = website.colorPalette || theme.colors;
  
  const css = `
:root {
  --color-primary: ${colors.primary};
  --color-secondary: ${colors.secondary};
  --color-accent: ${colors.accent};
  --color-text: ${colors.text};
  --color-background: ${colors.background};
}
  `;
  
  await fs.writeFile(
    path.join(tempDir, 'styles/theme.css'),
    css
  );
}
```

#### 11. Copy Customer Images
```javascript
async function copyCustomerImages(tempDir, website) {
  await fs.ensureDir(path.join(tempDir, 'public/images'));
  
  const images = await prisma.mediaAsset.findMany({
    where: { userId: website.userId }
  });
  
  for (const image of images) {
    // Download from image.cdnUrl to public/images/
  }
}
```

#### 12. Update package.json
```javascript
const packageJson = await fs.readJson(
  path.join(tempDir, 'package.json')
);

packageJson.name = slug;
packageJson.description = `Website for ${website.salonName}`;

await fs.writeJson(
  path.join(tempDir, 'package.json'),
  packageJson,
  { spaces: 2 }
);
```

#### 13. Commit and Push
```javascript
await customerGit.add('.');
await customerGit.commit(`Generated site for ${website.salonName}`);
await customerGit.push('origin', slug);
```

#### 14. Update Database
```javascript
await prisma.website.update({
  where: { websiteId },
  data: {
    gitBranch: slug,
    deploymentStatus: 'pending_build'
  }
});
```

#### 15. Trigger Dokploy Deployment
```javascript
async function deployToDokploy(website, branch) {
  const axios = require('axios');
  
  const response = await axios.post(
    `${process.env.DOKPLOY_API_URL}/applications`,
    {
      name: website.slug,
      repository: process.env.GIT_REPO_URL,
      branch: branch,
      buildCommand: 'npm run build && npm run export',
      domains: [{
        host: website.subdomain,
        https: true
      }]
    },
    {
      headers: {
        'Authorization': `Bearer ${process.env.DOKPLOY_API_TOKEN}`
      }
    }
  );
  
  // Update database with deployment info
  await prisma.website.update({
    where: { websiteId: website.websiteId },
    data: {
      deploymentStatus: 'deploying',
      liveUrl: `https://${website.subdomain}`
    }
  });
}
```

---

## Deployment Process

### Flow Diagram

```
AI Agent Completes
        ↓
Database Populated:
  - website_pages
  - website_sections (with JSON content)
  - sections.templateComponent
        ↓
Trigger: generateCustomerSite(websiteId)
        ↓
1. Fetch all data from database
        ↓
2. Analyze what components are needed
   (based on sections.templateComponent)
        ↓
3. Clone master branch
        ↓
4. Create customer branch
        ↓
5. Delete everything
        ↓
6. Copy ONLY needed components
        ↓
7. Generate data.json from database
        ↓
8. Generate page files with imports
        ↓
9. Apply theme CSS with colors
        ↓
10. Commit & push
        ↓
11. Deploy to Dokploy
        ↓
Done! Site live in 3-5 minutes
```

### Example: What Gets Copied

#### Database Says
```javascript
// website_sections for home page
[
  { section: { templateComponent: "HeroModern" }, content: {...} },
  { section: { templateComponent: "ServicesGrid" }, content: {...} },
  { section: { templateComponent: "TestimonialsSlider" }, content: {...} }
]
```

#### Script Copies
```
FROM master:
  components/sections/heroes/HeroModern/
  components/sections/services/ServicesGrid/
  components/sections/testimonials/TestimonialsSlider/
  components/ui/Button/           (dependency)
  components/ui/Card/             (dependency)
  lib/utils.ts                    (dependency)

TO customer branch glamour-hair:
  components/sections/heroes/HeroModern/
  components/sections/services/ServicesGrid/
  components/sections/testimonials/TestimonialsSlider/
  components/ui/Button/
  components/ui/Card/
  lib/utils.ts
```

#### Generates
```javascript
// data.json
{
  "pages": [{
    "slug": "index",
    "sections": [
      {
        "component": "HeroModern",
        "content": {
          "heading": "Transform Your Look at Glamour Hair",
          "subheading": "Premium Hair & Beauty Services",
          "cta_text": "Book Appointment"
        }
      }
    ]
  }]
}

// pages/index.tsx
import HeroModern from '@/components/sections/heroes/HeroModern';
import ServicesGrid from '@/components/sections/services/ServicesGrid';

export default function HomePage({ data }) {
  return (
    <main>
      {data.sections.map((section) => {
        switch (section.component) {
          case 'HeroModern':
            return <HeroModern content={section.content} />;
          case 'ServicesGrid':
            return <ServicesGrid content={section.content} />;
          default:
            return null;
        }
      })}
    </main>
  );
}
```

---

## Key Responsibilities

### AI Agent's Job
- Decide which sections to use
- Generate content (JSON)
- Store in database

### Generation Script's Job
- Read decisions from database
- Copy only needed files from master
- Populate with database content
- Create deployable static site

### No Duplication
- Components defined once in master
- Copied to customer branches as needed
- Database content injected at build time

---

## Admin Automation

System stores:
- Website snapshot
- User preferences
- AI-generated insights
- Performance report

Used for analytics, recommendations, and upsells (SEO services, booking, etc.)

---

## Technical Requirements for Landing Page

### Framework
- **Next.js + Tailwind CSS**
- Responsive & mobile-first design
- Smooth transitions and step animations (Framer Motion)
- SEO meta tags and Open Graph setup
- Components structured for modular editing
- Use AI-generated placeholder texts and images
- Include comments in code for each section

### Design Style
Salon-inspired elegance:
- Soft gradients
- Clean UI
- Gentle animations
- Expressive typography (Playfair Display + Inter)
- **Visually inspiring**
- **Emotionally persuasive**
- **Technically production-ready**

---

## Summary

Rebrand.Salon is a comprehensive AI-powered website builder that combines:
1. **Intuitive User Experience** - Clear, guided flows for both rebranding and new site creation
2. **Modular Architecture** - Reusable components and themes for flexibility
3. **Intelligent Automation** - AI-driven content generation and decision making
4. **Efficient Deployment** - Smart copy-and-populate approach with Git-based workflow
5. **Performance-Focused** - Built-in SEO optimization and performance tracking

The platform delivers professional, customized salon websites in minutes while maintaining code quality, scalability, and easy maintenance.
