---
title: Visão geral das marcas de serviço do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre marcas de serviço. As marcas de serviço ajudam a minimizar a complexidade da criação da regra de segurança.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: e4c18f1148259d246445b94ac6117ebefa9470a4
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975381"
---
# <a name="virtual-network-service-tags"></a>Marcas de serviço de rede virtual 
<a name="network-service-tags"></a>

Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados, minimizando a complexidade das atualizações frequentes para as regras de segurança de rede. 

Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) ou no [Firewall do Azure](https://docs.microsoft.com/azure/firewall/service-tags). Use marcas de serviço no lugar de endereços IP específicos ao criar regras de segurança. Ao especificar o nome da marca de serviço (por exemplo, **ApiManagement**) no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. 

Você pode usar as marcas de serviço para obter o isolamento de rede e proteger os recursos do Azure da Internet geral ao acessar os serviços do Azure que têm pontos de extremidade públicos. Crie regras de grupo de segurança de rede de entrada/saída para negar o tráfego de/para a **Internet** e permitir o tráfego de/para **AzureCloud** ou outras [marcas de serviço disponíveis](#available-service-tags) de serviços específicos do Azure. 

## <a name="available-service-tags"></a>Marcas de serviço disponíveis
A tabela a seguir inclui todas as marcas de serviço disponíveis para uso em regras de [grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

As colunas indicam se a marca:

- É adequado para regras que abrangem o tráfego de entrada ou de saída.
- Dá suporte ao escopo [regional](https://azure.microsoft.com/regions) .
- É utilizável em regras de [Firewall do Azure](https://docs.microsoft.com/azure/firewall/service-tags) .

Por padrão, as marcas de serviço refletem os intervalos para toda a nuvem. Algumas marcas de serviço também permitem um controle mais granular restringindo os intervalos de IP correspondentes a uma região especificada. Por exemplo, o **armazenamento** de marca de serviço representa o armazenamento do Azure para toda a nuvem, mas o **armazenamento. westus** limita o intervalo apenas aos intervalos de endereços IP de armazenamento da região westus. A tabela a seguir indica se cada marca de serviço dá suporte a esse escopo regional.  

| Etiqueta | Finalidade | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com o Firewall do Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Tráfego de gerenciamento para o gerenciamento de API do Azure-implantações dedicadas. | Ambos | Não | Sim |
| **ApplicationInsightsAvailability** | Disponibilidade de Application Insights. | Ambos | Não | Não |
| **AppService**    | Serviço de aplicações do Azure. Essa marca é recomendada para regras de segurança de saída para front-ends do aplicativo Web. | Saída | Sim | Sim |
| **AppServiceManagement** | Tráfego de gerenciamento para implantações dedicadas a Ambiente do Serviço de Aplicativo. | Ambos | Não | Sim |
| **AzureActiveDirectory** | Azure Active Directory. | Saída | Não | Sim |
| **AzureActiveDirectoryDomainServices** | Tráfego de gerenciamento para implantações dedicadas a Azure Active Directory Domain Services. | Ambos | Não | Sim |
| **AzureAdvancedThreatProtection** | Proteção avançada contra ameaças do Azure. | Saída | Não | Não |
| **AzureBackup** |Backup do Azure.<br/><br/>*Observação:* Essa marca tem uma dependência nas marcas de **armazenamento** e **AzureActiveDirectory** . | Saída | Não | Sim |
| **AzureBotService** | Serviço de bot do Azure. | Saída | Não | Não |
| **AzureCloud** | Todos os [endereços IP públicos do datacenter](https://www.microsoft.com/download/details.aspx?id=41653). | Saída | Sim | Sim |
| **AzureCognitiveSearch** | Pesquisa Cognitiva do Azure (se estiver usando indexadores com um qualificable). | Ambos | Não | Não |
| **AzureConnectors** | Conectores de aplicativos lógicos do Azure para conexões de investigação/back-end. | Entrada | Sim | Sim |
| **AzureContainerRegistry** | Registro de contêiner do Azure. | Saída | Sim | Sim |
| **AzureCosmosDB** | Azure Cosmos DB. | Saída | Sim | Sim |
| **AzureDatabricks** | Azure Databricks. | Ambos | Não | Não |
| **AzureDataExplorerManagement** | Gerenciamento de Data Explorer do Azure. | Entrada | Não | Não |
| **AzureDataLake** | Azure Data Lake. | Saída | Não | Sim |
| **AzureEventGrid** | Grade de eventos do Azure. <br/><br/>*Observação:* Esta marca aborda os pontos de extremidade da grade de eventos do Azure no centro-sul dos EUA, leste dos EUA, leste dos EUA 2, oeste dos EUA 2 e Centro dos EUA apenas. | Ambos | Não | Não |
| **AzureFrontDoor** | Porta frontal do Azure. | Ambos | Não | Não |
| **AzureInformationProtection** | Proteção de informações do Azure.<br/><br/>*Observação:* Essa marca tem uma dependência nas marcas **AzureActiveDirectory** e **AzureFrontDoor. frontend** . Além disso, leia a lista de permissões a seguir IPs (essa dependência será removida em breve): 13.107.6.181 & 13.107.9.181. | Saída | Não | Não |
| **AzureIoTHub** | Hub IoT do Azure. | Saída | Não | Não |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Observação:* Essa marca tem uma dependência na marca **AzureActiveDirectory** . | Saída | Sim | Sim |
| **AzureLoadBalancer** | O balanceador de carga de infraestrutura do Azure. A marca se traduz no [endereço IP virtual do host](security-overview.md#azure-platform-considerations) (168.63.129.16) onde as investigações de integridade do Azure se originam. Se você não estiver usando Azure Load Balancer, poderá substituir essa regra. | Ambos | Não | Não |
| **AzureMachineLearning** | Azure Machine Learning. | Ambos | Não | Sim |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon e métricas personalizadas (pontos de extremidade GiG).<br/><br/>*Observação:* Por Log Analytics, essa marca tem uma dependência na marca de **armazenamento** . | Saída | Não | Sim |
| **AzurePlatformDNS** | O serviço DNS de infraestrutura básica (padrão).<br/><br>Você pode usar essa marca para desabilitar o DNS padrão. Tenha cuidado ao usar essa marca. Recomendamos que você leia as [considerações da plataforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Também recomendamos que você execute os testes antes de usar essa marca. | Saída | Não | Não |
| **AzurePlatformIMDS** | O IMDS (serviço de metadados de instância do Azure), que é um serviço de infraestrutura básico.<br/><br/>Você pode usar essa marca para desabilitar o IMDS padrão. Tenha cuidado ao usar essa marca. Recomendamos que você leia as [considerações da plataforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Também recomendamos que você execute os testes antes de usar essa marca. | Saída | Não | Não |
| **AzurePlatformLKM** | Licenciamento do Windows ou serviço de gerenciamento de chaves.<br/><br/>Você pode usar essa marca para desabilitar os padrões de licenciamento. Tenha cuidado ao usar essa marca. Recomendamos que você leia as [considerações da plataforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Também recomendamos que você execute os testes antes de usar essa marca. | Saída | Não | Não |
| **AzureResourceManager** | Azure Resource Manager. | Saída | Não | Não |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Observação:* Essa marca tem uma dependência nas marcas de **armazenamento**, **AzureActiveDirectory**e **EventHub** . | Saída | Não | Não |
| **AzureTrafficManager** | Endereços IP de investigação do Gerenciador de tráfego do Azure.<br/><br/>Para obter mais informações sobre endereços IP de investigação do Gerenciador de tráfego, consulte [perguntas frequentes do Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Entrada | Não | Sim |  
| **BatchNodeManagement** | Tráfego de gerenciamento para implantações dedicadas ao lote do Azure. | Ambos | Não | Sim |
| **CognitiveServicesManagement** | Os intervalos de endereços para o tráfego para serviços cognitivas do Azure. | Saída | Não | Não |
| **Dynamics365ForMarketingEmail** | Os intervalos de endereços para o serviço de email de marketing do Dynamics 365. | Saída | Sim | Não |
| **ElasticAFD** | Porta de recepção do Azure elástica. | Ambos | Não | Não |
| **EventHub** | Hubs de eventos do Azure. | Saída | Sim | Sim |
| **Gatewaymanager** | Tráfego de gerenciamento para implantações dedicadas ao gateway de VPN do Azure e ao gateway de aplicativo. | Entrada | Não | Não |
| **GuestAndHybridManagement** | Configuração de convidado e automação do Azure. | Ambos | Não | Sim |
| **HDInsight** | Azure HDInsight. | Entrada | Sim | Não |
| **Internet** | O espaço de endereço IP que está fora da rede virtual e acessível pela Internet pública.<br/><br/>O intervalo de endereços inclui o [espaço de endereço IP público de Propriedade do Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Ambos | Não | Não |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Saída | Não | Não |
| **MicrosoftContainerRegistry** | Registro de contêiner do Azure. | Saída | Sim | Sim |
| **ServiceBus** | O tráfego do barramento de serviço do Azure que usa a camada de serviço Premium. | Saída | Sim | Sim |
| **ServiceFabric** | Service Fabric do Azure. | Saída | Não | Não |
| **Sql** | Banco de dados SQL do Azure, banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL e Azure SQL Data Warehouse.<br/><br/>*Observação:* Essa marca representa o serviço, mas não instâncias específicas do serviço. Por exemplo, representa o serviço da Base de Dados SQL do Azure, mas não uma base de dados ou um servidor SQL. | Saída | Sim | Sim |
| **SqlManagement** | Tráfego de gerenciamento para implantações dedicadas do SQL. | Ambos | Não | Sim |
| **Armazenamento** | Armazenamento do Azure. <br/><br/>*Observação:* Essa marca representa o serviço, mas não instâncias específicas do serviço. Por exemplo, representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica. | Saída | Sim | Sim |
| **VirtualNetwork** | O espaço de endereço de rede virtual (todos os intervalos de endereços IP definidos para a rede virtual), todos os espaços de endereço locais conectados, redes virtuais [emparelhadas](virtual-network-peering-overview.md) , redes virtuais conectadas a um [Gateway de rede virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), o [endereço IP virtual do host e os](security-overview.md#azure-platform-considerations)prefixos de endereço usados em [rotas definidas pelo usuário](virtual-networks-udr-overview.md). Essa marca também pode conter rotas padrão. | Ambos | Não | Não |

>[!NOTE]
>No modelo de implantação clássico (antes de Azure Resource Manager), há suporte para um subconjunto das marcas listadas na tabela anterior. Essas marcas são escritas de forma diferente:
>
>| Ortografia clássica | Marca do Gerenciador de recursos equivalente |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> As marcas de serviço dos serviços do Azure denotam os prefixos de endereço da nuvem específica que está sendo usada. Por exemplo, os intervalos de IP subjacentes que correspondem ao valor da marca **SQL** na nuvem pública do Azure serão diferentes dos intervalos subjacentes na nuvem do Azure na China.

> [!NOTE]
> Se implementar um [ponto final de serviço de rede virtual](virtual-network-service-endpoints-overview.md) relativo a um serviço, como o Armazenamento do Azure ou a Base de Dados SQL do Azure, o Azure adiciona uma [rota](virtual-networks-udr-overview.md#optional-default-routes) para uma sub-rede da rede virtual do serviço. Os prefixos de endereço na rota são os mesmos prefixos de endereço ou intervalos CIDR, como os da marca de serviço correspondente.

## <a name="service-tags-on-premises"></a>Marcas de serviço locais  
Você pode obter a marca de serviço e as informações de intervalo atuais para incluir como parte das configurações de firewall local. Essa informação é a lista pontual atual dos intervalos de IP que correspondem a cada marca de serviço. Você pode obter as informações programaticamente ou por meio de um download de arquivo JSON, conforme descrito nas seções a seguir.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Usar a API de descoberta de marca de serviço (visualização pública)
Você pode recuperar programaticamente a lista atual de marcas de serviço junto com detalhes do intervalo de endereços IP:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [O Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Embora esteja em visualização pública, a API de descoberta pode retornar informações que são menos atuais do que as informações retornadas pelos downloads JSON. (Consulte a próxima seção.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Descobrir marcas de serviço usando arquivos JSON baixáveis 
Você pode baixar arquivos JSON que contêm a lista atual de marcas de serviço junto com detalhes do intervalo de endereços IP. Essas listas são atualizadas e publicadas semanalmente. Os locais para cada nuvem são:

- [Público do Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Um subconjunto dessas informações foi publicado em arquivos XML para o Azure [Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)e [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=54770). Esses downloads XML serão preteridos até 30 de junho de 2020 e não estarão mais disponíveis após essa data. Você deve migrar para o usando a API de descoberta ou downloads de arquivo JSON, conforme descrito nas seções anteriores.

### <a name="tips"></a>Sugestões 
- Você pode detectar atualizações de uma publicação para a próxima, observando valores de *changeNumber* maiores no arquivo JSON. Cada subseção (por exemplo, **Storage. westus**) tem seu próprio *changeNumber* que é incrementado conforme ocorrem alterações. O nível superior do *changeNumber* do arquivo é incrementado quando qualquer uma das subseções é alterada.
- Para obter exemplos de como analisar as informações de marca de serviço (por exemplo, obter todos os intervalos de endereços para armazenamento na Westus), consulte a documentação do [PowerShell da API de descoberta de marca de serviço](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Passos seguintes
- Saiba como [criar um grupo de segurança de rede](tutorial-filter-network-traffic.md).
