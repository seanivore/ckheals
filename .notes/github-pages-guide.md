# GitHub Pages Deployment Guide for Webflow Sites

## Prerequisites
- Webflow site exported as HTML/CSS/JS
- GitHub repository already created and linked
- Initial files committed to your repository

## Step 1: Organize Your Files
- Ensure all your website files are in the root of your repository
- Make sure you have an `index.html` file at the root level

## Step 2: Configure GitHub Pages

1. Go to your GitHub repository in a browser
2. Click on "Settings" tab
3. Scroll down to "GitHub Pages" section
4. Set Source to "Deploy from a branch"
5. Select branch (usually "main" or "master")
6. Select folder (usually "/ (root)")
7. Click "Save"

## Step 3: Wait for Deployment
- GitHub will show a message saying "Your site is being published at [URL]"
- This may take a few minutes

## Step 4: Configure Custom Domain (Optional)

1. In GitHub Pages settings, enter your custom domain in the "Custom domain" field
2. Click "Save"
3. Configure your DNS:
   - Add a CNAME record pointing to `yourusername.github.io`
   - Or add A records pointing to GitHub Pages IP addresses:
     ```
     185.199.108.153
     185.199.109.153
     185.199.110.153
     185.199.111.153
     ```
4. Wait for DNS changes to propagate (may take up to 48 hours)
5. Check "Enforce HTTPS" once your domain is verified

## Step 5: Fix Relative Links (If Needed)

If your site uses relative links that don't work after deployment:

1. Check all links in your HTML files
2. Update links to use relative paths correctly:
   - Good: `href="./styles/main.css"` or `href="/styles/main.css"`
   - Not good: `href="styles/main.css"` (may work locally but fail on GitHub Pages)

## Step 6: Verify Your Site
- Visit your GitHub Pages URL (or custom domain)
- Test navigation, images, and interactive features
- Check browser console for any errors

## Troubleshooting

### 404 Errors
- Ensure index.html is in the correct location
- Check case sensitivity in filenames and paths
- Verify that all referenced files are committed

### CSS/JS Not Loading
- Check file paths in your HTML
- Make sure all assets were included in the repository
- Verify that media file types are supported

### Custom Domain Not Working
- Verify DNS settings are correct
- Check that CNAME file exists in repository root (if using custom domain)
- Allow time for DNS propagation

## Helpful Commands

Update your site after making changes:
```bash
git add .
git commit -m "Update website content"
git push
```

GitHub Pages will automatically rebuild your site after each push to the configured branch. 