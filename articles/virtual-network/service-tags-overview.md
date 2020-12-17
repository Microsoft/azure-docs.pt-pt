---
title: Visão geral das etiquetas de serviço Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre etiquetas de serviço. As etiquetas de serviço ajudam a minimizar a complexidade da criação de regras de segurança.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: e357667e650e9feeb8dceeffe71c287cde9fe8a0
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631606"
---
# <a name="virtual-network-service-tags"></a>Tags de serviço de rede virtual
<a name="network-service-tags"></a>

Uma etiqueta de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gere os prefixos de endereços englobados pela etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede.

Pode utilizar tags de serviço para definir controlos de acesso à rede em [grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) de rede ou [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags). Utilize etiquetas de serviço no lugar de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da etiqueta de serviço, como **ApiManagement,** no campo de *origem* ou *destino* adequado de uma regra, pode permitir ou negar o tráfego para o serviço correspondente.

Pode utilizar tags de serviço para alcançar o isolamento da rede e proteger os seus recursos Azure da Internet geral, ao mesmo tempo que acede aos serviços Azure que têm pontos finais públicos. Crie regras de grupo de segurança de rede de entrada/saída para negar o tráfego de/para a **Internet** e permitir o tráfego de/para **a AzureCloud** ou outras tags de serviço disponíveis de [serviços específicos](#available-service-tags) da Azure.

![Isolamento de rede dos serviços da Azure utilizando etiquetas de serviço](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Etiquetas de serviço disponíveis
A tabela a seguir inclui todas as etiquetas de serviço disponíveis para utilização nas regras [do grupo de segurança da rede.](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)

As colunas indicam se a etiqueta:

- É adequado para regras que cobrem o tráfego de entrada ou saída.
- Apoia o âmbito [regional.](https://azure.microsoft.com/regions)
- É utilizável nas regras [do Azure Firewall.](https://docs.microsoft.com/azure/firewall/service-tags)

Por padrão, as etiquetas de serviço refletem as gamas para toda a nuvem. Algumas tags de serviço também permitem um maior controlo granular, limitando as gamas IP correspondentes a uma região especificada. Por exemplo, a etiqueta de serviço **Armazenamento** representa Azure Storage para toda a nuvem, mas **Storage.WestUS** reduz a gama para apenas o endereço IP de armazenamento que varia da região westus. O quadro seguinte indica se cada etiqueta de serviço suporta este âmbito regional.  

| Etiqueta | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| **Grupo de Ação** | Grupo de Ação. | Entrada | No | No |
| **ApiManagement** | Tráfego de gestão para implementações dedicadas à Azure API Management. <br/><br/>*Nota:* Esta etiqueta representa o ponto final do serviço Azure API Management para o plano de controlo por região. Isto permite que os clientes realizem operações de gestão nas APIs, Operações, Políticas, NomeadosValues configurados no serviço de Gestão da API.  | Entrada | Yes | Yes |
| **AplicaçõesInsightsAilabilidade** | Disponibilidade de Insights de Aplicação. | Entrada | No | No |
| **Apconfiguration** | Configuração de aplicativos. | Saída | No | No |
| **AppService**    | Serviço de Aplicações do Azure. Esta etiqueta é recomendada para regras de segurança de saída para aplicações web e aplicações function.  | Saída | Yes | Yes |
| **AppServiceManagement** | Tráfego de gestão para implementações dedicadas ao Ambiente de Serviço de Aplicações. | Ambos | No | Yes |
| **AzureActiveDirectory** | Azure Active Directory. | Saída | No | Yes |
| **AzureActiveDirectoryDomainServices** | Tráfego de gestão para implementações dedicadas aos Serviços de Domínio do Diretório Ativo Azure. | Ambos | No | Yes |
| **AzureAdvancedThreatProtecção** | Azure Advanced Threat Protection. | Saída | No | No |
| **Estrutura AzureArcInfra** | Servidores ativados pelo Azure Arc, Azure Arc ativavam o tráfego de Kubernetes e configuração de hóspedes.<br/><br/>*Nota:* Esta tag tem uma dependência das tags **AzureActiveDirectory**,**AzureTrafficManager** e **AzureResourceManager.** *Esta etiqueta não é atualmente configurável através do Portal Azure*.| Saída | No | Yes |
| **AzureBackup** |ReforçoS Azure.<br/><br/>*Nota:* Esta etiqueta tem uma dependência das etiquetas **Storage** e **AzureActiveDirectory.** | Saída | No | Yes |
| **AzureBotService** | Serviço Azure Bot. | Saída | No | No |
| **AzureCloud** | Todos os [endereços IP públicos do datacenter](https://www.microsoft.com/download/details.aspx?id=56519). | Saída | Yes | Yes |
| **AzureCognitiveSearch** | Pesquisa Cognitiva Azure. <br/><br/>Esta etiqueta ou os endereços IP abrangidos por esta etiqueta podem ser utilizados para conceder aos indexantes acesso seguro a fontes de dados. Consulte a documentação de ligação do [indexante](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) para obter mais detalhes. <br/><br/> *Nota*: O IP do serviço de pesquisa não está incluído na lista de gamas IP para esta etiqueta de serviço e **também precisa de ser adicionado** à firewall IP de fontes de dados. | Entrada | No | No |
| **AzureConnectors** | Conectores Azure Logic Apps para ligações sonda/back-end. | Entrada | Yes | Yes |
| **AzureContainerRegistry** | Registo de Contentores Azure. | Saída | Yes | Yes |
| **AzureCosmosDB** | Azure Cosmos DB. | Saída | Yes | Yes |
| **AzureDatabricks** | Azure Databricks. | Ambos | No | No |
| **AzureDataExplorerManagement** | Gestão de Exploradores de Dados Azure. | Entrada | No | No |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Saída | No | Yes |
| **AzureDevSpaces** | Espaços Azure Dev. | Saída | No | No |
| **AzureDevOps** | Azure Dev Ops.<br/><br/>*Nota: Esta etiqueta não é atualmente configurável através do Portal Azure*| Entrada | No | Yes |
| **AzureDigitalTwins** | Gémeos Digitais Azure.<br/><br/>*Nota:* Esta etiqueta ou os endereços IP abrangidos por esta etiqueta podem ser utilizados para restringir o acesso aos pontos finais configurados para as rotas do evento. *Esta etiqueta não é atualmente configurável através do Portal Azure* | Entrada | No | Yes |
| **AzureEventGrid** | Grelha de Eventos Azure. | Ambos | No | No |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Porta da Frente Azul. | Ambos | No | No |
| **AzureInformationProtection** | Proteção de Informação Azure.<br/><br/>*Nota:* Esta tag tem uma dependência das tags **AzureActiveDirectory**, **AzureFrontDoor.Frontend** e **AzureFrontDoor.FirstParty.** | Saída | No | No |
| **AzureIoTHub** | Hub IoT do Azure. | Saída | No | No |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Nota:* Esta etiqueta tem uma dependência da etiqueta **AzureActiveDirectory.** | Saída | Yes | Yes |
| **AzureLoadBalancer** | O equilibrador de carga de infraestrutura Azure. A etiqueta traduz-se para o [endereço IP virtual do anfitrião](security-overview.md#azure-platform-considerations) (168.63.129.16) de onde provêm as sondas de saúde Azure. Isto inclui apenas o tráfego de sondas, não o tráfego real para o seu recurso backend. Se não estiver a utilizar o Balançador de Carga Azure, pode anular esta regra. | Ambos | No | No |
| **AzureMachineLearning** | Aprendizagem automática Azure. | Ambos | No | Yes |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon e métricas personalizadas (pontos finais GiG).<br/><br/>*Nota:* Para o Log Analytics, esta etiqueta tem uma dependência da etiqueta **de armazenamento.** | Saída | No | Yes |
| **AzureOpenDatasets** | Conjuntos de dados Azure Open.<br/><br/>*Nota:* Esta etiqueta tem uma dependência da etiqueta **AzureFrontDoor.Frontend** e **Storage** tag. | Saída | No | No |
| **AzurePlatformDNS** | O serviço DNS de infraestrutura básica (padrão).<br/><br>Pode utilizar esta etiqueta para desativar o DNS predefinido. Tenha cuidado quando usar esta etiqueta. Recomendamos que leia considerações da [plataforma Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Recomendamos também que efetue testes antes de utilizar esta etiqueta. | Saída | No | No |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), que é um serviço básico de infraestrutura.<br/><br/>Pode utilizar esta etiqueta para desativar o IMDS predefinido. Tenha cuidado quando usar esta etiqueta. Recomendamos que leia considerações da [plataforma Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Recomendamos também que efetue testes antes de utilizar esta etiqueta. | Saída | No | No |
| **AzurePlatformLKM** | Serviço de licenciamento do Windows ou serviço de gestão de chaves.<br/><br/>Pode utilizar esta etiqueta para desativar os predefinidos para o licenciamento. Tenha cuidado quando usar esta etiqueta. Recomendamos que leia considerações da [plataforma Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)  Recomendamos também que efetue testes antes de utilizar esta etiqueta. | Saída | No | No |
| **AzureResourceManager** | Azure Resource Manager. | Saída | No | No |
| **Azuresignalr** | Sinaleiro Azure. | Saída | No | No |
| **AzureSiteRecovery** | Recuperação do local de Azure.<br/><br/>*Nota:* Esta etiqueta tem uma dependência das **etiquetas AzureActiveDirectory**, **AzureKeyVault**, **EventHub,****GuestAndHybridManagement** e **Storage.** | Saída | No | No |
| **AzureTrafficManager** | Azure Traffic Manager sonda endereços IP.<br/><br/>Para obter mais informações sobre endereços IP da sonda Traffic Manager, consulte [o Azure Traffic Manager FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Entrada | No | Yes |  
| **BatchNodeManagement** | Tráfego de gestão para implantações dedicadas ao Azure Batch. | Ambos | No | Yes |
| **CognitiveServicesManagement** | Os intervalos de endereços para o tráfego dos Serviços Cognitivos Azure. | Ambos | No | No |
| **DataFactory**  | Azure Data Factory | Ambos | No | No |
| **DataFactoryManagement** | Tráfego de gestão para Azure Data Factory. | Saída | No | No |
| **Dynamics365ForMarketingEmail** | As gamas de endereços para o serviço de e-mail de marketing da Dynamics 365. | Saída | Yes | No |
| **EventHub** | Azure Event Hubs. | Saída | Yes | Yes |
| **GatewayManager** | Tráfego de gestão para implementações dedicadas à Azure VPN Gateway e Gateway de aplicação. | Entrada | No | No |
| **GuestAndHybridManagement** | Azure Automation e Configuração de Hóspedes. | Saída | No | Yes |
| **HDInsight** | Azure HDInsight. | Entrada | Yes | No |
| **Internet** | O espaço de endereço IP que está fora da rede virtual e acessível pela internet pública.<br/><br/>O intervalo de endereços inclui o [espaço de endereço IP público propriedade do Azure.](https://www.microsoft.com/download/details.aspx?id=41653) | Ambos | No | No |
| **LogicApps** | Aplicativos lógicos. | Ambos | No | No |
| **LogicAppsManagement** | Tráfego de gestão para Apps Lógicas. | Entrada | No | No |
| **MicrosoftCloudAppSecurity** | Segurança da aplicação Microsoft Cloud. | Saída | No | No |
| **MicrosoftContainerRegistry** | Registo de contentores para imagens de contentores da Microsoft. <br/><br/>*Nota:* Esta etiqueta tem uma dependência da tag **AzureFrontDoor.FirstParty.** | Saída | Yes | Yes |
| **PowerQueryOnline** | Consulta de energia online. | Ambos | No | No |
| **ServiceBus** | Tráfego de autocarros Azure Service que utiliza o nível de serviço Premium. | Saída | Yes | Yes |
| **ServiceFabric** | Tecido de Serviço Azure.<br/><br/>*Nota:* Esta etiqueta representa o ponto final de serviço do Service Fabric para o plano de controlo por região. Isto permite que os clientes realizem operações de gestão para os seus clusters de Tecido de Serviço a partir do seu VNET (ponto final, por exemplo. https:// westus.servicefabric.azure.com) | Ambos | No | No |
| **Sql** | Base de Dados Azure SQL, Base de Dados Azure para MySQL, Base de Dados Azure para PostgreSQL e Azure Synapse Analytics.<br/><br/>*Nota:* Esta etiqueta representa o serviço, mas não instâncias específicas do serviço. Por exemplo, representa o serviço da Base de Dados SQL do Azure, mas não uma base de dados ou um servidor SQL. Esta etiqueta não se aplica a caso gerido pela SQL. | Saída | Yes | Yes |
| **SqlManagement** | Tráfego de gestão para implantações dedicadas ao SQL. | Ambos | No | Yes |
| **Armazenamento** | Armazenamento do Azure. <br/><br/>*Nota:* Esta etiqueta representa o serviço, mas não instâncias específicas do serviço. Por exemplo, representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica. | Saída | Yes | Yes |
| **ArmazenamentoSyncService** | Serviço de Sincronização de Armazenamento. | Ambos | No | No |
| **WindowsVirtualDesktop** | Windows Virtual Desktop. | Ambos | No | Yes |
| **VirtualNetwork** | O espaço de endereço de rede virtual (todos os intervalos de endereços IP definidos para a rede virtual), todos os espaços de endereço ligados no local, redes [virtuais,](virtual-network-peering-overview.md) redes virtuais ligadas a um [gateway de rede virtual,](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)o [endereço IP virtual do anfitrião,](security-overview.md#azure-platform-considerations)e prefixos de endereços utilizados nas [rotas definidas pelo utilizador.](virtual-networks-udr-overview.md) Esta etiqueta também pode conter rotas predefinidos. | Ambos | No | No |

>[!NOTE]
>No modelo de implementação clássico (antes do Azure Resource Manager), é suportado um subconjunto das tags listadas na tabela anterior. Estas etiquetas são escritas de forma diferente:
>
>| Ortografia clássica | Etiqueta de gestor de recursos equivalente |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> As etiquetas de serviço dos serviços Azure denotam os prefixos do endereço da nuvem específica que está a ser utilizada. Por exemplo, as gamas IP subjacentes que correspondem ao valor da etiqueta **Sql** na nuvem pública Azure serão diferentes das gamas subjacentes na nuvem Azure China.

> [!NOTE]
> Se implementar um [ponto final de serviço de rede virtual](virtual-network-service-endpoints-overview.md) relativo a um serviço, como o Armazenamento do Azure ou a Base de Dados SQL do Azure, o Azure adiciona uma [rota](virtual-networks-udr-overview.md#optional-default-routes) para uma sub-rede da rede virtual do serviço. Os prefixos de endereço na rota são os mesmos prefixos de endereço, ou gamas CIDR, que as da etiqueta de serviço correspondente.

## <a name="service-tags-on-premises"></a>Etiquetas de serviço no local  
Pode obter a etiqueta de serviço atual e as informações de alcance para incluir como parte das configurações de firewall no local. Esta informação é a lista atual de pontos a tempo das gamas IP que correspondem a cada tag de serviço. Pode obter a informação programáticamente ou através de um download de ficheiroS JSON, conforme descrito nas seguintes secções.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Utilize a API de Descoberta de Marca de Serviço (pré-visualização pública)
Pode recuperar programáticamente a lista atual de tags de serviço juntamente com detalhes do intervalo de endereços IP:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Enquanto está em pré-visualização pública, a API discovery pode devolver informações menos atuais do que a informação devolvida pelos downloads JSON. (Ver a secção seguinte.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Descubra as tags de serviço utilizando ficheiros JSON transferíveis 
Pode descarregar ficheiros JSON que contenham a lista atual de tags de serviço juntamente com detalhes do intervalo de endereços IP. Estas listas são atualizadas e publicadas semanalmente. As localizações para cada nuvem são:

- [Público de Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)   

Os intervalos de endereço IP nestes ficheiros estão na notação CIDR. 

> [!NOTE]
>Um subconjunto desta informação foi publicado em ficheiros XML para [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)e [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770). Estes downloads XML serão depreciados até 30 de junho de 2020 e deixarão de estar disponíveis após essa data. Deverá migrar para utilizar os downloads de ficheiros Discovery API ou JSON, conforme descrito nas secções anteriores.

### <a name="tips"></a>Sugestões 
- Pode detetar atualizações de uma publicação para outra, observando valores de *alteração* aumentados no ficheiro JSON. Cada subsecção (por exemplo, **Storage.WestUS)** tem a sua própria *alteração Número de números* que é incrementada à medida que as mudanças ocorrem. O nível superior da alteração do ficheiro *Número de alterações* é incrementado quando qualquer uma das subsecções é alterada.
- Por exemplo, como analisar as informações da etiqueta de serviço (por exemplo, obter todas as gamas de endereços para armazenamento em WestUS), consulte a documentação da [API PowerShell](https://aka.ms/discoveryapi_powershell) da Marca de Serviço.

## <a name="next-steps"></a>Passos seguintes
- Saiba como [criar um grupo de segurança de rede.](tutorial-filter-network-traffic.md)
