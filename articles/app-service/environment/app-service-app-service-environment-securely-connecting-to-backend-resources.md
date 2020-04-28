---
title: Ligar à parte traseira v1
description: Saiba como ligar-se de forma segura para apoiar os recursos de um Ambiente de Serviço de Aplicações. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: stefsch
ms.assetid: f82eb283-a6e7-4923-a00b-4b4ccf7c4b5b
ms.topic: article
ms.date: 10/04/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 03f773e286697a12188f238cf2f422a18a20054f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74687300"
---
# <a name="connect-securely-to-back-end-resources-from-an-app-service-environment"></a>Conecte-se de forma segura para apoiar os recursos finais de um ambiente de Serviço de Aplicações
Uma vez que um App Service Environment é sempre criado **numa** rede virtual do Azure Resource Manager, **ou** numa [rede virtual][virtualnetwork]modelo de implementação clássica, as ligações de saída de um App Service Environment para outros recursos backend podem fluir exclusivamente através da rede virtual.  Com uma mudança recente feita em junho de 2016, as ASE também podem ser implantadas em redes virtuais que utilizam faixas de endereços públicos, ou espaços de endereçoS RFC1918 (isto é, endereços privados).  

Por exemplo, pode haver um Servidor SQL em execução num conjunto de máquinas virtuais com a porta 1433 bloqueada.  O ponto final pode ser ACLd apenas para permitir o acesso de outros recursos na mesma rede virtual.  

Como outro exemplo, pontos finais sensíveis podem ser executados no local e ser ligados ao Azure através de ligações [Site-to-Site][SiteToSite] ou [Azure ExpressRoute.][ExpressRoute]  Como resultado, apenas os recursos em redes virtuais ligadas aos túneis Site-to-Site ou ExpressRoute poderão aceder aos pontos finais no local.

Para todos estes cenários, as aplicações que executam um App Service Environment poderão ligar-se de forma segura aos vários servidores e recursos.  O tráfego de saída de apps que executam um App Service Environment para pontos finais privados na mesma rede virtual (ou ligado à mesma rede virtual), só fluirá sobre a rede virtual.  O tráfego de saída para pontos finais privados não fluirá pela Internet pública.

Uma ressalva aplica-se ao tráfego de saída de um App Service Environment para pontos finais dentro de uma rede virtual.  Os ambientes de serviço de aplicações não conseguem atingir pontos finais de máquinas virtuais localizadas na **mesma** subnet que o Ambiente de Serviço de Aplicações.  Isto normalmente não deve ser um problema enquanto os Ambientes de Serviço saem implantados numa subnet reservada para uso exclusivo apenas pelo Ambiente de Serviço seletiva.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividade de Saída e Requisitos de DNS
Para que um Ambiente de Serviço de Aplicações funcione corretamente, requer acesso de saída a vários pontos finais. Uma lista completa dos pontos finais externos utilizados por uma ASE está na secção "Conectividade de Rede Necessária" da Configuração da Rede para artigo [ExpressRoute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

Os Ambientes de Serviço de Aplicações requerem uma infraestrutura DNS válida configurada para a rede virtual.  Se, por alguma razão, a configuração do DNS for alterada após a criação de um Ambiente de Serviço de Aplicações, os desenvolvedores podem forçar um Ambiente de Serviço de Aplicação a captar a nova configuração DNS.  Desencadear um reboot de ambiente rolante utilizando o ícone "Restart" localizado no topo da lâmina de gestão do ambiente do serviço de aplicações no portal fará com que o ambiente apanhe a nova configuração DNS.

Recomenda-se também que quaisquer servidores DNS personalizados no vnet sejam configurados antes do tempo antes de criar um Ambiente de Serviço de Aplicações.  Se a configuração dNS de uma rede virtual for alterada enquanto um Ambiente de Serviço de Aplicações está a ser criado, isso resultará na falha do processo de criação do App Service Environment.  Na mesma linha, se um servidor DNS personalizado existir na outra extremidade de um gateway VPN, e o servidor DNS estiver inacessível ou indisponível, o processo de criação do App Service Environment também falhará.

## <a name="connecting-to-a-sql-server"></a>Ligação a um Servidor SQL
Uma configuração comum do Servidor SQL tem um ponto final de escuta na porta 1433:

![Ponto final do servidor SQL][SqlServerEndpoint]

Existem duas abordagens para restringir o tráfego a este ponto final:

* [Listas de Controlo de Acesso][NetworkAccessControlLists] à Rede (Rede ACLs)
* [Grupos de Segurança da Rede][NetworkSecurityGroups]

## <a name="restricting-access-with-a-network-acl"></a>Restringir o acesso com uma rede ACL
A porta 1433 pode ser protegida através de uma lista de controlo de acesso à rede.  O exemplo abaixo dos endereços de clientes whitelists originários do interior de uma rede virtual, e bloqueia o acesso a todos os outros clientes.

![Exemplo da lista de controlo de acesso à rede][NetworkAccessControlListExample]

Quaisquer aplicações em execução no Ambiente de Serviço de Aplicações na mesma rede virtual que o Servidor SQL serão capazes de se ligar à instância do Servidor SQL utilizando o endereço IP **interno VNet** para a máquina virtual Do Servidor SQL.  

A cadeia de ligação exemplo abaixo refere o Servidor SQL usando o seu endereço IP privado.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Embora a máquina virtual também tenha um ponto final público, as tentativas de ligação utilizando o endereço IP público serão rejeitadas por causa da rede ACL. 

## <a name="restricting-access-with-a-network-security-group"></a>Restringir o acesso com um grupo de segurança de rede
Uma abordagem alternativa para garantir o acesso é com um grupo de segurança da rede.  Os grupos de segurança da rede podem ser aplicados a máquinas virtuais individuais ou a uma subrede que contenha máquinas virtuais.

Primeiro, é necessário criar um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Restringir o acesso apenas ao tráfego interno VNet é muito simples com um grupo de segurança de rede.  As regras padrão num grupo de segurança de rede apenas permitem o acesso de outros clientes da rede na mesma rede virtual.

Como resultado, bloquear o acesso ao SQL Server é tão simples como aplicar um grupo de segurança de rede com as suas regras padrão para as máquinas virtuais que executam o Servidor SQL, ou a sub-rede que contém as máquinas virtuais.

A amostra a seguir aplica um grupo de segurança da rede à sub-rede contendo:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'

O resultado final é um conjunto de regras de segurança que bloqueiam o acesso externo, permitindo ao mesmo tempo o acesso interno vNet:

![Regras de Segurança da Rede Padrão][DefaultNetworkSecurityRules]

## <a name="getting-started"></a>Introdução
Para começar com ambientes de serviço de aplicações, consulte [Introdução ao Ambiente de Serviço de Aplicações][IntroToAppServiceEnvironment]

Para mais detalhes sobre o controlo do tráfego de entrada para o seu Ambiente de Serviço de Aplicações, consulte controlar o tráfego de entrada para um Ambiente de Serviço de [Aplicações][ControlInboundASE]

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
