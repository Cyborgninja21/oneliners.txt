# OPENSSL

=======

```
(*) Encrypt a file using OpenSSL: `openssl aes-256-cbc -a -salt -iter 5 -in data.tar.gz -out data.enc`
(*) Decrypt a file using OpenSSL: `openssl aes-256-cbc -d -a -iter 5 -in data.enc -out data_decrypted.tar.gz`
(*) List SAN domains for a certificate: `echo | openssl s_client -connect google.com:443 2>&1 | openssl x509 -noout -text |  awk -F, -v OFS="\n" '/DNS:/{x=gsub(/ *DNS:/, ""); $1=$1; print $0}'`
(*) Download certificate from FTP: `echo | openssl s_client -servername ftp.domain.com -connect ftp.domain.com:21 -starttls ftp 2>/dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'`
(*) Download certificate chain from FTP: `echo | openssl s_client -showcerts -connect ftp.domain.com:ftp -starttls ftp 2>/dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p'`
(*) Check SSL expiry from commandline: `echo | openssl s_client -showcerts -servername google.com -connect gnupg.org:443 2>/dev/null | openssl x509 -inform pem -noout -text`
(*) SHA256 signature sum check of file: `openssl dgst -sha256  <FILENAME>`
(*) Generate a random password 30 characters long: `openssl rand -rand /dev/urandom -base64 30`
(*) Openssl Generate Self Signed SSL Certifcate: `openssl req -newkey rsa:2048 -nodes -keyout /etc/ssl/private/myblog.key -x509 -days 365 -out /etc/ssl/private/myblog.pem`
(*) Generate a certificate signing request (CSR) for an existing private key. CSR.csr MUST be exists before: `openssl req -out CSR.csr -key privateKey.key -new`
(*) Generate a new private key and Certificate Signing Request. CSR.csr MUST be extist before !: `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key`
(*) Generate pem cert from host with ssl port: `openssl s_client -connect HOSTNAME.at:443 -showcerts </dev/null 2>/dev/null | openssl x509 -outform PEM > meinzertifikat.pem`
(*) Download SSL/TLS pem format cert from https web host: `openssl s_client -showcerts -connect google.com:443 </dev/null 2>/dev/null | openssl x509 -outform PEM > /tmp/google.com.cer`
(*) Host cpu performance: `openssl speed md5`
(*) Finding the fingerprint of a given certificate: `openssl x509 -in cert.pem -fingerprint -noout`
(*) Generate a certificate signing request based on an existing certificate. certificate.crt MUST be exists before !: `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key`
(*) Connect to a server and show full certificate chain: `openssl s_client -showcerts -host example.com -port 443 </dev/null`
(*) Encrypted archive with openssl and tar: `tar --create --file - --posix --gzip -- <dir> | openssl enc -e -aes256 -out <file>`
```