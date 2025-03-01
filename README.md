# decr
### README

# `decr.sh`

## Description
Some websites on the onion ring-network enforce security by two-factor key challenge, where you are given a PGP message which has to be decrypted using your own private key to extract a new URL which confirms your request. This process on a mac typically means copy the response text, save it to a new text file, then run gpg on this textfile to extract a new textfile. You would then open this file and find the relevant line of text to copy and paste in the browser window to proceed. It is tedious and the manual tasks themselves does not add anything to the security. This means a perfect use case for a script. 

`decr.sh` is a Bash script that aim to automate these steps for you by checking the pastebin in memory for any PGP encrypted message. If found, it decrypts the message in memory, extracts the first onion-URL from the decrypted text, and copies the URL back to the clipboard so once you run the command you just have to go back to the browser window and paste whatever is in the memory now and it should be the answer to the two factor challenge. 

The script also supports input via command line or through passing in a file name as option. It uses two different methods to extract the desired URL from the decrypted text:
1. **Regex-Based Extraction**: Uses a refined regular expression to directly identify the target URL.
2. **Marker-Based Extraction**: Falls back to extracting the URL based on predefined markers if the primary method fails.

It depends on gpg installed and if you have it set up with empty key it will all be automatic (it will ask for you key password if you have it setup).
It also depends on pbpaste and pbcopy which allows us to manipulate the clipboard on OSX.

## Usage
```sh
./decr.sh [-s string] [-f file] [-h]
```

### Options
- `-s string`: Decrypt and extract from the given string.
- `-f file`: Decrypt and extract from the given file.
- `-h`: Show this help message.

### Dependencies
- `gpg`: GNU Privacy Guard, required for decrypting the PGP message.
- `pbpaste`: MacOS command to get the clipboard content.
- `pbcopy`: MacOS command to set the clipboard content.

## Installation
1. Download the `decr.sh` script.
2. Make the script executable:
    ```sh
    chmod +x decr.sh
    ```
3. Move the script to a directory in your PATH, for example:
    ```sh
    sudo mv decr.sh /usr/local/bin/decr
    ```

## Example Usage
1. **From String Input:**
    ```sh
    decr -s '-----BEGIN PGP MESSAGE-----
    ... your PGP encrypted message ...
    -----END PGP MESSAGE-----'
    ```

2. **From File Input:**
    ```sh
    decr -f /path/to/your/pgp_message.txt
    ```

3. **From Clipboard:**
    ```sh
    decr
    ```

## How It Works
1. **Regex-Based Extraction:**
    - The script attempts to extract the URL directly using a regular expression that matches the structure of the URL:
      ```
      ^[a-zA-Z0-9]+\.onion/two_factor_login/[a-zA-Z0-9]+$
      ```

2. **Marker-Based Extraction:**
    - If the regex-based extraction fails, the script falls back to extracting the URL based on markers:
      ```
      (?<=-----).*(?=-----)
      ```

## Example Decrypted Text
```
----- BEGIN TEXT -----
abcdefghijklmnop.onion/two_factor_login/qrstuvwxyz
----- END TEXT -----
```

The script will extract `abcdefghijklmnop.onion/two_factor_login/qrstuvwxyz` and copy it to the clipboard.

## Error Handling
- If no valid input is provided via options or clipboard, the script will show an error message and display the help text.
- If the decryption fails, an error message will be shown.
- If no valid URL is extracted, an error message will be shown.

## License
This project is licensed under the MIT License.

## Author
Christian Nilsson

## Contact
kanzie@gmail.com
