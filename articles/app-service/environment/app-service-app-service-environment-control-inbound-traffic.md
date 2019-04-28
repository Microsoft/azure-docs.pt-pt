---
title: Controlar o tráfego de entrada para o ambiente de serviço de aplicações - Azure
description: Saiba mais sobre como configurar regras de segurança de rede para controlar o tráfego de entrada para um ambiente de serviço de aplicações.
services: app-service
documentationcenter: ''
author: ccompy
manager: erikre
editor: ''
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 84575dcb67845a074ce19cf9d819e1dda3f90e20
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130794"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Como controlar o tráfego de entrada para um ambiente de serviço de aplicações
## <a name="overview"></a>Descrição geral
Um ambiente de serviço de aplicações podem ser criado no **ambos** uma rede virtual do Azure Resource Manager, **ou** um modelo de implementação clássica [rede virtual] [ virtualnetwork].  Uma nova rede virtual e uma nova sub-rede podem ser definidas no momento num que ambiente de serviço de aplicações é criado.  Em alternativa, um ambiente de serviço de aplicações podem ser criado numa rede virtual já existente e uma sub-rede já existente.  Com uma alteração feita em Junho de 2016, os ASEs também podem ser implementados em redes virtuais que utilizam intervalos de endereços públicos ou espaços de endereços de RFC1918 (ou seja, endereços privados).  Para obter mais detalhes sobre como criar um ambiente de serviço de aplicações, veja [como criar um ambiente de serviço de aplicações][HowToCreateAnAppServiceEnvironment].

Um ambiente de serviço de aplicações tem sempre de ser criado dentro de uma sub-rede porque uma sub-rede fornece um limite de rede que pode ser utilizado para bloquear o tráfego de entrada atrás de dispositivos a montante e serviços, de modo a que o tráfego HTTP e HTTPS apenas é aceite a partir específicos a montante Endereços IP.

Tráfego de rede de entrada e saída numa sub-rede é controlado com um [grupo de segurança de rede][NetworkSecurityGroups]. Controlar o tráfego de entrada necessita de criar regras de segurança de rede num grupo de segurança de rede e, em seguida, atribuir a segurança de rede na sub-rede que contém o ambiente de serviço de aplicações de grupo.

Depois de um grupo de segurança de rede é atribuído a uma sub-rede, o tráfego de entrada para aplicações no ambiente do serviço de aplicações é permitido/bloqueado com base de permissões e negar regras definidas no grupo de segurança de rede.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Portas de rede de entrada usados num ambiente de serviço de aplicações
Antes de bloquear o tráfego de rede de entrada com um grupo de segurança de rede, é importante saber o conjunto de portas de rede necessários e opcionais usados por um ambiente de serviço de aplicações.  Fechar acidentalmente desativar tráfego para algumas portas pode resultar em perda de funcionalidade num ambiente de serviço de aplicações.

Segue-se uma lista das portas utilizadas pelo ambiente de serviço de aplicações. Todas as portas são **TCP**, a menos que claramente indicação em contrário:

* 454:  **Necessária a porta** usado pela infraestrutura do Azure para o gerenciamento e manutenção de ambientes de serviço de aplicações através de SSL.  Bloqueia o tráfego para esta porta.  Esta porta é vinculada sempre para o VIP público de um ASE.
* 455:  **Necessária a porta** usado pela infraestrutura do Azure para o gerenciamento e manutenção de ambientes de serviço de aplicações através de SSL.  Bloqueia o tráfego para esta porta.  Esta porta é vinculada sempre para o VIP público de um ASE.
* 80:  Porta predefinida para o tráfego de entrada HTTP para as aplicações em execução em planos de serviço de aplicações num ambiente de serviço de aplicações.  Num ASE com ILB ativado, esta porta está vinculada ao endereço do ILB de ASE.
* 443: Porta predefinida para tráfego de entrada de SSL para aplicações em execução em planos de serviço de aplicações num ambiente de serviço de aplicações.  Num ASE com ILB ativado, esta porta está vinculada ao endereço do ILB de ASE.
* 21:  Canal de controlo de FTP.  Esta porta pode ser bloqueada com segurança se não está a ser utilizado o FTP.  Num ASE com ILB ativado, esta porta pode ser vinculada para o endereço do ILB para um ASE.
* 990:  Canal de controlo de FTPS.  Esta porta pode ser bloqueada com segurança se não está a ser utilizado o FTPS.  Num ASE com ILB ativado, esta porta pode ser vinculada para o endereço do ILB para um ASE.
* 10001-10020: Canais de dados para FTP.  Tal como acontece com o canal de controlo, estas portas podem com segurança bloqueadas se não está a ser utilizado o FTP.  Num ASE com ILB ativado, esta porta pode ser vinculada ao endereço ILB de ASE.
* 4016: Utilizado para depuração remota com o Visual Studio 2012.  Esta porta pode ser bloqueada com segurança se o recurso não está a ser utilizado.  Num ASE com ILB ativado, esta porta está vinculada ao endereço do ILB de ASE.
* 4018: Utilizado para depuração remota com o Visual Studio 2013.  Esta porta pode ser bloqueada com segurança se o recurso não está a ser utilizado.  Num ASE com ILB ativado, esta porta está vinculada ao endereço do ILB de ASE.
* 4020: Utilizado para depuração remota com o Visual Studio 2015.  Esta porta pode ser bloqueada com segurança se o recurso não está a ser utilizado.  Num ASE com ILB ativado, esta porta está vinculada ao endereço do ILB de ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividade de saída e os requisitos de DNS
Para um ambiente de serviço de aplicações funcionar corretamente, também é necessário acesso de saída para vários pontos de extremidade. Uma lista completa dos pontos finais externos utilizados por um ASE é na seção "Necessária conectividade de rede" a [configuração de rede para o ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) artigo.

Os ambientes de serviço de aplicações exigem uma infra-estrutura DNS válida configurada para a rede virtual.  Se por algum motivo, a configuração de DNS for alterada depois de criar um ambiente de serviço de aplicações, os desenvolvedores podem forçar um ambiente de serviço de aplicações de retirada a nova configuração de DNS.  Acionar um reinício sem interrupção de ambiente com o ícone "Reiniciar" localizado na parte superior do painel de gestão do ambiente de serviço de aplicações no [portal do Azure] [ NewPortal] fará com que o ambiente para pegar o novo DNS configuração.

Também é recomendável que todos os servidores DNS personalizados na vnet em modo de ser configurado antes do tempo antes de criar um ambiente de serviço de aplicações.  Se a configuração de DNS de uma rede virtual é alterada durante a criação de um ambiente de serviço de aplicações, que resultará na falha de processo de criação do ambiente de serviço de aplicações.  Numa veia semelhante, se um servidor DNS personalizado existe na outra extremidade de um gateway VPN e o servidor DNS está inacessível ou não está disponível, o processo de criação do ambiente de serviço de aplicações também irá falhar.

## <a name="creating-a-network-security-group"></a>Criar um grupo de segurança de rede
Para obter detalhes completos sobre como o trabalho de grupos de segurança de rede, consulte o seguinte procedimento [informações][NetworkSecurityGroups].  A gestão de serviço do Azure aborda exemplo a seguir destaca de grupos de segurança de rede, com foco em configurar e aplicar um grupo de segurança de rede a uma sub-rede que contém um ambiente de serviço de aplicações.

**Nota:** Grupos de segurança de rede podem ser configurados graficamente com o [Portal do Azure](https://portal.azure.com) ou através do PowerShell do Azure.

Grupos de segurança de rede primeiro são criados como uma entidade autônoma associada a uma subscrição. Uma vez que os grupos de segurança de rede são criados numa região do Azure, certifique-se de que o grupo de segurança de rede é criado na mesma região que o ambiente de serviço de aplicações.

A seguir demonstra a criação de um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Depois de criar um grupo de segurança de rede, um ou mais regras de segurança de rede são adicionadas ao mesmo.  Uma vez que o conjunto de regras podem ser alterados ao longo do tempo, recomenda-se ao espaço que o esquema de numeração utilizado para prioridades de regra para facilitar inserir regras adicionais ao longo do tempo.

O exemplo abaixo mostra uma regra que conceda explicitamente acesso às portas de gestão necessários para a infraestrutura do Azure para gerir e manter um ambiente de serviço de aplicações.  Tenha em atenção que todo o tráfego de gestão flui através de SSL e está protegido por certificados de cliente, para que, apesar das portas estão abertas estão inacessíveis pelos qualquer entidade que não seja a infraestrutura de gestão do Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Quando o bloqueio de acesso à porta 80 e 443 para um ambiente de serviço de aplicações por trás de dispositivos a montante ou serviços "Ocultar", terá de saber o endereço IP a montante.  Por exemplo, se estiver a utilizar uma firewall de aplicações web (WAF), o WAF terá seu próprio endereço IP (ou endereços) que utiliza quando o tráfego de ligação de proxy para um ambiente de serviço de aplicações downstream.  Terá de utilizar este endereço IP na *SourceAddressPrefix* parâmetro de uma regra de segurança de rede.

No exemplo abaixo, o tráfego de entrada de um endereço IP a montante específico for explicitamente permitido.  O endereço *1.2.3.4* é utilizado como um marcador de posição para o endereço IP de uma WAF a montante.  Altere o valor de acordo com o endereço utilizado pelo seu dispositivo a montante ou o serviço.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Se o suporte a FTP for o pretendido, as seguintes regras podem servir como um modelo para conceder acesso a dados e a porta do controlo de FTP portas de canal.  Uma vez que o FTP é um protocolo com monitoração de estado, poderá não conseguir encaminhar o tráfego FTP por meio de um dispositivo de firewall ou proxy HTTP/HTTPS tradicional.  Neste caso precisará definir o *SourceAddressPrefix* para um valor diferente - por exemplo o intervalo de endereços IP de desenvolvimento ou implementação de máquinas que FTP clientes estão a ser executados. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Nota:** o intervalo de portas de canal de dados podem ser alterados durante o período de pré-visualização.)

Se for utilizada a depuração remota com o Visual Studio, as regras seguintes demonstram como conceder acesso.  Existe uma regra separada para cada versão suportada do Visual Studio, uma vez que cada versão utiliza uma porta diferente para depuração remota.  Tal como acontece com acesso FTP, tráfego de depuração remoto não pode fluir corretamente através de uma WAF tradicional ou o dispositivo de proxy.  O *SourceAddressPrefix* em vez disso, pode ser definida para o intervalo de endereços IP dos computadores de desenvolvedores, executar o Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Atribuição de um grupo de segurança de rede a uma sub-rede
Um grupo de segurança de rede tem uma regra de segurança predefinida que nega o acesso a todo o tráfego externo.  O resultado da combinação de regras de segurança de rede descritas acima e a regra de segurança predefinida bloquear o tráfego de entrada, é que apenas o tráfego de intervalos de endereços de origem associado a um *permitir* ação será capaz de enviar tráfego para aplicações em execução num ambiente de serviço de aplicações.

Depois de um grupo de segurança de rede é preenchido com as regras de segurança, ele precisa ser atribuída à sub-rede que contém o ambiente de serviço de aplicações.  O comando de atribuição faz referência tanto o nome da rede virtual onde reside o ambiente de serviço de aplicações, bem como o nome da sub-rede em que o ambiente de serviço de aplicações foi criado.  

O exemplo abaixo mostra um grupo de segurança de rede que está sendo atribuído a uma sub-rede e rede virtual:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Assim que for concluída com êxito a atribuição de grupo de segurança de rede (a atribuição é um operações de longa execução e pode demorar alguns minutos para concluir), apenas a correspondência de tráfego de entrada *permitir* regras com êxito irão entrar em aplicações no serviço de aplicações Ambiente.

Para ser completo, o exemplo seguinte mostra como remover e, portanto, dis-associar o grupo de segurança de rede da sub-rede:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerações especiais para IP-SSL explícito
Se uma aplicação é configurada com um endereço IP SSL explícito (aplicável *apenas* para os ASEs que tenham um VIP público), em vez de usar o endereço IP do padrão do ambiente de serviço de aplicações, HTTP e HTTPS de tráfego fluxos para a sub-rede através de um conjunto diferente de portas que não sejam as portas 80 e 443.

O par individual das portas utilizadas por cada endereço IP SSL pode ser encontrado na interface de utilizador do portal do painel de experiência do Usuário de detalhes do ambiente de serviço de aplicações.  Selecionadas "All settings"--> "Endereços IP".  O painel de "Endereços IP" mostra uma tabela de todos os endereços IP SSL explicitamente configurados para o ambiente de serviço de aplicações, juntamente com o par de portas especial que é utilizado para encaminhar o tráfego HTTP e HTTPS, associado a cada endereço IP SSL.  É esse par de porta que precisa ser usado para os parâmetros de DestinationPortRange ao configurar as regras num grupo de segurança de rede.

Quando uma aplicação num ASE está configurada para utilizar o IP SSL, os clientes externos não Verão e não precisa se preocupar sobre o mapeamento do par de portas especial.  O tráfego para as aplicações irão fluir normalmente para o endereço IP SSL configurado.  A tradução para o par de portas especial automaticamente acontece internamente durante a vertente final do encaminhamento de tráfego para a sub-rede que contém o ASE. 

## <a name="getting-started"></a>Introdução
Para começar a utilizar com ambientes de serviço de aplicações, veja [introdução ao ambiente de serviço de aplicações][IntroToAppServiceEnvironment]

Para obter mais detalhes em torno de aplicações que se ligam em segurança ao recurso de back-end de um ambiente de serviço de aplicações, consulte [com segurança ligação aos recursos de back-end a partir um ambiente de serviço de aplicações][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

