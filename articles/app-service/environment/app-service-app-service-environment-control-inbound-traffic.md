---
title: Controlar o tráfego de entrada para o ambiente do serviço de aplicativo-Azure
description: Saiba mais sobre como configurar regras de segurança de rede para controlar o tráfego de entrada para um Ambiente do Serviço de Aplicativo.
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: ''
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: c887ae5568bfd0f72f8d90daecd95547ed7b8b7d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70070411"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Como controlar o tráfego de entrada para um Ambiente do Serviço de Aplicativo
## <a name="overview"></a>Descrição geral
Um Ambiente do Serviço de Aplicativo pode ser criado em uma rede virtual Azure Resource Manager **ou** em uma [rede virtual][virtualnetwork]do modelo de implantação clássico.  Uma nova rede virtual e uma nova sub-rede podem ser definidas no momento em que um Ambiente do Serviço de Aplicativo é criado.  Como alternativa, um Ambiente do Serviço de Aplicativo pode ser criado em uma rede virtual pré-existente e uma sub-rede já existente.  Com uma alteração feita em junho de 2016, o ASEs também pode ser implantado em redes virtuais que usam intervalos de endereços públicos ou espaços de endereço RFC1918 (ou seja, endereços privados).  Para obter mais detalhes sobre a criação de um Ambiente do Serviço de Aplicativo consulte [como criar um ambiente do serviço de aplicativo][HowToCreateAnAppServiceEnvironment].

Um Ambiente do Serviço de Aplicativo sempre deve ser criado em uma sub-rede porque uma sub-rede fornece um limite de rede que pode ser usado para bloquear o tráfego de entrada por trás de dispositivos e serviços de upstream, de modo que o tráfego HTTP e HTTPS seja aceito somente de upstream específico Endereços IP.

O tráfego de rede de entrada e saída em uma sub-rede é controlado usando um [grupo de segurança de rede][NetworkSecurityGroups]. O controle do tráfego de entrada requer a criação de regras de segurança de rede em um grupo de segurança de rede e, em seguida, a atribuição do grupo de segurança de rede à sub-rede que contém o Ambiente do Serviço de Aplicativo.

Depois que um grupo de segurança de rede é atribuído a uma sub-rede, o tráfego de entrada para aplicativos no Ambiente do Serviço de Aplicativo é permitido/bloqueado com base nas regras de permissão e negação definidas no grupo de segurança de rede.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Portas de rede de entrada usadas em um Ambiente do Serviço de Aplicativo
Antes de bloquear o tráfego de rede de entrada com um grupo de segurança de rede, é importante saber o conjunto de portas de rede necessárias e opcionais usadas por um Ambiente do Serviço de Aplicativo.  Fechar acidentalmente o tráfego para algumas portas pode resultar em perda de funcionalidade em um Ambiente do Serviço de Aplicativo.

A seguir está uma lista de portas usadas por um Ambiente do Serviço de Aplicativo. Todas as portas são **TCP**, a menos que indicado de outra forma claramente:

* 454:  **Porta necessária** usada pela infraestrutura do Azure para gerenciar e manter ambientes de serviço de aplicativo via SSL.  Não bloqueie o tráfego para esta porta.  Essa porta é sempre associada ao VIP público de um ASE.
* 455:  **Porta necessária** usada pela infraestrutura do Azure para gerenciar e manter ambientes de serviço de aplicativo via SSL.  Não bloqueie o tráfego para esta porta.  Essa porta é sempre associada ao VIP público de um ASE.
* 80:  Porta padrão para tráfego HTTP de entrada para aplicativos em execução nos planos do serviço de aplicativo em um Ambiente do Serviço de Aplicativo.  Em um ASE habilitado para ILB, essa porta está associada ao endereço ILB do ASE.
* 443: Porta padrão para tráfego SSL de entrada para aplicativos em execução nos planos do serviço de aplicativo em um Ambiente do Serviço de Aplicativo.  Em um ASE habilitado para ILB, essa porta está associada ao endereço ILB do ASE.
* 21:  Canal de controle para FTP.  Essa porta pode ser bloqueada com segurança se o FTP não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta pode ser associada ao endereço ILB de um ASE.
* 990:  Canal de controle para FTPS.  Essa porta pode ser bloqueada com segurança se a FTPS não estiver sendo usada.  Em um ASE habilitado para ILB, essa porta pode ser associada ao endereço ILB de um ASE.
* 10001-10020: Canais de dados para FTP.  Assim como no canal de controle, essas portas podem ser bloqueadas com segurança se o FTP não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta pode ser associada ao endereço ILB do ASE.
* 4016: Usado para depuração remota com o Visual Studio 2012.  Essa porta poderá ser bloqueada com segurança se o recurso não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta está associada ao endereço ILB do ASE.
* 4018: Usado para depuração remota com Visual Studio 2013.  Essa porta poderá ser bloqueada com segurança se o recurso não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta está associada ao endereço ILB do ASE.
* 4020: Usado para depuração remota com o Visual Studio 2015.  Essa porta poderá ser bloqueada com segurança se o recurso não estiver sendo usado.  Em um ASE habilitado para ILB, essa porta está associada ao endereço ILB do ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividade de saída e requisitos de DNS
Para que um Ambiente do Serviço de Aplicativo funcione corretamente, ele também requer acesso de saída a vários pontos de extremidade. Uma lista completa dos pontos de extremidade externos usados por um ASE está na seção "conectividade de rede necessária" do artigo [configuração de rede para ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Os ambientes de serviço de aplicativo exigem uma infraestrutura de DNS válida configurada para a rede virtual.  Se por algum motivo a configuração de DNS for alterada depois que um Ambiente do Serviço de Aplicativo tiver sido criado, os desenvolvedores poderão forçar um Ambiente do Serviço de Aplicativo a escolher a nova configuração de DNS.  Disparar uma reinicialização do ambiente sem interrupção usando o ícone "reiniciar" localizado na parte superior da folha de gerenciamento de Ambiente do Serviço de Aplicativo no [portal do Azure][NewPortal] fará com que o ambiente pegue a nova configuração de DNS.

Também é recomendável que todos os servidores DNS personalizados na vnet sejam configurados antecipadamente antes da criação de um Ambiente do Serviço de Aplicativo.  Se a configuração de DNS de uma rede virtual for alterada enquanto um Ambiente do Serviço de Aplicativo estiver sendo criado, isso resultará na falha do processo de criação de Ambiente do Serviço de Aplicativo.  Em um sentido semelhante, se existir um servidor DNS personalizado na outra extremidade de um gateway de VPN e o servidor DNS estiver inacessível ou indisponível, o processo de criação de Ambiente do Serviço de Aplicativo também falhará.

## <a name="creating-a-network-security-group"></a>Criando um grupo de segurança de rede
Para obter detalhes completos sobre como os grupos de segurança de rede funcionam, consulte as [informações][NetworkSecurityGroups]a seguir.  O exemplo de gerenciamento de serviços do Azure abaixo aborda os destaques de grupos de segurança de rede, com foco na configuração e aplicação de um grupo de segurança de rede a uma sub-rede que contém um Ambiente do Serviço de Aplicativo.

**Nota:** Os grupos de segurança de rede podem ser configurados graficamente usando o [portal do Azure](https://portal.azure.com) ou por meio de Azure PowerShell.

Os grupos de segurança de rede são criados primeiro como uma entidade autônoma associada a uma assinatura. Como os grupos de segurança de rede são criados em uma região do Azure, verifique se o grupo de segurança de rede foi criado na mesma região que o Ambiente do Serviço de Aplicativo.

O seguinte demonstra a criação de um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Depois que um grupo de segurança de rede é criado, uma ou mais regras de segurança de rede são adicionadas a ele.  Como o conjunto de regras pode mudar ao longo do tempo, é recomendável espaçar o esquema de numeração usado para as prioridades de regra para facilitar a inserção de regras adicionais ao longo do tempo.

O exemplo a seguir mostra uma regra que concede explicitamente acesso às portas de gerenciamento necessárias à infraestrutura do Azure para gerenciar e manter um Ambiente do Serviço de Aplicativo.  Observe que todo o tráfego de gerenciamento passa por SSL e é protegido por certificados de cliente, portanto, embora as portas sejam abertas, elas ficam inacessíveis por qualquer entidade que não seja a infraestrutura de gerenciamento do Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Ao bloquear o acesso à porta 80 e 443 para "Ocultar" um Ambiente do Serviço de Aplicativo por trás de dispositivos ou serviços upstream, você precisará saber o endereço IP de upstream.  Por exemplo, se você estiver usando um WAF (firewall do aplicativo Web), o WAF terá seu próprio endereço IP (ou endereços) que ele usa ao fazer o proxy do tráfego para um Ambiente do Serviço de Aplicativo downstream.  Você precisará usar esse endereço IP no parâmetro *SourceAddressPrefix* de uma regra de segurança de rede.

No exemplo abaixo, o tráfego de entrada de um endereço IP de upstream específico é explicitamente permitido.  O endereço *1.2.3.4* é usado como um espaço reservado para o endereço IP de um WAF upstream.  Altere o valor para corresponder ao endereço usado pelo seu dispositivo ou serviço upstream.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Se o suporte a FTP for desejado, as regras a seguir poderão ser usadas como um modelo para conceder acesso à porta de controle de FTP e às portas de canal de dados.  Como o FTP é um protocolo com estado, talvez você não consiga rotear o tráfego de FTP por meio de um firewall ou dispositivo proxy tradicional de HTTP/HTTPS.  Nesse caso, você precisará definir o *SourceAddressPrefix* para um valor diferente, por exemplo, o intervalo de endereços IP de computadores de desenvolvimento ou de implantação nos quais os clientes FTP estão em execução. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Observação:** o intervalo de portas do canal de dados pode ser alterado durante o período de visualização.)

Se a depuração remota com o Visual Studio for usada, as regras a seguir demonstrarão como conceder acesso.  Há uma regra separada para cada versão com suporte do Visual Studio, já que cada versão usa uma porta diferente para a depuração remota.  Assim como acontece com o acesso ao FTP, o tráfego de depuração remota pode não fluir corretamente por meio de um dispositivo WAF ou proxy tradicional.  Em vez disso, o *SourceAddressPrefix* pode ser definido como o intervalo de endereços IP de máquinas de desenvolvedor que executam o Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Atribuindo um grupo de segurança de rede a uma sub-rede
Um grupo de segurança de rede tem uma regra de segurança padrão que nega o acesso a todo o tráfego externo.  O resultado da combinação das regras de segurança de rede descritas acima e da regra de segurança padrão bloqueando o tráfego de entrada é que somente o tráfego de intervalos de endereços de origem associados a uma ação *permitir* poderá enviar tráfego para aplicativos em execução em um Ambiente do Serviço de Aplicativo.

Depois que um grupo de segurança de rede é populado com regras de segurança, ele precisa ser atribuído à sub-rede que contém o Ambiente do Serviço de Aplicativo.  O comando de atribuição referencia o nome da rede virtual onde reside a Ambiente do Serviço de Aplicativo, bem como o nome da sub-rede em que o Ambiente do Serviço de Aplicativo foi criado.  

O exemplo a seguir mostra um grupo de segurança de rede que está sendo atribuído a uma sub-rede e rede virtual:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Depois que a atribuição do grupo de segurança de rede for bem-sucedida (a atribuição é uma operação de execução longa e pode levar alguns minutos para ser concluída), somente o tráfego de entrada correspondente às regras de *permissão* atingirá com êxito os aplicativos no ambiente do serviço de aplicativo.

Para fins de integridade, o exemplo a seguir mostra como remover e, portanto, associar o grupo de segurança de rede da sub-rede:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerações especiais para IP-SSL explícito
Se um aplicativo estiver configurado com um endereço IP-SSL explícito (aplicável *somente* a ases que têm um VIP público), em vez de usar o endereço IP padrão do ambiente do serviço de aplicativo, o tráfego HTTP e HTTPS fluirá para a sub-rede em um conjunto diferente de portas Além das portas 80 e 443.

O par individual de portas usadas por cada endereço IP-SSL pode ser encontrado na interface do usuário do portal na folha UX de detalhes do Ambiente do Serviço de Aplicativo.  Selecione "todas as configurações"--> "endereços IP".  A folha "endereços IP" mostra uma tabela de todos os endereços IP-SSL configurados explicitamente para o Ambiente do Serviço de Aplicativo, juntamente com o par de portas especiais usado para rotear o tráfego HTTP e HTTPS associado a cada endereço IP-SSL.  É esse par de portas que precisa ser usado para os parâmetros DestinationPortRange ao configurar regras em um grupo de segurança de rede.

Quando um aplicativo em um ASE é configurado para usar IP-SSL, os clientes externos não verão e não precisarão se preocupar com o mapeamento de pares de portas especiais.  O tráfego para os aplicativos fluirá normalmente para o endereço IP-SSL configurado.  A conversão para o par de portas especiais ocorre automaticamente durante o segmento final do tráfego de roteamento na sub-rede que contém o ASE. 

## <a name="getting-started"></a>Introdução
Para começar a usar os ambientes do serviço de aplicativo, consulte [introdução ao ambiente do serviço de aplicativo][IntroToAppServiceEnvironment]

Para obter detalhes sobre os aplicativos que se conectam com segurança ao recurso de back-end de um Ambiente do Serviço de Aplicativo, consulte [conexão segura com recursos de back-end de um ambiente do serviço de aplicativo][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

