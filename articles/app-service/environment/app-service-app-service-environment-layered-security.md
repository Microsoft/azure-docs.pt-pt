---
title: Em camadas a arquitetura de segurança com ambientes de serviço de aplicações - Azure
description: Implementar uma arquitetura de segurança em camadas com ambientes do serviço de aplicações.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 5e25de1ad2042ac978c3698165b9d9baba20e816
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130692"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementar uma arquitetura de segurança em camadas com ambientes de serviço de aplicações
## <a name="overview"></a>Descrição geral
Uma vez que os ambientes do serviço de aplicações proporcionam um ambiente de tempo de execução isolado implementado numa rede virtual, os desenvolvedores podem criar uma arquitetura de segurança em camadas fornecer diferentes níveis de acesso de rede para cada camada da aplicação física.

É um desejo comum ocultar o back-ends de API de acesso geral de Internet e permitir apenas APIs deve ser chamado por aplicações web a montante.  [Grupos de segurança (NSGs) de rede] [ NetworkSecurityGroups] pode ser utilizado em sub-redes que contêm os ambientes de serviço de aplicações para restringir o acesso público a aplicações de API.

O diagrama abaixo mostra uma arquitetura de exemplo com uma aplicação de WebAPI com base implementada num ambiente de serviço de aplicações.  Três instâncias de aplicações web separado, implementadas em três diferentes ambientes do App Service, fazem chamadas de back-end para a mesma aplicação de WebAPI.

![Arquitetura conceptual][ConceptualArchitecture] 

Os sinais de mais verde indica que o grupo de segurança de rede na sub-rede que contém "apiase" permite chamadas de entrada a partir de aplicações web a montante, como chamadas bem ze sebe sama.  No entanto o mesmo grupo de segurança de rede explicitamente nega o acesso ao tráfego de entrada geral a partir da Internet. 

O restante deste artigo descreve os passos necessários para configurar o grupo de segurança de rede na sub-rede que contém "apiase."

## <a name="determining-the-network-behavior"></a>Determinar o comportamento de rede
Para saber quais regras de segurança de rede são necessários, terá de determinar quais os clientes de rede terá permissão para atingir o ambiente de serviço de aplicações que contém a aplicação API e os clientes que serão bloqueados.

Uma vez que [(NSGs) de grupos de segurança de rede] [ NetworkSecurityGroups] são aplicados a sub-redes e ambientes de serviço de aplicações são implementadas em sub-redes, as regras contidas num NSG aplicam-se a **todos os** aplicações em execução num ambiente de serviço de aplicações.  Usando a arquitetura de exemplo neste artigo, uma vez aplicado um grupo de segurança de rede para a sub-rede que contém "apiase", todas as aplicações em execução no "apiase" ambiente de serviço de aplicações serão protegidas através do mesmo conjunto de regras de segurança. 

* **Determine o endereço IP de saída de chamadas, a montante:**  O que é o endereço IP ou os endereços dos chamadores a montante?  Estes endereços tem de ser explicitamente permissão de acesso no NSG.  Uma vez que as chamadas entre ambientes de serviço de aplicações são consideradas chamadas de "Internet", o endereço IP de saída atribuídos a cada um dos três ambientes de serviço de aplicações a montante necessita de permissão de acesso no NSG para a sub-rede "apiase".   Para obter mais informações sobre como determinar o endereço IP de saída para as aplicações em execução num ambiente de serviço de aplicações, consulte a [arquitetura de rede] [ NetworkArchitecture] artigo de descrição geral.
* **A aplicação de API de back-end precisará chamar em si?**  Um ponto sutil e esquecido, às vezes, é o cenário em que a aplicação de back-end precisa chamar em si.  Se precisar de uma aplicação de API de back-end num ambiente de serviço de aplicações para a chamada a mesma, também é tratada como uma chamada de "Internet".  A arquitetura de exemplo, esta ação requer acesso a partir do endereço IP de saída do "apiase" ambiente de serviço de aplicações também.

## <a name="setting-up-the-network-security-group"></a>Como configurar o grupo de segurança de rede
Depois do conjunto de endereços IP de saída são conhecidas, a próxima etapa é construir um grupo de segurança de rede.  Grupos de segurança de rede podem ser criados ambas com Resource Manager com base em redes virtuais, bem como as redes virtuais clássicas.  Os exemplos abaixo mostram a criação e configuração de um NSG numa rede virtual clássica com o Powershell.

Para a arquitetura de exemplo, os ambientes estão localizados no Sul E.u.a., para que um NSG vazio é criado nessa região:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Primeiro explícita permitir é adicionada uma regra para a infraestrutura de gestão do Azure como indicados no artigo [tráfego de entrada] [ InboundTraffic] para ambientes de serviço de aplicações.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Em seguida, duas regras são adicionadas para permitir chamadas HTTP e HTTPS a partir do primeiro a montante App Service Environment ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Enxágüe e repita para a segunda e terceira a montante ambientes do App Service ("fe2ase" e "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Por último, conceda acesso para o endereço IP de saída do ambiente de serviço de aplicações a API de back-end para que possa chamar de volta no próprio.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Não existem outras regras de segurança de rede são necessárias, porque cada NSG tem um conjunto de regras predefinidas que bloquear o acesso de entrada da Internet, por predefinição.

A lista completa das regras no grupo de segurança de rede são mostradas abaixo.  Observe como a última regra, o que está realçada, blocos de entrada acesso a partir de todos os chamadores, que não sejam os autores de chamadas que tenham sido explicitamente concedidos acesso.

![Configuração de NSG][NSGConfiguration] 

A etapa final é aplicar o NSG à sub-rede que contém "apiase" ambiente de serviço de aplicações.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Com o NSG aplicado à sub-rede, apenas as três a montante ambientes do App Service e o ambiente de serviço de aplicações que contém o back-end de API, são permitidas para chamar para o ambiente de "apiase".

## <a name="additional-links-and-information"></a>Links adicionais e informações
Informações sobre [grupos de segurança de rede](../../virtual-network/security-overview.md).

Noções básicas sobre [endereços IP de saída] [ NetworkArchitecture] e ambientes de serviço de aplicações.

[Portas de rede] [ InboundTraffic] utilizado por ambientes do serviço de aplicações.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
