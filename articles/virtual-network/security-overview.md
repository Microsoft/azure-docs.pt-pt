---
title: Visão geral dos grupos de segurança do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre os grupos de segurança de aplicações e rede. Os grupos de segurança ajudam-no a filtrar o tráfego de rede entre recursos do Azure.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: ca4908e642644ccbf349841d143bfcc18e944025
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305842"
---
# <a name="security-groups"></a>Grupos de segurança
<a name="network-security-groups"></a>

Pode filtrar o tráfego de rede de e para recursos do Azure numa [rede virtual](virtual-networks-overview.md) do Azure com um grupo de segurança de rede. Os grupos de segurança de rede contêm [regras de segurança](#security-rules) que permitem ou negam o tráfego de entrada ou de saída de e para vários tipos de recursos do Azure. Para saber que recursos do Azure podem ser implementados numa rede virtual e associar grupos de segurança de rede aos mesmos, veja [Virtual network integration for Azure services](virtual-network-for-azure-services.md) (Integração da rede virtual para serviços do Azure). Para cada regra, pode especificar a origem e o destino, a porta e o protocolo.

Este artigo explica os conceitos dos grupos de segurança de rede para ajudá-lo a utilizá-los eficientemente. Se nunca tiver criado um grupo de segurança de rede, pode seguir um [tutorial](tutorial-filter-network-traffic.md) rápido para ganhar experiência na criação de um. Se estiver familiarizado com os grupos de segurança de rede e tiver de geri-los, veja [Manage a network security group](manage-network-security-group.md) (Gerir um grupo de segurança de rede). Se estiver com problemas de comunicação e precisar de resolver problemas nos grupos de segurança de rede, veja [Diagnose a virtual machine network traffic filter problem](diagnose-network-traffic-filter-problem.md) (Diagnosticar problemas de filtro de tráfego de rede de uma máquina virtual). Pode ativar [registos de fluxos de grupos de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para [analisar o tráfego de rede](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) de e para recursos que têm um grupo de segurança de rede associado.

## <a name="security-rules"></a>Regras de segurança

Os grupos de segurança de rede contêm zero ou tantas regras conforme pretender, dentro dos [limites](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) das subscrições do Azure. Cada regra especifica as propriedades seguintes:

|Propriedade  |Explicação  |
|---------|---------|
|Nome|Um nome exclusivo no grupo de segurança de rede.|
|Priority | Um número entre 100 e 4096. As regras são processadas por ordem de prioridade, sendo os números mais baixos processados antes dos mais elevados, uma vez que têm uma prioridade superior. Quando o tráfego corresponder a uma regra, o processamento para. Como resultado, qualquer regra que exista com prioridades inferiores (números mais elevados) e que tenham os mesmos atributos das regras com prioridades superiores não é processada.|
|Origem ou destino| Qualquer endereço IP ou um endereço IP individual, um bloco CIDR (Classless Inter-domain Routing) (por exemplo, 10.0.0.0/24), [etiqueta de serviço](#service-tags) ou [grupo de segurança de aplicações](#application-security-groups). Se especificar um endereço para um recurso do Azure, indique o endereço IP privado atribuído ao mesmo. Os grupos de segurança de rede são processados depois de o Azure traduzir um endereço IP público num privado para tráfego de entrada e antes de traduzir um endereço IP privado num público para tráfego de saída. Saiba mais sobre os [endereços IP](virtual-network-ip-addresses-overview-arm.md) do Azure. Especificar um intervalo, uma etiqueta de serviço ou um grupo de segurança de aplicações permite-lhe criar menos regras de segurança. A capacidade de especificar vários endereços IP individuais e intervalos de endereços IP (não pode indicar etiquetas de serviço ou grupos de aplicações) numa regra é denominada [regras de segurança aumentadas](#augmented-security-rules). As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede gerados através do modelo de implementação do Resource Manager. Não pode especificar vários endereços IP nem intervalos de endereços IP em grupos de segurança de rede criados com o modelo de implementação clássica. Saiba mais sobre os [modelos de implementação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protocol     | TCP, UDP, ICMP ou any.|
|Direction| Indica se a regra se aplica a tráfego de entrada ou de saída.|
|Intervalo de portas     |Pode especificar uma porta individual ou um intervalo de portas. Por exemplo, pode indicar 80 ou 10000-10005. Especificar intervalos permite-lhe criar menos regras de segurança. As regras de segurança aumentadas só podem ser criadas em grupos de segurança de rede gerados através do modelo de implementação do Resource Manager. Não pode especificar várias portas nem intervalos de portas na mesma regra de segurança em grupos de segurança de rede criados com o modelo de implementação clássica.   |
|Action     | Permitir ou negar        |

As regras de segurança dos grupos de segurança de rede são avaliadas por prioridade, utilizando as informações de cinco cadeias de identificação (origem, porta de origem, destino, porta de destino e protocolo) para permitir ou negar o tráfego. É criado um registo de fluxo para as ligações existentes. A comunicação é permitida ou negada com base no estado da ligação do registo do fluxo. O registo do fluxo permite que um grupo de segurança de rede tenha monitoração de estado. Se especificar uma regra de segurança de saída para qualquer endereço através da porta 80, por exemplo, não é necessário especificar uma regra de segurança de entrada para a resposta ao tráfego de saída. Só tem de especificar uma regra de segurança de entrada se a comunicação for iniciada externamente. O oposto também se aplica. Se o tráfego de entrada for permitido numa porta, não é necessário especificar uma regra de segurança de saída para responder ao tráfego através da porta.
As ligações existentes não podem ser interrompidas quando remover uma regra de segurança que ativou o fluxo. Os fluxos de tráfego são interrompidos quando as ligações são paradas e não há qualquer tráfego a fluir em qualquer direção, pelo menos, durante alguns minutos.

Há limites ao número de regras de segurança que pode criar num grupo de segurança de rede. Para obter mais detalhes, veja [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Regras de segurança aumentadas

As regras de segurança aumentadas simplificam a definição de segurança das redes virtuais, permitindo-lhe definir políticas de segurança de rede maiores e mais complexas com menos regras. Pode combinar várias portas e vários endereços IP explícitos e intervalos numa regra de segurança individual e facilmente compreendida. Utilize as regras aumentadas nos campos de origem, destino e porta das regras. Para simplificar a manutenção da definição de segurança de rede, combine regras de segurança aumentadas com [etiquetas de serviço](#service-tags) ou [grupos de segurança de aplicações](#application-security-groups). Há limites para o número de endereços, intervalos e portas que você pode especificar em uma regra. Para obter mais detalhes, veja [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="service-tags"></a>Etiquetas de serviço

As etiquetas de serviço representam um grupo de prefixos de endereços IP, que ajudam a minimizar a complexidade da criação de regras de segurança. Não pode criar as suas próprias etiquetas de serviço nem especificar que endereços IP estão incluídos nas mesmas. A Microsoft gere os prefixos de endereços que as etiquetas abrangem e atualiza-as automaticamente à medida que os endereços são alterados. Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. 

As seguintes marcas de serviço estão disponíveis para uso em [regras de grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules). Marcas de serviço com asterisco no final (ou seja, AzureCloud *) também podem ser usadas em [regras de rede do firewall do Azure](https://docs.microsoft.com/azure/firewall/service-tags). 

* **VirtualNetwork** (Gerenciador de recursos) (**VIRTUAL_NETWORK** para clássico): Essa marca inclui o espaço de endereço de rede virtual (todos os intervalos CIDR definidos para a rede virtual), todos os espaços de endereço [](virtual-network-peering-overview.md) locais conectados, redes virtuais emparelhadas ou rede virtual conectada a um [Gateway de rede virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e endereço prefixos usados em [rotas definidas pelo usuário](virtual-networks-udr-overview.md). Lembre-se de que essa marca pode conter a rota padrão. 
* **AzureLoadBalancer** (Gerenciador de recursos) (**AZURE_LOADBALANCER** para clássico): Esta marca denota o balanceador de carga de infraestrutura do Azure. A etiqueta traduz o [Endereço IP virtual do anfitrião](security-overview.md#azure-platform-considerations) (168.63.129.16), onde as sondas de estado de funcionamento do Azure têm origem. Se não estiver a utilizar o balanceador de carga do Azure, pode substituir esta regra.
* **Internet** (Gerenciador de recursos) (**Internet** para clássico): Essa marca denota o espaço de endereço IP que está fora da rede virtual e pode ser acessada pela Internet pública. O intervalo de endereços inclui o [espaço de endereço IP público pertencente ao Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureCloud*** (somente Gerenciador de recursos): Essa marca denota o espaço de endereço IP do Azure, incluindo todos os [endereços IP públicos](https://www.microsoft.com/download/details.aspx?id=41653)do datacenter. Se especificar *AzureCloud* no valor, o tráfego é permitido ou negado para os endereços IP públicos do Azure. Se você quiser permitir o acesso a AzureCloud em uma [região](https://azure.microsoft.com/regions)específica, poderá especificar a região no seguinte formato AzureCloud. [nome da região]. Essa marca é recomendada para a regra de segurança de saída. 
* **AzureTrafficManager*** (somente Gerenciador de recursos): Essa marca denota o espaço de endereço IP para os endereços IP de investigação do Gerenciador de tráfego do Azure. Pode encontrar mais informações sobre os endereços IP da pesquisa do Gestor de Tráfego nas [FAQ do Gestor de Tráfego do Microsoft Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). Essa marca é recomendada para a regra de segurança de entrada.  
* **Armazenamento*** (somente no Gerenciador de recursos): Essa marca denota o espaço de endereço IP para o serviço de armazenamento do Azure. Se especificar *Storage* no valor, o tráfego é permitido ou negado para o armazenamento. Se você quiser permitir o acesso ao armazenamento em uma [região](https://azure.microsoft.com/regions)específica, poderá especificar a região no armazenamento de formato a seguir. [nome da região]. A etiqueta representa o serviço, mas não instâncias específicas do mesmos. Por exemplo, representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica. Essa marca é recomendada para a regra de segurança de saída. 
* **SQL*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do banco de dados SQL do Azure, banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL e serviços de SQL Data Warehouse do Azure. Se especificar *Sql* no valor, o tráfego é permitido ou negado para o Sql. Se você quiser permitir o acesso ao SQL em uma [região](https://azure.microsoft.com/regions)específica, poderá especificar a região no formato SQL a seguir. [nome da região]. A etiqueta representa o serviço, mas não instâncias específicas do mesmos. Por exemplo, representa o serviço da Base de Dados SQL do Azure, mas não uma base de dados ou um servidor SQL. Essa marca é recomendada para a regra de segurança de saída. 
* **AzureCosmosDB*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço de banco de dados Cosmos do Azure. Se especificar *AzureCosmosDB* no valor, o tráfego é permitido ou negado para o AzureCosmosDB. Se só pretender permitir o acesso ao AzureCosmosDB numa [região](https://azure.microsoft.com/regions) específica, pode especificar a região no seguinte formato AzureCosmosDB.[ nome da região]. Essa marca é recomendada para a regra de segurança de saída. 
* **AzureKeyVault*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço do cofre de chaves do Azure. Se especificar *AzureKeyVault* no valor, o tráfego é permitido ou negado para o AzureKeyVault. Se só pretender permitir o acesso ao AzureKeyVault numa [região](https://azure.microsoft.com/regions) específica, pode especificar a região no seguinte formato AzureKeyVault.[ nome da região]. Essa marca tem dependência na marca **AzureActiveDirectory** . Essa marca é recomendada para a regra de segurança de saída.  
* **EventHub*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço do Azure EventHub. Se especificar *EventHub* no valor, o tráfego será permitido ou negado para o EventHub. Se só pretender permitir o acesso ao EventHub numa [região](https://azure.microsoft.com/regions) específica, poderá especificar a região no seguinte formato EventHub.[nome da região]. Essa marca é recomendada para a regra de segurança de saída. 
* **ServiceBus*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço ServiceBus do Azure usando a camada de serviço Premium. Se especificar *ServiceBus* no valor, o tráfego será permitido ou negado para o ServiceBus. Se só pretender permitir o acesso ao ServiceBus numa [região](https://azure.microsoft.com/regions) específica, poderá especificar a região no seguinte formato ServiceBus.[nome da região]. Essa marca é recomendada para a regra de segurança de saída. 
* **MicrosoftContainerRegistry*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço de registro de contêiner da Microsoft. Se especificar *MicrosoftContainerRegistry* no valor, o tráfego será permitido ou negado para o MicrosoftContainerRegistry. Se só pretender permitir o acesso ao MicrosoftContainerRegistry numa [região](https://azure.microsoft.com/regions) específica, poderá especificar a região no seguinte formato MicrosoftContainerRegistry.[nome da região]. Essa marca é recomendada para a regra de segurança de saída. 
* **AzureContainerRegistry*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço de registro de contêiner do Azure. Se especificar *AzureContainerRegistry* no valor, o tráfego será permitido ou negado para o AzureContainerRegistry. Se só pretender permitir o acesso ao AzureContainerRegistry numa [região](https://azure.microsoft.com/regions) específica, poderá especificar a região no seguinte formato AzureContainerRegistry.[nome da região]. Essa marca é recomendada para a regra de segurança de saída. 
* **AppService*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço AppService do Azure. Se especificar *AppService* no valor, o tráfego será permitido ou negado para o AppService. Se só pretender permitir o acesso ao AppService numa [região](https://azure.microsoft.com/regions) específica, poderá especificar a região no seguinte formato AppService.[region nome da região]. Essa marca é recomendada para a regra de segurança de saída para front-ends do webapps.  
* **AppServiceManagement*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do tráfego de gerenciamento para implantações Ambiente do Serviço de Aplicativo dedicadas. Se especificar *AppServiceManagement* no valor, o tráfego será permitido ou negado para o AppServiceManagement. Essa marca é recomendada para a regra de segurança de entrada/saída. 
* **ApiManagement*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do tráfego de gerenciamento para implantações dedicadas do APIM. Se especificar *ApiManagement* no valor, o tráfego será permitido ou negado para ApiManagement. Essa marca é recomendada para a regra de segurança de entrada/saída. 
* **AzureConnectors*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço dos conectores de aplicativos lógicos para conexões de teste/back-end. Se especificar *AzureConnectors* no valor, o tráfego será permitido ou negado para o AzureConnectors. Se só pretender permitir o acesso ao AzureConnectors numa [região](https://azure.microsoft.com/regions) específica, poderá especificar a região no seguinte formato AzureConnectors.[nome da região]. Essa marca é recomendada para a regra de segurança de entrada. 
* **Gatewaymanager** (Somente no Gerenciador de recursos): Essa marca denota os prefixos de endereço do tráfego de gerenciamento para implantações dedicadas de gateways de VPN/aplicativo. Se especificar *GatewayManager* no valor, o tráfego será permitido ou negado para o GatewayManager. Essa marca é recomendada para a regra de segurança de entrada. 
* **AzureDataLake*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço de Azure Data Lake. Se especificar *AzureDataLake* no valor, o tráfego será permitido ou negado para o AzureDataLake. Essa marca é recomendada para a regra de segurança de saída. 
* **AzureActiveDirectory*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço AzureActiveDirectory. Se especificar *AzureActiveDirectory* no valor, o tráfego será permitido ou negado para o AzureActiveDirectory. Essa marca é recomendada para a regra de segurança de saída.
* **AzureMonitor*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do Log Analytics, do App insights, do AzMon e das métricas personalizadas (pontos de extremidade GiG). Se você especificar *AzureMonitor* para o valor, o tráfego será permitido ou negado para AzureMonitor. Por Log Analytics, essa marca tem dependência na marca de **armazenamento** . Essa marca é recomendada para a regra de segurança de saída.
* **Imfabric*** (somente no Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço do Service Fabric. Se você especificar ** o infabric para o valor, o tráfego será permitido ou negado ao infabric. Essa marca é recomendada para a regra de segurança de saída. 
* **AzureMachineLearning*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço AzureMachineLearning. Se você especificar *AzureMachineLearning* para o valor, o tráfego será permitido ou negado para AzureMachineLearning. Essa marca é recomendada para a regra de segurança de saída. 
* **BatchNodeManagement*** (somente Gerenciador de recursos): Esta marca denota os prefixos de endereço do tráfego de gerenciamento para implantações dedicadas do lote do Azure. Se você especificar *BatchNodeManagement* para o valor, o tráfego será permitido ou negado do serviço de lote para os nós de computação. Essa marca é recomendada para a regra de segurança de entrada/saída. 
* **AzureBackup*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do serviço AzureBackup. Se você especificar *AzureBackup* para o valor, o tráfego será permitido ou negado para AzureBackup. Essa marca tem dependência na marcação de **armazenamento** e **AzureActiveDirectory** . Essa marca é recomendada para a regra de segurança de saída. 
* **AzureActiveDirectoryDomainServices*** (somente Gerenciador de recursos): Essa marca denota os prefixos de endereço do tráfego de gerenciamento para implantações Azure Active Directory Domain Services dedicadas. Se você especificar *AzureActiveDirectoryDomainServices* para o valor, o tráfego será permitido ou negado para AzureActiveDirectoryDomainServices. Essa marca é recomendada para a regra de segurança de entrada/saída.  
* **SqlManagement*** (somente no Gerenciador de recursos): Essa marca denota os prefixos de endereço do tráfego de gerenciamento para implantações dedicadas do SQL. Se você especificar *SqlManagement* para o valor, o tráfego será permitido ou negado ao SqlManagement. Essa marca é recomendada para a regra de segurança de entrada/saída. 
* **CognitiveServicesManagement** (Somente no Gerenciador de recursos): Essa marca denota os prefixos de endereço de tráfego para serviços cognitivas. Se você especificar *CognitiveServicesManagement* para o valor, o tráfego será permitido ou negado para CognitiveServicesManagement. Essa marca é recomendada para a regra de segurança de saída.  
* **Dynamics365ForMarketingEmail** (Somente no Gerenciador de recursos): Esta marca denota os prefixos de endereço do serviço de email de marketing do Dynamics 365. Se você especificar *Dynamics365ForMarketingEmail* para o valor, o tráfego será permitido ou negado para Dynamics365ForMarketingEmail. Se você quiser permitir o acesso a Dynamics365ForMarketingEmail em uma [região](https://azure.microsoft.com/regions)específica, poderá especificar a região no seguinte formato Dynamics365ForMarketingEmail. [nome da região].
* **AzurePlatformDNS** (Somente no Gerenciador de recursos): Essa marca denota o DNS, que é um serviço de infraestrutura básico. Se você especificar *AzurePlatformDNS* para o valor, poderá desabilitar a consideração padrão da [plataforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) para DNS. Tome cuidado ao usar essa marca. O teste é recomendado antes de usar essa marca. 
* **AzurePlatformIMDS** (Somente no Gerenciador de recursos): Essa marca denota IMDS, que é um serviço de infraestrutura básico. Se você especificar *AzurePlatformIMDS* para o valor, poderá desabilitar a consideração padrão da [plataforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) para IMDS. Tome cuidado ao usar essa marca. O teste é recomendado antes de usar essa marca. 
* **AzurePlatformLKM** (Somente no Gerenciador de recursos): Esta marca denota o licenciamento do Windows ou o serviço de gerenciamento de chaves. Se você especificar *AzurePlatformLKM* para o valor, poderá desabilitar a consideração padrão da [plataforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) para licenciamento. Tome cuidado ao usar essa marca. O teste é recomendado antes de usar essa marca. 

> [!NOTE]
> As marcas de serviço dos serviços do Azure denotam os prefixos de endereço da nuvem específica que está sendo usada. 

> [!NOTE]
> Se implementar um [ponto final de serviço de rede virtual](virtual-network-service-endpoints-overview.md) relativo a um serviço, como o Armazenamento do Azure ou a Base de Dados SQL do Azure, o Azure adiciona uma [rota](virtual-networks-udr-overview.md#optional-default-routes) para uma sub-rede da rede virtual do serviço. Os prefixos de endereço da rota são os mesmos prefixos de endereço, ou intervalos CIDR, da etiqueta de serviço correspondente.

### <a name="service-tags-in-on-premises"></a>Marcas de serviço no local  
Você pode baixar e integrar com um firewall local a lista de marcas de serviço com detalhes de prefixo nas seguintes publicações semanais para as nuvens [públicas](https://www.microsoft.com/download/details.aspx?id=56519), do [governo dos EUA](https://www.microsoft.com/download/details.aspx?id=57063), da [China](https://www.microsoft.com/download/details.aspx?id=57062)e da [Alemanha](https://www.microsoft.com/download/details.aspx?id=57064) do Azure.

Você também pode recuperar essas informações programaticamente usando a **API de descoberta de marca de serviço** (visualização pública) – [REST](https://aka.ms/discoveryapi_rest), [Azure PowerShell](https://aka.ms/discoveryapi_powershell)e [CLI do Azure](https://aka.ms/discoveryapi_cli). 

> [!NOTE]
> As publicações semanais a seguir (versão antiga) para as nuvens [públicas](https://www.microsoft.com/en-us/download/details.aspx?id=41653), [China](https://www.microsoft.com/en-us/download/details.aspx?id=42064)e [Alemanha](https://www.microsoft.com/en-us/download/details.aspx?id=54770) do Azure serão preteridas até 30 de junho de 2020. Comece a usar as publicações atualizadas, conforme descrito acima. 

## <a name="default-security-rules"></a>Regras de segurança predefinidas

O Azure cria as seguintes regras predefinidas em cada grupo de segurança de rede que criar:

### <a name="inbound"></a>Entrada

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priority|Source|Portas de origem|Destino|Portas de destino|Protocol|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Any|Allow|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priority|Source|Portas de origem|Destino|Portas de destino|Protocol|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Any|Allow|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priority|Source|Portas de origem|Destino|Portas de destino|Protocol|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Any|Negar|

### <a name="outbound"></a>Saída

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priority|Source|Portas de origem| Destino | Portas de destino | Protocol | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Any | Allow |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Priority|Source|Portas de origem| Destino | Portas de destino | Protocol | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Any | Allow |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priority|Source|Portas de origem| Destino | Portas de destino | Protocol | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Any | Negar |

Nas colunas **Source** (Origem) e **Destination** (Destino), *VirtualNetwork*, *AzureLoadBalancer* e *Internet* são [etiquetas de serviço](#service-tags) e não endereços IP. Na coluna protocolo, **qualquer um** abrange TCP, UDP e ICMP. Ao criar uma regra, você pode especificar TCP, UDP, ICMP ou any. *0.0.0.0/0* nas colunas **Source** e **Destination** representa todos os endereços. Clientes como portal do Azure, CLI do Azure ou PowerShell podem usar * ou qualquer para essa expressão.
 
Não pode remover as regras predefinidas, mas pode criar regras com prioridades superiores para substituí-las.

## <a name="application-security-groups"></a>Grupos de segurança de aplicações

Os grupos de segurança de aplicações permitem-lhe configurar a segurança de rede como uma extensão natural da estrutura de uma aplicação, possibilitando o agrupamento de máquinas virtuais e a definição de políticas de segurança de rede com base nesses grupos. Pode reutilizar a política de segurança em escala sem a manutenção manual de endereços IP explícitos. A plataforma lida com a complexidade dos endereços IP explícitos e dos múltiplos conjuntos de regras, permitindo-lhe focar-se na lógica de negócio. Para compreender melhor os grupos de segurança de rede, considere o exemplo seguinte:

![Grupos de segurança de aplicações](./media/security-groups/application-security-groups.png)

Na imagem anterior, *NIC1* e *NIC2* são membros do grupo de segurança de rede *AsgWeb*. *NIC3* é membro do grupo de segurança de rede *AsgLogic*. *NIC4* é membro do grupo de segurança de rede *AsgDb*. Embora cada interface de rede neste exemplo seja membro de apenas um grupo de segurança de rede, as interfaces de rede podem ser membros de vários grupos, até atingirem os [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Não está associado nenhum grupo de segurança de rede às interfaces de rede. *NSG1* está associado a ambas as sub-redes e contém as seguintes regras:

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Esta regra é necessária para permitir o tráfego da Internet para os servidores Web. Uma vez que o tráfego de entrada a partir da Internet é negado pela regra de segurança [DenyAllInbound](#denyallinbound) predefinida, não é necessária qualquer regra adicional para os grupos de segurança de aplicações *AsgLogic* ou *AsgDb*.

|Priority|Source|Portas de origem| Destino | Portas de destino | Protocol | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Allow |

### <a name="deny-database-all"></a>Deny-Database-All

Uma vez que a regra de segurança predefinida [AllowVNetInBound](#allowvnetinbound) permite todas as comunicações entre recursos na mesma rede virtual, esta regra é necessária para negar o tráfego de todos os recursos.

|Priority|Source|Portas de origem| Destino | Portas de destino | Protocol | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Any | Negar |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Esta regra permite o tráfego do grupo de segurança de aplicações *AsgLogic* para o grupo de segurança de aplicações *AsgDb*. A prioridade desta regra é mais alta do que a da regra *Deny-Database-All*. Como resultado, esta regra é processada antes da regra *Deny-Database-All*, de modo a que o tráfego do grupo de segurança de aplicações *AsgLogic* seja permitido, ao passo que o restante tráfego é bloqueado.

|Priority|Source|Portas de origem| Destino | Portas de destino | Protocol | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

As regras que especifiquem grupos de segurança de aplicações como a origem ou o destino só são aplicadas às interfaces de rede que são membros do grupo de segurança de aplicações. Se a interface de rede não for membro de um grupo de segurança de aplicações, a regra não é aplicada à interface de rede, mesmo que o grupo de segurança de rede esteja associado à sub-rede.

Os grupos de segurança de aplicação têm as seguintes restrições:

-   Há limites ao número de grupos de segurança de rede que podem ser incluídos numa subscrição, bem como outros limites relacionados com os grupos de segurança de aplicações. Para obter mais detalhes, veja [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Pode especificar um grupo de segurança de aplicações como origem e destino numa regra de segurança. Não pode especificar vários grupos de segurança de aplicações na origem ou no destino.
- Todas as interfaces de rede atribuídas a um grupo de segurança de aplicações têm de existir na mesma rede virtual em que se encontra a primeira interface de rede atribuída ao grupo de segurança da aplicação. Por exemplo, se a primeira interface de rede atribuída a um grupo de segurança de aplicações denominado *AsgWeb* estiver na rede virtual denominada *VNet1*, todas interfaces de rede subsequentes atribuídas a*ASGWeb* têm de existir em *VNet1*. Não é possível adicionar interfaces de rede de redes virtuais diferentes ao mesmo grupo de segurança de aplicação.
- Se especificar um grupo de segurança de aplicação como a origem e o destino numa regra de segurança, as interfaces de rede em ambos os grupos de segurança de aplicações têm de existir na mesma rede virtual. Por exemplo, se *AsgLogic* contivesse interfaces de rede de *VNet1* e *AsgDb* contivesse interfaces de rede de *VNet2*, não poderia atribuir *AsgLogic* como a origem e *AsgDb* como o destino numa regra. Todas as interfaces de rede para os grupos de segurança de origem e de destino têm de estar na mesma rede virtual.

> [!TIP]
> Para minimizar o número de regras de segurança de que precisa, bem como a necessidade de as alterar, planei os grupos de segurança de aplicações de que precisa e crie regras com etiquetas de serviço ou grupos de segurança de aplicações em vez de endereços IP individuais ou intervalos de endereços IP, sempre que possível.

## <a name="how-traffic-is-evaluated"></a>Como o tráfego é avaliado

Pode implementar recursos de vários serviços do Azure numa rede virtual do Azure. Para obter uma lista completa, veja [Services that can be deployed into a virtual network](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (Serviços que podem ser implementados numa rede virtual). Pode associar nenhum ou um grupo de segurança de rede à [sub-rede](virtual-network-manage-subnet.md#change-subnet-settings) e à [interface de rede](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) de cada rede virtual numa máquina virtual. O mesmo grupo de segurança de rede pode ser associado a tantas sub-redes e interfaces de rede que escolher.

A imagem seguinte ilustra diferentes cenários de implementação dos grupos de segurança de rede para permitir o tráfego de rede de e para a Internet a partir da porta TCP 80:

![NSG-processing](./media/security-groups/nsg-interaction.png)

Veja a imagem anterior, juntamente com o texto seguinte, para compreender de que forma é que o Azure processa as regras de entrada e de saída para os grupos de segurança de rede:

### <a name="inbound-traffic"></a>Tráfego de entrada

Relativamente ao tráfego de entrada, o Azure processa primeiro as regras num grupo de segurança de rede associado a uma sub-rede, se existir alguma, e, depois, as regras num grupo de segurança de rede associado à interface de rede, se existir alguma.

- **VM1**: As regras de segurança em *NSG1* são processadas, pois estão associadas a *Subnet1* e *VM1* estão em *Subnet1*. A menos que tenha criado uma regra que permita a porta de entrada 80, o tráfego é negado pela regra de segurança predefinida [DenyAllInbound](#denyallinbound) e nunca é avaliado por *NSG2*, pois *NSG2* está associado à interface de rede. Se *NSG1* tiver uma regra de segurança que permite a porta 80, o tráfego é processado por *NSG2*. Para permitir a porta 80 na máquina virtual, tanto *NSG1*, como *NSG2*, têm de ter uma regra que permite a porta 80 a partir da Internet.
- **VM2**: As regras em *NSG1* são processadas porque *VM2* também está em *Subnet1*. Uma vez que *VM2* não tem um grupo de segurança de rede associado à respetiva interface de rede, recebe todo o tráfego permitido através *NSG1* ou é-lhe negado todo o tráfego negado por *NSG1*. O tráfego é permitido ou negado para todos os recursos na mesma sub-rede se um grupo de segurança de rede estiver associado a uma sub-rede.
- **VM3**: Como não há nenhum grupo de segurança de rede associado a *Subnet2*, o tráfego é permitido na sub-rede e processado pelo *NSG2*, pois *NSG2* está associado ao adaptador de rede anexado ao *VM3*.
- **VM4**: O tráfego é permitido para *VM4,* pois um grupo de segurança de rede não está associado a *Subnet3*ou a interface de rede na máquina virtual. Todo o tráfego de rede é permitido através de uma sub-rede e de uma interface de rede se não houver nenhum grupo de segurança de rede associado às mesmas.

### <a name="outbound-traffic"></a>Tráfego de saída

Relativamente ao tráfego de saída, o Azure processa primeiro as regras num grupo de segurança de rede associado a uma interface de rede, se existir alguma, e, depois, as regras num grupo de segurança de rede associado à sub-rede, se existir alguma.

- **VM1**: As regras de segurança no *NSG2* são processadas. A menos que crie uma regra de segurança que negue a porta 80 de saída para a Internet, o tráfego é permitido pela regra de segurança predefinida [AllowInternetOutbound](#allowinternetoutbound) em *NSG1* e *NSG2*. Se *NSG2* tiver uma regra de segurança que negue a porta 80, o tráfego é negado e nunca avaliado por *NSG1*. Para negar a porta 80 a partir da máquina virtual, um ou ambos os grupos de segurança de rede têm de ter uma regra que negue a porta 80 para a Internet.
- **VM2**: Todo o tráfego é enviado por meio do adaptador de rede para a sub-rede, já que o adaptador de rede anexado a *VM2* não tem um grupo de segurança de rede associado a ele. As regras em *NSG1* são processadas.
- **VM3**: Se *NSG2* tiver uma regra de segurança que nega a porta 80, o tráfego será negado. Se *NSG2* tiver uma regra de segurança que permita a porta 80, é permitido tráfego de saída para a Internet na mesma, uma vez que não existe nenhum grupo de segurança de rede associado a *Subnet2*.
- **VM4**: Todo o tráfego de rede é permitido de *VM4,* porque um grupo de segurança de rede não está associado ao adaptador de rede anexado à máquina virtual ou ao *Subnet3*.

Pode ver as [regras de segurança em vigor](virtual-network-network-interface.md#view-effective-security-rules) numa interface de rede para ver facilmente as regras agregadas que estão aplicadas à mesma. Também pode utilizar a capacidade [Verificação de fluxo de IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Observador de Rede do Azure para saber se a comunicação é permitida de ou para uma interface de rede. O fluxo de IP indica se a comunicação é permitida ou negada e que regra de segurança de rede permite ou nega o tráfego.

> [!NOTE]
> Os grupos de segurança de rede são associados a sub-redes ou a máquinas virtuais e serviços de nuvem implantados no modelo de implantação clássico e a sub-redes ou interfaces de rede no modelo de implantação do Resource Manager. Para saber mais sobre os modelos de implementação do Azure, veja [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Compreender os modelos de implementação do Azure).

> [!TIP]
> A menos que tenha um motivo específico, recomendamos que associe um grupo de segurança de rede a uma sub-rede ou a uma interface de rede, mas não a ambas. Uma vez que as regras num grupo de segurança de rede associado a uma sub-rede podem entrar em conflito com as regras num grupo associado a uma interface de rede, poderão ocorrer problemas de comunicação inesperados que exijam resolução.

## <a name="azure-platform-considerations"></a>Considerações sobre a plataforma do Azure

- **IP virtual do nó do host**: Serviços de infraestrutura básica, como DHCP, DNS, IMDS e monitoramento de integridade, são fornecidos por meio dos endereços IP de host virtualizados 168.63.129.16 e 169.254.169.254. Esses endereços IP pertencem à Microsoft e são os únicos endereços IP virtualizados usados em todas as regiões para essa finalidade.
- **Licenciamento (serviço de gerenciamento de chaves)** : As imagens do Windows em execução em máquinas virtuais devem ser licenciadas. Para garantir o licenciamento, é enviado um pedido para os servidores de anfitrião do Key Management Service que processam estas consultas. O pedido é feito através da porta 1688 de saída. Para implementações que utilizam a configuração de [rota predefinida 0.0.0.0/0](virtual-networks-udr-overview.md#default-route), esta regra de plataforma será desativada.
- **Máquinas virtuais em pools com balanceamento de carga**: A porta de origem e o intervalo de endereços aplicados são do computador de origem, não do balanceador de carga. A porta de destino e o intervalo de endereços destinam-se ao computador de destino, não ao balanceador de carga.
- **Instâncias de serviço do Azure**: As instâncias de vários serviços do Azure, como HDInsight, ambientes de serviço de aplicativo e conjuntos de dimensionamento de máquinas virtuais, são implantadas em sub-redes de rede virtual. Para obter uma lista completa dos serviços que pode implementar em redes virtuais, veja [Rede virtual para os serviços do Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Certifique-se de que se familiariza com os requisitos de portas de cada serviço antes de aplicar um grupo de segurança de rede à sub-rede na qual o recurso está implementado. Se negar portas de que os serviços precisam, estes não funcionarão corretamente.
- **Enviando email de saída**: A Microsoft recomenda que você utilize os serviços de retransmissão SMTP autenticados (normalmente conectados via porta TCP 587, mas, muitas vezes, outras também) para enviar emails de máquinas virtuais do Azure. Os serviços de reencaminhamento de SMTP especializam-se na reputação do remetente, para minimizar a possibilidade de os fornecedores de e-mail de terceiros rejeitarem mensagens. Esses serviços de reencaminhamento de SMTP incluem, sem estarem limitados, o Exchange Online Protection e o SendGrid. A utilização dos serviços de reencaminhamento de SMTP não está de forma alguma restringida no Azure, independentemente do seu tipo de subscrição. 

  Se tiver criado a sua subscrição do Azure antes de 15 de novembro de 2017, para além de poder utilizar os serviços de reencaminhamento de SMTP, também pode enviar e-mails diretamente através da porta TCP 25. Se tiver criado a sua subscrição após 15 de novembro de 2017, poderá não conseguir enviar e-mails diretamente através da porta 25. O comportamento da comunicação de saída através da porta 25 depende do seu tipo de subscrição, da seguinte forma:

     - **Enterprise Agreement**: A comunicação de saída da porta 25 é permitida. Pode enviar e-mails de saída diretamente a partir de máquinas virtuais para fornecedores de e-mail externos, sem restrições impostas pela plataforma do Azure. 
     - **Pré-pago:** A comunicação de saída da porta 25 está bloqueada de todos os recursos. Se tiver de enviar e-mails a partir de uma máquina virtual diretamente para fornecedores de e-mail externos (sem utilizar um reencaminhamento de SMTP autenticado), pode fazer um pedido para remover a restrição. Os pedidos são analisados e aprovados à discrição da Microsoft, apenas sendo concedidos após as verificações contra fraudes. Para fazer um pedido, abra um processo de suporte com o tipo de problema *Técnico*, *Conectividade da Rede Virtual*, *Não é possível enviar e-mails (SMTP/Porta 25)* . No processo de suporte, inclua detalhes sobre o motivo pelo qual a subscrição tem de enviar e-mails diretamente para fornecedores de e-mail, em vez de utilizar um reencaminhamento de SMTP autenticado. Se a sua subscrição for isenta, apenas as máquinas virtuais criadas após a data de isenção conseguem realizar comunicações de saída através da porta 25.
     - **Msdn, Azure Pass, Azure via Open, educação, BizSpark e avaliação gratuita**: A comunicação de saída da porta 25 está bloqueada de todos os recursos. Não pode fazer pedidos para remover a restrição, porque os pedidos não são concedidos. Se tiver de enviar e-mails a partir da sua máquina virtual, tem de utilizar um serviço de reencaminhamento de SMTP.
     - **Provedor de serviços de nuvem**: Os clientes que estão consumindo recursos do Azure por meio de um provedor de serviços de nuvem podem criar um caso de suporte com seu provedor de serviços de nuvem e solicitar que o provedor crie um caso de desbloqueio em seu nome, se uma retransmissão de SMTP segura não puder ser usada.

  Se o Azure lhe permitir enviar e-mails através da porta 25, a Microsoft não pode garantir que os fornecedores de e-mail aceitarão e-mails enviados a partir da sua máquina virtual. Se um fornecedor específico rejeitar e-mails da sua máquina virtual, trabalhe diretamente com o fornecedor para resolver quaisquer problemas relacionados com a entrega de mensagens ou com a filtragem de spam, ou tem de utilizar um serviço de reencaminhamento de SMTP autenticado.

## <a name="next-steps"></a>Passos Seguintes

* Leia o artigo [Create a network security group](tutorial-filter-network-traffic.md) (Criar um grupo de segurança de rede).
