---
title: Conectar-se a uma máquina virtual SQL Server (Resource Manager) | Microsoft Docs
description: Saiba como se conectar ao SQL Server em execução em uma máquina virtual no Azure. Este tópico usa o modelo de implantação clássico. Os cenários diferem de acordo com a configuração de rede e o local do cliente.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ae5c4cdd76f164d13da349c355a30d8b6dc83058
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102092"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Conectar-se a uma máquina virtual SQL Server no Azure

## <a name="overview"></a>Descrição geral

Este tópico descreve como se conectar à instância do SQL Server em execução em uma máquina virtual do Azure. Ele aborda alguns [cenários gerais de conectividade](#connection-scenarios) e, em seguida, fornece [as etapas no portal para alterar as configurações de conectividade](#change). Se você precisar solucionar problemas ou configurar a conectividade fora do portal, consulte a [configuração manual](#manual) no final deste tópico. 

Se você preferir ter um passo a passo completo de provisionamento e conectividade, consulte Provisionando [uma máquina Virtual SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Cenários de conexão

A maneira como um cliente se conecta ao SQL Server em execução em uma máquina virtual difere dependendo do local do cliente e da configuração de rede.

Se você provisionar uma VM SQL Server no portal do Azure, terá a opção de especificar o tipo de **conectividade SQL**.

![Opção de conectividade pública do SQL durante o provisionamento](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

As opções de conectividade incluem:

| Opção | Descrição |
|---|---|
| **Publicada** | Conectar-se a SQL Server pela Internet |
| **Pessoal** | Conectar-se a SQL Server na mesma rede virtual |
| **Local** | Conectar-se a SQL Server localmente na mesma máquina virtual | 

As seções a seguir explicam as opções **públicas** e **privadas** mais detalhadamente.

## <a name="connect-to-sql-server-over-the-internet"></a>Conectar-se a SQL Server pela Internet

Se você quiser se conectar ao seu mecanismo de banco de dados SQL Server da Internet, selecione **público** para o tipo de **conectividade SQL** no portal durante o provisionamento. O portal executa automaticamente as seguintes etapas:

* Habilita o protocolo TCP/IP para SQL Server.
* Configura uma regra de firewall para abrir a porta TCP SQL Server (padrão 1433).
* Habilita a autenticação SQL Server, necessária para acesso público.
* Configura o grupo de segurança de rede na VM para todo o tráfego TCP na porta SQL Server.

> [!IMPORTANT]
> As imagens de máquina virtual para as edições SQL Server Developer e Express não habilitam automaticamente o protocolo TCP/IP. Para as edições Developer e Express, você deve usar SQL Server Configuration Manager para [habilitar manualmente o protocolo TCP/IP](#manualtcp) depois de criar a VM.

Qualquer cliente com acesso à Internet pode se conectar à instância de SQL Server especificando o endereço IP público da máquina virtual ou qualquer rótulo DNS atribuído a esse endereço IP. Se a porta de SQL Server for 1433, você não precisará especificá-la na cadeia de conexão. A cadeia de conexão a seguir se conecta a uma VM do SQL com `sqlvmlabel.eastus.cloudapp.azure.com` um rótulo DNS de usar a autenticação do SQL (você também pode usar o endereço IP público).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Embora isso habilite a conectividade para clientes pela Internet, isso não significa que qualquer pessoa possa se conectar ao seu SQL Server. Clientes externos têm o nome de usuário e a senha corretos. No entanto, para segurança adicional, você pode evitar a porta conhecida 1433. Por exemplo, se você configurou SQL Server para escutar na porta 1500 e estabeleceu regras apropriadas de firewall e de grupo de segurança de rede, você pode se conectar acrescentando o número da porta ao nome do servidor. O exemplo a seguir altera o anterior adicionando um número de porta personalizado, **1500**, ao nome do servidor:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Quando você consulta SQL Server em uma VM pela Internet, todos os dados de saída do datacenter do Azure estão sujeitos aos preços normais das [transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Conectar-se a SQL Server em uma rede virtual

Quando você escolhe **privado** para o tipo de **conectividade SQL** no portal, o Azure configura a maioria das configurações de forma idêntica a **pública**. A única diferença é que não há nenhuma regra de grupo de segurança de rede para permitir tráfego externo na porta de SQL Server (padrão 1433).

> [!IMPORTANT]
> As imagens de máquina virtual para as edições SQL Server Developer e Express não habilitam automaticamente o protocolo TCP/IP. Para as edições Developer e Express, você deve usar SQL Server Configuration Manager para [habilitar manualmente o protocolo TCP/IP](#manualtcp) depois de criar a VM.

A conectividade privada é geralmente usada em conjunto com a [rede virtual](../../../virtual-network/virtual-networks-overview.md), o que permite vários cenários. Você pode conectar VMs na mesma rede virtual, mesmo se essas VMs existirem em diferentes grupos de recursos. E com uma [VPN site a site](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), você pode criar uma arquitetura híbrida que conecta VMs com redes e computadores locais.

As redes virtuais também permitem que você ingresse suas VMs do Azure em um domínio. Essa é a única maneira de usar a autenticação do Windows para SQL Server. Os outros cenários de conexão exigem a autenticação do SQL com nomes de usuário e senhas.

Supondo que você tenha configurado o DNS em sua rede virtual, você pode se conectar à instância do SQL Server especificando o nome do computador da VM SQL Server na cadeia de conexão. O exemplo a seguir também pressupõe que a autenticação do Windows também foi configurada e que o usuário recebeu acesso à instância de SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a>Alterar as configurações de conectividade do SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Você pode alterar as configurações de conectividade para sua máquina virtual SQL Server no portal do Azure.

1. No portal do Azure, selecione **máquinas virtuais do SQL**.

2. Selecione sua VM SQL Server.

3. Em **configurações**, selecione **segurança**.

4. Altere o **nível de conectividade do SQL** para a configuração necessária. Opcionalmente, você pode usar essa área para alterar a porta SQL Server ou as configurações de autenticação do SQL.

   ![Alterar a conectividade do SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Aguarde alguns minutos para que a atualização seja concluída.

   ![Notificação de atualização da VM do SQL](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a>Habilitar TCP/IP para edições Developer e Express

Ao alterar SQL Server configurações de conectividade, o Azure não habilita automaticamente o protocolo TCP/IP para as edições SQL Server Developer e Express. Os passos abaixo explicam como ativar manualmente o TCP/IP para que consiga ligar remotamente através de um endereço IP.

Primeiro, conecte-se ao computador SQL Server com a área de trabalho remota.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Em seguida, habilite o protocolo TCP/IP com **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Ligar com o SSMS

As etapas a seguir mostram como criar um rótulo DNS opcional para sua VM do Azure e, em seguida, conectar-se com SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a>Configuração manual e solução de problemas

Embora o portal forneça opções para configurar a conectividade automaticamente, é útil saber como configurar a conectividade manualmente. Entender os requisitos também pode ajudar a solucionar problemas.

A tabela a seguir lista os requisitos para se conectar ao SQL Server em execução em uma VM do Azure.

| Requisito | Descrição |
|---|---|
| [Habilitar o modo de autenticação de SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | SQL Server autenticação é necessária para se conectar à VM remotamente, a menos que você tenha configurado Active Directory em uma rede virtual. |
| [Criar um logon do SQL](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Se você estiver usando a autenticação do SQL, precisará de um logon do SQL com um nome de usuário e senha que também tenham permissões para o banco de dados de destino. |
| [Habilitar o protocolo TCP/IP](#manualtcp) | SQL Server deve permitir conexões via TCP. |
| [Habilitar Regra de firewall para a porta de SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | O firewall na VM deve permitir o tráfego de entrada na porta SQL Server (padrão 1433). |
| [Criar uma regra de grupo de segurança de rede para TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Você deve permitir que a VM receba tráfego na porta SQL Server (padrão 1433) se desejar se conectar pela Internet. Conexões somente de rede local e virtual não exigem isso. Essa é a única etapa necessária no portal do Azure. |

> [!TIP]
> As etapas na tabela acima são feitas para você quando você configura a conectividade no Portal. Use estas etapas apenas para confirmar sua configuração ou para configurar a conectividade manualmente para SQL Server.

## <a name="next-steps"></a>Próximos Passos

Para ver as instruções de provisionamento junto com essas etapas de conectividade, consulte Provisionando [uma máquina Virtual SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

Para outros tópicos relacionados à execução de SQL Server em VMs do Azure, consulte [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).