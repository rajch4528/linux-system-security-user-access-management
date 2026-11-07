# Linux System Security & User Access Management

**Author:** Raj Atul Chavan  
**Level:** Beginner  
**Platform:** Red Hat Enterprise Linux (RHEL), Rocky Linux, or AlmaLinux  
**Environment:** Personal virtual machine  
**Focus:** Linux users, groups, ownership, permissions, and least privilege

## Project overview
A beginner hands-on lab exploring Linux access controls. You will create a practice user and group, configure ownership and permissions, test access, and correct an intentionally over-permissive test file.

**Status:** Starter project. Complete the lab, add your own screenshots, and fill in the report before presenting it as completed.

## Objectives
- Inspect Linux system information.
- Create and verify a local practice account and group.
- Configure directory ownership and permissions.
- Apply file permissions using `chmod`.
- Test read/write access as the practice user.
- Explain the principle of least privilege.

## Requirements
- Computer capable of running a Linux VM
- Oracle VirtualBox or another hypervisor
- RHEL 9, Rocky Linux, or AlmaLinux
- An account with authorised `sudo` privileges

No dedicated networking hardware is required. Commands may vary slightly by distribution.

## Safety and scope
Run commands only inside your own personal/disposable VM or an explicitly authorised environment. Do not modify accounts or permissions on production, shared, college, or third-party systems without permission. Never publish passwords, tokens, private keys, or sensitive terminal output.

## Lab procedure

### 1. Inspect the system
```bash
whoami
hostnamectl
cat /etc/os-release
```
Record the actual username, hostname, and distribution/version.

### 2. Create a practice user
```bash
sudo useradd student1
sudo passwd student1
id student1
```
Use a lab-only password; do not include it in screenshots. If the account already exists, inspect it rather than creating a duplicate.

### 3. Create a group and add the user
```bash
sudo groupadd securityteam
sudo usermod -aG securityteam student1
id student1
```
`-aG` appends the supplementary group without replacing existing supplementary groups. If the group already exists, inspect it first.

### 4. Create a group-managed directory
```bash
sudo mkdir -p /opt/securitylab
sudo chown root:securityteam /opt/securitylab
sudo chmod 2770 /opt/securitylab
ls -ld /opt/securitylab
```
Mode `2770` grants full access to owner and group, denies access to others, and sets the setgid bit. The setgid bit helps new items inherit the directory's group.

### 5. Create a restricted file
```bash
sudo touch /opt/securitylab/confidential.txt
sudo chown root:securityteam /opt/securitylab/confidential.txt
sudo chmod 640 /opt/securitylab/confidential.txt
ls -l /opt/securitylab/confidential.txt
```
Mode `640`: owner read/write, group read, others no permissions.

### 6. Test access as the practice user
```bash
su - student1
id
cat /opt/securitylab/confidential.txt
echo "Permission test" >> /opt/securitylab/confidential.txt
exit
```
A member of `securityteam` should be able to read the file. Appending should be denied because the group has read-only permission. Record the exact output from your VM; do not invent results. If group membership is not reflected in an existing session, start a fresh session and check again.

### 7. Demonstrate and correct broad permissions
Use only a disposable test file:
```bash
sudo touch /tmp/weak_permissions.txt
sudo chmod 777 /tmp/weak_permissions.txt
ls -l /tmp/weak_permissions.txt
sudo chmod 600 /tmp/weak_permissions.txt
ls -l /tmp/weak_permissions.txt
```
Mode `777` grants read, write, and execute permissions to everyone. Mode `600` grants read/write access only to the owner. Do not apply these modes indiscriminately to system files.

## Expected observations
Exact IDs, hostnames, distribution details, and terminal messages depend on your VM. Generally:
- `id student1` reports UID, GID, and group memberships.
- `ls -ld` displays directory ownership and permission bits.
- `ls -l` displays file ownership and permission bits.
- The access test demonstrates the difference between group read and group write access.
- The test file's permission display changes after correction.

A command's output alone does not prove a real-world vulnerability. Explain results in the context of this isolated lab.

## Evidence checklist
- [ ] Linux distribution/version (`cat /etc/os-release`)
- [ ] Practice user created and verified
- [ ] Group membership verified
- [ ] Directory owner/group and mode
- [ ] File owner/group and mode
- [ ] Actual read/write access test as `student1`
- [ ] Before-and-after test-file permissions
- [ ] Short explanation of least privilege

Save your own screenshots in `screenshots/`. Remove personal information and secrets before publishing.

## Cleanup
Only if these are the exact test resources you created and you have confirmed they contain nothing you need, remove them from your disposable VM:
```bash
sudo userdel -r student1
sudo groupdel securityteam
sudo rm -rf /opt/securitylab
sudo rm -f /tmp/weak_permissions.txt
```
Review every path before running cleanup commands.

## Reflection
After performing the lab, explain what you learned about users/groups, `chmod`, `chown`, least privilege, and what your actual access tests showed.

## References
- [Red Hat Enterprise Linux 9 documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9)
- [chmod manual](https://man7.org/linux/man-pages/man1/chmod.1.html)
- [chown manual](https://man7.org/linux/man-pages/man1/chown.1.html)
- [useradd manual](https://man7.org/linux/man-pages/man8/useradd.8.html)
- [usermod manual](https://man7.org/linux/man-pages/man8/usermod.8.html)
