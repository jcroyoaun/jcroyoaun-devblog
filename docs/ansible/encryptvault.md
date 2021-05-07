ENCRYPT PASSWORDS WITH ANSIBLE-VAULT
==========================
Introduction
-----------
This is a short, concise tutorial on how to encrypt a value (a password, for example) to be used in an Ansible playbook. 

Problem that solves :
------------
We sometimes need to declare passwords within our Ansible scripts, and we don't want to expose them to the world.

How to:
```
ansible-vault encrypt_string --vault-password-file /az/srvr/private/passfile 'mypass' --name 'myencrypted_pass'
```

This would result in something like :
```
myencrypted_pass: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          30633536656461393734306565666636323165643733616461316261623934366435396663303934
          6532646233303864373439313135396461383363366163330a613862636532613130343034356565
          66346130643139303564363063306666333436613462373461643137633135373233363863376266
          3561376137316436350a323637393730333662636565613836353938613834383062353164613164
          3334
```