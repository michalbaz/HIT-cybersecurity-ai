# Lab 1: From CTI to SOC Investigation and MITRE ATT&CK Mapping
* **Students:** Michal Bazarsky

* **Title:** Inside the LiteLLM 1.107.0 supply chain compromise: Credential theft through Python startup hooks
* **Publisher:** Microsoft Threat Intelligence
* **Publication Date:** October 23rd, 2026
* **URL:** https://www.microsoft.com/en-us/security/blog/2026/10/23/inside-the-litellm-1-107-0-supply-chain-compromise-credential-theft-through-python-startup-hooks/

  ## Attack Summery:
  This CTI report by Microsoft Threat Intelligence details a highly targeted software supply chain compromise affecting the Python AI ecosystem. 
  Threat actors successfully compromised version 1.107.0 of LiteLLM, a popular gateway used to route traffic between various LLMs. 
  The affected environment includes AI developer workstations, build agents, and containerized AI workloads where this specific package version was installed. 
  The primary attack objective was the mass theft of sensitive credentials, source code, and cloud access keys.

  ### Observed Sequence:
  First the compromised package installed 3 files: `cook.pth`, `cook.py`, and `sitecustomize.py` under `litellm-1.107.0.data/purelib/`.
  During installation, these files were relocated into the Python environment’s library directory, ensuring the payload executed automatically whenever Python initialized.
  The payload, a credential stealer, searched for developer, AI-tool, cloud, browser, SSH and environment secrets, plus selected LiteLLM database records,
  encrypted the results and sent them to an attacker-controlled web endpoint.

  ## The Attack Flow:
  **[Initial Access]** Victim installs the compromised `LiteLLM 1.107.0` Python package.
⬇️
**[Execution / Persistence]** Malicious package installs Python startup hooks, triggering execution when Python starts.
⬇️
**[Credential Access]** Malware scans the local system for SSH keys, cloud credentials, and browser data.
⬇️
**[Command & Control / Exfiltration]** Malware encrypts stolen data and transmits it to a remote C2 server, checking a remote GitHub file for C2 updates.

  ## MITRE ATT&CK Mapping and connection to the SOC investigation:

| **Observed behavior** | **ATT&CK tactic** | **Technique / ID** | **Evidence and mapping rationale** | **SOC telemetry / investigation** |
| ------ | ------ | ------ | ------ | ------ |
| Compromised LiteLLM package distribution installed by victims. | Initial Access | Supply Chain Compromise: Compromise Software Dependencies and Development Tools (T1195.002) | The report states: "The compromised LiteLLM 1.107.0 release delivered malicious Python startup files to environments where the package was installed." | **Tier 1:** Monitor endpoint software inventory alerts for the presence of `LiteLLM` version 1.107.0.<br><br>**Tier 2:** Investigate CI/CD pipeline build logs to identify how the package was introduced. |
| Code executes automatically via Python startup processing. | Persistence | Event Triggered Execution (T1546) | The report states: "Python startup processing launched the credential collector without requiring the application to actively use LiteLLM gateway functionality." | **Tier 1:** Triage EDR alerts for anomalous script execution.<br><br>**Tier 2:** Perform file integrity checks on standard Python startup files (e.g., `sitecustomize.py`) to locate the malicious hook. |
| Collector targets cloud credentials, SSH material, and browser data. | Credential Access | Unsecured Credentials: Private Keys (T1552.004) | The report notes the collector targeted "cloud credentials, browser data, SSH material... and private-key extensions." | **Tier 1:** Monitor file system telemetry for untrusted processes rapidly reading sensitive directories (e.g., `~/.ssh/`).<br><br>**Tier 2:** Correlate anomalous file reads with subsequent outbound network connections. |
| Malware fetches a GitHub-hosted `domain.txt` file to find its C2 server. | Command and Control | Web Service: Dead Drop Resolver (T1102.001) | The report states the malware "reads a GitHub-hosted domain.txt file, which acts as a mutable pointer to an attacker-controlled server." | *(No SOC investigation required for this row as the lab only requires three)* |

  
  
  
