---
title: Ligue-se a uma máquina virtual SQL Server (Gestor de Recursos) | Microsoft Docs
description: Saiba como ligar-se à sua máquina virtual SQL Server no Azure. Este tópico utiliza o modelo clássico de implementação. Os cenários diferem consoante a configuração da rede e a localização do cliente.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37cb8f5a2ff0916f53ae50f5750664204ab1ba75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737494"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Ligue-se a uma máquina virtual SQL Server no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Descrição Geral

Este tópico descreve como ligar-se ao seu SQL na máquina virtual Azure (VM). Abrange [alguns cenários gerais de conectividade](#connection-scenarios) e, em seguida, fornece [passos no portal para alterar as definições de conectividade](#change). Se precisar de resolução de problemas ou configurar a conectividade fora do portal, consulte a [configuração manual](#manual) no final deste tópico. 

Se preferir ter uma passagem completa tanto de provisão como de conectividade, consulte [a Provisão de uma máquina virtual SQL Server no Azure](create-sql-vm-portal.md).

## <a name="connection-scenarios"></a>Cenários de conexão

A forma como um cliente se conecta a um SQL Server VM difere consoante a localização do cliente e a configuração de rede.

Se forvia um SQL Server VM no portal Azure, tem a opção de especificar o tipo de **conectividade SQL**.

![Opção de conectividade SQL pública durante o provisionamento](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

As suas opções de conectividade incluem:

| Opção | Descrição |
|---|---|
| **Público** | Ligue-se ao SQL Server através da internet. |
| **Privado** | Ligue-se ao SQL Server na mesma rede virtual. |
| **Local** | Ligue-se ao SQL Server localmente na mesma máquina virtual. | 

As seguintes secções explicam mais detalhadamente as opções **Públicas** e **Privadas.**

## <a name="connect-to-sql-server-over-the-internet"></a>Conecte-se ao SQL Server através da internet

Se pretender ligar-se ao seu motor de base de dados SQL Server a partir da internet, selecione **Public** para o tipo **de conectividade SQL** no portal durante o fornecimento. O portal faz automaticamente os seguintes passos:

* Ativa o protocolo TCP/IP para o SQL Server.
* Configura uma regra de firewall para abrir a porta SQL Server TCP (padrão 1433).
* Permite a autenticação do SQL Server, necessária para acesso público.
* Configura o grupo de segurança de rede no VM para todo o tráfego TCP na porta do Servidor SQL.

> [!IMPORTANT]
> As imagens de máquina virtual para as edições SQL Server Developer e Express não ativam automaticamente o protocolo TCP/IP. Para as edições Developer e Express, deve utilizar o SqL Server Configuration Manager para [ativar manualmente o protocolo TCP/IP](#manualtcp) após a criação do VM.

Qualquer cliente com acesso à Internet pode ligar-se à instância do SQL Server especificando o endereço IP público da máquina virtual ou qualquer etiqueta DNS atribuída a esse endereço IP. Se a porta SQL Server for 1433, não precisa de especificar na cadeia de ligação. O seguinte fio de ligação liga-se a um SQL VM com uma etiqueta DNS de utilização da `sqlvmlabel.eastus.cloudapp.azure.com` autenticação SQL (também pode utilizar o endereço IP público).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Embora esta cadeia permita conectividade para os clientes através da internet, isso não implica que qualquer pessoa possa ligar-se à sua instância sql Server. Os clientes externos têm de usar o nome de utilizador e a palavra-passe corretos. No entanto, para maior segurança, pode evitar o conhecido porto 1433. Por exemplo, se configurar o SQL Server para ouvir na porta 1500 e estabelecer regras adequadas de firewall e grupo de segurança de rede, poderá ligar-se anexando o número da porta ao nome do servidor. O exemplo a seguir altera o anterior adicionando um número de porta personalizado, **1500,** ao nome do servidor:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Quando consulta o SQL Server em VM através da internet, todos os dados de saída do datacenter Azure estão sujeitos a preços normais [em transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Conecte-se ao SQL Server dentro de uma rede virtual

Ao escolher **Private** para o tipo **de conectividade SQL** no portal, o Azure configura a maioria das configurações idênticas ao **Público**. A única diferença é que não existe uma regra do grupo de segurança de rede que permita o tráfego externo na porta do Servidor SQL (padrão 1433).

> [!IMPORTANT]
> As imagens de máquina virtual para as edições SQL Server Developer e Express não ativam automaticamente o protocolo TCP/IP. Para as edições Developer e Express, deve utilizar o SqL Server Configuration Manager para [ativar manualmente o protocolo TCP/IP](#manualtcp) após a criação do VM.

A conectividade privada é frequentemente usada em conjunto com uma [rede virtual](../../../virtual-network/virtual-networks-overview.md), que permite vários cenários. Pode ligar VMs na mesma rede virtual, mesmo que existam VMs em diferentes grupos de recursos. E com uma [VPN site-to-site,](../../../vpn-gateway/tutorial-site-to-site-portal.md)você pode criar uma arquitetura híbrida que conecta VMs com redes e máquinas no local.

As redes virtuais também permitem que se junte aos seus VMs Azure a um domínio. Esta é a única forma de utilizar a autenticação do Windows no SQL Server. Os outros cenários de ligação requerem autenticação SQL com nomes de utilizador e palavras-passe.

Assumindo que configurar o DNS na sua rede virtual, pode ligar-se à sua instância sql Server especificando o nome do computador VM do SQL Server na cadeia de ligação. O exemplo a seguir pressupõe também que a autenticação do Windows foi configurada e que o utilizador teve acesso à instância do SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> Alterar definições de conectividade SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pode alterar as definições de conectividade da sua máquina virtual SQL Server no portal Azure.

1. No portal Azure, selecione **máquinas virtuais SQL**.

2. Selecione o seu SQL Server VM.

3. Em **Definições**, selecione **Security**.

4. Altere o **nível de conectividade SQL** para a definição necessária. Pode utilizar opcionalmente esta área para alterar a porta do SqL Server ou as definições de autenticação SQL.

   ![Alterar conectividade SQL](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Aguarde vários minutos para que a atualização esteja concluída.

   ![Notificação de atualização sql VM](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> Ativar TCP/IP para edições developer e Express

Ao alterar as definições de conectividade do SQL Server, o Azure não ativa automaticamente o protocolo TCP/IP para o SQL Server Developer e as edições Express. Os passos abaixo explicam como ativar manualmente o TCP/IP para que consiga ligar remotamente através de um endereço IP.

Primeiro, ligue-se à máquina virtual SQL Server com um ambiente de trabalho remoto.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Em seguida, ative o protocolo TCP/IP com **o SqL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Ligar com o SSMS

Os passos a seguir mostram como criar uma etiqueta DE DNS opcional para o seu Azure VM e, em seguida, conectar-se com o SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> Configuração manual e resolução de problemas

Embora o portal forneça opções para configurar automaticamente a conectividade, é útil saber configurar manualmente a conectividade. Compreender os requisitos também pode ajudar a resolver problemas.

A tabela que se segue lista os requisitos para ligar ao SQL Server no Azure VM.

| Requisito | Description |
|---|---|
| [Ativar o modo de autenticação do SQL Server](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | A autenticação do SQL Server é necessária para ligar remotamente ao VM, a menos que tenha configurado o Ative Directory numa rede virtual. |
| [Criar um login SQL](/sql/relational-databases/security/authentication-access/create-a-login) | Se estiver a utilizar a autenticação SQL, necessita de um login SQL com um nome de utilizador e senha que também tenha permissões na sua base de dados alvo. |
| [Ativar o protocolo TCP/IP](#manualtcp) | O SQL Server deve permitir ligações sobre o TCP. |
| [Ativar a regra de firewall para a porta do servidor SQL](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | A firewall do VM deve permitir o tráfego de entrada na porta SQL Server (padrão 1433). |
| [Criar uma regra de grupo de segurança de rede para TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Tem de permitir que o VM receba tráfego na porta SQL Server (padrão 1433) se quiser ligar através da internet. As ligações locais e virtuais apenas em rede não requerem isto. Este é o único passo necessário no portal Azure. |

> [!TIP]
> Os passos na tabela anterior são feitos para si quando configura a conectividade no portal. Utilize estes passos apenas para confirmar a sua configuração ou para configurar a conectividade manualmente para o SQL Server.

## <a name="next-steps"></a>Passos seguintes

Para obter instruções de provisionamento juntamente com estes passos de conectividade, consulte [provisionar uma máquina virtual SQL Server no Azure](create-sql-vm-portal.md).

Para outros tópicos relacionados com a execução do SQL Server em VMs Azure, consulte [o SQL Server em máquinas virtuais Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).