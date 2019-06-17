---
title: Ligar a uma máquina de Virtual do SQL Server (Resource Manager) | Documentos da Microsoft
description: Saiba como ligar ao SQL Server em execução numa máquina Virtual no Azure. Este tópico utiliza o modelo de implementação clássica. Os cenários diferem consoante a configuração de rede e a localização do cliente.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 70e478ac70b7ab53f1357394f3a3cb0d92f41f00
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075795"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Ligar a uma Máquina Virtual do SQL Server no Azure

## <a name="overview"></a>Descrição geral

Este tópico descreve como ligar à sua instância do SQL Server em execução numa máquina virtual do Azure. Ele cobre algumas [cenários de conectividade](#connection-scenarios) e, em seguida, fornece [os passos no portal para alterar as definições de conectividade](#change). Se tiver de resolver problemas ou configurar a conectividade fora do portal, consulte a [configuração manual](#manual) no final deste tópico. 

Se preferiria ter um guia completo de aprovisionamento e conectividade, veja [aprovisionar uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Cenários de ligação

A forma como um cliente se conecta ao SQL Server em execução numa máquina Virtual é diferente consoante a localização do cliente e a configuração de rede.

Se aprovisionar uma VM do SQL Server no portal do Azure, tem a opção de especificar o tipo de **conectividade SQL**.

![Opção de conectividade SQL pública durante o aprovisionamento](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

As suas opções de conectividade incluem:

| Opção | Descrição |
|---|---|
| **Público** | Ligar ao SQL Server através da internet |
| **Privada** | Ligar ao SQL Server na mesma rede virtual |
| **Local** | Ligar ao SQL Server localmente na mesma máquina virtual | 

As secções seguintes explicam os **pública** e **privada** opções mais detalhadamente.

## <a name="connect-to-sql-server-over-the-internet"></a>Ligar ao SQL Server através da Internet

Se pretender ligar ao seu motor de base de dados do SQL Server a partir da Internet, selecione **pública** para o **conectividade SQL** tipo no portal do durante o aprovisionamento. O portal faz automaticamente os seguintes passos:

* Permite que o protocolo de TCP/IP para o SQL Server.
* Configura uma regra de firewall para abrir a porta TCP do SQL Server (padrão é 1433).
* Permite a autenticação do SQL Server, necessária para acesso público.
* Configura o grupo de segurança de rede na VM para todo o tráfego TCP na porta do SQL Server.

> [!IMPORTANT]
> As imagens de máquina virtual para o SQL Server Developer e as edições Express não ativa automaticamente o protocolo de TCP/IP. Para edições Developer e Express, tem de utilizar o Gestor de configuração do SQL Server para [ativar manualmente o protocolo de TCP/IP](#manualtcp) depois de criar a VM.

Qualquer cliente com acesso à internet, pode ligar à instância do SQL Server ao especificar o endereço IP público da máquina virtual ou qualquer etiqueta DNS atribuído a esse endereço IP. Se a porta do SQL Server é 1433, não é necessário especificá-la na cadeia de ligação. A seguinte cadeia de ligação se liga a uma VM do SQL com uma etiqueta de DNS `sqlvmlabel.eastus.cloudapp.azure.com` utilizando a autenticação do SQL (também poderia usar o endereço IP público).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Embora isso permita a conectividade para clientes através da internet, isso não significa que qualquer pessoa pode ligar ao SQL Server. Fora os clientes têm do nome de utilizador correto e a palavra-passe. No entanto, para segurança adicional, pode evitar esta porta 1433. Por exemplo, se tiver configurado o SQL Server para escutar na firewall apropriadas porta 1500 e estabelecidos e regras de grupo de segurança de rede, pode ligar ao acrescentar o número de porta para o nome do servidor. O exemplo seguinte altera o um anterior ao adicionar um número de porta personalizada, **1500**, para o nome do servidor:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Quando consultar o SQL Server numa VM através da internet, todos os dados de saída do datacenter do Azure está sujeito a normal [dos preços das transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Ligar ao SQL Server numa rede virtual

Quando escolhe **privada** para o **conectividade SQL** tipo no portal, o Azure configura a maioria das configurações idênticas aos **pública**. A única diferença é que não existe nenhuma regra de grupo de segurança de rede para permitir o tráfego externo na porta do SQL Server (padrão é 1433).

> [!IMPORTANT]
> As imagens de máquina virtual para o SQL Server Developer e as edições Express não ativa automaticamente o protocolo de TCP/IP. Para edições Developer e Express, tem de utilizar o Gestor de configuração do SQL Server para [ativar manualmente o protocolo de TCP/IP](#manualtcp) depois de criar a VM.

Conectividade privada, muitas vezes, é utilizada em conjunto com [rede Virtual](../../../virtual-network/virtual-networks-overview.md), que permite vários cenários. Pode ligar as VMs na mesma rede virtual, mesmo que essas VMs existem em grupos de recursos diferentes. E com um [VPN site a site](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), pode criar uma arquitetura híbrida que se liga a VMs com redes no local e máquinas.

Redes virtuais também permitem-lhe associar as suas VMs do Azure a um domínio. Esta é a única forma de utilizar a autenticação do Windows para o SQL Server. Os outros cenários de ligação exigem a autenticação de SQL com os nomes de utilizador e palavras-passe.

Partindo do princípio de que configurou o DNS na sua rede virtual, pode ligar à sua instância do SQL Server, especificando o nome de computador de VM do SQL Server na cadeia de ligação. O exemplo seguinte também pressupõe que a autenticação do Windows também foi configurada e se foram concedido ao utilizador acesso à instância do SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Alterar definições de conectividade SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pode alterar as definições de conectividade para a máquina virtual do SQL Server no portal do Azure.

1. No portal do Azure, selecione **máquinas virtuais do SQL**.

2. Selecione a sua VM do SQL Server.

3. Sob **configurações**, selecione **segurança**.

4. Alteração da **nível de conectividade SQL** para sua definição necessária. Opcionalmente, pode usar essa área para alterar a porta do SQL Server ou as definições de autenticação do SQL.

   ![Alterar a conectividade SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Aguarde alguns minutos até que a atualização concluir.

   ![Notificação de atualização de VM do SQL Server](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Ativar TCP/IP para edições Developer e Express

Quando alterar as definições de conectividade do SQL Server, Azure não ativa automaticamente o protocolo de TCP/IP para o SQL Server Developer e Express editions. Os passos abaixo explicam como ativar manualmente o TCP/IP para que consiga ligar remotamente através de um endereço IP.

Em primeiro lugar, ligue à máquina do SQL Server com o ambiente de trabalho remoto.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Em seguida, ative o protocolo de TCP/IP com **Gestor de configuração do SQL Server**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Ligar com o SSMS

Os passos seguintes mostram como criar uma etiqueta de DNS opcional para a sua VM do Azure e, em seguida, ligue-se com o SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Configuração manual e resolução de problemas

Embora o portal fornece opções para configurar automaticamente a conectividade, é útil para saber como configurar manualmente a conectividade. Compreender os requisitos também pode ajudar a resolução de problemas.

A tabela seguinte lista os requisitos para ligar ao SQL Server em execução numa VM do Azure.

| Requisito | Descrição |
|---|---|
| [Ativar o modo de autenticação do SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | Autenticação do SQL Server é necessária para ligar à VM remotamente, a menos que tiver configurado o Active Directory numa rede Virtual. |
| [Criar um início de sessão SQL](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Se estiver a utilizar autenticação do SQL, tem um início de sessão de SQL com um nome de utilizador e palavra-passe que também tem permissões para a base de dados de destino. |
| [Ativar o protocolo TCP/IP](#manualtcp) | SQL Server tem de permitir ligações através de TCP. |
| [Ativar regra de firewall para a porta do SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | A firewall na VM tem de permitir tráfego de entrada na porta do SQL Server (padrão é 1433). |
| [Criar uma regra de grupo de segurança de rede para TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Tem de permitir a VM receber o tráfego na porta do SQL Server (padrão é 1433) se pretender ligar através da internet. Ligações de locais e virtuais rede-só não exigem isso. Este é o único passo obrigatório no portal do Azure. |

> [!TIP]
> Os passos na tabela acima são feitos por si quando configurar a conectividade no portal. Utilize apenas estes passos para confirmar a sua configuração ou para configurar manualmente a conectividade para o SQL Server.

## <a name="next-steps"></a>Próximos Passos

Para ver instruções juntamente com estes passos de conectividade de aprovisionamento, consulte [aprovisionar uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para outros tópicos relacionados ao executar o SQL Server em VMs do Azure, consulte [SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).