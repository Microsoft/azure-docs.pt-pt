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
ms.date: 07/27/2020
ms.author: b-juche
ms.openlocfilehash: bdcffcea8d695b6a3d49272ffa135187d77aae1e
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535483"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>Configure um cliente NFS para ficheiros Azure NetApp

Uma grande variedade de distribuições Linux estão disponíveis para usar com ficheiros Azure NetApp. Este artigo descreve configurações para dois dos ambientes mais utilizados: RHEL 8 e Ubuntu 18.04.

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

1. Instalar pacotes:  
    `sudo yum -y install realmd packagekit sssd adcli samba-common krb5-workstation chrony`

2. Configure o cliente NTP.  
    Ubuntu 18.04 utiliza `chrony` por predefinição.  Seguindo as diretrizes de configuração em [Ubuntu Bionic: Utilizar o crono para configurar o NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp).

3. Junte-se ao Domínio do Diretório Ativo:  
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou= OU=$YOUROU,DC=$DOMAIN,DC=TLD`

## <a name="next-steps"></a>Passos seguintes  

* [Criar um volume NFS para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Criar um volume de duplo protocolo para ficheiros Azure NetApp](create-volumes-dual-protocol.md)

