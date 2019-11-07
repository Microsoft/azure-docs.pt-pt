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
ms.openlocfilehash: 0eb15d1b9b56522b9caa1bb10890eb2b485714e8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587527"
---
# <a name="virtual-network-service-tags"></a>Marcas de serviço de rede virtual 
<a name="network-service-tags"></a>

Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. Ele ajuda a minimizar a complexidade de atualizações frequentes em regras de segurança de rede. Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) ou no [Firewall do Azure](https://docs.microsoft.com/azure/firewall/service-tags). Ao criar regras de segurança, pode utilizar etiquetas de serviço em vez de endereços IP específicos. Ao especificar o nome da marca de serviço (por exemplo, **ApiManagement**) no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente. A Microsoft gerencia os prefixos de endereço abordados pela marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados. 

## <a name="available-service-tags"></a>Marcas de serviço disponíveis
A tabela a seguir inclui todas as marcas de serviço disponíveis para serem usadas em regras de [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

As colunas indicam se a marca é:

- Adequado para regras que abrangem o tráfego de entrada ou de saída
- Suporte ao escopo [regional](https://azure.microsoft.com/regions) 
- Utilizável em regras de [Firewall do Azure](https://docs.microsoft.com/azure/firewall/service-tags)

Por padrão, as marcas de serviço refletem os intervalos para toda a nuvem.  Algumas marcas de serviço também permitem um controle mais refinado restringindo os intervalos de IP correspondentes a uma região especificada.  Por exemplo, embora o **armazenamento** de marca de serviço represente o armazenamento do Azure para toda a nuvem, o **Storage. westus** limita isso apenas aos intervalos de endereços IP de armazenamento da região westus.  As descrições de cada marca de serviço abaixo indicam se oferecem suporte a tal escopo regional.  



| Etiqueta | Objetivo | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com o Firewall do Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Tráfego de gerenciamento para implantações dedicadas do APIM. | Ambos | Não | Sim |
| **AppService**    | Serviço do serviço de aplicativo. Essa marca é recomendada para regras de segurança de saída para front-ends WebApp. | Saída | Sim | Sim |
| **AppServiceManagement** | O tráfego de gerenciamento para implantações Ambiente do Serviço de Aplicativo dedicadas. | Ambos | Não | Sim |
| **AzureActiveDirectory** | Serviço de Azure Active Directory. | Saída | Não | Sim |
| **AzureActiveDirectoryDomainServices** | O tráfego de gerenciamento para implantações Azure Active Directory Domain Services dedicadas. | Ambos | Não | Sim |
| **AzureBackup** |Serviço de backup do Azure.<br/><br/>*Observação:* Essa marca tem uma dependência nas marcas de **armazenamento** e **AzureActiveDirectory** . | Saída | Não | Sim |
| **AzureCloud** | Todos os [endereços IP públicos do datacenter](https://www.microsoft.com/download/details.aspx?id=41653). | Saída | Sim | Sim |
| **AzureConnectors** | Conectores de aplicativos lógicos para conexões de teste/back-end. | Entrada | Sim | Sim |
| **AzureContainerRegistry** | Serviço de registro de contêiner do Azure. | Saída | Sim | Sim |
| **AzureCosmosDB** | Serviço de banco de dados Cosmos do Azure. | Saída | Sim | Sim |
| **AzureDataLake** | Serviço de Azure Data Lake. | Saída | Não | Sim |
| **AzureKeyVault** | Serviço de keyvault do Azure.<br/><br/>*Observação:* Essa marca tem uma dependência na marca **AzureActiveDirectory** . | Saída | Sim | Sim |
| **AzureLoadBalancer** | Balanceador de carga de infraestrutura do Azure. A etiqueta traduz o [Endereço IP virtual do anfitrião](security-overview.md#azure-platform-considerations) (168.63.129.16), onde as sondas de estado de funcionamento do Azure têm origem. Se não estiver a utilizar o balanceador de carga do Azure, pode substituir esta regra. | Ambos | Não | Não |
| **AzureMachineLearning** | Serviço de Azure Machine Learning. | Saída | Não | Sim |
| **AzureMonitor** | Log Analytics, o app insights, o AzMon e as métricas personalizadas (pontos de extremidade GiG).<br/><br/>*Observação:* Por Log Analytics, essa marca tem dependência na marca de **armazenamento** . | Saída | Não | Sim |
| **AzurePlatformDNS** | O serviço DNS de infraestrutura básica (padrão).<br/><br>Você pode usar essa marca para desabilitar o DNS padrão. Tenha cuidado ao usar essa marca. É recomendável ler [Considerações sobre a plataforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . O teste é recomendado antes de usar essa marca. | Saída | Não | Não |
| **AzurePlatformIMDS** | IMDS, que é um serviço de infraestrutura básico.<br/><br/>Você pode usar essa marca para desabilitar o IMDS padrão.  Tenha cuidado ao usar essa marca. É recomendável ler [Considerações sobre a plataforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . O teste é recomendado antes de usar essa marca. | Saída | Não | Não |
| **AzurePlatformLKM** | Licenciamento do Windows ou serviço de gerenciamento de chaves.<br/><br/>Você pode usar essa marca para desabilitar os padrões de licenciamento. Tenha cuidado ao usar essa marca.  É recomendável ler [Considerações sobre a plataforma Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . O teste é recomendado antes de usar essa marca. | Saída | Não | Não |
| **AzureTrafficManaged** | Endereços IP de investigação do Gerenciador de tráfego do Azure.<br/><br/>Pode encontrar mais informações sobre os endereços IP da pesquisa do Gestor de Tráfego nas [FAQ do Gestor de Tráfego do Microsoft Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Entrada | Não | Sim |  
| **BatchNodeManagement** | Tráfego de gerenciamento para implantações dedicadas do lote do Azure. | Ambos | Não | Sim |
| **CognitiveServicesManagement** | Os intervalos de endereços para o tráfego para serviços cognitivas | Saída | Não | Não |
| **Dynamics365ForMarketingEmail** | Os intervalos de endereços para o serviço de email de marketing do Dynamics 365. | Saída | Sim | Não |
| **EventHub** | Serviço do Azure EventHub. | Saída | Sim | Sim |
| **Gatewaymanager** | O tráfego de gerenciamento para implantações dedicadas de gateways de VPN/aplicativo. | Entrada | Não | Não |
| **IP** | O espaço de endereço IP que está fora da rede virtual e acessível pela Internet pública.<br/><br/>O intervalo de endereços inclui o [espaço de endereço IP público pertencente ao Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Ambos | Não | Não |
| **MicrosoftContainerRegistry** | Serviço de registro de contêiner da Microsoft. | Saída | Sim | Sim |
| **ServiceBus** | Serviço do barramento de serviço do Azure usando a camada de serviço Premium. | Saída | Sim | Sim |
| **ServiceFabric** | Serviço de Service Fabric. | Saída | Não | Não |
| **SQL** | Banco de dados SQL do Azure, banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL e serviços de SQL Data Warehouse do Azure.<br/><br/>*Observação:* Essa marca representa o serviço, mas não instâncias específicas do serviço. Por exemplo, representa o serviço da Base de Dados SQL do Azure, mas não uma base de dados ou um servidor SQL. | Saída | Sim | Sim |
| **SqlManagement** | Tráfego de gerenciamento para implantações do SQL dedicado. | Ambos | Não | Sim |
| **Armazenamento** | Serviço de armazenamento do Azure. <br/><br/>*Observação:* A marca representa o serviço, mas não as instâncias específicas do serviço. Por exemplo, representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica. | Saída | Sim | Sim |
| **VirtualNetwork** | O espaço de endereço de rede virtual (todos os intervalos de endereços IP definidos para a rede virtual), todos os espaços de endereço locais conectados, redes virtuais [emparelhadas](virtual-network-peering-overview.md) ou rede virtual conectada a um [Gateway de rede virtual](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), o [IP virtual endereço dos](security-overview.md#azure-platform-considerations) prefixos de endereço e host usados em [rotas definidas pelo usuário](virtual-networks-udr-overview.md). Lembre-se de que essa marca também pode conter rotas padrão. | Ambos | Não | Não |

>[!NOTE]
>Ao trabalhar no ambiente *clássico* (pré Azure Resource Manager), há suporte para um conjunto selecionado das marcas acima.  Eles usam uma grafia alternativa:

| Ortografia clássica | Marca do Gerenciador de recursos equivalente |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> As marcas de serviço dos serviços do Azure denotam os prefixos de endereço da nuvem específica que está sendo usada. por exemplo, os intervalos de IP subjacentes correspondentes ao valor da marca **SQL** serão diferentes entre a nuvem pública do Azure e a nuvem do Azure China.

> [!NOTE]
> Se implementar um [ponto final de serviço de rede virtual](virtual-network-service-endpoints-overview.md) relativo a um serviço, como o Armazenamento do Azure ou a Base de Dados SQL do Azure, o Azure adiciona uma [rota](virtual-networks-udr-overview.md#optional-default-routes) para uma sub-rede da rede virtual do serviço. Os prefixos de endereço da rota são os mesmos prefixos de endereço, ou intervalos CIDR, da etiqueta de serviço correspondente.



## <a name="service-tags-in-on-premises"></a>Marcas de serviço no local  
Você pode obter a marca de serviço e as informações de intervalo atuais para incluir como parte das configurações de firewall local.  Essa informação é a lista pontual atual dos intervalos de IP correspondentes a cada marca de serviço.  As informações podem ser obtidas programaticamente ou por meio do download do arquivo JSON da seguinte maneira.

### <a name="service-tag-discovery-api-public-preview"></a>API de descoberta de marca de serviço (visualização pública)
Você pode recuperar programaticamente a lista atual de marcas de serviço com detalhes do intervalo de endereços IP:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Enquanto estiver em visualização pública, a API de descoberta poderá retornar informações que não sejam as atuais como downloads de JSON (abaixo).


### <a name="discover-service-tags-using-downloadable-json-files"></a>Descobrir marcas de serviço usando arquivos JSON para download 
Você pode baixar arquivos JSON que contenham a lista atual de marcas de serviço com detalhes do intervalo de endereços IP. Elas são atualizadas e publicadas semanalmente.  Os locais para cada nuvem são:

- [Público do Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Um subconjunto dessas informações foi publicado anteriormente em arquivos XML para o [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064) e [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=54770). Esses downloads XML serão preteridos até 30 de junho de 2020 e não estarão mais disponíveis após essa data. Migre para o usando a API de descoberta ou o arquivo JSON baixado conforme descrito acima.

### <a name="tips"></a>Sugestões 
- Você pode detectar atualizações de uma publicação para a próxima, por meio de valores de *changeNumber* maiores dentro do arquivo JSON. Cada subseção (por exemplo, **Storage. westus**) tem seu próprio *changeNumber* que é incrementado conforme ocorrem alterações.  O nível superior do *changeNumber* do arquivo é incrementado quando qualquer uma das subseções é alterada.
- Para obter exemplos de como analisar as informações de marca de serviço (por exemplo, obter todos os intervalos de endereços para armazenamento na Westus), consulte a documentação do [PowerShell da API de descoberta de marca de serviço](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Passos seguintes
- Leia o artigo [Create a network security group](tutorial-filter-network-traffic.md) (Criar um grupo de segurança de rede).

