# Ansible role: Jenkins with Pipeline

Installs a minimal installation of Jenkins with the Pipeline plugin to create jobs.
Assumes there will be one job for each git repository, and that the pipelines
are specified in `Jenkinsfile` at the root of each repository.

The `jenkins` user must be able to clone from the git host, so the role also
install SSH keys.

```yaml
- name: jenkins-pipeline
  jenkins_admin_password: use-a-vault-variable-for-this
  jenkins_ssh_private_key: jenkins-id_rsa
  jenkins_ssh_public_key: jenkins-id_rsa.pub
  jenkins_git_user: git
  jenkins_git_host: git.example.com
  jenkins_git_path: git
  jenkins_git_repositories:
  - your-repo
```

See [this blog post](https://wjoel.com/posts/ansible-jenkins-pipeline-part-1.html)
for more information.

## SSH keys

You may need to add SSH keys to `known_hosts` for Jenkins to be able
to check out git repositories. This can also be done with Ansible.
Here's an example:

```yaml
  tasks:
  - name: Add git.example.com to known hosts
    become: yes
    known_hosts:
      path: '~jenkins/.ssh/known_hosts'
      name: git.example.com
      key: "git.example.com ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQDKpPLDi490gas7v6DWscdzSoWOOZEAa70riTVVQlD/FXwjMA8ZX4q/InySgsEIseqvXqKwpQ+AncSnTNHIKy4Q6kT1n0tFmqyJTbBsfx26ljqZ0KkEm1PtHaNtXYpEfq1LHK8a813/HDiBpdJtbU7R5pCqFE+vd52gZyUG0WRA9CcZSmWj1PdiwfLIMx8CI0arsYbh02/pPeQ8NIGNgZ7H7cDtjqFp0bOYkO8KVJZajpr7YsYI6CBnSuAwaCicdYvAbY0EgGkHyNRFJWDLOrHyHsUOntxm7a6jFTkIxZ3Sri0R9EcpJV9/WHhW4GRm5SfqW7uHzU9qJ2/8a2s+AjP13Hv5H08iXefKQ8BzuB9qMxbC2uRq3WHAU6/T5H4/N7BTsqTE/cTEfZspUG9JeRBQMS3GQt6TCEc7EbZ1NzZjSBduwbJFop80TufED7sVT3LhkbzRDa45a45n1Q8N8vsnIrEWdCAaKwcdP1lIfzwWKoNzwznEJLNZdQr1lKa2R/nrUYLY0JUjwJyMgMYgxQriMccUqYzWBeE1McQkXFqec3Xda1hyMgA7lIe8sF5iMxZmed91GeddcUh6D0WJkkO4iI58OESlAJ28X9sVsWV/3W2kbmjeFnf/QZn6Sgc0QWFrZT4vMEMjJtWvL9itmIrON2fvAPKJIhhG6deNRfuPjw=="
  - name: Add git.example.com (ECDSA) to known hosts
    become: yes
    known_hosts:
      path: '~jenkins/.ssh/known_hosts'
      name: git.example.com
      key: "git.example.com ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGKIHhtR206LKUvevpSuL5nOt9LBzNVXkqRnBdBqhaXbuStPM2OXQQRUxgA3PBb05lhtbMXol7di1Qp75BDdJM4="
  - name: Set permissions for known_hosts
    become: yes
    file: path="~jenkins/.ssh/known_hosts" owner=jenkins group=jenkins mode=0600
```
