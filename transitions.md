
user -> ssh
  password@head
  password(md5)@ file(/etc/passwd)

user -> ssh
  private_cert@file:id_rsa
    config: ssh-keygen
  public_cert@file:id_rsa.pub
    config: ssh-copy-id/scp

user -> ssh
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


rails -> database
  kerberos:
rails -> database
  user/password:
    file:database.yml *
    file:pg_shadow md5(password)
rails -> database
  host:
    allow through
