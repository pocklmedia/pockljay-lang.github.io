# Setting Up a Namecheap nc.me Domain with GitHub Pages

This guide will walk you through the complete process of connecting your Namecheap nc.me domain to your GitHub Pages site, including DNS configuration, HTTPS setup, and domain verification.

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Step 1: Configure DNS Records in Namecheap](#step-1-configure-dns-records-in-namecheap)
3. [Step 2: Add Custom Domain in GitHub Repository Settings](#step-2-add-custom-domain-in-github-repository-settings)
4. [Step 3: Verify Domain in Namecheap](#step-3-verify-domain-in-namecheap)
5. [Step 4: Enable HTTPS](#step-4-enable-https)
6. [Step 5: Handle DNS Propagation](#step-5-handle-dns-propagation)
7. [Troubleshooting](#troubleshooting)

## Prerequisites

Before you begin, ensure you have:
- A registered nc.me domain from Namecheap
- A GitHub account with a GitHub Pages repository
- Admin access to your GitHub repository
- Access to your Namecheap account dashboard

## Step 1: Configure DNS Records in Namecheap

### 1.1 Access Namecheap DNS Settings

1. Log in to your [Namecheap account](https://www.namecheap.com/myaccount/login/)
2. Click on **Domain List** in the left sidebar
3. Find your nc.me domain and click **Manage**
4. Navigate to the **Advanced DNS** tab

### 1.2 Add A Records for GitHub Pages

GitHub Pages uses specific IP addresses for apex domains. Add the following A records:

1. Click **Add New Record**
2. Create **four A records** with these values:

| Type | Host | Value | TTL |
|------|------|-------|-----|
| A Record | @ | 185.199.108.153 | Automatic |
| A Record | @ | 185.199.109.153 | Automatic |
| A Record | @ | 185.199.110.153 | Automatic |
| A Record | @ | 185.199.111.153 | Automatic |

> **Note:** The `@` symbol represents your root domain (e.g., yourdomain.nc.me)

### 1.3 Add CNAME Record for www Subdomain

To support both `yourdomain.nc.me` and `www.yourdomain.nc.me`:

1. Click **Add New Record**
2. Create a CNAME record:

| Type | Host | Value | TTL |
|------|------|-------|-----|
| CNAME Record | www | yourusername.github.io. | Automatic |

Replace `yourusername.github.io` with your actual GitHub Pages URL (e.g., `pocklmedia.github.io`)

> **Important:** Make sure to include the trailing dot (.) after `.github.io.`

### 1.4 Remove Conflicting Records

- Delete any existing A records pointing to different IPs
- Remove any parking page or default Namecheap redirects
- Ensure there are no conflicting CNAME records

### 1.5 Save Changes

Click **Save all changes** at the bottom of the page.

## Step 2: Add Custom Domain in GitHub Repository Settings

### 2.1 Navigate to Repository Settings

1. Go to your GitHub repository (e.g., `https://github.com/yourusername/yourrepo`)
2. Click on **Settings** tab
3. In the left sidebar, click on **Pages**

### 2.2 Configure Custom Domain

1. In the **Custom domain** section, enter your domain: `yourdomain.nc.me`
2. Click **Save**

> **Note:** GitHub will automatically create or update a `CNAME` file in your repository root with your custom domain.

### 2.3 Wait for DNS Check

GitHub will verify your DNS configuration. This process may take a few minutes. You'll see one of these messages:
- ✅ **DNS check successful** - Your DNS is configured correctly
- ⚠️ **DNS check in progress** - Wait a few minutes and refresh
- ❌ **DNS check unsuccessful** - Review your DNS settings

## Step 3: Verify Domain in Namecheap

### 3.1 Get Verification Code from GitHub

1. In your GitHub repository **Settings > Pages**
2. Look for the **DNS verification** section
3. Click **Add a DNS TXT record** or **Verify**
4. GitHub will provide a TXT record value (format: `_github-pages-challenge-yourorg.yourdomain.nc.me`)

### 3.2 Add TXT Record in Namecheap

1. Return to Namecheap **Advanced DNS** tab
2. Click **Add New Record**
3. Add the verification TXT record:

| Type | Host | Value | TTL |
|------|------|-------|-----|
| TXT Record | _github-pages-challenge-yourorg | [verification code from GitHub] | Automatic |

Example:
- **Host:** `_github-pages-challenge-pocklmedia`
- **Value:** `abc123def456` (use the actual code from GitHub)

4. Click **Save all changes**

### 3.3 Complete Verification in GitHub

1. Wait 5-10 minutes for DNS propagation
2. Return to GitHub **Settings > Pages**
3. Click **Verify** or refresh the page
4. You should see: ✅ **Domain verified**

## Step 4: Enable HTTPS

### 4.1 Enable HTTPS in GitHub Pages

1. In your repository **Settings > Pages**
2. Once DNS check is successful and domain is verified, scroll to **Enforce HTTPS**
3. Check the box for **Enforce HTTPS**

> **Important:** The HTTPS option will only become available after:
> - DNS check is successful
> - Domain is verified
> - SSL certificate is issued (may take up to 24 hours)

### 4.2 SSL Certificate Issuance

GitHub Pages automatically provisions an SSL certificate from Let's Encrypt:
- This process typically takes **15 minutes to 1 hour**
- In some cases, it may take up to **24 hours**
- You'll see a message: "Not yet available for your site because the certificate has not finished being issued"

### 4.3 Verify HTTPS is Working

Once enabled:
1. Visit `https://yourdomain.nc.me` in your browser
2. Check for the padlock icon 🔒 in the address bar
3. Verify the certificate is valid and issued by Let's Encrypt

## Step 5: Handle DNS Propagation

### 5.1 Understanding DNS Propagation

DNS propagation is the time it takes for DNS changes to update across the internet:
- **Typical time:** 15 minutes to 48 hours
- **Average time:** 2-4 hours
- **Factors:** TTL settings, ISP caching, geographic location

### 5.2 Check DNS Propagation Status

Use these tools to verify your DNS changes:

1. **DNS Checker**: [https://dnschecker.org](https://dnschecker.org)
   - Enter your domain
   - Select record type (A or CNAME)
   - View propagation status globally

2. **Command Line Tools**:
   ```bash
   # Check A records
   dig yourdomain.nc.me
   
   # Check CNAME record
   dig www.yourdomain.nc.me
   
   # Check from specific DNS server
   nslookup yourdomain.nc.me 8.8.8.8
   ```

3. **WhatsMyDNS**: [https://whatsmydns.net](https://whatsmydns.net)
   - Shows DNS propagation from multiple locations worldwide

### 5.3 Expected DNS Results

**A Records (apex domain):**
```
yourdomain.nc.me.  300  IN  A  185.199.108.153
yourdomain.nc.me.  300  IN  A  185.199.109.153
yourdomain.nc.me.  300  IN  A  185.199.110.153
yourdomain.nc.me.  300  IN  A  185.199.111.153
```

**CNAME Record (www subdomain):**
```
www.yourdomain.nc.me.  300  IN  CNAME  yourusername.github.io.
```

### 5.4 Clear Local DNS Cache

If you're not seeing changes:

**Windows:**
```cmd
ipconfig /flushdns
```

**macOS:**
```bash
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

**Linux:**
```bash
sudo systemd-resolve --flush-caches
```

**Browser Cache:**
- Clear browser cache and cookies
- Try incognito/private browsing mode
- Test in a different browser

## Troubleshooting

### Issue: "DNS check unsuccessful"

**Causes:**
- DNS records not yet propagated
- Incorrect IP addresses
- Conflicting DNS records

**Solutions:**
1. Verify all four A records are correct
2. Wait 30-60 minutes for propagation
3. Remove any conflicting records in Namecheap
4. Check that you're using GitHub's current IP addresses

### Issue: "HTTPS not available"

**Causes:**
- Certificate still being issued
- DNS not fully propagated
- Domain not verified

**Solutions:**
1. Wait up to 24 hours for certificate issuance
2. Ensure domain is verified in GitHub
3. Verify DNS propagation is complete
4. Try removing and re-adding the custom domain

### Issue: "Domain already taken"

**Causes:**
- Domain is already used by another GitHub Pages site
- Previous configuration not cleared

**Solutions:**
1. Remove domain from any other repositories
2. Check all repositories in your GitHub account
3. Contact GitHub Support if domain is stuck

### Issue: "www subdomain not working"

**Causes:**
- Missing or incorrect CNAME record
- Propagation delay

**Solutions:**
1. Verify CNAME record: `www` → `yourusername.github.io.`
2. Include trailing dot in CNAME target
3. Wait for DNS propagation (15-30 minutes)

### Issue: "Certificate errors or mixed content"

**Causes:**
- HTTPS not fully enabled
- Hard-coded HTTP links in content

**Solutions:**
1. Ensure "Enforce HTTPS" is checked
2. Update all internal links to use HTTPS or relative URLs
3. Check for external resources loading over HTTP

### Verification Commands

```bash
# Check if domain resolves to GitHub Pages
curl -I https://yourdomain.nc.me

# Check DNS A records
dig +short yourdomain.nc.me

# Check DNS CNAME record
dig +short www.yourdomain.nc.me

# Check HTTPS certificate
openssl s_client -connect yourdomain.nc.me:443 -servername yourdomain.nc.me

# Check GitHub Pages specific
curl -H "Host: yourdomain.nc.me" https://185.199.108.153
```

## Additional Resources

- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Namecheap DNS Settings](https://www.namecheap.com/support/knowledgebase/article.aspx/434/2237/how-do-i-set-up-host-records-for-a-domain/)
- [GitHub Pages Custom Domain Guide](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)
- [Let's Encrypt Certificate Status](https://letsencrypt.org/docs/)

## Summary Checklist

Use this checklist to ensure you've completed all steps:

- [ ] Added four A records in Namecheap pointing to GitHub Pages IPs
- [ ] Added CNAME record for www subdomain
- [ ] Removed conflicting DNS records
- [ ] Added custom domain in GitHub repository settings
- [ ] Waited for DNS propagation (2-4 hours typical)
- [ ] Verified domain in GitHub with TXT record
- [ ] Enabled HTTPS in GitHub Pages settings
- [ ] Waited for SSL certificate issuance (up to 24 hours)
- [ ] Tested both apex domain and www subdomain
- [ ] Verified HTTPS is working with padlock icon
- [ ] Cleared local DNS cache if needed
- [ ] Updated any hard-coded links to use new domain

---

**Last Updated:** November 2024

For questions or issues, please refer to the [GitHub Pages documentation](https://docs.github.com/en/pages) or contact support.
