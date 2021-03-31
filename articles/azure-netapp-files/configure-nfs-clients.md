---
title: Configure um cliente NFS para a Azure NetApp Files | Microsoft Docs
description: Descreve como configurar clientes NFS para usar com ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: c1cdeaa41dda11f2ab520cf8d31ddb2116587082
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94409574"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Configurar um cliente NFS para o Azure NetApp Files

A configuração do cliente NFS descrita neste artigo faz parte da configuração quando configura a [encriptação NFSv4.1 Kerberos](configure-kerberos-encryption.md) ou [cria um volume de duplo protocolo](create-volumes-dual-protocol.md). Uma grande variedade de distribuições Linux estão disponíveis para usar com ficheiros Azure NetApp. Este artigo descreve configurações para dois dos ambientes mais utilizados: RHEL 8 e Ubuntu 18.04. 

Independentemente do sabor Linux que usa, são necessárias as seguintes configurações:
* Configure um cliente NTP para evitar problemas com o distorção do tempo.
* Configure as entradas DNS do cliente Linux para resolução de nomes.  
    Esta configuração deve incluir o registo "A" (para a frente) e o registo PTR (inverso). 
* Para a união de domínios, crie uma conta de computador para o cliente Linux no Diretório Ativo alvo (que é criado durante o comando de junção do reino). 
    > [!NOTE] 
    > A `$SERVICEACCOUNT` variável utilizada nos comandos abaixo deve ser uma conta de utilizador com permissões ou delegação para criar uma conta de computador na Unidade Organizacional específica.

## <a name="rhel-8-configuration"></a>Configuração RHEL 8 

Esta secção descreve as configurações RHEL necessárias para encriptação NFSv4.1 Kerberos e protocolo duplo.  

Os exemplos desta secção utilizam o seguinte nome de domínio e endereço IP:  

* Nome de domínio: `contoso.com`
* IP privado: `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>Configuração RHEL 8 se estiver a utilizar encriptação NFSv4.1 Kerberos

1. Configure `/etc/resolv.conf` com o servidor DNS adequado.  

    Por exemplo:  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. Adicione o registo do cliente NFS no servidor DNS para a zona de procuração para a frente e para trás.

3. Para verificar o DNS, utilize os seguintes comandos do cliente NFS:   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. Instalar pacotes:   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  Configure o cliente NTP.  

    O RHEL 8 utiliza cronodinho por defeito. Seguindo as diretrizes de configuração [na utilização da `Chrony` suite para configurar o NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

6.  Junte-se ao domínio do Diretório Ativo:  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    Por exemplo: 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    Certifique-se de que `default_realm` está definido para o reino fornecido em `/etc/krb5.conf` .  Caso contrário, adicione-o `[libdefaults]` na secção do ficheiro, como mostra o seguinte exemplo:
    
    `default_realm = CONTOSO.COM`

7. Reiniciar todos os serviços da NFS:  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    O reinício evita a condição de erro durante a `“mount.nfs: an incorrect mount option was specified”` montagem de Kerberos.

8. Executar o `kinit` comando com a conta de utilizador para obter bilhetes: 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    Por exemplo:   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>Configuração RHEL 8 se estiver a utilizar o protocolo duplo

Os seguintes passos são opcionais. Só precisa de executar os passos se utilizar o mapeamento do utilizador no cliente NFS: 

1. Preencha todos os passos descritos na [configuração RHEL 8 se estiver a utilizar a secção de encriptação Kerberos NFSv4.1.](#rhel8_nfsv41_kerberos)   

2. Adicione um registo de DNS estático no seu ficheiro /etc/anfitriões para utilizar o nome de domínio totalmente qualificado (FQDN) para a sua AD, em vez de utilizar o endereço IP no ficheiro de configuração SSSD:  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. Adicione uma secção extra para os domínios resolverem os identificadores a partir do servidor AD LDAP:    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   

4. Certifique-se de que `/etc/nsswitch.conf` tem a `sss` entrada:   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. Reinicie o `sssd` serviço e limpe a cache:   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. Teste para garantir que o seu cliente está integrado com o servidor LDAP:   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Configuração Ubuntu   

Esta secção descreve as configurações Ubuntu necessárias para encriptação NFSv4.1 Kerberos e protocolo duplo. 

Os exemplos desta secção utilizam o seguinte nome de domínio e endereço IP:  

* Nome de domínio: `contoso.com`
* IP privado: `10.6.1.4`

1. Configure `/etc/resolv.conf` com o servidor DNS adequado:

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. Adicione o registo do cliente NFS no servidor DNS para a zona de procuração para a frente e para trás.
 
    Para verificar o DNS, utilize os seguintes comandos do cliente NFS:

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. Instalar pacotes:
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    Quando solicitado, `$DOMAIN.NAME` insira (utilizando maiúsculas, por exemplo, `CONTOSO.COM` ) como o reino padrão de Kerberos.

4. Reiniciar o `rpc-gssd.service` serviço: 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18.04 utiliza cronodinho por defeito. Seguindo as diretrizes de configuração em [Ubuntu Bionic: Utilizar o crono para configurar o NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

6. Junte-se ao domínio do Diretório Ativo:   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    Por exemplo:    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. Realize `kinit` com o utilizador para obter bilhetes: 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    Por exemplo:    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>Configuração Ubuntu se estiver a utilizar o protocolo duplo  

Os seguintes passos são opcionais.  Só precisa de executar os passos se pretender utilizar o mapeamento do utilizador no cliente NFS:  

1. Executar o seguinte comando para atualizar os pacotes instalados:   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    O exemplo a seguir utiliza valores de amostra. Quando o comando lhe pedir a entrada, deve fornecer informações com base no seu ambiente. 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. Executar o seguinte comando para reiniciar e ativar o serviço:

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

O exemplo seguinte consulta o servidor AD LDAP do cliente Ubuntu LDAP para um utilizador LDAP `‘hari1’` : 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>Passos seguintes  

* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar um volume de duplo protocolo para ficheiros Azure NetApp](create-volumes-dual-protocol.md)

