---
title: Configure um cliente NFS para a Azure NetApp Files Microsoft Docs
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
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: b2e597ff8fc761b66de6228063c471933a364144
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449644"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Configurar um cliente NFS para o Azure NetApp Files

A configuração do cliente NFS descrita neste artigo faz parte da configuração quando configura a [encriptação NFSv4.1 Kerberos](configure-kerberos-encryption.md) ou [cria um volume de duplo protocolo](create-volumes-dual-protocol.md). Uma grande variedade de distribuições Linux estão disponíveis para usar com ficheiros Azure NetApp. Este artigo descreve configurações para dois dos ambientes mais utilizados: RHEL 8 e Ubuntu 18.04. 

Independentemente do sabor Linux que usa, são necessárias as seguintes configurações:
* Configure um cliente NTP para evitar problemas com o distorção do tempo.
* Configure as entradas DNS do cliente Linux para resolução de nomes.  
    Esta configuração inclui o registo "A" (para a frente) e o registo PTR (inverso). 
* Para a união de domínios, crie uma conta de computador no Diretório Ativo alvo (que é criado durante o comando de junção do reino). 
    > [!NOTE] 
    > A `$SERVICEACCOUNT` variável utilizada nos comandos abaixo deve ser uma conta de utilizador com permissões ou delegação para criar uma conta de computador na Unidade Organizacional específica.
* Capacite o cliente a montar volumes NFS e outras ferramentas de monitorização relevantes.

## <a name="rhel-8-configuration"></a>Configuração RHEL 8 

1. Instalar pacotes:   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony`

2. Configure o cliente NTP:  
    O RHEL 8 utiliza `chrony` por defeito.  Seguindo as diretrizes de configuração na [utilização da suite Chrony para configurar o NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp).

3. Junte-se ao domínio do Diretório Ativo:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="ubuntu-configuration"></a>Configuração Ubuntu 
Esta secção descreve a configuração Ubuntu para os clientes NFS.  

### <a name="if-you-are-using-nfsv41-kerberos-encryption"></a>Se estiver a usar encriptação NFSv4.1 Kerberos 

1. Instalar pacotes:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Configure o cliente NTP.  
    Ubuntu 18.04 utiliza `chrony` por predefinição.  Seguindo as diretrizes de configuração em [Ubuntu Bionic: Utilizar o crono para configurar o NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Junte-se ao Domínio do Diretório Ativo:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

### <a name="if-you-are-using-dual-protocol"></a>Se estiver a utilizar o protocolo duplo  

1. Executar o seguinte comando para atualizar os pacotes instalados:  
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    Exemplo:   

    `base dc=hariscus,dc=com` `uri ldap://10.20.0.4:389/`
    `ldap_version 3`
    `rootbinddn cn=admin,cn=Users,dc=hariscus,dc=com`
    `pam_password ad`
 
2. Executar o seguinte comando para reiniciar e ativar o serviço:   
    `sudo systemctl restart nscd && sudo systemctl enable nscd`

O exemplo seguinte consulta o servidor AD LDAP do cliente Ubuntu LDAP para um utilizador LDAP `ldapu1` :   

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="next-steps"></a>Passos seguintes  

* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar um volume de duplo protocolo para ficheiros Azure NetApp](create-volumes-dual-protocol.md)

