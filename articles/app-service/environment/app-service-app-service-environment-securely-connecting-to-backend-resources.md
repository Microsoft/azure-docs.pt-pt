---
title: Ligar para trás v1
description: Saiba como conectar-se de forma segura para reter recursos de um Ambiente de Serviço de Aplicações. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 9f8e288f771b9d584a0fd3430115f5fa60f68e47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88961810"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Conecte-se de forma segura para apoiar recursos finais de um ambiente de Serviço de Aplicações
Uma vez que um Ambiente de Serviço de Aplicações é sempre criado **numa** rede virtual Azure Resource **Manager, ou** numa rede [virtual][virtualnetwork]de modelo de implementação clássica, as ligações de saída de um Ambiente de Serviço de Aplicações para outros recursos de backend podem fluir exclusivamente através da rede virtual. A partir de junho de 2016, as ASEs também podem ser implantadas em redes virtuais que utilizam tanto as gamas de endereços públicos como os espaços de endereços RFC1918 (endereços privados).  

Por exemplo, pode haver um SQL Server a funcionar num conjunto de máquinas virtuais com a porta 1433 bloqueada.  O ponto final pode ser o ACLd apenas para permitir o acesso a partir de outros recursos na mesma rede virtual.  

Como outro exemplo, os pontos finais sensíveis podem ser executados no local e ser ligados ao Azure através de ligações [Site-to-Site][SiteToSite] ou [Azure ExpressRoute.][ExpressRoute]  Como resultado, apenas os recursos em redes virtuais ligadas aos túneis Site-to-Site ou ExpressRoute podem aceder aos pontos finais no local.

Para todos estes cenários, as aplicações em execução num Ambiente de Serviço de Aplicações podem ligar-se de forma segura aos vários servidores e recursos. Se o tráfego de saída de apps for executado num Ambiente de Serviço de Aplicações para pontos finais privados na mesma rede virtual ou ligado à mesma rede virtual, apenas fluirá sobre a rede virtual.  O tráfego de saída para pontos finais privados não fluirá pela Internet pública.

Uma questão aplica-se ao tráfego de saída de um Ambiente de Serviço de Aplicações a pontos finais dentro de uma rede virtual. Os Ambientes de Serviço de Aplicações não conseguem chegar aos pontos finais de máquinas virtuais que estão localizadas na **mesma** sub-rede que o App Service Environment. Esta limitação normalmente não deve ser um problema, se os Ambientes de Serviço de Aplicação forem implantados numa sub-rede que é reservada exclusivamente pelo Ambiente de Serviço de Aplicações.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividade de Saída e Requisitos de DNS
Para que um Ambiente de Serviço de Aplicações funcione corretamente, requer acesso de saída a vários pontos finais. Uma lista completa dos pontos finais externos utilizados por um ASE está na secção "Conectividade de rede obrigatória" da Configuração de Rede para o artigo [ExpressRoute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

Os Ambientes de Serviço de Aplicações requerem uma infraestrutura de DNS válida configurada para a rede virtual.  Se a configuração do DNS for alterada após a criação de um Ambiente de Serviço de Aplicações, os desenvolvedores podem forçar um Ambiente de Serviço de Aplicações a captar a nova configuração de DNS. No topo da lâmina de gestão do Ambiente do Serviço de Aplicações no portal, selecione o ícone **Restart** para desencadear um reboot de ambiente rolante, o que faz com que o ambiente apanhe a nova configuração de DNS.

Também é recomendado que quaisquer servidores DNS personalizados na vnet sejam configurado antes de criar um Ambiente de Serviço de Aplicações.  Se a configuração de DNS de uma rede virtual for alterada durante a criação de um Ambiente de Serviço de Aplicações, isso resultará na falha do processo de criação do Ambiente do Serviço de Aplicações. Na outra extremidade de um gateway VPN, se houver um servidor DNS personalizado que seja inacessível ou indisponível, o processo de criação do App Service Environment também falhará.

## <a name="connecting-to-a-sql-server"></a>Ligação a um servidor SQL
Uma configuração comum do SQL Server tem um ponto final a ouvir na porta 1433:

![Ponto final do servidor SQL][SqlServerEndpoint]

Existem duas abordagens para restringir o tráfego a este ponto final:

* [Listas de Controlo de Acesso à Rede][NetworkAccessControlLists] (ACLs de rede)
* [Grupos de Segurança de Rede][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Restringir o acesso com uma rede ACL
A porta 1433 pode ser protegida através de uma lista de controlo de acesso à rede.  O exemplo abaixo adiciona às permissões de atribuição que os endereços do cliente provêm de uma rede virtual e bloqueia o acesso a todos os outros clientes.

![Exemplo da lista de controlo de acesso à rede][NetworkAccessControlListExample]

Quaisquer aplicações que são executadas no Ambiente de Serviço de Aplicações, na mesma rede virtual que o SQL Server, podem ligar-se à instância do SQL Server. Utilize o endereço IP **interno VNet** para a máquina virtual SQL Server.  

A cadeia de ligação de exemplo abaixo refere o SQL Server utilizando o seu endereço IP privado.

`Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient`

Embora a máquina virtual também tenha um ponto final público, as tentativas de ligação para usar o endereço IP público serão rejeitadas por causa da rede ACL. 

## <a name="restricting-access-with-a-network-security-group"></a>Restringir o acesso com um grupo de segurança de rede
Uma abordagem alternativa para garantir o acesso é com um grupo de segurança de rede.  Os grupos de segurança da rede podem ser aplicados a máquinas virtuais individuais ou a uma sub-rede que contenha máquinas virtuais.

Primeiro, terá de criar um grupo de segurança de rede:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Restringir o acesso a apenas tráfego interno VNet é simples com um grupo de segurança de rede.  As regras padrão num grupo de segurança de rede só permitem o acesso de outros clientes da rede na mesma rede virtual.

Como resultado, bloquear o acesso ao SQL Server é simples. Basta aplicar um grupo de segurança de rede com as suas regras padrão para as máquinas virtuais que executam o SQL Server, ou a sub-rede que contém as máquinas virtuais.

A amostra abaixo aplica um grupo de segurança de rede à sub-rede que contém:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
```

O resultado final é um conjunto de regras de segurança que bloqueiam o acesso externo, permitindo ao mesmo tempo o acesso interno da VNet:

![Regras de segurança da rede padrão][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Introdução
Para começar com ambientes de serviço de aplicações, consulte [Introdução ao Ambiente de Serviço de Aplicações][IntroToAppServiceEnvironment]

Para obter detalhes sobre o controlo do tráfego de entrada no seu Ambiente de Serviço de Aplicações, consulte [controle o tráfego de entrada para um Ambiente de Serviço de Aplicações][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: ../../vpn-gateway/vpn-gateway-multi-site.md
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: /previous-versions/azure/virtual-network/virtual-networks-acl
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png