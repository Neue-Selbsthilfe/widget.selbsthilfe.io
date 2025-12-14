# Copilot Instructions for widget.selbsthilfe.io

## Project Overview

This is a self-help contact point search widget for Germany, built as a static website hosted on GitHub Pages. The widget provides an embeddable iframe that displays searchable self-help contact information (Selbsthilfe-Kontaktstellen) across German states.

## Architecture

- **Static site**: Pure HTML/CSS/JavaScript (no build process required)
- **Data source**: JSON files in `data/` directory, fetched daily from selbsthilfe-labor.de API
- **Main files**:
  - `index.html` - Landing page with widget embed button
  - `search-widget-iframe.html` - Main widget interface with search/filter functionality
  - `data/addresses.json` - Contact point data
  - `data/states.json` - German state (Bundesland) data
  - `data/postal-codes.json` - ZIP code data

## Key Features

- Search by name, city, or postal code
- Filter by state (Bundesland) and city
- Responsive design (desktop and mobile)
- Geolocation support with distance calculation _(planned feature)_
- XSS protection via HTML escaping

## Coding Conventions

### Language and Style

- **Primary language**: German for UI text and variable names where appropriate; code comments are written in English
- **HTML/CSS**: Standard conventions, use semantic HTML5
- **JavaScript**: Vanilla JS (ES6+), no frameworks or build tools
- **Naming**: Use camelCase for JavaScript variables and functions

### Security Practices

**CRITICAL**: Always use `escapeHtml()` function to prevent XSS attacks when displaying any user input or API data.

```javascript
function escapeHtml(text) {
    if (!text) return '';
    const div = document.createElement('div');
    div.textContent = text;
    return div.innerHTML;
}
```

Apply `escapeHtml()` to:
- User search input
- API response data (names, addresses, emails, URLs)
- Error messages
- Any dynamic content inserted into the DOM

### Responsive Design

- Current implementation uses flexbox with wrapping to provide basic responsiveness across screen sizes.
- There are no explicit media queries or breakpoints; layout adapts naturally via flexbox.
- Explicit mobile/desktop layouts and a `768px` breakpoint using media queries are planned as a future enhancement.
### Geolocation (Planned/Future Functionality)

_The following geolocation features are planned for future implementation and are not currently present in the codebase:_
- Automatic geolocation on page load (with user permission)
- Manual geolocation button
- Distance calculation using Haversine formula
- Sorting results by distance when location is available

## Data Management

### Data Files

All data files are in `data/` directory:
- `addresses.json` - Array of contact points with fields: id, name, street, address, city, postalCode, state, lat, lng, phone, contact, mail, url, description
  - Note: `address` and `street` contain the same value (street address)
  - Note: `contact` and `mail` contain the same value (email address)
  - These duplications exist for compatibility with different naming conventions
- `states.json` - Array of German states (Bundesländer)
- `postal-codes.json` - Array of postal codes with cities

### Data Updates

Data is automatically updated via `.github/workflows/fetch-data.yml`:
- Runs daily at 2:00 AM UTC (cron: `0 2 * * *`)
- Fetches from three selbsthilfe-labor.de API endpoints:
  - `/rest/sh/kontaktstelle/1.0.0/` - Contact points (paginated, all pages fetched)
  - `/rest/sh/zip/1.0.0/` - Postal codes (first page, up to 100 items; only the first page is fetched, so not all postal codes are included if there are more than 100)
  - `/rest/sh/state/1.0.0/` - States (all ~16 German Bundesländer)
- Uses `API_TOKEN` secret for authentication
- Transforms JSON:API format to simplified format
- Validates JSON before committing
- Commits changes directly to main branch

## Workflows

### Deploy to GitHub Pages

`.github/workflows/deploy-pages.yml`:
- Triggers on push to main branch
- Deploys static files to GitHub Pages
- No build step required

### Fetch API Data

`.github/workflows/fetch-data.yml`:
- Manual trigger available via workflow_dispatch
- Requires `API_TOKEN` secret for API access
- Fetches from multiple API endpoints (kontaktstelle, zip, state)
- Inline Node.js script for data transformation

## Development Guidelines

### Local Development

```bash
# Start local server
python3 -m http.server 8000

# Open in browser
http://localhost:8000
```

### Making Changes

1. Test locally with a simple HTTP server
2. Ensure all dynamic content uses `escapeHtml()`
3. Test responsive layout at different screen sizes
4. Validate that search and filter functionality works
5. Check browser console for errors

### Adding New Features

- Keep it simple - no build tools or dependencies
- Maintain vanilla JavaScript approach
- Ensure mobile responsiveness
- Follow existing code style
- Add appropriate HTML escaping for security

## Common Tasks

### Modifying Search Logic

Search is performed in `search-widget-iframe.html`:
- Search filters are applied in `filterResults()` function
- Results are displayed in `displayResults()` function
- Always escape dynamic content with `escapeHtml()`

### Updating Styles

- Inline styles in `<style>` tags in HTML files
- Use existing CSS class naming conventions
- Maintain responsive design with media queries

### API Integration

If modifying API integration:
- Update transformation logic in `fetch-data.yml` workflow
- Ensure JSON validation before commit
- Test with both full and empty API responses
- Handle errors gracefully

## Security Checklist

Before committing code:
- [ ] All user input is escaped with `escapeHtml()`
- [ ] All API data is escaped with `escapeHtml()`
- [ ] No inline event handlers (use addEventListener)
- [ ] External links have `rel="noopener noreferrer"`
- [ ] No secrets or API tokens in code
- [ ] JSON data is validated before use

## Testing Approach

- **Manual testing**: Test in browser (Chrome, Firefox, Safari)
- **Responsive testing**: Test on mobile devices or browser dev tools
- **Functionality testing**: 
  - Search by name, city, postal code
  - Filter by state and city
  - Geolocation functionality
  - Results display correctly
- **Security testing**: Verify HTML escaping works with special characters

## Important Notes

- This is a static site - no server-side logic
- Data updates happen via GitHub Actions, not runtime
- All JavaScript runs in the browser
- Keep the codebase simple and maintainable
- German language is preferred for user-facing content
