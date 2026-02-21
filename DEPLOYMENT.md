# WordPress Deployment Workflow

This repository is set up with a complete CI/CD pipeline for your GoDaddy WordPress hosting.

## Workflow Overview

```
develop → main → GitHub Actions → FTP/SFTP → GoDaddy Hosting
```

### Steps:

1. **Make changes in `develop` branch**
   ```bash
   git checkout develop
   # Make your changes
   git add .
   git commit -m "Your commit message"
   git push origin develop
   ```

2. **Review and merge to `main`**
   ```bash
   git checkout main
   git pull origin main
   git merge develop
   git push origin main
   ```

3. **Automatic deployment triggers**
   - When you push to `main`, GitHub Actions automatically:
     - Checks out your code
     - Deploys all files to your GoDaddy hosting via FTP/SFTP
     - Excludes sensitive files (.env, .git, node_modules, etc.)

## Initial Setup

### 1. First-time WordPress checkout
Pull your entire WordPress installation from GoDaddy (via FTP/SFTP) and commit it to this repo:

```bash
# After downloading WordPress files to your local machine
cd /path/to/website
git add .
git commit -m "Initial WordPress installation"
git push origin develop
```

### 2. Configure GitHub Secrets

Add your GoDaddy FTP/SFTP credentials as GitHub Secrets:

1. Go to your GitHub repository
2. Settings → Secrets and variables → Actions
3. Add the following secrets:

| Secret Name | Value | Example |
|---|---|---|
| `FTP_HOST` | Your GoDaddy FTP/SFTP host | `ftp.example.com` or `sftp.example.com` |
| `FTP_USERNAME` | Your FTP username | `user@example.com` |
| `FTP_PASSWORD` | Your FTP password | (Keep this secure!) |
| `FTP_PROTOCOL` | `ftp` or `sftp` | `sftp` (recommended for security) |
| `FTP_PORT` | FTP/SFTP port | `21` (FTP) or `22` (SFTP) |

**Finding your GoDaddy FTP details:**
- Log in to GoDaddy Account Manager
- Go to your hosting account
- Look for FTP/File Manager section
- Your FTP host is usually `ftp.yourdomain.com`
- Username/password are your hosting credentials

### 3. Deploy remote path (optional)
If your WordPress is not in the hosting root:
- Edit `.github/workflows/deploy.yml`
- Change `remote-dir: ./` to `remote-dir: ./public_html` (or your path)

## File Organization

```
website/
├── wp-admin/
├── wp-content/
│   ├── plugins/          (custom & third-party)
│   ├── themes/           (custom & third-party)
│   └── uploads/          (⚠️ ignored in git - don't commit user uploads)
├── wp-includes/
├── wp-config.php         (⚠️ not committed - use production config on server)
├── index.php
├── .gitignore            (ignore uploads, cache, wp-config, etc.)
├── .github/
│   └── workflows/
│       └── deploy.yml    (auto-deployment workflow)
└── README.md
```

## Important Notes

### ⚠️ Security & Best Practices

1. **wp-config.php**: Never commit your production database credentials
   - Store locally only
   - Use WordPress environment-specific configurations

2. **wp-content/uploads/**: Not committed (ignored by .gitignore)
   - User uploads are managed separately on the server
   - Don't lose them when deploying!

3. **Sensitive Data**:
   - Never commit API keys, database passwords, or secrets
   - Use environment variables or server-side config

4. **Before first deployment**:
   - Test in `staging` or `develop` first
   - Back up your WordPress database
   - Test deployment with a small change

## Workflow Examples

### Adding a custom plugin
```bash
git checkout develop
# Add plugin to wp-content/plugins/my-plugin/
git add wp-content/plugins/my-plugin/
git commit -m "Add custom plugin"
git push origin develop

# After testing, merge to main
git checkout main
git merge develop
git push origin main
# → Automatically deployed to GoDaddy
```

### Updating a theme
```bash
git checkout develop
# Edit files in wp-content/themes/my-theme/
git add wp-content/themes/my-theme/
git commit -m "Update theme styles"
git push origin develop

# Review changes, then merge
git checkout main
git merge develop
git push origin main
# → Automatically deployed
```

### Quick fix hotfix (main only)
```bash
git checkout main
# Make urgent fix
git add .
git commit -m "Hotfix: description"
git push origin main
# → Immediately deployed
```

## Troubleshooting

### Deployment failed in GitHub Actions
1. Check workflow run logs: GitHub repo → Actions → Latest run
2. Verify FTP credentials in Secrets
3. Ensure remote path is correct
4. Check if FTP/SFTP port is accessible

### Files not updating on live site
1. Clear browser cache
2. Clear WordPress cache (if using cache plugin)
3. Check file permissions on GoDaddy server (usually 644 for files, 755 for directories)

### Need to pull production changes back
```bash
# Download latest from GoDaddy via FTP
# Then push to repository
git add .
git commit -m "Sync production changes"
git push origin develop
```

## Deployment History

Track your deployments in GitHub:
- Go to Actions tab in your repository
- See all deployment runs and their status
- Click on a run to see detailed logs

---

Questions? Refer to:
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [GoDaddy FTP Setup](https://www.godaddy.com/help)
- [WordPress Documentation](https://wordpress.org/documentation/)
