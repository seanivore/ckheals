# Common Webflow Export Issues and Fixes for GitHub Pages

## Understanding Webflow Exports

When you export a site from Webflow for custom hosting, there are a few quirks that might need attention for GitHub Pages to work correctly.

## Form Handling

Webflow forms won't work out of the box on GitHub Pages because:
1. GitHub Pages is static (no server-side processing)
2. Webflow's form handling relies on their servers

### Solution for Forms:
1. Use a third-party form service like Formspree:
   ```html
   <!-- Replace the form action with your Formspree endpoint -->
   <form action="https://formspree.io/f/yourformid" method="POST">
   ```

2. Or use Netlify Forms (requires hosting on Netlify instead of GitHub Pages)

## Asset Paths

Sometimes Webflow exports use absolute paths that need adjustment.

### Path Fixes:
1. Open your HTML files and look for paths that start with `/`:
   ```html
   <img src="/images/logo.png"> <!-- Might cause issues -->
   ```

2. Change to relative paths if needed:
   ```html
   <img src="./images/logo.png"> <!-- Better for GitHub Pages -->
   ```

## JavaScript Issues

Webflow's JavaScript might rely on jQuery or other libraries.

### Check Script References:
1. Make sure all script tags use HTTPS:
   ```html
   <!-- Change this -->
   <script src="http://code.jquery.com/jquery-3.6.0.min.js"></script>
   
   <!-- To this -->
   <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
   ```

## CNAME File

If using a custom domain, GitHub Pages requires a CNAME file.

### Creating a CNAME file:
1. Create a file named `CNAME` (no extension) in the root:
   ```bash
   echo "www.ckheals.com" > CNAME
   ```

2. Add, commit, and push:
   ```bash
   git add CNAME
   git commit -m "Add CNAME for custom domain"
   git push
   ```

## Testing Locally

Before pushing to GitHub, you can test your site locally:

```bash
# Install a simple HTTP server if you don't have one
npm install -g http-server

# Run the server in your project directory
http-server .

# Visit http://localhost:8080 in your browser
```

## Fixing Meta Tags

Update meta tags to ensure proper sharing on social media:

```html
<meta property="og:title" content="CK Heals - Holistic Healing Services">
<meta property="og:description" content="Energy healing, personal training, and wellness services.">
<meta property="og:image" content="https://www.ckheals.com/images/og-image.jpg">
<meta property="og:url" content="https://www.ckheals.com">
```

## Custom Domain SSL Issues

If you're having trouble with HTTPS on your custom domain:

1. Wait 24 hours after setting up the custom domain
2. Ensure your DNS is correctly configured
3. In GitHub Pages settings, uncheck and recheck "Enforce HTTPS" 