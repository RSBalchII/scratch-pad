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

---

## 2026-05-15: Git Repository Cleanup Session

### 🔧 Git/Repository Issues

#### 1. Messy Repository History with Agent Files
**Issue:** Working directory contained numerous agent configuration files (AGENTS.md, MEMORY.md, PROFILE.md, SOUL.md, BOOTSTRAP.md, HEARTBEAT.md, agent.json, chats.json, skill.json) that were not part of the actual project code.

**Impact:** These files appeared in git status as modified/deleted (D), creating noise and potential confusion about what should be committed.

**Resolution:** 
- Created orphan commit on new branch (`main-clean-v4`)
- Only added project-relevant files: `.gitignore`, `job-leads/`, `cv-resume-2026-05-14/*.md`, `pain-points.md`
- Deleted old local branches (main-2, main-v3)

**Lesson:** Always verify which files are part of the project vs. agent workspace before committing. Consider using `.gitignore` patterns to exclude agent config directories.

---

#### 2. Multiple Out-of-Sync Branches
**Issue:** Repository had multiple branches (main, main-2, main-v3) with divergent histories and no clear default branch.

**Impact:** Confusion about which branch is current; remote tracking was misconfigured.

**Resolution:** 
- Created fresh orphan commit on new branch
- Pushed clean branch to remote (`origin/main-clean-v4`)
- Deleted old local branches to reduce clutter

**Lesson:** Before starting a cleanup session, assess the branch structure and plan ahead. May need to delete or merge old branches before creating new ones.

---

#### 3. Windows CRLF Line Ending Warnings
**Issue:** Git reported CRLF (Windows) vs LF (Unix) line ending warnings for all markdown files:
```
warning: in the working copy of 'file.md', CRLF will be replaced by LF the next time Git touches it
```

**Impact:** While not blocking, these warnings indicate potential cross-platform compatibility issues and unnecessary diffs.

**Resolution:** Files were committed with Windows line endings; future work should consider:
- Using `.gitattributes` to standardize line endings
- Configuring git to handle CRLF consistently across platforms

---

#### 4. Remote Branch Management via CLI
**Issue:** GitHub CLI (`gh`) did not support convenient branch listing/deletion commands for remote branches in this environment.

```bash
# Expected but unavailable: gh branch list -r origin
# Available instead: gh api /repos/.../branches (requires manual parsing)
```

**Impact:** Had to use browser UI or curl API calls to manage remote branches.

**Resolution:** Used GitHub web interface for branch deletion and management.

---

### 📋 Key Takeaways for Future Sessions

1. **Audit before committing:** Check what files are actually part of the project vs. agent workspace
2. **Use orphan commits for clean starts:** `git checkout --orphan <branch-name>` creates fresh history
3. **Be explicit about allowed directories** when adding to index
4. **Document OS-specific issues** (Windows encoding, CRLF) in pain-points.md
5. **Add pain-points.md to .gitignore** - this is internal agent documentation

---

## Known Limitations

*(See existing limitations section above)*

---

*Last Updated: 2026-05-15*