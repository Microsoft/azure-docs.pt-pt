---
title: Ligue-se a uma máquina virtual do servidor SQL em Azure (Clássico) Microsoft Docs
description: Saiba como ligar-se ao SQL Server em execução numa Máquina Virtual em Azure. Este tópico utiliza o modelo clássico de implementação. Os cenários diferem consoante a configuração da rede e a localização do cliente.
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
ms.openlocfilehash: 52f25d89691a2a721025848e28d119a0fbe5e322
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135986"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Ligar a uma Máquina Virtual do SQL Server no Azure (Implementação Clássica)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)
> * [Clássico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Descrição geral
Este tópico descreve como ligar-se à sua instância SQL Server em execução numa máquina virtual Azure. Cobre [alguns cenários gerais de conectividade](#connection-scenarios) e, em seguida, fornece [passos detalhados para configurar a conectividade do Servidor SQL num VM Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Classic.](../../../azure-resource-manager/management/deployment-models.md) Este artigo abrange utilizando o modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Se estiver a utilizar VMs do Gestor de Recursos, consulte [Connect to a SQL Server Virtual Machine on Azure utilizando o Resource Manager](../../../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md).

## <a name="connection-scenarios"></a>Cenários de conexão
A forma como um cliente se conecta ao SQL Server a funcionar numa Máquina Virtual difere consoante a localização do cliente e a configuração de máquina/rede. Os cenários incluem:

* [Ligue ao SQL Server no mesmo serviço de nuvem](#connect-to-sql-server-in-the-same-cloud-service)
* [Conecte-se ao SQL Server através da internet](#connect-to-sql-server-over-the-internet)
* [Conecte-se ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Antes de se ligar a qualquer um destes métodos, deve seguir os [passos deste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Ligue ao SQL Server no mesmo serviço de nuvem
Várias máquinas virtuais podem ser criadas no mesmo serviço de nuvem. Para compreender este cenário de máquinas virtuais, consulte [como ligar máquinas virtuais a uma rede virtual ou serviço de nuvem.](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service) Este cenário é quando um cliente de uma máquina virtual tenta ligar-se ao SQL Server a funcionar noutra máquina virtual no mesmo serviço de cloud.

Neste cenário, pode ligar-se utilizando o **Nome** VM (também mostrado como **Nome de Computador** ou nome de **anfitrião** no portal). Este é o nome que forneceu para o VM durante a criação. Por exemplo, se nomeou o seu SQL VM **mysqlvm,** um VM cliente no mesmo serviço de nuvem poderia usar o seguinte fio de ligação para ligar:

`Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>`

### <a name="connect-to-sql-server-over-the-internet"></a>Conecte-se ao SQL Server através da Internet
Se pretender ligar-se ao seu motor de base de dados SQL Server a partir da Internet, tem de criar um ponto final de máquina virtual para a comunicação TCP de entrada. Este passo de configuração do Azure direciona o tráfego da porta TCP recebido para uma porta TCP a que a máquina virtual tenha acesso.

Para se ligar através da internet, deve utilizar o nome DNS do VM e o número da porta do ponto final VM (configurado mais tarde neste artigo). Para encontrar o Nome DNS, navegue até ao portal Azure e selecione **máquinas Virtuais (clássicas)**. Em seguida, selecione a sua máquina virtual. O **nome DNS** é indicado na secção **Geral.**

Por exemplo, considere uma máquina virtual clássica chamada **Mysqlvm** com um nome DNS de **mysqlvm7777.cloudapp.net** e um ponto final VM de **57500**. Assumindo a conectividade devidamente configurada, a seguinte cadeia de ligação poderia ser usada para aceder à máquina virtual a partir de qualquer lugar da internet:

`Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>`

Embora isto permita conectividade para os clientes através da internet, isso não implica que qualquer pessoa possa ligar-se ao seu SQL Server. Os clientes externos têm o nome de utilizador e a palavra-passe corretos. Para maior segurança, não utilize o conhecido porto 1433 para o ponto final da máquina virtual pública. E, se possível, considere adicionar um ACL no seu ponto final para restringir o tráfego apenas aos clientes que permitir. Para obter instruções sobre a utilização de ACLs com pontos finais, consulte [gerir a ACL num ponto final](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> É importante notar que quando utiliza esta técnica para comunicar com o SQL Server, todos os dados de saída do datacenter Azure estão sujeitos a preços normais [nas transferências de dados de saída.](https://azure.microsoft.com/pricing/details/data-transfers/)
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Conecte-se ao SQL Server na mesma rede virtual
[A Rede Virtual](../../../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Pode ligar VMs na mesma rede virtual, mesmo que existam VMs em diferentes serviços na nuvem. E com uma [VPN site-to-site,](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)você pode criar uma arquitetura híbrida que conecta VMs com redes e máquinas no local.

As redes virtuais também permitem juntar os seus VMs Azure a um domínio. Esta é a única forma de utilizar a autenticação do Windows no SQL Server. Os outros cenários de ligação requerem autenticação SQL com nomes de utilizador e palavras-passe.

Se vai configurar um ambiente de domínio e autenticação do Windows, não precisa de utilizar os passos deste artigo para configurar o ponto final público ou a Autenticação e logins SQL. Neste cenário, pode ligar-se à sua instância SQL Server especificando o nome VM do servidor SQL na cadeia de ligação. O exemplo a seguir pressupõe que a Autenticação do Windows também foi configurada e que o utilizador teve acesso à instância do SQL Server.

`Server=mysqlvm;Integrated Security=true`

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Passos para configurar a conectividade do Servidor SQL num VM Azure
Os seguintes passos demonstram como ligar-se à instância do SQL Server através da internet utilizando o SQL Server Management Studio (SSMS). No entanto, os mesmos passos aplicam-se para tornar a sua máquina virtual SQL Server acessível às suas aplicações, funcionando tanto no local como no Azure.

Antes de poder ligar-se à instância do SQL Server a partir de outro VM ou da internet, tem de completar as seguintes tarefas descritas nas secções que se seguem:

* [Criar um ponto final TCP para a máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Abra as portas TCP na firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Configurar o SQL Server para escutar o protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Configurar o SQL Server para autenticação em modo misto](#configure-sql-server-for-mixed-mode-authentication)
* [Criar inícios de sessão de autenticação do SQL Server](#create-sql-server-authentication-logins)
* [Determinar o nome DNS da máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
* [Ligar ao Motor da Base de Dados a partir de outro computador](#connect-to-the-database-engine-from-another-computer)

O caminho de ligação é resumido pelo seguinte diagrama:

![Ligação a uma máquina virtual SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Passos Seguintes
Se também está a planear utilizar grupos de disponibilidade alwaysOn para alta disponibilidade e recuperação de desastres, deve considerar a implementação de um ouvinte. Os clientes da base de dados ligam-se ao ouvinte em vez de diretamente a uma das instâncias do SQL Server. O ouvinte encaminha os clientes para a réplica primária do grupo de disponibilidade. Para obter mais informações, consulte [configurar um ouvinte ILB para grupos de disponibilidade AlwaysOn em Azure](../classic/ps-sql-int-listener.md).

É importante rever todas as melhores práticas de segurança para o SQL Server em execução numa máquina virtual Azure. Para obter mais informações, veja [Security Considerations for SQL Server in Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/security-considerations-best-practices.md) (Considerações de segurança para o SQL Server em Máquinas Virtuais do Azure).

[Explorar o Percurso de Aprendizagem](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais do Azure. 

Para outros tópicos relacionados com a execução do SQL Server em VMs Azure, consulte [o SQL Server em Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

