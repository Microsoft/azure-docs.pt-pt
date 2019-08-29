---
title: Conectar-se a uma máquina virtual SQL Server no Azure (clássico) | Microsoft Docs
description: Saiba como se conectar ao SQL Server em execução em uma máquina virtual no Azure. Este tópico usa o modelo de implantação clássico. Os cenários diferem de acordo com a configuração de rede e o local do cliente.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 60c5af609cea876370c74684362e9b44db40c1ee
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101955"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Ligar a uma Máquina Virtual do SQL Server no Azure (Implementação Clássica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Clássico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Descrição geral
Este tópico descreve como se conectar à instância do SQL Server em execução em uma máquina virtual do Azure. Ele aborda alguns [cenários gerais de conectividade](#connection-scenarios) e fornece [etapas detalhadas para configurar a conectividade de SQL Server em uma VM do Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássico. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Se você estiver usando VMs do Gerenciador de recursos, consulte [conectar-se a uma SQL Server máquina virtual no Azure usando o Resource Manager](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Cenários de conexão
A maneira como um cliente se conecta ao SQL Server em execução em uma máquina virtual difere dependendo do local do cliente e da configuração da máquina/rede. Os cenários incluem:

* [Conectar-se a SQL Server no mesmo serviço de nuvem](#connect-to-sql-server-in-the-same-cloud-service)
* [Conectar-se a SQL Server pela Internet](#connect-to-sql-server-over-the-internet)
* [Conectar-se a SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Antes de se conectar com qualquer um desses métodos, você deve seguir as [etapas neste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Conectar-se a SQL Server no mesmo serviço de nuvem
Várias máquinas virtuais podem ser criadas no mesmo serviço de nuvem. Para entender esse cenário de máquinas virtuais, consulte [como conectar máquinas virtuais a uma rede virtual ou serviço de nuvem](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). Nesse cenário, um cliente em uma máquina virtual tenta se conectar ao SQL Server em execução em outra máquina virtual no mesmo serviço de nuvem.

Nesse cenário, você pode se conectar usando o **nome** da VM (também mostrado como **nome do computador** ou **hostname** no Portal). Esse é o nome que você forneceu para a VM durante a criação. Por exemplo, se você tiver nomeado sua VM do SQL **mysqlvm**, uma VM do cliente no mesmo serviço de nuvem poderá usar a seguinte cadeia de conexão para se conectar:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Conectar-se a SQL Server pela Internet
Se você quiser se conectar ao seu mecanismo de banco de dados SQL Server da Internet, deverá criar um ponto de extremidade de máquina virtual para a comunicação TCP de entrada. Este passo de configuração do Azure direciona o tráfego da porta TCP recebido para uma porta TCP a que a máquina virtual tenha acesso.

Para se conectar pela Internet, você deve usar o nome DNS da VM e o número da porta do ponto de extremidade da VM (configurado mais adiante neste artigo). Para localizar o nome DNS, navegue até a portal do Azure e selecione **máquinas virtuais (clássicas)** . Em seguida, selecione sua máquina virtual. O **nome DNS** é mostrado na seção **visão geral** .

Por exemplo, considere uma máquina virtual clássica chamada **mysqlvm** com um nome DNS de **mysqlvm7777.cloudapp.net** e um ponto de extremidade de VM de **57500**. Supondo que a conectividade esteja configurada corretamente, a cadeia de conexão a seguir pode ser usada para acessar a máquina virtual de qualquer lugar na Internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Embora essa cadeia de conexão habilite a conectividade para clientes pela Internet, isso não significa que qualquer pessoa possa se conectar ao seu SQL Server. Clientes externos têm o nome de usuário e a senha corretos. Para segurança adicional, não use a porta 1433 conhecida para o ponto de extremidade da máquina virtual pública. E, se possível, considere adicionar uma ACL em seu ponto de extremidade para restringir o tráfego somente para os clientes que você permitir. Para obter instruções sobre como usar ACLs com pontos de extremidade, consulte [Manage The ACL on a Endpoint](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Quando você usa essa técnica para se comunicar com SQL Server, todos os dados de saída do datacenter do Azure estão sujeitos aos preços normais das [transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Conectar-se a SQL Server na mesma rede virtual
A [rede virtual](../../../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Você pode conectar VMs na mesma rede virtual, mesmo se essas VMs existirem em diferentes serviços de nuvem. E com uma [VPN site a site](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), você pode criar uma arquitetura híbrida que conecta VMs com redes e computadores locais.

As redes virtuais também permitem que você ingresse suas VMs do Azure em um domínio. Ingressar em um domínio é a única maneira de usar a autenticação do Windows com o SQL Server. Os outros cenários de conexão exigem a autenticação do SQL com nomes de usuário e senhas.

Se você planeja configurar um ambiente de domínio e a autenticação do Windows, não é necessário configurar o ponto de extremidade público ou a autenticação e os logons do SQL. Nesse cenário, você pode se conectar à instância do SQL Server especificando o nome da VM SQL Server na cadeia de conexão. O exemplo a seguir pressupõe que a autenticação do Windows foi configurada e que o usuário recebeu acesso à instância de SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Etapas para configurar a conectividade de SQL Server em uma VM do Azure
As etapas a seguir demonstram como se conectar à instância do SQL Server pela Internet usando o SSMS (SQL Server Management Studio). No entanto, as mesmas etapas se aplicam para tornar sua SQL Server máquina virtual acessível para seus aplicativos, em execução no local e no Azure.

Antes de poder se conectar à instância do SQL Server de outra VM ou da Internet, você deve concluir as seguintes tarefas:

* [Criar um ponto de extremidade TCP para a máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Abrir portas TCP no firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Configurar SQL Server para escutar no protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Configurar SQL Server para a autenticação de modo misto](#configure-sql-server-for-mixed-mode-authentication)
* [Criar SQL Server logons de autenticação](#create-sql-server-authentication-logins)
* [Determinar o nome DNS da máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
* [Conectar-se ao Mecanismo de Banco de Dados de outro computador](#connect-to-the-database-engine-from-another-computer)

O caminho de conexão é resumido pelo seguinte diagrama:

![Conectando-se a uma máquina virtual SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Próximos Passos
Se você também estiver planejando usar Grupos de Disponibilidade AlwaysOn para alta disponibilidade e recuperação de desastres, deverá considerar a implementação de um ouvinte. Os clientes de banco de dados se conectam ao ouvinte em vez de diretamente a uma das instâncias de SQL Server. O ouvinte roteia os clientes para a réplica primária no grupo de disponibilidade. Para obter mais informações, consulte [configurar um ouvinte ILB para grupos de disponibilidade AlwaysOn no Azure](../classic/ps-sql-int-listener.md).

É importante examinar todas as práticas recomendadas de segurança para SQL Server em execução em uma máquina virtual do Azure. Para obter mais informações, veja [Security Considerations for SQL Server in Azure Virtual Machines](../sql/virtual-machines-windows-sql-security.md) (Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure).

[Explorar o Percurso de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais do Azure. 

Para outros tópicos relacionados à execução de SQL Server em VMs do Azure, consulte [SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

