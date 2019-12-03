---
title: Segurança em camadas v1
description: Saiba como implementar uma arquitetura de segurança em camadas em seu ambiente do serviço de aplicativo. Este documento é fornecido somente para clientes que usam o ASE v1 herdado.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: a8920e97d315dc7bfd0ba22386b8b637afb7c05e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688788"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementando uma arquitetura de segurança em camadas com ambientes de serviço de aplicativo
Como os ambientes do serviço de aplicativo fornecem um ambiente de tempo de execução isolado implantado em uma rede virtual, os desenvolvedores podem criar uma arquitetura de segurança em camadas, fornecendo diferentes níveis de acesso à rede para cada camada de aplicativo físico.

Um desejo comum é ocultar os back-ends de API do acesso geral à Internet e permitir que as APIs sejam chamadas por aplicativos Web upstream.  Os [NSGs (grupos de segurança de rede)][NetworkSecurityGroups] podem ser usados em sub-redes que contêm ambientes de serviço de aplicativo para restringir o acesso público a aplicativos de API.

O diagrama a seguir mostra uma arquitetura de exemplo com um aplicativo baseado em WebAPI implantado em um Ambiente do Serviço de Aplicativo.  Três instâncias de aplicativo Web separadas, implantadas em três ambientes de serviço de aplicativo separados, fazem chamadas de back-end para o mesmo aplicativo WebAPI.

![Arquitetura conceitual][ConceptualArchitecture] 

Os sinais de adição verdes indicam que o grupo de segurança de rede na sub-rede que contém "apiase" permite chamadas de entrada dos aplicativos Web upstream, bem como chamadas de si mesmo.  No entanto, o mesmo grupo de segurança de rede nega explicitamente o acesso ao tráfego de entrada geral da Internet. 

O restante deste artigo percorre as etapas necessárias para configurar o grupo de segurança de rede na sub-rede que contém "apiase".

## <a name="determining-the-network-behavior"></a>Determinando o comportamento da rede
Para saber quais regras de segurança de rede são necessárias, você precisa determinar quais clientes de rede terão permissão para acessar o Ambiente do Serviço de Aplicativo que contém o aplicativo de API e quais clientes serão bloqueados.

Como os [NSGs (grupos de segurança de rede)][NetworkSecurityGroups] são aplicados a sub-redes e os ambientes de serviço de aplicativo são implantados em sub-redes, as regras contidas em um NSG se aplicam a **todos os** aplicativos em execução em um ambiente do serviço de aplicativo.  Usando a arquitetura de exemplo deste artigo, depois que um grupo de segurança de rede é aplicado à sub-rede que contém "apiase", todos os aplicativos em execução no Ambiente do Serviço de Aplicativo "apiase" serão protegidos pelo mesmo conjunto de regras de segurança. 

* **Determine o endereço IP de saída dos chamadores upstream:**  Qual é o endereço IP ou endereços dos chamadores upstream?  Esses endereços precisarão ter o acesso explicitamente permitido no NSG.  Como as chamadas entre os ambientes do serviço de aplicativo são consideradas chamadas de "Internet", o endereço IP de saída atribuído a cada um dos três ambientes do serviço de aplicativo upstream precisa ter acesso permitido no NSG para a sub-rede "apiase".   Para obter mais informações sobre como determinar o endereço IP de saída para aplicativos em execução em um Ambiente do Serviço de Aplicativo, consulte o artigo Visão geral da [arquitetura de rede][NetworkArchitecture] .
* **O aplicativo de API de back-end precisará chamar a si mesmo?**  Um ponto sutil, às vezes ignorado, é o cenário em que o aplicativo de back-end precisa chamar a si mesmo.  Se um aplicativo de API de back-end em um Ambiente do Serviço de Aplicativo precisar chamar a si mesmo, ele também será tratado como uma chamada de "Internet".  Na arquitetura de exemplo, isso requer a permissão de acesso do endereço IP de saída do Ambiente do Serviço de Aplicativo "apiase" também.

## <a name="setting-up-the-network-security-group"></a>Configurando o grupo de segurança de rede
Depois que o conjunto de endereços IP de saída for conhecido, a próxima etapa será construir um grupo de segurança de rede.  Os grupos de segurança de rede podem ser criados para as redes virtuais baseadas no Resource Manager, bem como para as redes virtuais clássicas.  Os exemplos a seguir mostram como criar e configurar um NSG em uma rede virtual clássica usando o PowerShell.

Para a arquitetura de exemplo, os ambientes estão localizados no EUA Central do Sul, portanto, um NSG vazio é criado nessa região:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Primeiro, uma regra de permissão explícita é adicionada à infraestrutura de gerenciamento do Azure, conforme observado no artigo sobre o [tráfego de entrada][InboundTraffic] para ambientes do serviço de aplicativo.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Em seguida, duas regras são adicionadas para permitir chamadas HTTP e HTTPS da primeira Ambiente do Serviço de Aplicativo upstream ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Aplique e repita para o segundo e terceiro ambientes do serviço de aplicativo upstream ("fe2ase" e "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Por fim, conceda acesso ao endereço IP de saída das Ambiente do Serviço de Aplicativo da API de back-end para que ela possa chamar de volta.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Nenhuma outra regra de segurança de rede é necessária, pois cada NSG tem um conjunto de regras padrão que bloqueiam o acesso de entrada da Internet, por padrão.

A lista completa de regras no grupo de segurança de rede é mostrada abaixo.  Observe como a última regra, que é realçada, bloqueia o acesso de entrada de todos os chamadores, além de chamadores que receberam acesso explicitamente.

![Configuração do NSG][NSGConfiguration] 

A etapa final é aplicar o NSG à sub-rede que contém a Ambiente do Serviço de Aplicativo "apiase".

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Com o NSG aplicado à sub-rede, apenas os três ambientes do serviço de aplicativo upstream e a Ambiente do Serviço de Aplicativo que contém o back-end da API, têm permissão para chamar o ambiente "apiase".

## <a name="additional-links-and-information"></a>Links e informações adicionais
Informações sobre [grupos de segurança de rede](../../virtual-network/security-overview.md).

Noções básicas sobre [endereços IP de saída][NetworkArchitecture] e ambientes de serviço de aplicativo.

[Portas de rede][InboundTraffic] usadas pelos ambientes do serviço de aplicativo.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
