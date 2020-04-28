---
title: Segurança em camadas v1
description: Aprenda a implementar uma arquitetura de segurança em camadas no seu ambiente de Serviço de Aplicações. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: a8920e97d315dc7bfd0ba22386b8b637afb7c05e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74688788"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementação de uma arquitetura de segurança em camadas com ambientes de serviço de aplicações
Uma vez que o App Service Environments fornece um ambiente de tempo de execução isolado implantado numa rede virtual, os desenvolvedores podem criar uma arquitetura de segurança em camadas que fornece diferentes níveis de acesso à rede para cada nível de aplicação física.

Um desejo comum é esconder back-ends da API do acesso geral à Internet, e apenas permitir que as APIs sejam chamadas por aplicações web a montante.  Os grupos de segurança da [rede (NSGs)][NetworkSecurityGroups] podem ser utilizados em subredes que contenham Ambientes de Serviço sinuoso para restringir o acesso do público a aplicações API.

O diagrama abaixo mostra uma arquitetura de exemplo com uma aplicação baseada em WebAPI implantada num Ambiente de Serviço de Aplicações.  Três instâncias de aplicações web separadas, implementadas em três ambientes de serviço de aplicação separados, fazem chamadas de back-end para a mesma aplicação WebAPI.

![Arquitetura Conceptual][ConceptualArchitecture] 

Os sinais verdes plus indicam que o grupo de segurança da rede na subnet que contém "apiase" permite chamadas de entrada das aplicações web a montante, bem como chamadas de si mesma.  No entanto, o mesmo grupo de segurança da rede nega explicitamente o acesso ao tráfego de entrada geral a partir da Internet. 

O restante deste artigo percorre os passos necessários para configurar o grupo de segurança da rede na subnet contendo "apiase".

## <a name="determining-the-network-behavior"></a>Determinação do Comportamento da Rede
Para saber quais as regras de segurança da rede, é necessário determinar quais os clientes da rede que serão autorizados a chegar ao App Service Environment contendo a app API e quais os clientes que serão bloqueados.

Uma vez que os grupos de segurança da [rede (NSGs)][NetworkSecurityGroups] são aplicados a subredes, e os Ambientes de Serviço saem implantados em subredes, as regras contidas num NSG aplicam-se a **todas as** aplicações que executam um App Service Environment.  Utilizando a arquitetura da amostra para este artigo, uma vez aplicado um grupo de segurança de rede à subnet contendo "apiase", todas as aplicações que executam o "apiase" App Service Environment serão protegidas pelo mesmo conjunto de regras de segurança. 

* **Determine o endereço IP de saída dos chamadores** a montante:  Qual é o endereço IP ou endereços dos chamadores a montante?  Estes endereços terão de ser explicitamente autorizados a aceder ao NSG.  Uma vez que as chamadas entre os Ambientes de Serviço si são consideradas chamadas "Internet", o endereço IP de saída atribuído a cada um dos três ambientes de serviço de aplicações a montante precisa de ser autorizado a ter acesso no NSG para a subnet "apiase".   Para obter mais informações sobre a determinação do endereço IP de saída para aplicações em execução num Ambiente de Serviço de Aplicações, consulte o artigo De visão geral da [Arquitetura da Rede.][NetworkArchitecture]
* **A aplicação aPI back-end terá de se auto-denominar?**  Um ponto, por vezes negligenciado e subtil, é o cenário em que a aplicação back-end precisa de se autodenominar.  Se uma aplicação API de back-end em um App Service Environment precisa de se chamar, também é tratada como uma chamada de "Internet".  Na arquitetura da amostra, isto requer permitir o acesso a partir do endereço IP de saída do "apiase" App Service Environment também.

## <a name="setting-up-the-network-security-group"></a>Criação do Grupo de Segurança da Rede
Uma vez conhecido o conjunto de endereços IP de saída, o próximo passo é construir um grupo de segurança de rede.  Os grupos de segurança de rede podem ser criados tanto para redes virtuais baseadas em Resource Manager, como para redes virtuais clássicas.  Os exemplos abaixo mostram a criação e configuração de um NSG numa rede virtual clássica usando powershell.

Para a arquitetura da amostra, os ambientes estão localizados no Centro Sul dos EUA, pelo que um NSG vazio é criado nessa região:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Primeiro é adicionada uma regra de autorização explícita para a infraestrutura de gestão azure, como se nota no artigo sobre o [tráfego de entrada][InboundTraffic] para ambientes de serviço de aplicações.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Em seguida, são adicionadas duas regras para permitir chamadas HTTP e HTTPS do primeiro ambiente de serviço de aplicação a montante ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Enxagúe e repita para o segundo e terceiro ambientes de serviço de aplicações a montante ("fe2ase" e "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Por último, conceder acesso ao endereço IP de saída do Ambiente de Serviço de Aplicações da API para que possa voltar a ligar para si próprio.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Não são necessárias outras regras de segurança da rede, porque cada NSG tem um conjunto de regras padrão que bloqueiam o acesso à entrada a partir da Internet, por padrão.

A lista completa de regras do grupo de segurança da rede é mostrada abaixo.  Note como a última regra, que é realçada, bloqueia o acesso de entrada de todos os chamadores, além de chamadas que foram explicitamente concedidas acesso.

![Configuração NSG][NSGConfiguration] 

O passo final é aplicar o NSG na sub-rede que contém o "apiase" App Service Environment.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Com o NSG aplicado à subnet, apenas os três ambientes de serviço de aplicações a montante, e o Ambiente de Serviço de Aplicações contendo a API back-end, são autorizados a chamar para o ambiente "apiase".

## <a name="additional-links-and-information"></a>Links e Informações Adicionais
Informação sobre grupos de [segurança da rede.](../../virtual-network/security-overview.md)

Compreender [endereços IP de saída][NetworkArchitecture] e Ambientes de Serviço de Aplicações.

Portas de [rede utilizadas][InboundTraffic] por Ambientes de Serviço de Aplicações.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
