---
title: Ligue em segurança a back-end recursos de ambiente de serviço de aplicações - Azure
description: Saiba mais sobre como ligar de forma segura a recursos de back-end de um ambiente de serviço de aplicações.
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: aea51234d26e5dbaef836419c2a13a12f8083e6f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130709"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Ligue em segurança a back-end recursos a partir de um ambiente de serviço de aplicações
## <a name="overview"></a>Descrição geral
Uma vez que um ambiente de serviço de aplicações é sempre criado em **ambos** uma rede virtual do Azure Resource Manager, **ou** um modelo de implementação clássica [rede virtual] [ virtualnetwork], ligações de saída a partir de um ambiente de serviço de aplicações para outros recursos de back-end podem fluir exclusivamente através da rede virtual.  Com uma alteração recente feita em Junho de 2016, os ASEs também podem ser implementados em redes virtuais que utilizam intervalos de endereços públicos ou espaços de endereços de RFC1918 (ou seja, endereços privados).  

Por exemplo, pode ser um SQL Server em execução num cluster de máquinas virtuais com a porta 1433 bloqueado.  O ponto final pode ser ACLd para permitir apenas o acesso a partir de outros recursos na mesma rede virtual.  

Como outro exemplo, pontos de extremidade confidenciais podem ser executadas no local e ser ligados ao Azure através de qualquer um [Site a Site] [ SiteToSite] ou [Azure ExpressRoute] [ ExpressRoute] ligações.  Como resultado, apenas os recursos em redes virtuais ligadas ao Site a Site ou ExpressRoute túneis será capazes de aceder aos pontos finais no local.

Para todos estes cenários, será capazes de ligar de forma segura para os vários recursos e servidores de aplicações em execução num ambiente de serviço de aplicações.  O tráfego de saída a partir de aplicações em execução num ambiente de serviço de aplicações para pontos de extremidade privados na mesma rede virtual (ou ligado à mesma rede virtual), será apenas fluxo através da rede virtual.  Tráfego de saída para pontos de extremidade privados não irá fluir através da Internet pública.

Uma limitação aplica-se ao tráfego de saída de um ambiente de serviço de aplicações para os pontos finais numa rede virtual.  Ambientes de serviço de aplicações não é possível aceder a pontos de extremidade de máquinas virtuais localizadas no **mesmo** sub-rede que o ambiente de serviço de aplicações.  Isso normalmente não deve ser um problema, desde que os ambientes de serviço de aplicações são implementados numa sub-rede reservada para utilização exclusiva por apenas o ambiente de serviço de aplicações.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividade de saída e os requisitos de DNS
Para um ambiente de serviço de aplicações funcionar corretamente, ela requer acesso de saída para vários pontos de extremidade. Uma lista completa dos pontos finais externos utilizados por um ASE é na seção "Necessária conectividade de rede" a [configuração de rede para o ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artigo.

Os ambientes de serviço de aplicações exigem uma infra-estrutura DNS válida configurada para a rede virtual.  Se por algum motivo, a configuração de DNS for alterada depois de criar um ambiente de serviço de aplicações, os desenvolvedores podem forçar um ambiente de serviço de aplicações de retirada a nova configuração de DNS.  Acionar um reinício sem interrupção de ambiente com o ícone de "Restart" na parte superior do painel de gestão do ambiente de serviço de aplicações no portal do fará com que o ambiente de retirada a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na vnet em modo de ser configurado antes do tempo antes de criar um ambiente de serviço de aplicações.  Se a configuração de DNS de uma rede virtual é alterada durante a criação de um ambiente de serviço de aplicações, que resultará na falha de processo de criação do ambiente de serviço de aplicações.  Numa veia semelhante, se um servidor DNS personalizado existe na outra extremidade de um gateway VPN e o servidor DNS está inacessível ou não está disponível, o processo de criação do ambiente de serviço de aplicações também irá falhar.

## <a name="connecting-to-a-sql-server"></a>Ligar a um servidor SQL
Uma configuração comum do SQL Server tem um ponto final à escuta na porta 1433:

![SQL Server Endpoint][SqlServerEndpoint]

Existem duas abordagens para limitar o tráfego para este ponto final:

* [Listas de controlo de acesso de rede] [ NetworkAccessControlLists] (ACLs de rede)
* [Grupos de segurança de rede][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Restringir o acesso com uma ACL de rede
A porta 1433 pode ser protegida com uma lista de controlo de acesso de rede.  O exemplo abaixo cliente de listas de permissões resolve com origem na mesma rede virtual e bloqueia o acesso a todos os outros clientes.

![Exemplo de lista de controle de acesso de rede][NetworkAccessControlListExample]

Todas as aplicações em execução no ambiente de serviço de aplicações na mesma rede virtual, o SQL Server, será possível estabelecer ligação com a instância do SQL Server com o **VNet interna** endereço IP para a máquina virtual do SQL Server.  

A cadeia de ligação de exemplo abaixo referencia o SQL Server com o respetivo endereço IP privado.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Apesar da máquina virtual possui um ponto de final público também, tentativas de ligação com o endereço IP público serão rejeitadas devido a ACL de rede. 

## <a name="restricting-access-with-a-network-security-group"></a>Restringir o acesso com um grupo de segurança de rede
Uma abordagem alternativa para proteger o acesso é com um grupo de segurança de rede.  Grupos de segurança de rede podem ser aplicados a máquinas virtuais individuais, ou a uma sub-rede que contém as máquinas virtuais.

Primeiro um grupo de segurança de rede tem de ser criada:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir o acesso a apenas VNet o tráfego interno é muito simples com um grupo de segurança de rede.  As regras predefinidas num grupo de segurança de rede apenas permitem o acesso de outros clientes de rede na mesma rede virtual.

Como resultado o bloqueio de acesso ao SQL Server é tão simples quanto a aplicar um grupo de segurança de rede com suas regras predefinidas para as máquinas de virtuais a executar o SQL Server ou a sub-rede que contém as máquinas virtuais.

O exemplo abaixo aplica-se um grupo de segurança de rede para a sub-rede que contém:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

O resultado final é um conjunto de regras de segurança que bloquear o acesso externo, permitindo o acesso interno da VNet:

![Regras de segurança de rede predefinidas][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Introdução
Para começar a utilizar com ambientes de serviço de aplicações, veja [introdução ao ambiente de serviço de aplicações][IntroToAppServiceEnvironment]

Para obter mais detalhes em torno de controlar o tráfego de entrada para o ambiente de serviço de aplicações, consulte [controlar o tráfego de entrada para um ambiente de serviço de aplicações][ControlInboundASE]

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
