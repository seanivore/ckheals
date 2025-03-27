# Downloading Content from a Live Webflow Site

## Method 1: Using wget (Recommended)

`wget` is a powerful command-line tool that can mirror websites. It's ideal for downloading all the content from the live Webflow site.

### Installation (if needed)

```bash
# On macOS with Homebrew
brew install wget

# On macOS without Homebrew
# Download from https://www.gnu.org/software/wget/ or use curl
```

### Basic Website Download

```bash
# Navigate to your project directory
cd /Users/seanivore/Development/webflow-client-ckheals

# Download the entire website with wget
wget \
  --recursive \
  --no-clobber \
  --page-requisites \
  --html-extension \
  --convert-links \
  --restrict-file-names=windows \
  --domains ckheals.com \
  --no-parent \
  https://www.ckheals.com
```

### Explanation of Options
- `--recursive`: Download the entire website
- `--no-clobber`: Skip downloads that would download to existing files
- `--page-requisites`: Get all assets needed to display the page
- `--html-extension`: Save files with .html extension
- `--convert-links`: Convert links to work locally
- `--restrict-file-names=windows`: Modify filenames to work across platforms
- `--domains ckheals.com`: Don't follow links outside this domain
- `--no-parent`: Don't follow links to parent directory

## Method 2: Using httrack

HTTrack is a free utility with a user interface that can download websites.

```bash
# Install HTTrack (macOS)
brew install httrack

# Run HTTrack to download the site
httrack https://www.ckheals.com -O "/Users/seanivore/Development/webflow-client-ckheals" "+*.ckheals.com/*" -v
```

## Method 3: Using a Browser Extension

### For a quick download:
1. Install the "Save Page WE" or "SingleFile" extension in Chrome/Firefox
2. Visit each page of the website
3. Use the extension to save the complete page
4. Organize the files in your project directory

## After Downloading

### Reorganize Files (if needed)
```bash
# For wget downloads, move files from www.ckheals.com/ to root level
mv www.ckheals.com/* .
rmdir www.ckheals.com
```

### Check File Structure
```bash
# Make sure index.html is at the root level
ls -la

# Check if all assets were downloaded
ls -la css/
ls -la js/
ls -la images/
```

### Create CNAME File
```bash
# Create CNAME file for GitHub Pages custom domain
echo "www.ckheals.com" > CNAME
```

### Test Locally Before Pushing
```bash
# Use a simple HTTP server to test
python -m http.server
# or
npx http-server
```

## Troubleshooting Common Issues

### Missing Assets
- Check for assets loaded via JavaScript
- Look for CDN links that might need to be downloaded separately

### Form Functionality
- Forms will need to be modified to work on GitHub Pages
- Consider using a service like Formspree

### Dynamic Content
- Any dynamic content from Webflow won't work on static hosting
- Replace with static alternatives or client-side solutions 