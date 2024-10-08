
## Package CVE Checker

This tool is designed for educational purposes only and/or use in controlled environments where you have explicit permission. 

The tool takes the output from `apt list --upgradeable`, parses each line for the package name and version, and queries the Vulners.com **FREE** API for CVEs matching the criteria. I mainly use it to check for software related local priv esc vectors on debian based systems.

### Flags Explained:
- **-f / --file**: Takes the output of `apt list --upgradeable` as input.
- **-K / --API_KEY**: Your Vulners API key.
- **-S / --CVSS**: Filters results by CVSS score. For example, for local privilege escalation exploits without user interaction, set this to at least 9.0 (in most cases).
- **-o / --output**: Specifies the output markdown file to save results. I recommend copying the file/output into a markdown viewer (e.g., Obsidian) for better readability.

### Additional Information

- Please review https://vulners.com/docs/api_reference/api/ about how to make an account and request an API key.
- If the generated file shows a local priv esc CVE, that does not necessarily mean it is exploitable. Further checks need to be done, for example some CVEs only apply to specific distribution versions or if other conditions are met.
- As the ingested output can be pretty large, this needs a while to run especially as there is some rate limiting involved with the free tier API key. After multiple testing I settled for this when using it myself with the free key:
```
elif response.status_code == 418:  # Handle rate-limiting
    print("Rate limited by API (418). Backing off...")
    time.sleep(delay * (attempt + 1))  # Exponential backoff
```
- Feel free to edit/remove the sleep/delay it if you got a paid key.
- I am not a coder, and a large chunk of it was created with GPT 4o, as such I could not cover all fringe scenarios where it may not work.
- Any feedback (critical or otherwise) is much appreciated.

### Example workflow

0. Install required libraries `pip3 install -r requirements.txt`
1. Run `apt list --upgradeable > pkgoutput.txt` on the target and transfer the file to your machine.
2. Run the script `python3 Package-CVE-Checker.py -f pkgoutput.txt -K <YOUR_API_KEY> -S 9.0 -o results.md`
3. Copy the file or its output into Obsidian to look for interesting CVEs. The entire output could be quite large even with CVSS 9.0 and above, you can try searching for "escalation" or "root" inside the file.
