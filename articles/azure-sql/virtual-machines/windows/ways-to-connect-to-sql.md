---
title: Ligue-se a uma máquina virtual do Servidor SQL (Gestor de Recursos) [ Microsoft Docs
description: Aprenda a ligar-se ao SQL Server em execução numa máquina virtual em Azure. Este tópico usa o modelo de implantação clássico. Os cenários diferem consoante a configuração de rede e a localização do cliente.
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
ms.openlocfilehash: 60690aab0d1f8ccc618c3e147f553a8fda704047
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041953"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Ligar a uma Máquina Virtual do SQL Server no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Descrição geral

Este tópico descreve como se conectar ao seu sQL Server em execução numa máquina virtual Azure. Abrange [alguns cenários gerais](#connection-scenarios) de conectividade e, em seguida, fornece [passos no portal para alterar as definições](#change)de conectividade . Se precisar de resolver problemas ou configurar a conectividade fora do portal, consulte a [configuração manual](#manual) no final deste tópico. 

Se preferir ter um walk-through completo tanto de provisionamento como de conectividade, consulte [o Provisioning a SQL Server Virtual Machine on Azure](create-sql-vm-portal.md).

## <a name="connection-scenarios"></a>Cenários de conexão

A forma como um cliente se conecta ao SQL Server a funcionar numa Máquina Virtual difere consoante a localização do cliente e a configuração de rede.

Se fornecer um VM de servidor SQL no portal Azure, tem a opção de especificar o tipo de **conectividade SQL**.

![Opção pública de conectividade SQL durante o provisionamento](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

As suas opções de conectividade incluem:

| Opção | Descrição |
|---|---|
| **Público** | Ligue-se ao SQL Server através da internet |
| **Privado** | Ligue-se ao SQL Server na mesma rede virtual |
| **Local** | Ligue-se ao SQL Server localmente na mesma máquina virtual | 

As seguintes secções explicam mais detalhadamente as opções **públicas** e **privadas.**

## <a name="connect-to-sql-server-over-the-internet"></a>Ligue-se ao SQL Server através da Internet

Se pretender ligar-se ao seu motor de base de dados SQL Server a partir da Internet, selecione **Public** para o tipo de **conectividade SQL** no portal durante o fornecimento. O portal faz automaticamente os seguintes passos:

* Ativa o protocolo TCP/IP para o Servidor SQL.
* Configura uma regra de firewall para abrir a porta SQL Server TCP (padrão 1433).
* Permite a autenticação do servidor SQL, necessária para o acesso público.
* Configura o grupo de segurança da rede no VM para todo o tráfego de TCP na porta SQL Server.

> [!IMPORTANT]
> As imagens de máquinas virtuais para as edições SQL Server Developer e Express não permitem automaticamente o protocolo TCP/IP. Para edições de Desenvolvedor e Express, deve utilizar o SQL Server Configuration Manager para [ativar manualmente o protocolo TCP/IP](#manualtcp) após a criação do VM.

Qualquer cliente com acesso à Internet pode ligar-se à instância do SQL Server especificando o endereço IP público da máquina virtual ou qualquer etiqueta DNS atribuída a esse endereço IP. Se a porta SQL Server for 1433, não precisa de especificar na cadeia de ligação. A seguinte cadeia de ligação liga-se a um VM SQL com uma etiqueta DNS de utilização da `sqlvmlabel.eastus.cloudapp.azure.com` Autenticação SQL (pode também utilizar o endereço IP público).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Embora isto permita a conectividade para clientes através da internet, isso não implica que alguém possa ligar-se ao seu Servidor SQL. Os clientes externos têm o nome de utilizador e a palavra-passe corretos. No entanto, para segurança adicional, pode evitar o conhecido porto 1433. Por exemplo, se configurar o SQL Server para ouvir na porta 1500 e estabelecer regras adequadas de firewall e grupo de segurança de rede, pode ligar-se através da adesão do número de porta ao nome Server. O exemplo seguinte altera o anterior adicionando um número de porta personalizado, **1500,** ao nome do servidor:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Quando consulta o SQL Server num VM através da internet, todos os dados de saída do centro de dados Azure estão sujeitos a preços normais [nas transferências](https://azure.microsoft.com/pricing/details/data-transfers/)de dados de saída .

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Ligue-se ao SQL Server dentro de uma rede virtual

Ao escolher **o Privado** para o tipo de conectividade **SQL** no portal, o Azure confunde a maioria das configurações idênticas ao **Público**. A única diferença é que não existe uma regra do grupo de segurança de rede que permita tráfego externo na porta SQL Server (padrão 1433).

> [!IMPORTANT]
> As imagens de máquinas virtuais para as edições SQL Server Developer e Express não permitem automaticamente o protocolo TCP/IP. Para edições de Desenvolvedor e Express, deve utilizar o SQL Server Configuration Manager para [ativar manualmente o protocolo TCP/IP](#manualtcp) após a criação do VM.

A conectividade privada é frequentemente utilizada em conjunto com a [Rede Virtual](../../../virtual-network/virtual-networks-overview.md), que permite vários cenários. Pode ligar VMs na mesma rede virtual, mesmo que esses VMs existam em diferentes grupos de recursos. E com uma [VPN site-to-site,](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)você pode criar uma arquitetura híbrida que conecta VMs com redes e máquinas no local.

As redes virtuais também lhe permitem juntar os seus VMs Azure a um domínio. Esta é a única forma de utilizar a Autenticação do Windows para o Servidor SQL. Os outros cenários de ligação requerem autenticação SQL com nomes de utilizador e palavras-passe.

Assumindo que configuraram dNS na sua rede virtual, pode ligar-se à sua instância do Servidor SQL especificando o nome do computador VM do Servidor SQL na cadeia de ligação. O exemplo que se segue também pressupõe que a Autenticação do Windows também foi configurada e que o utilizador teve acesso à instância do Servidor SQL.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a>Alterar as definições de conectividade SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pode alterar as definições de conectividade para a sua máquina virtual SQL Server no portal Azure.

1. No portal Azure, selecione **máquinas virtuais SQL**.

2. Selecione o seu VM do Servidor SQL.

3. Em **Definições,** selecione **Security**.

4. Mude o **nível de conectividade SQL** para a definição necessária. Pode utilizar opcionalmente esta área para alterar a porta SQL Server ou as definições de Autenticação SQL.

   ![Alterar a conectividade SQL](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Aguarde alguns minutos para que a atualização esteja concluída.

   ![Notificação de atualização De VM SQL](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a>Ativar as edições TCP/IP para Edições de Desenvolvimento e Expresso

Ao alterar as definições de conectividade do Servidor SQL, o Azure não ativa automaticamente o protocolo TCP/IP para as edições SQL Server Developer e Express. Os passos abaixo explicam como ativar manualmente o TCP/IP para que consiga ligar remotamente através de um endereço IP.

Primeiro, ligue-se à máquina SQL Server com ambiente de trabalho remoto.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Em seguida, ative o protocolo TCP/IP com o Gestor de Configuração do **Servidor SQL**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Ligar com o SSMS

Os seguintes passos mostram como criar uma etiqueta DNS opcional para o seu VM Azure e, em seguida, ligar-se ao SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a>Configuração manual e resolução de problemas

Embora o portal ofereça opções para configurar automaticamente a conectividade, é útil saber configurar manualmente a conectividade. Compreender os requisitos também pode ajudar a resolver problemas.

A tabela seguinte lista os requisitos para ligar ao Servidor SQL em execução num VM Azure.

| Requisito | Descrição |
|---|---|
| [Ativar o modo de autenticação do servidor SQL](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | A autenticação do servidor SQL é necessária para se ligar remotamente ao VM, a menos que tenha configurado o Diretório Ativo numa Rede Virtual. |
| [Criar um login SQL](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Se estiver a utilizar a Autenticação SQL, necessita de um SQL Login com um nome de utilizador e uma palavra-passe que também tenha permissões na base de dados do alvo. |
| [Ativar o protocolo TCP/IP](#manualtcp) | O Servidor SQL deve permitir ligações sobre TCP. |
| [Ativar a regra de firewall para a porta SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | A firewall do VM deve permitir o tráfego de entrada na porta SQL Server (predefinido 1433). |
| [Criar uma regra de grupo de segurança de rede para TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Deve permitir que o VM receba tráfego na porta SQL Server (padrão 1433) se pretender ligar-se através da internet. As ligações locais e virtuais não o exigem. Este é o único passo necessário no portal Azure. |

> [!TIP]
> Os passos na tabela acima são feitos para si quando configurar a conectividade no portal. Utilize apenas estes passos para confirmar a sua configuração ou para configurar a conectividade manualmente para o Servidor SQL.

## <a name="next-steps"></a>Passos Seguintes

Para ver instruções de provisionamento juntamente com estes passos de conectividade, consulte [o fornecimento de uma máquina virtual do Servidor SQL no Azure](create-sql-vm-portal.md).

Para outros tópicos relacionados com a execução do Servidor SQL em VMs Azure, consulte [o Servidor SQL em Máquinas Virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).