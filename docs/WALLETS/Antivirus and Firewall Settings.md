# Antivirus and Firewall Settings

## Antivirus Considerations

Many antivirus programs may flag the Salvium binaries as potentially unwanted software due to its cryptocurrency mining capabilities. This is a false positive, but it can interfere with the wallet's functionality. To ensure smooth operation:

1. **Create an Exception:**
   * Create a new folder for your Salvium wallet files.
   * Add this folder to your antivirus software's exception list.
   * Some common antivirus programs and how to add exceptions:
     * **Windows Defender:** Settings > Update & Security > Windows Security > Virus & threat protection > Manage settings > Add or remove exclusions
     * **Avast:** Menu > Settings > General > Exceptions
     * **AVG:** Menu > Settings > General > Exceptions
     * **Bitdefender:** Protection > Antivirus > Settings > Exclusions

2. **Safe Download:**
   * Always download the Salvium binaries from the official website (salvium.io).
   * Verify the download's checksum to ensure file integrity.

3. **False Positive Reporting:**
   * If your antivirus flags Salvium as malware, please report it as a false positive to help improve detection accuracy.

## Firewall Configuration

Salvium binaries needs to communicate with the network. You may need to configure your firewall to allow this traffic:

1. **Windows Firewall:**
   * When you first run Salvium binaries, Windows will likely prompt you to allow it through the firewall.
   * If not prompted or if you need to change settings later:
     * Open Windows Defender Firewall
     * Click "Allow an app or feature through Windows Defender Firewall"
     * Find "salvium-wallet-gui" in the list and ensure it's checked for both private and public networks

2. **Third-party Firewalls:**
   * The process will vary, but generally:
     * Open your firewall software
     * Look for application rules or exceptions
     * Add salvium-wallet-gui.exe and monerod.exe (if running a full node)
     * Allow both incoming and outgoing connections

3. **Port Forwarding:**
   * If you're running a full node, you may want to forward port 29081 (or your custom port) in your router settings for better connectivity.

## Specific Settings for Salvium

1. **Daemon (salviumd) Permissions:**
   * Ensure salviumd.exe is allowed through your firewall if running a full node.
   * Default port: 29081 (TCP)

2. **Wallet GUI Permissions:**
   * Allow salvium-wallet-gui.exe or salvium-wallet-cli.exe through your firewall.
   * It doesn't require specific ports, but needs outgoing internet access.

3. **Mining Considerations:**
   * If you plan to use the built-in mining feature, your antivirus may require additional exceptions.
   * Some antivirus software may need to be temporarily disabled during initial setup of mining features.

## Troubleshooting

1. **Connection Issues:**
   * If you're having trouble connecting to the network, check your firewall logs for blocked connections related to Salvium.
   * Temporarily disable your firewall to test if it's the cause of connection problems.
   * Define a seed node.
   * Ensure you only have one daemon running
   * Confirm that you are in administrator mode (GUI wallet should do this by default)

2. **Slow Performance:**
   * Real-time scanning by antivirus software can slow down wallet operations, especially with large transaction volumes.
   * Consider adding the wallet's data directory to your antivirus exclusion list.

3. **Missing Files:**
   * If your antivirus quarantines Salvium files, restore them and add exceptions as needed.
   * Re-download from the official source if files are corrupted or deleted by antivirus software.

Remember, while it's important to configure your security software to work with Salvium, never compromise your overall system security. Always maintain updated antivirus software and be cautious when granting exceptions.