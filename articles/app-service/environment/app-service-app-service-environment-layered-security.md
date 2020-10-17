---
title: Segurança em camadas v1
description: Saiba como implementar uma arquitetura de segurança em camadas no seu ambiente de Serviço de Aplicações. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: ab26ad08947a4a9929ae0d41b669160b3d159224
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150229"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementação de uma arquitetura de segurança em camadas com ambientes de serviço de aplicações
Uma vez que os Ambientes de Serviço de Aplicação fornecem um ambiente de tempo de execução isolado implantado numa rede virtual, os desenvolvedores podem criar uma arquitetura de segurança em camadas que fornece diferentes níveis de acesso à rede para cada nível de aplicação física.

Um desejo comum é esconder as extremidades traseiras da API do acesso geral à Internet, e apenas permitir que as APIs sejam chamadas por aplicações web a montante.  [Os grupos de segurança de rede (NSGs)][NetworkSecurityGroups] podem ser utilizados em sub-redes que contenham Ambientes de Serviço de Aplicações para restringir o acesso do público a aplicações API.

O diagrama abaixo mostra uma arquitetura de exemplo com uma aplicação baseada no WebAPI implantada num Ambiente de Serviço de Aplicações.  Três instâncias de aplicações web separadas, implementadas em três Ambientes de Serviço de Aplicações separadas, fazem chamadas de back-end para a mesma aplicação WebAPI.

![Arquitetura Conceptual][ConceptualArchitecture] 

Os sinais verdes mais indicam que o grupo de segurança da rede na sub-rede que contém "apiase" permite chamadas de entrada a partir das aplicações web a montante, bem como chamadas de si mesma.  No entanto, o mesmo grupo de segurança da rede nega explicitamente o acesso ao tráfego de entrada geral a partir da Internet. 

O restante deste artigo percorre as etapas necessárias para configurar o grupo de segurança da rede na sub-rede contendo "apiase".

## <a name="determining-the-network-behavior"></a>Determinação do Comportamento da Rede
Para saber quais são as regras de segurança da rede, é necessário determinar quais os clientes da rede que poderão chegar ao Ambiente de Serviço de Aplicações que contém a app API e quais os clientes que serão bloqueados.

Uma vez que [os grupos de segurança de rede (NSGs)][NetworkSecurityGroups] são aplicados a sub-redes, e os Ambientes de Serviço de Aplicações são implantados em sub-redes, as regras contidas num NSG aplicam-se a **todas as** aplicações em execução num Ambiente de Serviço de Aplicações.  Utilizando a arquitetura da amostra para este artigo, uma vez aplicado um grupo de segurança de rede na sub-rede que contém "apiase", todas as aplicações em execução no "apiase" App Service Environment serão protegidas pelo mesmo conjunto de regras de segurança. 

* **Determinar o endereço IP de saída dos chamadores a montante:**  O que é o endereço IP ou endereços dos chamadores a montante?  Estes endereços terão de ser explicitamente autorizados a aceder no NSG.  Uma vez que as chamadas entre Ambientes de Serviço de Aplicações são consideradas chamadas "Internet", o endereço IP de saída atribuído a cada um dos três Ambientes de Serviço de Aplicações a montante tem de ser autorizado a ter acesso no NSG para a sub-rede "apiase".   Para obter mais informações sobre a determinação do endereço IP de saída para aplicações em execução num Ambiente de Serviço de Aplicações, consulte o artigo ['Visão][NetworkArchitecture] Geral da Arquitetura da Rede'.
* **Será que a app API de back-end precisa de se chamar?**  Um ponto por vezes negligenciado e subtil é o cenário em que a aplicação de back-end precisa de se chamar a si mesma.  Se uma aplicação de API de back-end num Ambiente de Serviço de Aplicações precisa de se chamar, também é tratada como uma chamada de "Internet".  Na arquitetura da amostra, isto requer permitir o acesso a partir do endereço IP de saída do "apiase" App Service Environment também.

## <a name="setting-up-the-network-security-group"></a>Criação do Grupo de Segurança da Rede
Uma vez conhecido o conjunto de endereços IP de saída, o próximo passo é construir um grupo de segurança de rede.  Os grupos de segurança de rede podem ser criados tanto para redes virtuais baseadas em Gestores de Recursos, como para redes virtuais clássicas.  Os exemplos abaixo mostram a criação e configuração de um NSG numa rede virtual clássica utilizando o PowerShell.

Para a arquitetura da amostra, os ambientes estão localizados no centro-americano sul, por isso um NSG vazio é criado naquela região:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" 
-Label "Only allow web frontend and loopback traffic"
```

Em primeiro lugar, é adicionada uma regra de consentimento explícito para a infraestrutura de gestão Azure, como se nota no artigo sobre o [tráfego de entrada][InboundTraffic] para ambientes de serviço de aplicações.

```azurepowershell-interactive
#Open ports for access by Azure management infrastructure
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" 
-Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Em seguida, são adicionadas duas regras para permitir chamadas HTTP e HTTPS do primeiro Ambiente de Serviço de Aplicações a montante ("fe1ase").

```azurepowershell-interactive
#Grant access to requests from the first upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" 
-Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" 
-Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Enxaguar e repetir para o segundo e terceiro ambientes de serviço de aplicações a montante ("fe2ase" e "fe3ase").

```azurepowershell-interactive
#Grant access to requests from the second upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" 
-Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" 
-Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

#Grant access to requests from the third upstream web front-end
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" 
-Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" 
-Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Por último, conceder acesso ao endereço IP de saída do Ambiente de Serviço de Aplicações da API de back-end para que possa voltar a ligar para si próprio.

```azurepowershell-interactive
#Allow apps on the apiase environment to call back into itself
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" 
-Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" 
-Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' 
-DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Não são necessárias outras regras de segurança da rede, porque cada NSG tem um conjunto de regras padrão que bloqueiam o acesso à entrada da Internet, por padrão.

A lista completa de regras do grupo de segurança da rede é apresentada abaixo.  Note como a última regra, que é realçada, bloqueia o acesso à entrada de todos os chamadores, com além de quem telefonar que tenha tido acesso explícito.

![Configuração NSG][NSGConfiguration] 

O passo final é aplicar o NSG à sub-rede que contém o "apiase" App Service Environment.

```azurepowershell-interactive
#Apply the NSG to the backend API subnet
Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet 
-VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'
```

Com o NSG aplicado à sub-rede, apenas os três Ambientes de Serviço de Aplicações a montante, e o Ambiente de Serviço de Aplicações que contém o back-end da API, são autorizados a chamar para o ambiente "apiase".

## <a name="additional-links-and-information"></a>Links e Informações Adicionais
Informação sobre [grupos de segurança de rede.](../../virtual-network/network-security-groups-overview.md)

Compreender [endereços IP de saída][NetworkArchitecture] e Ambientes de Serviço de Aplicações.

[Portas de rede utilizadas][InboundTraffic] por Ambientes de Serviço de Aplicações.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png