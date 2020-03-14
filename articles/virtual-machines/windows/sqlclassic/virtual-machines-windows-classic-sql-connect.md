---
title: Ligue-se a uma máquina virtual do Servidor SQL no Azure (Clássico)  Clássico)  Microsoft Docs
description: Aprenda a ligar-se ao SQL Server em execução numa máquina virtual em Azure. Este tópico usa o modelo de implantação clássico. Os cenários diferem consoante a configuração de rede e a localização do cliente.
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
experimental: true
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 4627d9c4fa5c87e8e80ab80892062dabd77e9229
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249714"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Ligue-se a uma máquina virtual do Servidor SQL no Azure (Implantação Clássica)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Clássico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Descrição geral
Este tópico descreve como se conectar ao seu sQL Server em execução numa máquina virtual Azure. Abrange [alguns cenários gerais](#connection-scenarios) de conectividade e, em seguida, fornece [passos detalhados para configurar a conectividade Do Servidor SQL num VM Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Clássico.](../../../azure-resource-manager/management/deployment-models.md) Este artigo cobre a utilização do modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Se estiver a utilizar VMs do Gestor de Recursos, consulte [o Connect to a SQL Server Virtual Machine on Azure utilizando](../sql/virtual-machines-windows-sql-connect.md)o Resource Manager .

## <a name="connection-scenarios"></a>Cenários de conexão
A forma como um cliente se conecta ao SQL Server em execução numa Máquina Virtual difere dependendo da localização do cliente e da configuração de máquina/rede. Os cenários incluem:

* [Ligue-se ao SQL Server no mesmo serviço de nuvem](#connect-to-sql-server-in-the-same-cloud-service)
* [Ligue-se ao SQL Server através da internet](#connect-to-sql-server-over-the-internet)
* [Ligue-se ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Antes de se ligar a qualquer um destes métodos, deve seguir os [passos deste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Ligue-se ao SQL Server no mesmo serviço de nuvem
Várias máquinas virtuais podem ser criadas no mesmo serviço de nuvem. Para compreender este cenário de máquinas virtuais, consulte [como ligar máquinas virtuais a uma rede virtual ou serviço de nuvem.](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service) Este cenário é quando um cliente de uma máquina virtual tenta ligar-se ao SQL Server em execução noutra máquina virtual no mesmo serviço de nuvem.

Neste cenário, pode ligar-se utilizando o **Nome** VM (também mostrado como Nome de **Computador** ou nome de **anfitrião** no portal). Este é o nome que forneceu para o VM durante a criação. Por exemplo, se nomeou o seu **msqlvm**SQL VM, um cliente VM no mesmo serviço de nuvem poderia usar a seguinte cadeia de ligação para ligar:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Ligue-se ao SQL Server através da Internet
Se pretender ligar-se ao seu motor de base de dados SQL Server a partir da Internet, tem de criar um ponto final de máquina virtual para a comunicação tCP de entrada. Este passo de configuração do Azure direciona o tráfego da porta TCP recebido para uma porta TCP a que a máquina virtual tenha acesso.

Para ligar através da internet, deve utilizar o nome DNS do VM e o número de porta de ponto final VM (configurado mais tarde neste artigo). Para encontrar o Nome DNS, navegue para o portal Azure e selecione **máquinas Virtuais (clássicas)** . Em seguida, selecione a sua máquina virtual. O **nome DNS** é mostrado na secção **Visão Geral.**

Por exemplo, considere uma máquina virtual clássica chamada **mysqlvm** com um Nome DNS de **mysqlvm7777.cloudapp.net** e um ponto final VM de **57500**. Assumindo uma conectividade devidamente configurada, a seguinte cadeia de ligação poderia ser usada para aceder à máquina virtual a partir de qualquer lugar da internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Embora isto permita a conectividade para clientes através da internet, isso não implica que alguém possa ligar-se ao seu Servidor SQL. Os clientes externos têm o nome de utilizador e a palavra-passe corretos. Para obter segurança adicional, não utilize a conhecida porta 1433 para o ponto final da máquina virtual pública. E se possível, considere adicionar um ACL no seu ponto final para restringir o tráfego apenas aos clientes que permitir. Para obter instruções sobre a utilização de ACLs com pontos finais, consulte [Gerir o ACL num ponto final](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> É importante notar que quando utiliza esta técnica para comunicar com o SQL Server, todos os dados de saída do centro de dados Azure estão sujeitos a preços normais [nas transferências](https://azure.microsoft.com/pricing/details/data-transfers/)de dados de saída .
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Ligue-se ao SQL Server na mesma rede virtual
[A Rede Virtual](../../../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Pode ligar VMs na mesma rede virtual, mesmo que esses VMs existam em diferentes serviços na nuvem. E com uma [VPN site-to-site,](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)você pode criar uma arquitetura híbrida que conecta VMs com redes e máquinas no local.

As redes virtuais também lhe permitem juntar os seus VMs Azure a um domínio. Esta é a única forma de utilizar a Autenticação do Windows para o Servidor SQL. Os outros cenários de ligação requerem autenticação SQL com nomes de utilizador e palavras-passe.

Se vai configurar um ambiente de domínio e autenticação do Windows, não precisa de utilizar os passos deste artigo para configurar o ponto final público ou a Autenticação SQL e logins. Neste cenário, pode ligar-se à sua instância do Servidor SQL especificando o nome VM do Servidor SQL na cadeia de ligação. O exemplo seguinte pressupõe que a Autenticação do Windows também foi configurada e que o utilizador teve acesso à instância do Servidor SQL.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Passos para configurar a conectividade do Servidor SQL num VM Azure
Os seguintes passos demonstram como se conectar à instância do Servidor SQL através da internet utilizando o SQL Server Management Studio (SSMS). No entanto, os mesmos passos aplicam-se para tornar a sua máquina virtual SQL Server acessível para as suas aplicações, funcionando tanto no local como no Azure.

Antes de poder ligar-se à instância do Servidor SQL a partir de outro VM ou da internet, deve completar as seguintes tarefas descritas nas secções que se seguem:

* [Criar um ponto final de TCP para a máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Abrir portas TCP na firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Configure o Servidor SQL para ouvir o protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Configure o Servidor SQL para autenticação de modo misto](#configure-sql-server-for-mixed-mode-authentication)
* [Criar logins de autenticação do Servidor SQL](#create-sql-server-authentication-logins)
* [Determine o nome DNS da máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
* [Ligue-se ao Motor base de dados a partir de outro computador](#connect-to-the-database-engine-from-another-computer)

O caminho de ligação é resumido pelo seguinte diagrama:

![Ligação a uma máquina virtual do SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Passos Seguintes
Se também está a planear utilizar os Grupos de Disponibilidade AlwaysOn para uma elevada disponibilidade e recuperação de desastres, deve considerar a implementação de um ouvinte. Os clientes da base de dados ligam-se ao ouvinte em vez de diretamente a uma das instâncias do SQL Server. O ouvinte encaminha os clientes para a réplica primária no grupo de disponibilidade. Para mais informações, consulte [Configure um ouvinte ILB para SempreOn Availability Groups em Azure](../classic/ps-sql-int-listener.md).

É importante rever todas as melhores práticas de segurança para o SQL Server em execução numa máquina virtual Azure. Para obter mais informações, veja [Security Considerations for SQL Server in Azure Virtual Machines](../sql/virtual-machines-windows-sql-security.md) (Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure).

[Explorar o Percurso de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais do Azure. 

Para outros tópicos relacionados com a execução do Servidor SQL em VMs Azure, consulte [o Servidor SQL em Máquinas Virtuais Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

