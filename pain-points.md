# Pain Points & Issues Log

**Purpose:** Document recurring technical issues, tool limitations, and OS-specific problems encountered during job search automation. This file is for internal reference only - do not commit to git.

---

## 2026-05-14: Job Search Session

### 🔧 Tool/Platform Issues

#### 1. Browser Automation Failures
**Issue:** `browser_use.start()` failed repeatedly with timeout errors when launching Chrome CDP endpoint.

```
Error: Browser start failed: Timed out waiting for Chrome CDP endpoint on port XXXXX
```

**Root Cause:** 
- Multiple attempts to launch browser (headless, headed, private_mode) all failed
- Port allocation conflicts or Chrome not properly installed/configured
- System appears to lack a working browser installation

**Impact:** Could not use browser-based scraping for LinkedIn/Google Jobs APIs.

**Workaround:** Switched to RSS feed parsing and direct API calls.

---

#### 2. WeWorkRemotely API Endpoint
**Issue:** `/api/v1/jobs/` endpoint returned HTTP 404 Not Found.

```
WeWorkRemotely API failed with status 404
```

**Root Cause:** API endpoint may have changed or requires authentication.

**Impact:** Could not use structured JSON API for job listings.

**Workaround:** Used WeWorkRemotely RSS feed (`/remote-jobs.rss`) and parsed XML.

---

#### 3. Google Search API Limitations
**Issue:** Google's public search API returned HTML instead of parseable results.

**Impact:** Could not extract structured job data from search results.

**Workaround:** Focused on RSS feeds with known structures (WeWorkRemotely).

---

### 💻 OS/Environment Issues

#### 4. Windows File Encoding Errors
**Issue:** UnicodeEncodeError when writing job leads file containing emoji characters.

```python
UnicodeEncodeError: 'charmap' codec can't encode character '\U0001f525' 
in position 342: character maps to <undefined>
```

**Root Cause:** Windows default encoding (cp1252) cannot handle UTF-8 emoji characters.

**Impact:** Script crashed when trying to write markdown file with fire emoji.

**Workaround:** Added explicit `encoding='utf-8'` parameter to file open calls.

---

#### 5. Python Path/Indentation Errors
**Issue:** Multi-line command execution in shell caused indentation errors.

```python
IndentationError: unexpected indent
```

**Root Cause:** Command was pasted as single line with embedded newlines, causing syntax issues when executed.

**Impact:** Had to rewrite scripts as proper `.py` files instead of inline commands.

---

### 📋 Data/Format Issues

#### 6. RSS Feed Parsing Failures
**Issue:** Some RSS feeds returned HTTP 404 or non-RSS content.

```
Error fetching RSS: HTTP Error 404: Not Found
```

**Impact:** DuckDuckGo and other job board RSS feeds not available.

**Workaround:** Focused on WeWorkRemotely which provided reliable RSS feed.

---

#### 7. URL Deduplication Issues
**Issue:** RSS feed returned URLs with duplicates (same job listed multiple times).

```
Found 28 job links (some duplicate)
```

**Impact:** Potential for duplicate resume generation.

**Workaround:** Used set-based deduplication on URLs before processing.

---

## Known Limitations

### Browser Automation
- Chrome CDP endpoint timing out on this system
- May require:
  - Installing Chrome/Chromium browser
  - Configuring proper user data directory
  - Using different browser (Firefox, Edge)

### API Access
- Google Jobs API requires authentication for production use
- LinkedIn Jobs API is not publicly accessible via automation
- WeWorkRemotely RSS may have rate limiting

### Encoding
- Windows systems require explicit UTF-8 encoding for files with Unicode characters
- Scripts should always specify `encoding='utf-8'` when opening files

---

## Recommendations

1. **Browser Setup:** If browser automation is needed, ensure Chrome is installed and configure:
   ```bash
   --no-sandbox --disable-gpu --user-data-dir=<path>
   ```

2. **Fallback Strategies:** Always have RSS feed fallbacks for job boards that block API access.

3. **Encoding Safety:** All file operations should specify `encoding='utf-8'`.

4. **Error Handling:** Add retry logic for HTTP requests with timeout handling.

---

*Last Updated: 2026-05-14*