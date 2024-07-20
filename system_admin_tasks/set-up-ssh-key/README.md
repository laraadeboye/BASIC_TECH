# Set up SSH key using ssh-keygen
1. Run ssh-keygen and generate a new SSH key with the ed25519 algorithm
```sh
ssh-keygen -t ed25519
```
2. Either choose a file name for your key pair or press enter to choose the default
3. Optionally set up a passphrase that you can remember

4. Locate the two files in the directory:
- id_ed25519 (private key)
- id_ed25519.pub (public key)

5. Copy the contents of the id_ed25519.pub and add it to the `authorized_keys` folder in the server

- Create a new file in the ~/.ssh directory: `authorized_keys`
- Paste the public key into this file
- Save and close the file 

6. SSH from your local machine:
```sh
ssh username@server_ip
```
