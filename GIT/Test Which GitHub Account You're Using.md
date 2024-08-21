To check which SSH key (and corresponding GitHub account) you are using for a particular operation (like pushing to a repository), you can use the ssh -T command to test the SSH connection. This will tell you which GitHub account the SSH key is associated with.

# Test Which GitHub Account You're Using
## Check GitHub Account:
```sh
    ssh -T github-office
```
_OR_

```sh
    ssh -T github-personal
```
If your SSH configuration is correct, you should see a message like:
```
 Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```
## check the configuration for a specific repository

```sh
    git config user.name
    git config user.email
```
# Set the Username and Email for the Repository:
> If you want to Set the Username and Email for the Repository:
### For your office repository:

```bash
git config user.name "user name"
git config user.email "user@mail"
```
### For your personal repository:

```bash
git config user.name "user name"
git config user.email "user@mail"
```