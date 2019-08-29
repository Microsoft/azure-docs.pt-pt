---
title: Conectar-se com segurança aos recursos de back-end do ambiente do serviço de aplicativo-Azure
description: Saiba mais sobre como se conectar com segurança a recursos de back-end de um Ambiente do Serviço de Aplicativo.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: adb7c246a9f8c8d202d45b58f4d22eeb8d51a773
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069967"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Conectar-se com segurança aos recursos de back-end de um ambiente do serviço de aplicativo
## <a name="overview"></a>Descrição geral
Como uma Ambiente do Serviço de Aplicativo é sempre criada em uma rede virtual Azure Resource Manager **ou** em uma [rede virtual][virtualnetwork]do modelo de implantação clássico, as conexões de saída de um ambiente do serviço de aplicativo para outros recursos de back-end pode fluir exclusivamente pela rede virtual.  Com uma alteração recente feita em junho de 2016, o ASEs também pode ser implantado em redes virtuais que usam intervalos de endereços públicos ou espaços de endereço RFC1918 (ou seja, endereços privados).  

Por exemplo, pode haver um SQL Server em execução em um cluster de máquinas virtuais com a porta 1433 bloqueada.  O ponto de extremidade pode ser ACLd para permitir o acesso somente de outros recursos na mesma rede virtual.  

Como outro exemplo, pontos de extremidade confidenciais podem ser executados localmente e conectados ao Azure por meio de conexões [site a site][SiteToSite] ou [do Azure ExpressRoute][ExpressRoute] .  Como resultado, somente os recursos nas redes virtuais conectadas aos túneis site a site ou ExpressRoute poderão acessar pontos de extremidade locais.

Para todos esses cenários, os aplicativos em execução em um Ambiente do Serviço de Aplicativo poderão se conectar com segurança aos vários servidores e recursos.  O tráfego de saída de aplicativos em execução em um Ambiente do Serviço de Aplicativo para pontos de extremidade privados na mesma rede virtual (ou conectado à mesma rede virtual), fluirá apenas pela rede virtual.  O tráfego de saída para pontos de extremidade privados não fluirá pela Internet pública.

Uma limitação se aplica ao tráfego de saída de um Ambiente do Serviço de Aplicativo para pontos de extremidade em uma rede virtual.  Ambientes de serviço de aplicativo não podem alcançar pontos de extremidade de máquinas virtuais localizadas na **mesma** sub-rede que o ambiente do serviço de aplicativo.  Isso normalmente não deve ser um problema, contanto que os ambientes do serviço de aplicativo sejam implantados em uma sub-rede reservada para uso exclusivo apenas pelo Ambiente do Serviço de Aplicativo.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividade de saída e requisitos de DNS
Para que um Ambiente do Serviço de Aplicativo funcione corretamente, ele requer acesso de saída a vários pontos de extremidade. Uma lista completa dos pontos de extremidade externos usados por um ASE está na seção "conectividade de rede necessária" do artigo [configuração de rede para ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Os ambientes de serviço de aplicativo exigem uma infraestrutura de DNS válida configurada para a rede virtual.  Se por algum motivo a configuração de DNS for alterada depois que um Ambiente do Serviço de Aplicativo tiver sido criado, os desenvolvedores poderão forçar um Ambiente do Serviço de Aplicativo a escolher a nova configuração de DNS.  Disparar uma reinicialização do ambiente sem interrupção usando o ícone "reiniciar" localizado na parte superior da folha gerenciamento de Ambiente do Serviço de Aplicativo no portal fará com que o ambiente pegue a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na vnet sejam configurados antecipadamente antes da criação de um Ambiente do Serviço de Aplicativo.  Se a configuração de DNS de uma rede virtual for alterada enquanto um Ambiente do Serviço de Aplicativo estiver sendo criado, isso resultará na falha do processo de criação de Ambiente do Serviço de Aplicativo.  Em um sentido semelhante, se existir um servidor DNS personalizado na outra extremidade de um gateway de VPN e o servidor DNS estiver inacessível ou indisponível, o processo de criação de Ambiente do Serviço de Aplicativo também falhará.

## <a name="connecting-to-a-sql-server"></a>Conectando a um SQL Server
Uma configuração comum de SQL Server tem um ponto de extremidade ouvindo na porta 1433:

![Ponto de extremidade SQL Server][SqlServerEndpoint]

Há duas abordagens para restringir o tráfego para esse ponto de extremidade:

* [Listas de controle de acesso à rede][NetworkAccessControlLists] (ACLs de rede)
* [Grupos de segurança de rede][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Restringindo o acesso com uma ACL de rede
A porta 1433 pode ser protegida usando uma lista de controle de acesso à rede.  O exemplo abaixo lista de permissões de cliente endereços provenientes de dentro de uma rede virtual e bloqueia o acesso a todos os outros clientes.

![Exemplo de lista de controle de acesso de rede][NetworkAccessControlListExample]

Todos os aplicativos em execução no Ambiente do Serviço de Aplicativo na mesma rede virtual que o SQL Server poderão se conectar à instância do SQL Server usando o endereço IP **interno da VNet** para a máquina virtual SQL Server.  

A cadeia de conexão de exemplo a seguir faz referência à SQL Server usando seu endereço IP privado.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Embora a máquina virtual também tenha um ponto de extremidade público, as tentativas de conexão usando o endereço IP público serão rejeitadas por causa da ACL de rede. 

## <a name="restricting-access-with-a-network-security-group"></a>Restringindo o acesso com um grupo de segurança de rede
Uma abordagem alternativa para proteger o acesso é com um grupo de segurança de rede.  Os grupos de segurança de rede podem ser aplicados a máquinas virtuais individuais ou a uma sub-rede que contenha máquinas virtuais.

Primeiro, um grupo de segurança de rede precisa ser criado:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir o acesso apenas ao tráfego interno de VNet é muito simples com um grupo de segurança de rede.  As regras padrão em um grupo de segurança de rede só permitem o acesso de outros clientes de rede na mesma rede virtual.

Como resultado, o bloqueio do acesso ao SQL Server é tão simples quanto aplicar um grupo de segurança de rede com suas regras padrão para as máquinas virtuais em execução SQL Server ou a sub-rede que contém as máquinas virtuais.

O exemplo a seguir aplica um grupo de segurança de rede à sub-rede que o contém:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

O resultado final é um conjunto de regras de segurança que bloqueiam o acesso externo, permitindo acesso interno à VNet:

![Regras de segurança de rede padrão][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Introdução
Para começar a usar os ambientes do serviço de aplicativo, consulte [introdução ao ambiente do serviço de aplicativo][IntroToAppServiceEnvironment]

Para obter detalhes sobre como controlar o tráfego de entrada para seu Ambiente do Serviço de Aplicativo, consulte controlando o [tráfego de entrada para um ambiente do serviço de aplicativo][ControlInboundASE]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md
[SiteToSite]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[ControlInboundASE]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
