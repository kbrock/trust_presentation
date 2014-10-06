user -> kdc (config step)
  password@head
  password(mdf) @ 
user -> ssh (password)
  password@head
  password(md5) @ file(/etc/passwd)

user -> ssh (password / pam)
  password@head
  password@pam:*

user -> ssh (cert)
  private_cert@file:id_rsa
    config: ssh-keygen
  public_cert@file:id_rsa.pub
    config: ssh-copy-id/scp (user/password)

user -> ssh (kerberos)
  user_ticket@file:ktab
    config: kinit username/password
      kdc
  user_ticket@kdc
    [need to talk with kdc to validate user ticket]
    kdc_password@ktab <- config: ipa-config creates ktab (...)
    kdc_id@file:ktab
      kdc_id@ldap
user -> apache (proxy)
  apache -> rails (same host)
    rails -> database
      host
    users#me5(password,salt)

user(admin) -> apache
  proxy == user -> ui

apache -> rails
  host
apache -> pam
  host/user
pam -> idm:ldap
  kerberos keytab <- ipa-config
pam -> idm:kdc
  kerberos keytab <- ipa-config

rails -> database (kerberos)
  pam (host / user_id)
rails -> database (password)
  file:database.yml *
  file:pg_shadow md5(password)
rails -> database (host)
  allow through (host, user_id) - `pg_hosts.conf`
