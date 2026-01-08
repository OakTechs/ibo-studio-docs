# IBO Studio Documentation

Welcome to **IBO Studio**, a powerful visual builder for creating micro apps without writing code. Build beautiful, data-driven mobile experiences using an intuitive drag-and-drop interface and JSON-based configuration.

## What is IBO Studio?

IBO Studio is a no-code platform that empowers developers, designers, and product teams to rapidly build and deploy micro apps for mobile devices. Instead of writing thousands of lines of code, you visually compose apps using pre-built components and configure them with JSON.

### Key Features

🎨 **Visual Drag-and-Drop Editor**
- Intuitive canvas with real-time phone preview
- Component tree for hierarchical navigation
- Live updates as you build

📱 **Real-Time Mobile Preview**
- See exactly what users will experience
- Test interactions instantly
- Responsive layout preview

🧩 **Rich Component Library**
- 20+ pre-built components including forms, lists, cards, navigation, and data displays
- Fully customizable properties for each component
- Support for complex layouts and nested components

⚡ **JSON-Based Configuration**
- Human-readable app structure
- Easy version control and collaboration
- Export/import for reusability

🔗 **Dynamic Data Binding**
- Connect to REST APIs, databases (MySQL, PostgreSQL)
- State management with `@state.*` expressions
- Data source bindings with `@datasource.*` expressions

💾 **Version Control & Publishing**
- Auto-save drafts every 30 seconds
- Publish versioned releases with one click
- Version history with preview and restore capabilities

📊 **Data Sources**
- HTTP/REST API integration
- Database connections (MySQL, PostgreSQL)
- Static data support
- Real-time data fetching and pagination

## How It Works

1. **Create a Project** – Start a new micro app in the IBO Studio dashboard
2. **Design Your Screens** – Use the visual editor to drag and drop components onto the canvas
3. **Configure Components** – Set properties, bind data sources, and define actions using the properties panel
4. **Connect Data** – Link your app to APIs or databases through the data sources manager
5. **Test & Preview** – See changes instantly in the real-time phone preview
6. **Publish** – Export your JSON configuration or publish versioned releases

## Getting Started

Explore the documentation using the navigation menu on the left, or jump directly to these essential sections:

### Core Concepts

- **[Screen JSON Overview](overview.md)** – Understand the JSON schema structure and config-driven philosophy
- **[Components](components.md)** – Browse the complete component catalogue with examples
- **[Sections & Navigation](sections-and-navigation.md)** – Learn how to structure screens and implement navigation
- **[Customization & Theming](customization.md)** – Master the palette, typography, and component customization system

### Building Apps

- **[Data Sources](data-sources.md)** – Connect your app to APIs, databases, and static data
- **[Expressions & Actions](expressions-and-actions.md)** – Master dynamic bindings, calculations, and user interactions
- **[Build Playbook](build-playbook.md)** – Follow best practices and design patterns

### Reference

- **[Reference Template](reference-template.md)** – Templates and next steps for advanced usage

## The IBO Studio Editor

The editor interface consists of several key areas:

### Left Sidebar
- **Component Tree** – Hierarchical view of all components on the current screen
- **Code View** – Direct JSON editing with real-time validation
- **Data Sources** – Manage API connections and database queries

### Center Canvas
- **Phone Frame** – Visual preview of your app in a mobile device frame
- **Interactive Elements** – Click, drag, and position components
- **Real-Time Rendering** – See changes as you make them

### Right Sidebar
- **Properties Panel** – Configure selected component properties
- **Styling Options** – Colors, sizes, spacing, and typography
- **Data Bindings** – Link components to data sources and state

### Top Toolbar
- **Save & Publish** – Auto-save indicator and publish button
- **Preview Mode** – Test your app interactions
- **Version History** – Access previous versions

## Component Overview

IBO Studio provides a comprehensive library of components:

**Layout Components**
- TabContainer – Organize content into switchable tabs
- CustomCard – Flexible card container with headers and borders
- FormGrid – Responsive multi-column form layouts

**Data Display**
- ExpandableCardList – Rich cards with search, pagination, and expand/collapse
- CardImage – Image cards for products, profiles, and content
- List – Simple vertical lists
- SummaryCard – KPI displays and summary information
- Text – Formatted text with Markdown support

**Form Inputs**
- TextField – Text input with validation and keyboard types
- Dropdown – Select from static or dynamic options
- DateField – Date and datetime picker
- CheckBox – Multi-select options
- RadioGroup – Single selection from options
- Switch – Toggle on/off settings
- ChipSelect – Chip-based selection
- RichTextEditor – Formatted text editing

**Navigation & Actions**
- SearchBar – Filter and search functionality
- Button – Primary and secondary actions
- FloatingActionButton – Prominent primary action
- Divider – Visual section separator

Each component supports data binding, state management, and action triggers.

## JSON Configuration

IBO Studio apps are defined using a JSON schema that includes:

```json
{
  "version": 1,
  "type": "screen",
  "title": "My Micro App",
  "state": {},
  "dataSources": {},
  "bottomNav": {
    "items": []
  },
  "sections": []
}
```

This config-driven approach enables:
- ✅ Version control with Git
- ✅ Code review and collaboration
- ✅ Reusable templates and patterns
- ✅ Dynamic updates without redeployment
- ✅ Cross-team alignment on UI contracts

## Use Cases

IBO Studio is perfect for building:

- 📋 **Form-Based Apps** – Data collection, surveys, order entry
- 📊 **Dashboard Apps** – KPI displays, analytics views
- 📇 **Directory Apps** – Employee directories, product catalogs
- 🛒 **E-Commerce Mini-Apps** – Product browsing, cart management
- 📅 **Booking Apps** – Appointment scheduling, reservations
- 📱 **Internal Tools** – Admin panels, workflow management

## Why IBO Studio?

**For Developers**
- Reduce development time by 10x
- Focus on business logic, not UI boilerplate
- Easy maintenance and updates
- Type-safe JSON schema

**For Designers**
- Full control over visual appearance
- Rapid prototyping and iteration
- Consistent design system
- Real-time preview

**For Product Teams**
- Faster time to market
- A/B test UI variations easily
- Update content without deployments
- Collaborate with clear specifications

## Quick Links

- 🚀 [Get Started with Your First App](build-playbook.md)
- 📚 [Component Reference](components.md)
- 🔧 [Data Binding Guide](expressions-and-actions.md)
- 💡 [Best Practices](build-playbook.md)

## Support & Community

- **GitHub Issues** – Report bugs or request features
- **Documentation Updates** – This documentation is continuously updated
- **Examples** – Check the reference templates for working examples

---

**Ready to build?** Start by exploring the [Screen JSON Overview](overview.md) to understand the core concepts, then dive into the [Components](components.md) catalogue to see what's possible.

*Last updated: December 2025*
