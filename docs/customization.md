# Customization & Theming

IBO Studio provides a powerful, hierarchical customization system that lets you define colors, typography, and component-level styling. Apply consistent branding across all your micro apps using reusable themes, or override specific elements inline for unique screens.

## Overview

The customization system supports:

- 🎨 **Palette System** – Define brand colors once, reference everywhere with `@palette.*` syntax
- 🔤 **Typography Control** – Set font families (including Google Fonts) and scaling for body and title text
- 🧩 **Component Theming** – Style individual components (cards, buttons, app bars) with granular properties
- 📚 **Theme Registry** – Create reusable theme definitions shared across multiple screens
- 🔗 **Theme References** – Apply registered themes by reference, then override specific values
- 🎯 **Customization Hierarchy** – Layer global defaults, theme references, inline customization, and final overrides

## Customization Structure

Customization can be defined in four places (applied in this order):

1. **Registry Global** (`customizationRegistry.global`) – Base defaults for all screens
2. **Registry Theme** (`customizationRegistry.themes.<name>`) – Named, reusable themes
3. **Inline Customization** (`customization`) – Screen-specific styling
4. **Overrides** (`customizationOverrides`) – Final overrides that take precedence over everything

### Root Schema Keys

```json
{
  "customization": {
    "global": { /* Global palette, typography, components */ },
    "screen": { /* Screen-specific overrides */ }
  },
  "customizationRegistry": {
    "global": { /* Registry-level defaults */ },
    "themes": {
      "dark": { /* Named theme */ },
      "light": { /* Named theme */ }
    }
  },
  "customizationRef": "dark",
  "customizationOverrides": { /* Final overrides */ }
}
```

---

## Palette System

Define colors once in a palette, then reference them throughout your screen using `@palette.<key>` syntax.

### Defining Colors

```json
{
  "customization": {
    "global": {
      "palette": {
        "primary": "#1976D2",
        "secondary": "#FF9800",
        "surface": "#FFFFFF",
        "text": "#212121",
        "success": "#4CAF50",
        "warning": "#FFC107",
        "error": "#F44336",
        "border": "#E0E0E0"
      }
    }
  }
}
```

### Color Format Support

- **Hex RGB**: `#1976D2` or `#FF9800`
- **Hex ARGB**: `#FF1976D2` (first two digits are alpha/opacity)
- **Short Hex**: `#FFF` (expands to `#FFFFFF`)
- **0x Notation**: `0xFF1976D2`

### Using Palette Colors

Reference palette colors in any component or customization property:

```json
{
  "customization": {
    "global": {
      "palette": {
        "brand": "#6200EA",
        "accent": "#03DAC6"
      },
      "components": {
        "appBar": {
          "background": "@palette.brand",
          "foreground": "@palette.surface"
        },
        "button": {
          "background": "@palette.accent",
          "foreground": "@palette.text"
        }
      }
    }
  }
}
```

**Benefits:**
- Change brand colors in one place
- Maintain color consistency across components
- Easy theme switching (dark/light modes)
- Readability: `@palette.brand` is clearer than `#6200EA`

---

## Typography

Control font families and text scaling globally.

### Font Family

Set separate fonts for body text and titles:

```json
{
  "customization": {
    "global": {
      "typography": {
        "body": "Roboto",
        "title": "Montserrat",
        "scale": 1.0
      }
    }
  }
}
```

- `body` – Applied to body text, captions, labels, and general content
- `title` – Applied to headings, app bar titles, card titles
- `scale` – Multiplier for all text sizes (default: `1.0`; use `1.1` for 10% larger text)

### Google Fonts Integration

IBO Studio automatically resolves Google Fonts when you specify a family name:

```json
{
  "customization": {
    "global": {
      "typography": {
        "body": "Inter",
        "title": "Poppins"
      }
    }
  }
}
```

**Supported Google Fonts** (auto-detected):
- Roboto, Open Sans, Lato, Montserrat, Oswald, Raleway
- Poppins, Inter, Nunito, Ubuntu, Playfair Display
- And many more – just use the font name

**Custom/Asset Fonts:**
If you use a font family not available in Google Fonts, the system will pass it through as-is, allowing you to use custom fonts bundled with your app.

### Typography Examples

**Larger Text for Accessibility:**
```json
{
  "customization": {
    "global": {
      "typography": {
        "scale": 1.15
      }
    }
  }
}
```

**Brand-Specific Typography:**
```json
{
  "customization": {
    "global": {
      "typography": {
        "body": "Inter",
        "title": "Playfair Display",
        "scale": 1.0
      }
    }
  }
}
```

---

## Component Theming

Customize individual component types with granular style properties.

### Card Component

```json
{
  "customization": {
    "global": {
      "components": {
        "card": {
          "borderRadius": 16,
          "shadow": 4,
          "background": "@palette.surface",
          "border": "@palette.border"
        }
      }
    }
  }
}
```

**Card Properties:**
- `borderRadius` – Corner radius in pixels (default: `12`)
- `shadow` – Elevation/shadow depth, range `0-24` (default: `2`)
- `background` – Card background color (hex or `@palette.*`)
- `border` – Border color (hex or `@palette.*`)

**Example: Flat Cards**
```json
{
  "customization": {
    "global": {
      "components": {
        "card": {
          "borderRadius": 0,
          "shadow": 0,
          "border": "#E0E0E0"
        }
      }
    }
  }
}
```

### App Bar Component

```json
{
  "customization": {
    "global": {
      "components": {
        "appBar": {
          "background": "@palette.primary",
          "foreground": "#FFFFFF"
        }
      }
    }
  }
}
```

**App Bar Properties:**
- `background` – App bar background color
- `foreground` – Text and icon color

**Example: Dark App Bar**
```json
{
  "customization": {
    "global": {
      "components": {
        "appBar": {
          "background": "#212121",
          "foreground": "#FFFFFF"
        }
      }
    }
  }
}
```

### Button Component

```json
{
  "customization": {
    "global": {
      "components": {
        "button": {
          "background": "@palette.primary",
          "foreground": "#FFFFFF",
          "shape": "rounded"
        }
      }
    }
  }
}
```

**Button Properties:**
- `background` – Button background color
- `foreground` – Button text color
- `shape` – Button shape: `"pill"` (fully rounded) or `"rounded"` (moderate radius)

**Example: Pill-Shaped Buttons**
```json
{
  "customization": {
    "global": {
      "components": {
        "button": {
          "shape": "pill",
          "background": "@palette.accent",
          "foreground": "#FFFFFF"
        }
      }
    }
  }
}
```

---

## Theme Registry

Create reusable themes that can be applied to multiple screens.

### Defining Registry Themes

```json
{
  "customizationRegistry": {
    "global": {
      "palette": {
        "primary": "#1976D2",
        "secondary": "#FF9800",
        "surface": "#FFFFFF",
        "text": "#212121"
      },
      "typography": {
        "body": "Roboto",
        "scale": 1.0
      }
    },
    "themes": {
      "dark": {
        "palette": {
          "primary": "#BB86FC",
          "secondary": "#03DAC6",
          "surface": "#121212",
          "text": "#FFFFFF"
        },
        "components": {
          "card": {
            "background": "#1E1E1E",
            "shadow": 8
          }
        }
      },
      "light": {
        "palette": {
          "primary": "#6200EA",
          "secondary": "#03DAC6",
          "surface": "#FFFFFF",
          "text": "#000000"
        },
        "components": {
          "card": {
            "background": "#FFFFFF",
            "shadow": 2
          }
        }
      }
    }
  }
}
```

### Using Theme References

Apply a registered theme using `customizationRef`:

```json
{
  "customizationRef": "dark",
  "title": "Dashboard",
  "sections": [...]
}
```

**Resolution Order:**
1. Registry global defaults
2. Referenced theme (`customizationRef: "dark"`)
3. Inline customization
4. Customization overrides

---

## Customization Hierarchy

The system merges customization from multiple sources in a specific order:

### 1. Registry Global

Base defaults applied to all screens that use the registry:

```json
{
  "customizationRegistry": {
    "global": {
      "palette": {
        "primary": "#1976D2"
      },
      "typography": {
        "body": "Roboto"
      }
    }
  }
}
```

### 2. Inline Customization

Screen-specific customization defined directly in the screen JSON:

```json
{
  "customization": {
    "global": {
      "palette": {
        "primary": "#6200EA"
      }
    },
    "screen": {
      "components": {
        "card": {
          "shadow": 8
        }
      }
    }
  }
}
```

**Inline Structure:**
- `customization.global` – Global overrides for this screen
- `customization.screen` – Screen-specific theme block

**Priority:**
- If `customization.screen` exists and is not empty, it takes precedence
- Otherwise, uses `customizationRef` from registry
- Falls back to auto-detected theme if registry exists

### 3. Theme Reference

Reference a named theme from the registry:

```json
{
  "customizationRef": "dark",
  "customizationRegistry": {
    "themes": {
      "dark": {
        "palette": {
          "primary": "#BB86FC"
        }
      }
    }
  }
}
```

### 4. Customization Overrides

Final overrides that take absolute precedence:

```json
{
  "customizationOverrides": {
    "palette": {
      "primary": "#FF0000"
    },
    "components": {
      "button": {
        "shape": "pill"
      }
    }
  }
}
```

**Use overrides for:**
- A/B testing variations
- Feature flags that modify appearance
- Tenant-specific branding on shared screens

---

## Complete Examples

### Example 1: Simple Brand Customization

```json
{
  "version": 1,
  "type": "screen",
  "title": "Sales Dashboard",
  "customization": {
    "global": {
      "palette": {
        "primary": "#1565C0",
        "secondary": "#FFA726",
        "surface": "#FAFAFA",
        "text": "#212121"
      },
      "typography": {
        "body": "Inter",
        "title": "Poppins",
        "scale": 1.0
      },
      "components": {
        "card": {
          "borderRadius": 12,
          "shadow": 2,
          "background": "@palette.surface"
        },
        "appBar": {
          "background": "@palette.primary",
          "foreground": "#FFFFFF"
        },
        "button": {
          "background": "@palette.secondary",
          "foreground": "#FFFFFF",
          "shape": "rounded"
        }
      }
    }
  },
  "sections": [...]
}
```

---

### Example 2: Dark/Light Theme Registry

```json
{
  "version": 1,
  "type": "screen",
  "title": "Product Catalog",
  "customizationRegistry": {
    "global": {
      "typography": {
        "body": "Roboto",
        "title": "Montserrat",
        "scale": 1.0
      }
    },
    "themes": {
      "dark": {
        "palette": {
          "primary": "#BB86FC",
          "secondary": "#03DAC6",
          "surface": "#121212",
          "text": "#E1E1E1",
          "border": "#2C2C2C"
        },
        "components": {
          "card": {
            "background": "#1E1E1E",
            "shadow": 8,
            "borderRadius": 16
          },
          "appBar": {
            "background": "#1F1F1F",
            "foreground": "#FFFFFF"
          },
          "button": {
            "background": "@palette.primary",
            "foreground": "#000000",
            "shape": "pill"
          }
        }
      },
      "light": {
        "palette": {
          "primary": "#6200EA",
          "secondary": "#03DAC6",
          "surface": "#FFFFFF",
          "text": "#212121",
          "border": "#E0E0E0"
        },
        "components": {
          "card": {
            "background": "#FFFFFF",
            "shadow": 2,
            "borderRadius": 12
          },
          "appBar": {
            "background": "@palette.primary",
            "foreground": "#FFFFFF"
          },
          "button": {
            "background": "@palette.primary",
            "foreground": "#FFFFFF",
            "shape": "rounded"
          }
        }
      }
    }
  },
  "customizationRef": "dark",
  "sections": [...]
}
```

---

### Example 3: Per-Screen Override with Registry

```json
{
  "version": 1,
  "type": "screen",
  "title": "Settings",
  "customizationRegistry": {
    "global": {
      "palette": {
        "primary": "#1976D2",
        "surface": "#FFFFFF"
      },
      "typography": {
        "body": "Roboto"
      }
    },
    "themes": {
      "default": {
        "components": {
          "card": {
            "shadow": 2,
            "borderRadius": 12
          }
        }
      }
    }
  },
  "customizationRef": "default",
  "customization": {
    "screen": {
      "palette": {
        "primary": "#D32F2F"
      },
      "components": {
        "appBar": {
          "background": "@palette.primary"
        }
      }
    }
  },
  "sections": [...]
}
```

**What happens:**
1. Registry global sets `primary: #1976D2`
2. Theme `default` sets card styling
3. Inline screen customization overrides `primary: #D32F2F` for this screen only
4. App bar uses the overridden primary color

---

### Example 4: A/B Testing with Overrides

```json
{
  "version": 1,
  "type": "screen",
  "title": "Checkout",
  "customization": {
    "global": {
      "palette": {
        "primary": "#1976D2",
        "cta": "#4CAF50"
      },
      "components": {
        "button": {
          "background": "@palette.cta",
          "foreground": "#FFFFFF",
          "shape": "rounded"
        }
      }
    }
  },
  "customizationOverrides": {
    "components": {
      "button": {
        "shape": "pill",
        "background": "#FF5722"
      }
    }
  },
  "sections": [...]
}
```

**Use case:** Test button color and shape variations without editing the base customization. Overrides can be controlled by feature flags or A/B test groups.

---

## Best Practices

### 1. Define Palette Colors First

```json
{
  "customization": {
    "global": {
      "palette": {
        "primary": "#1976D2",
        "secondary": "#FF9800",
        "success": "#4CAF50",
        "warning": "#FFC107",
        "error": "#F44336",
        "surface": "#FFFFFF",
        "text": "#212121",
        "border": "#E0E0E0"
      }
    }
  }
}
```

**Then reference them everywhere** using `@palette.*` for consistency.

---

### 2. Use Registry for Multi-Screen Apps

If you have multiple screens sharing the same theme:

```json
{
  "customizationRegistry": {
    "global": {
      "palette": { /* shared colors */ },
      "typography": { /* shared fonts */ }
    },
    "themes": {
      "brand": { /* shared components */ }
    }
  },
  "customizationRef": "brand"
}
```

**Benefits:**
- Single source of truth
- Update branding globally by changing registry
- Override specific screens with inline customization

---

### 3. Keep Component Customization Minimal

Only override components that differ from defaults:

```json
{
  "customization": {
    "global": {
      "components": {
        "card": {
          "shadow": 4
        }
      }
    }
  }
}
```

Don't repeat default values—let the system handle them.

---

### 4. Use Semantic Color Names

Instead of generic names, use semantic palette keys:

```json
{
  "palette": {
    "primary": "#1976D2",      // Good
    "cta": "#4CAF50",           // Good: call-to-action
    "destructive": "#F44336",   // Good: delete/cancel actions
    "blue": "#1976D2"           // Bad: not semantic
  }
}
```

---

### 5. Test Color Contrast

Ensure sufficient contrast between foreground and background:

```json
{
  "components": {
    "appBar": {
      "background": "#1976D2",
      "foreground": "#FFFFFF"  // High contrast: good
    },
    "button": {
      "background": "#FFEB3B",
      "foreground": "#212121"  // High contrast: good
    }
  }
}
```

**Tool:** Use WebAIM Contrast Checker to verify WCAG compliance.

---

### 6. Scale Typography Carefully

```json
{
  "typography": {
    "scale": 1.1  // 10% larger—good for accessibility
  }
}
```

**Recommended range:** `0.9` - `1.2`  
**Avoid:** Values outside this range may break layouts.

---

### 7. Organize Overrides Strategically

Use overrides sparingly and document why:

```json
{
  "customizationOverrides": {
    "palette": {
      "primary": "#FF0000"  // A/B test variant: red CTA
    }
  }
}
```

**When to use overrides:**
- Feature flags
- A/B testing
- Tenant-specific branding
- Emergency hotfixes

---

## Troubleshooting

### Colors Not Applying

**Issue:** Palette colors aren't showing up  
**Solution:** Ensure you're using `@palette.` prefix:

```json
{
  "components": {
    "appBar": {
      "background": "@palette.primary"  // ✅ Correct
    }
  }
}
```

Not:
```json
{
  "components": {
    "appBar": {
      "background": "primary"  // ❌ Wrong
    }
  }
}
```

---

### Fonts Not Loading

**Issue:** Custom font family not displaying  
**Solution:**

1. **For Google Fonts:** Verify the font name matches exactly (case-insensitive):
   ```json
   {
     "typography": {
       "body": "Inter"  // ✅ Correct
     }
   }
   ```

2. **For Custom Fonts:** Ensure the font is bundled with your app and registered in `pubspec.yaml`:
   ```yaml
   flutter:
     fonts:
       - family: CustomFont
         fonts:
           - asset: fonts/CustomFont-Regular.ttf
   ```

---

### Theme Not Resolving

**Issue:** `customizationRef` theme not applying  
**Solution:** Ensure the theme exists in `customizationRegistry.themes`:

```json
{
  "customizationRef": "dark",
  "customizationRegistry": {
    "themes": {
      "dark": {  // ✅ Theme exists
        "palette": { /* ... */ }
      }
    }
  }
}
```

---

### Overrides Not Taking Effect

**Issue:** `customizationOverrides` being ignored  
**Solution:** Overrides should be at the root level, not nested inside `customization`:

```json
{
  "customization": { /* ... */ },
  "customizationOverrides": {  // ✅ Root level
    "palette": {
      "primary": "#FF0000"
    }
  }
}
```

---

## Quick Reference

### Palette Color Keys

Common palette keys used throughout IBO Studio:

- `primary` – Primary brand color (app bar, main actions)
- `secondary` – Accent color (FAB, highlights)
- `surface` – Background color for cards and surfaces
- `text` – Primary text color
- `border` – Border and divider color
- `success` – Success state color
- `warning` – Warning state color
- `error` – Error state color

### Typography Keys

- `body` – Font family for body text
- `title` – Font family for headings
- `scale` – Global text size multiplier

### Component Keys

- `card` – Card styling
- `appBar` – App bar styling
- `button` – Button styling

### Component Properties

**Card:**
- `borderRadius`, `shadow`, `background`, `border`

**App Bar:**
- `background`, `foreground`

**Button:**
- `background`, `foreground`, `shape`

---

## Next Steps

- **[Components](components.md)** – See how customization applies to individual components
- **[Sections & Navigation](sections-and-navigation.md)** – Structure your themed screens
- **[Build Playbook](build-playbook.md)** – Best practices for production apps
- **[Data Sources](data-sources.md)** – Connect your customized UI to data

---

**Pro Tip:** Start with a minimal palette and typography setup, then add component customization as needed. Over-customization can make maintenance harder—embrace defaults where possible.
