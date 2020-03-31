---
title: Visão geral das etiquetas de serviço Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre etiquetas de serviço. As etiquetas de serviço ajudam a minimizar a complexidade da criação de regras de segurança.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 568fc880711d42941fd9aef2ea19b8ac3123793a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384250"
---
# <a name="virtual-network-service-tags"></a>Tags de serviço de rede virtual
<a name="network-service-tags"></a>

Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereço sacados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede.

Pode utilizar etiquetas de serviço para definir controlos de acesso à rede em [grupos](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) de segurança de rede ou [firewall Azure](https://docs.microsoft.com/azure/firewall/service-tags). Utilize etiquetas de serviço no lugar de endereços IP específicos quando criar regras de segurança. Especificando o nome da etiqueta de serviço (por exemplo, **ApiManagement)** no campo de *origem* ou *destino* adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente.

Pode utilizar etiquetas de serviço para alcançar o isolamento da rede e proteger os seus recursos Azure da Internet geral, ao mesmo tempo que acede aos serviços Azure que têm pontos finais públicos. Crie regras de grupo de segurança de rede de entrada/saída para negar o tráfego de/para a **Internet** e permitir o tráfego de/para **o AzureCloud** ou [outras etiquetas](#available-service-tags) de serviço disponíveis de serviços específicos do Azure.

## <a name="available-service-tags"></a>Etiquetas de serviço disponíveis
A tabela seguinte inclui todas as etiquetas de serviço disponíveis para utilização nas regras do grupo de segurança da [rede.](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)

As colunas indicam se a etiqueta:

- É adequado para regras que cobrem o tráfego de entrada ou saída.
- Apoia o âmbito [regional.](https://azure.microsoft.com/regions)
- É utilizável nas regras [da Firewall Azure.](https://docs.microsoft.com/azure/firewall/service-tags)

Por padrão, as etiquetas de serviço refletem as gamas para toda a nuvem. Algumas etiquetas de serviço também permitem um maior controlo granular, restringindo as gamas IP correspondentes a uma região especificada. Por exemplo, a etiqueta de serviço **Storage** representa o Armazenamento Azure para toda a nuvem, mas **Storage.WestUS** reduz a gama para apenas o endereço IP de armazenamento da região de WestUS. O quadro seguinte indica se cada etiqueta de serviço suporta tal âmbito regional.  

| Etiqueta | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com firewall Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Grupo de Ação** | Grupo de Ação. | Entrada | Não | Não |
| **ApiManagement** | Tráfego de gestão para implantações dedicadas à Gestão Azure API. <br/><br/>*Nota:* Esta etiqueta representa o ponto final do serviço Azure API Management para o plano de controlo por região. Isto permite que os clientes realizem operações de gestão nas APIs, Operações, Políticas, Valores Nomeados configurados no serviço de Gestão API.  | Entrada | Sim | Sim |
| **AplicaçõesInsightsDisponibilidade** | Disponibilidade de Insights de Aplicação. | Entrada | Não | Não |
| **Configuração de aplicações** | Configuração de aplicativos. | Saída | Não | Não |
| **AppService**    | Serviço de Aplicações Azure. Esta etiqueta é recomendada para regras de segurança de saída para as extremidades frontais da aplicação web. | Saída | Sim | Sim |
| **Gestão de Serviços de Aplicação** | Tráfego de gestão para implementações dedicadas ao Ambiente de Serviço de Aplicações. | Ambos | Não | Sim |
| **AzureActiveDirectory** | Azure Active Directory. | Saída | Não | Sim |
| **AzureActiveDirectoryDomainServices** | Tráfego de gestão para implantações dedicadas aos Serviços de Domínio de Diretório Ativo Azure. | Ambos | Não | Sim |
| **AzureAdvancedThreatProtection** | Proteção avançada de ameaças azure. | Saída | Não | Não |
| **AzureBackup** |Reforço Azure.<br/><br/>*Nota:* Esta etiqueta tem uma dependência das etiquetas **De Armazenamento** e **AzureActiveDirectory.** | Saída | Não | Sim |
| **AzureBotService** | Serviço Azure Bot. | Saída | Não | Não |
| **AzureCloud** | Todos os [endereços IP públicos do datacenter](https://www.microsoft.com/download/details.aspx?id=56519). | Saída | Sim | Sim |
| **AzureCognitiveSearch** | Pesquisa Cognitiva Azure. <br/><br/>Esta etiqueta ou os endereços IP abrangidos por esta etiqueta podem ser utilizados para conceder aos indexantes acesso seguro a fontes de dados. Consulte a documentação de ligação do [indexante](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) para mais detalhes. <br/><br/> *Nota*: O IP do serviço de pesquisa não está incluído na lista de gamas IP para esta etiqueta de serviço e **também precisa de ser adicionado** à firewall IP de fontes de dados. | Entrada | Não | Não |
| **Ligadores Azure** | Conectores de aplicativos de lógica Azure para ligações de sonda/back-end. | Entrada | Sim | Sim |
| **Registo de Contentores Azure** | Registo de contentores azure. | Saída | Sim | Sim |
| **AzureCosmosDB** | Azure Cosmos DB. | Saída | Sim | Sim |
| **AzureDatabricks** | Tijolos de dados azure. | Ambos | Não | Não |
| **AzureDataExplorerManagement** | Gestão do Explorador de Dados Azure. | Entrada | Não | Não |
| **Lago AzureData** | Azure Data Lake Storage Gen1. | Saída | Não | Sim |
| **Espaços AzureDevEspaços** | Espaços Azure Dev. | Saída | Não | Não |
| **AzureEventGrid** | Grelha de Eventos Azure. <br/><br/>*Nota:* Esta etiqueta abrange pontos finais da Azure Event Grid nos EUA South Central, US East, US East 2, US West 2 e US Central apenas. | Ambos | Não | Não |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Porta da frente azure. | Ambos | Não | Não |
| **AzureInformationProtection** | Proteção de Informação Azure.<br/><br/>*Nota:* Esta etiqueta tem uma dependência das tags **AzureActiveDirectory,** **AzureFrontDoor.Frontend** e **AzureFrontDoor.FirstParty** tags. | Saída | Não | Não |
| **AzureIoTHub** | Hub Azure IoT. | Saída | Não | Não |
| **AzureKeyVault** | Cofre de Chaves Azure.<br/><br/>*Nota:* Esta etiqueta tem uma dependência da etiqueta **AzureActiveDirectory.** | Saída | Sim | Sim |
| **AzureLoadBalancer** | O equilibrador de carga de infraestruturas Azure. A etiqueta traduz-se no [endereço IP virtual do hospedeiro](security-overview.md#azure-platform-considerations) (168.63.129.16) de onde provêm as sondas de saúde Azure. Isto não inclui tráfego para o seu recurso Azure Load Balancer. Se não estiver a usar o Azure Load Balancer, pode anular esta regra. | Ambos | Não | Não |
| **AzureMachineLearning** | Aprendizagem automática azure. | Ambos | Não | Sim |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon e métricas personalizadas (pontos finais giG).<br/><br/>*Nota:* Para o Log Analytics, esta etiqueta tem uma dependência da etiqueta **de Armazenamento.** | Saída | Não | Sim |
| **AzureOpenDatasets** | Conjuntos de dados abertos azure.<br/><br/>*Nota:* Esta etiqueta tem uma dependência da etiqueta **AzureFrontDoor.Frontend** e **Storage.** | Saída | Não | Não |
| **AzurePlatformDNS** | O serviço de DNS de infraestrutura básica (padrão).<br/><br>Pode utilizar esta etiqueta para desativar o DNS predefinido. Seja cauteloso quando usar esta etiqueta. Recomendamos que leia [considerações da plataforma Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Recomendamos também que realize testes antes de utilizar esta etiqueta. | Saída | Não | Não |
| **AzurePlatformIMDS** | Serviço de Metadados de Instância Azure (IMDS), que é um serviço básico de infraestrutura.<br/><br/>Pode utilizar esta etiqueta para desativar o IMDS predefinido. Seja cauteloso quando usar esta etiqueta. Recomendamos que leia [considerações da plataforma Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Recomendamos também que realize testes antes de utilizar esta etiqueta. | Saída | Não | Não |
| **AzurePlatformLKM** | Serviço de licenciamento ou gestão de chaves do Windows.<br/><br/>Pode utilizar esta etiqueta para desativar os defeitos para licenciamento. Seja cauteloso quando usar esta etiqueta. Recomendamos que leia [considerações da plataforma Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)  Recomendamos também que realize testes antes de utilizar esta etiqueta. | Saída | Não | Não |
| **AzureResourceManager** | Gestor de Recursos Azure. | Saída | Não | Não |
| **AzureSignalR** | Sinal azul. | Saída | Não | Não |
| **Recuperação do AzureSite** | Azure Site Recovery.<br/><br/>*Nota:* Esta etiqueta tem uma dependência das tags **AzureActiveDirectory,** **AzureKeyVault,** **EventHub,****GuestAndHybridManagement** and **Storage.** | Saída | Não | Não |
| **AzureTrafficManager** | Endereços IP do Gestor de Tráfego Azure.<br/><br/>Para obter mais informações sobre os endereços IP do Gestor de Tráfego, consulte [o Gestor de Tráfego do Azure FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Entrada | Não | Sim |  
| **Gestão de BatchNode** | Tráfego de gestão para implantações dedicadas ao Lote Azure. | Ambos | Não | Sim |
| **CognitiveServicesManagement** | O endereço varia para tráfego para Serviços Cognitivos Azure. | Saída | Não | Não |
| **DataFactory**  | Azure Data Factory | Ambos | Não | Não |
| **Gestão dataFactory** | Tráfego de gestão para a Azure Data Factory. | Saída | Não | Não |
| **Dynamics365ForMarketingEmail** | O endereço varia para o serviço de e-mail de marketing da Dynamics 365. | Saída | Sim | Não |
| **ElasticafD** | Porta da frente elástico Azure. | Ambos | Não | Não |
| **EventHub** | Hubs de eventos Azure. | Saída | Sim | Sim |
| **GatewayManager** | Tráfego de gestão para implementações dedicadas ao Azure VPN Gateway e Application Gateway. | Entrada | Não | Não |
| **GuestAndHybridManagement** | Automação Azure e Configuração de Hóspedes. | Saída | Não | Sim |
| **HDInsight** | Azure HDInsight. | Entrada | Sim | Não |
| **Internet** | O espaço de endereçoIP que está fora da rede virtual e acessível pela internet pública.<br/><br/>A gama de endereços inclui o [espaço de endereços IP públicos do Azure.](https://www.microsoft.com/download/details.aspx?id=41653) | Ambos | Não | Não |
| **LogicApps** | Aplicativos lógicos. | Ambos | Não | Não |
| **LogicAppsManagement** | Tráfego de gestão para Aplicações Lógicas. | Entrada | Não | Não |
| **MicrosoftCloudAppSecurity** | Segurança de aplicações da Microsoft Cloud. | Saída | Não | Não |
| **MicrosoftContainerRegistry** | Registo de contentores para imagens de contentores da Microsoft. <br/><br/>*Nota:* Esta etiqueta tem uma dependência da etiqueta **AzureFrontDoor.FirstParty.** | Saída | Sim | Sim |
| **PowerQueryOnline** | Consulta de energia online. | Ambos | Não | Não |
| **ServiceBus** | Tráfego de ônibus de serviço Azure que utiliza o nível de serviço Premium. | Saída | Sim | Sim |
| **ServiceFabric** | Tecido de serviço Azure.<br/><br/>*Nota:* Esta etiqueta representa o ponto final do serviço Service Fabric para o plano de controlo por região. Isto permite que os clientes realizem operações de gestão para os seus clusters de Tecido de Serviço a partir do seu VNET (ponto final, por exemplo. https:// westus.servicefabric.azure.com) | Ambos | Não | Não |
| **Rio Sql** | Base de Dados Azure SQL, Base de Dados Azure para MySQL, Base de Dados Azure para PostgreSQL e Armazém de Dados Azure SQL.<br/><br/>*Nota:* Esta etiqueta representa o serviço, mas não instâncias específicas do serviço. Por exemplo, representa o serviço da Base de Dados SQL do Azure, mas não uma base de dados ou um servidor SQL. Esta etiqueta não se aplica à instância gerida pela SQL. | Saída | Sim | Sim |
| **SqlManagement** | Tráfego de gestão para implementações dedicadas à SQL. | Ambos | Não | Sim |
| **Armazenamento** | Armazém Azure. <br/><br/>*Nota:* Esta etiqueta representa o serviço, mas não instâncias específicas do serviço. Por exemplo, representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica. | Saída | Sim | Sim |
| **Serviço de Sincronização de Armazenamento** | Serviço de Sincronização de Armazenamento. | Ambos | Não | Não |
| **WindowsVirtualDesktop** | Windows Virtual Desktop. | Ambos | Não | Não |
| **VirtualNetwork** | O espaço de endereço seletiva da rede virtual (todas as gamas de endereços IP definidas para a rede virtual), todos os espaços de endereços ligados no local, redes [virtuais,](virtual-network-peering-overview.md) redes virtuais ligadas a um portal de [rede virtual,](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)o [endereço IP virtual do anfitrião,](security-overview.md#azure-platform-considerations)e prefixos de endereço utilizados nas rotas definidas pelo [utilizador.](virtual-networks-udr-overview.md) Esta etiqueta também pode conter rotas predefinidas. | Ambos | Não | Não |

>[!NOTE]
>No modelo de implantação clássico (antes do Azure Resource Manager), é suportado um subconjunto das etiquetas listadas na tabela anterior. Estas etiquetas são escritas de forma diferente:
>
>| Ortografia clássica | Etiqueta de Gestor de Recursos Equivalente |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> As etiquetas de serviço dos serviços Azure denotam os prefixos de endereço da nuvem específica que está a ser utilizada. Por exemplo, as gamas IP subjacentes que correspondem ao valor da etiqueta **Sql** na nuvem pública azure serão diferentes das gamas subjacentes na nuvem Azure China.

> [!NOTE]
> Se implementar um [ponto final de serviço de rede virtual](virtual-network-service-endpoints-overview.md) relativo a um serviço, como o Armazenamento do Azure ou a Base de Dados SQL do Azure, o Azure adiciona uma [rota](virtual-networks-udr-overview.md#optional-default-routes) para uma sub-rede da rede virtual do serviço. Os prefixos de endereço na rota são os mesmos prefixos de endereço, ou gamas CIDR, como os da etiqueta de serviço correspondente.

## <a name="service-tags-on-premises"></a>Etiquetas de serviço no local  
Pode obter a etiqueta de serviço atual e as informações de alcance para incluir como parte das configurações de firewall no local. Esta informação é a lista atual ponto-a-tempo das gamas IP que correspondem a cada etiqueta de serviço. Pode obter a informação programáticamente ou através de um download de ficheiros JSON, conforme descrito nas seguintes secções.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Utilize a Marca de Serviço Discovery API (pré-visualização pública)
Pode recuperar programáticamente a lista atual de tags de serviço juntamente com detalhes da gama de endereços IP:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Enquanto está em pré-visualização pública, a Discovery API pode devolver informações menos atuais do que as informações devolvidas pelos downloads da JSON. (Ver a próxima secção.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Descubra etiquetas de serviço utilizando ficheiros JSON descarregados 
Pode descarregar ficheiros JSON que contenham a lista atual de tags de serviço juntamente com os detalhes da gama de endereços IP. Estas listas são atualizadas e publicadas semanalmente. Os locais para cada nuvem são:

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Um subconjunto desta informação foi publicado em ficheiros XML para [o Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)e [Azure Germany.](https://www.microsoft.com/download/details.aspx?id=54770) Estes downloads xml serão depreciados até 30 de junho de 2020 e deixarão de estar disponíveis após essa data. Deve migrar para utilizar os downloads de ficheiros Discovery API ou JSON, conforme descrito nas secções anteriores.

### <a name="tips"></a>Sugestões 
- Pode detetar atualizações de uma publicação para outra, observando valores de *número* de variação aumentados no ficheiro JSON. Cada subsecção (por exemplo, **Storage.WestUS)** tem a sua própria *mudançaNúmero* que é incrementado à medida que as alterações ocorrem. O nível superior da alteração do *ficheiroNumber* é incrementado quando qualquer uma das subsecções é alterada.
- Por exemplo, como analisar a informação da etiqueta de serviço (por exemplo, obter todas as gamas de endereços para Armazenamento em WestUS), consulte a documentação da Marca de [Serviço Discovery API PowerShell.](https://aka.ms/discoveryapi_powershell)

## <a name="next-steps"></a>Passos seguintes
- Aprenda a [criar um grupo](tutorial-filter-network-traffic.md)de segurança de rede .
