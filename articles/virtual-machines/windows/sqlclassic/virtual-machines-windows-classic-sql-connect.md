---
title: Ligar a uma Máquina Virtual do SQL Server no Azure (clássico) | Documentos da Microsoft
description: Saiba como ligar ao SQL Server em execução numa máquina Virtual no Azure. Este tópico utiliza o modelo de implementação clássica. Os cenários diferem consoante a configuração de rede e a localização do cliente.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: b8994d4c1eabf4381bf8364c76f7328d225f7e1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62111532"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Ligar a uma Máquina Virtual do SQL Server no Azure (Implementação Clássica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Clássico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Descrição geral
Este tópico descreve como ligar à sua instância do SQL Server em execução numa máquina virtual do Azure. Ele cobre algumas [cenários de conectividade](#connection-scenarios) e, em seguida, fornece [detalhadas passos para configurar a conectividade do SQL Server numa VM do Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Se estiver a utilizar VMs do Resource Manager, consulte [ligar a um SQL Server Máquina Virtual no Azure com o Resource Manager](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Cenários de ligação
A forma como um cliente se conecta ao SQL Server em execução numa máquina Virtual é diferente consoante a localização do cliente e a configuração de máquina/sistema de rede. Os cenários incluem:

* [Ligar ao SQL Server no mesmo serviço cloud](#connect-to-sql-server-in-the-same-cloud-service)
* [Ligar ao SQL Server através da internet](#connect-to-sql-server-over-the-internet)
* [Ligar ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Antes de ligar com qualquer um destes métodos, tem de seguir a [os passos neste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Ligar ao SQL Server no mesmo serviço cloud
Várias máquinas virtuais podem ser criadas no mesmo serviço cloud. Para compreender este cenário de máquinas virtuais, veja [como ligar máquinas virtuais com um serviço de rede ou na cloud virtual](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). Este cenário é quando um cliente numa máquina virtual tenta ligar ao SQL Server em execução em outra máquina virtual no mesmo serviço cloud.

Neste cenário, pode ligar-se com a VM **Name** (também mostrado como **nome do computador** ou **nome de anfitrião** no portal). Este é o nome que indicou para a VM durante a criação. Por exemplo, se denominar a VM do SQL **mysqlvm**, uma VM do cliente no mesmo serviço cloud poderia usar a seguinte cadeia de ligação para ligar:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Ligar ao SQL Server através da Internet
Se pretender ligar ao seu motor de base de dados do SQL Server a partir da Internet, tem de criar um ponto de final de máquina virtual para comunicação TCP de entrada. Este passo de configuração do Azure direciona o tráfego da porta TCP recebido para uma porta TCP a que a máquina virtual tenha acesso.

Para ligar através da internet, tem de utilizar o nome DNS da VM e o número de porta de ponto final VM (configurado neste artigo). Para localizar o nome de DNS, navegue para o portal do Azure e selecione **máquinas virtuais (clássicas)** . Em seguida, selecione a sua máquina virtual. O **nome DNS** é mostrada na **descrição geral** secção.

Por exemplo, considere uma máquina virtual clássica com o nome **mysqlvm** com o nome DNS **mysqlvm7777.cloudapp.net** e um ponto final da VM de **57500**. Partindo do princípio de conectividade configurada corretamente, a seguinte cadeia de ligação poderia ser usada para acessar a máquina virtual a partir de qualquer lugar na internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Embora isso permita a conectividade para clientes através da internet, isso não significa que qualquer pessoa pode ligar ao SQL Server. Fora os clientes têm do nome de utilizador correto e a palavra-passe. Para segurança adicional, não utilize esta porta 1433 para o ponto de extremidade pública da máquina virtual. E se for possível, considere adicionar uma ACL no seu ponto final para restringir o tráfego apenas para os clientes permitir. Para obter instruções sobre como utilizar as ACLs com pontos finais, consulte [gerir a ACL num ponto de extremidade](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> É importante observar que, quando usar essa técnica para comunicar com o SQL Server, todos os dados de saída do datacenter do Azure está sujeito a normal [dos preços das transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Ligar ao SQL Server na mesma rede virtual
[Rede virtual](../../../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Pode ligar as VMs na mesma rede virtual, mesmo que essas VMs existem nos serviços cloud diferentes. E com um [VPN site a site](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), pode criar uma arquitetura híbrida que se liga a VMs com redes no local e máquinas.

Redes virtuais também lhe permite associar as suas VMs do Azure a um domínio. Esta é a única forma de utilizar a autenticação do Windows para o SQL Server. Os outros cenários de ligação exigem a autenticação de SQL com os nomes de utilizador e palavras-passe.

Se pretender configurar um ambiente de domínio e a autenticação do Windows, não é necessário utilizar os passos neste artigo para configurar o ponto final público ou a autenticação do SQL e inícios de sessão. Neste cenário, pode ligar à sua instância do SQL Server, especificando o nome de VM do SQL Server na cadeia de ligação. O exemplo a seguir pressupõe que a autenticação do Windows também foi configurada e se foram concedido ao utilizador acesso à instância do SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Passos para configurar a conectividade do SQL Server numa VM do Azure
Os passos seguintes demonstram como ligar à instância do SQL Server através da internet com o SQL Server Management Studio (SSMS). No entanto, os mesmos passos aplicam-se para tornar a sua máquina virtual do SQL Server acessível para as suas aplicações, em execução no local e no Azure.

Antes de poder ligar à instância do SQL Server a partir de outra VM ou da internet, tem de concluir as seguintes tarefas conforme descrito nas seções a seguir:

* [Criar um ponto final TCP para a máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Abrir portas TCP na firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Configurar o SQL Server para escutar o protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Configurar o SQL Server para autenticação em modo misto](#configure-sql-server-for-mixed-mode-authentication)
* [Criar inícios de sessão de autenticação de SQL Server](#create-sql-server-authentication-logins)
* [Determinar o nome DNS da máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
* [Ligar ao motor de base de dados de outro computador](#connect-to-the-database-engine-from-another-computer)

O caminho de ligação é resumido pelo diagrama seguinte:

![Ligar a uma máquina virtual do SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Próximos Passos
Se também estiver a planear utilizar grupos de Disponibilidade AlwaysOn para elevada disponibilidade e recuperação após desastre, deve considerar a implementação de um serviço de escuta. Os clientes da base de dados ligar-se para o serviço de escuta, em vez de diretamente a uma das instâncias do SQL Server. O serviço de escuta encaminha os clientes para a réplica primária do grupo de disponibilidade. Para obter mais informações, consulte [configurar um serviço de escuta ILB para grupos de Disponibilidade AlwaysOn no Azure](../classic/ps-sql-int-listener.md).

É importante rever todos os procedimentos recomendados de segurança para o SQL Server em execução numa máquina virtual do Azure. Para obter mais informações, veja [Security Considerations for SQL Server in Azure Virtual Machines](../sql/virtual-machines-windows-sql-security.md) (Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure).

[Explorar o Percurso de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais do Azure. 

Para outros tópicos relacionados ao executar o SQL Server em VMs do Azure, consulte [SQL Server em Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

