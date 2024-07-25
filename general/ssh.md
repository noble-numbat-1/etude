# SSH

## Generate SSH Key

```bash
# Then a prompt will ask to enter a file to save the key
$ ssh-keygen -t ed25519 -C "your_email@example.com"
```

## Add SSH Key to Agent

```bash
# Start the ssh-agent in the background
$ eval "$(ssh-agent -s)"

# Add your SSH private key to the ssh-agent.
$ ssh-add ~/.ssh/id_ed25519
```
