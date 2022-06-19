Role Name
=========

This role installs and configures a OpenVPN Server, including certificate management with EasyRSA.

Requirements
------------

Internet access to download EasyRSA.

Role Variables
--------------

General settings

This role uses the following variables. For certificate generation:
- `openvpn_easyrsa_install_location`: Directory for easyrsa installation directory.
- `openvpn_easyrsa_pki`: Directory with certificates.
- `openvpn_easyrsa_key_size`: Certificate key size.
- `openvpn_easyrsa_ca_pass`: Password for EasyRSA certificate authority.
- `openvpn_easyrsa_ca_expire`: CA certificate expiry interval in days.
- `openvpn_easyrsa_cert_expire`: Certificate expiry interval in days.
- `openvpn_easyrsa_crl_days`: Expected interval for new CRL publish.
- `openvpn_easyrsa_clients`: List of clients, dict per client with `hostname` and optional `password`. Example:
```yaml
openvpn_easyrsa_clients:
- hostname: client0
- hostname: client1
  password: MyPass
```
- `openvpn_easyrsa_req_country`: EasyRSA Country.
- `openvpn_easyrsa_req_province`: EasyRSA Province.
- `openvpn_easyrsa_req_city`: EasyRSA City.
- `openvpn_easyrsa_req_org`: EasyRSA Organisation.
- `openvpn_easyrsa_req_email`: EasyRSA Email.
- `openvpn_easyrsa_req_ou`: EasyRSA OU.

For OpenVPN:
- `openvpn_remote_address`: Remote address which clients connect to.
- `openvpn_remote_port`: Remote port.
- `openvpn_remote_protocol`: Protocol; udp or tcp. (Lowercase!)
- `openvpn_server_interface`: Name of network interface.
- `openvpn_server_subnet`: Subnet of OpenVPN network (CIDR notation).
- `openvpn_server_push_routes`: List of routes to push to clients (CIDR notation).
- `openvpn_server_name_server`: Nameserver for clients to use.
- `openvpn_server_domain_name`: Domain name to push to clients.
- `openvpn_server_dhpem`: Contents of dh.pem Diffie Hellman parameters. So you can generate it on a capable PC. Otherwise this will take more than 12 hours on less-capable CPUs. (13 hours for a RPi4.) Generate with `openssl dhparam -2 4096`.
- `openvpn_tls_auth`: Whether to configure TLS authentication. (Boolean.)

Dependencies
------------

This playbook was designed to have as little as possible dependencies. It was made with low-power embedded devices in mind (Ubiquiti EdgeOS for example). It does need internet access in order to retrieve EasyRSA and OpenVPN.

Example Playbook
----------------

This playbook can be used as follows.
- This repo in in `roles/openvpn`.
- Playbook site.yml:
```
- hosts: openvpn
  roles:
  - openvpn
```
- Inventory:
```
all:
  children:
    openvpn:
      hosts:
        sample-host:
      vars:
        openvpn_easyrsa_install_location: /opt/easyrsa
        openvpn_easyrsa_pki: /etc/openvpn/pki
        openvpn_eayrsa_key_size: 4096
        openvpn_easyrsa_ca_expire: 3650
        openvpn_easyrsa_cert_expire: 365
        openvpn_easyrsa_crl_days: 180
        openvpn_easyrsa_req_country: NL
        openvpn_easyrsa_req_province: Utrecht
        openvpn_easyrsa_req_city: Utrecht
        openvpn_easyrsa_req_org: PersonalVPN
        openvpn_easyrsa_req_ou: UtrechtVPN
        openvpn_remote_address: "{{ inventory_hostname }}"
        openvpn_remote_port: 1194
        openvpn_remote_protocol: udp
        openvpn_server_interface: tun0
        openvpn_server_subnet: 192.168.254.0/24
        openvpn_server_push_routes:
        - 192.168.253.0/24
        openvpn_server_name_server: 1.1.1.1
        openvpn_server_domain_name: myvpn.local
        openvpn_tls_auth: yes

        # Please put these in an Ansible Vault!
        openvpn_easyrsa_ca_pass: pleasechangeme
        openvpn_easyrsa_clients:
        - hostname: client0
          password: certificate_pass
        openvpn_server_dhpem: |
          -----BEGIN DH PARAMETERS-----
          MIICCAKCAgEAl9kZKK4ALvSUKleo/vGdbq3nfqNHLToEXaMm+GmCRwDMWHVv6K3O
          S5xrIMK7y1ZMOsa8jcORlUBJS9cCTMSFN+NZqHxc44A0S99aqIWW+ppEDkmbjc9J
          HwenrL9nGZT2WycZlFWkIOcWRbd4Ra3zrm4Lw7mxvhvABLyDDnwg6Bg9xUOFAHZT
          j59C7aa75BxSohnDNhjsnA/LvdXTt0Fn3idvzri/aF6aPwNk1yGeMzJ+e7gNLnbc
          8Gah7T5EsmQ3mbvmC5bgl5qxKJWrPs/gk7Pe0A2MswixPwTrlK3d/NlXnRylxdnv
          cP6UCBLSbzY7d34+PEdlBzfTVQqtfJp6zJ52c05NdyoNyVvMqf6NvaxEh4nna7Il
          P7DSHTVGUCHkpFF5MHyQC8QCjmmDq3cVAaOJ237yIKvkGoKckan5IfjX3kukym+x
          AQ8LizxzarzglVX10h6nR03X+wC/cUT+9sXg1Q2fnOyHgVun06Szl80i6ydWLqEO
          QBjNXowgWE07X3OeTmFCx3Gd829Ci1EdXL0hE8QnZAqftWrutByDngvBJ1x3fMii
          yuF9WVvwEwU4VkTxqlLZOaQYzXErvKysBOhXgn5yoLUC+fF2GlHXMCStv4J1UFRE
          7YxklKHracmojy2efrhJAuqGO0979Bb8I9GyqL0mDq1iwrK4UcBTFjsCAQI=
          -----END DH PARAMETERS-----
```      

License
-------

GPL2.0

Author Information
------------------

Made by TomWis97.
