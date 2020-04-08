---
title: Controlar o tráfego de entrada v1
description: Saiba como controlar o tráfego de entrada para um Ambiente de Serviço de Aplicações. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 857b2b00aadced567bc8ac191cdd9908f7bea7a3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804406"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Como controlar o tráfego de entrada para um ambiente de serviço de aplicações
## <a name="overview"></a>Descrição geral
Um Ambiente de Serviço de Aplicações pode ser criado **numa** rede virtual do Azure Resource Manager, **ou** numa [rede virtual][virtualnetwork]modelo de implementação clássica.  Uma nova rede virtual e uma nova subnet podem ser definidas no momento em que um App Service Environment é criado.  Alternativamente, um Ambiente de Serviço de Aplicações pode ser criado numa rede virtual pré-existente e numa subnet pré-existente.  Com uma alteração feita em junho de 2016, as ASE também podem ser implantadas em redes virtuais que utilizam faixas de endereços públicos, ou espaços de endereçoS RFC1918 (isto é, endereços privados).  Para mais detalhes sobre a criação de um app service environment, veja como criar um ambiente de serviço de [aplicações.][HowToCreateAnAppServiceEnvironment]

Um Ambiente de Serviço de Aplicações deve ser sempre criado dentro de uma subnet porque uma subnet fornece um limite de rede que pode ser usado para bloquear o tráfego de entrada atrás de dispositivos e serviços a montante, de modo a que o tráfego HTTP e HTTPS só seja aceite a partir de endereços IP específicos a montante.

O tráfego de rede de entrada e saída numa subnet é controlado através de um grupo de segurança da [rede][NetworkSecurityGroups]. O controlo do tráfego de entrada requer a criação de regras de segurança da rede num grupo de segurança de rede e, em seguida, atribuir ao grupo de segurança da rede a subrede que contém o App Service Environment.

Uma vez que um grupo de segurança de rede é atribuído a uma subnet, o tráfego de entrada para aplicações no App Service Environment é permitido/bloqueado com base nas regras de permitir e negar definidas no grupo de segurança da rede.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Portas de rede de entrada usadas em um ambiente de serviço de aplicações
Antes de bloquear o tráfego de rede de entrada com um grupo de segurança de rede, é importante conhecer o conjunto de portas de rede necessárias e opcionais utilizadas por um App Service Environment.  O encerramento acidental do tráfego em algumas portas pode resultar na perda de funcionalidade num Ambiente de Serviço de Aplicações.

Segue-se uma lista de portas utilizadas por um Ambiente de Serviço de Aplicações. Todas as portas são **TCP,** salvo indicação em contrário:

* 454: **Porta requerida** utilizada pela infraestrutura Azure para gerir e manter ambientes de serviço de aplicações via TLS.  Não bloqueie o tráfego para este porto.  Este porto está sempre ligado ao VIP público de uma ASE.
* 455: **Porta requerida** utilizada pela infraestrutura Azure para gerir e manter ambientes de serviço de aplicações via TLS.  Não bloqueie o tráfego para este porto.  Este porto está sempre ligado ao VIP público de uma ASE.
* 80: Porta padrão para tráfego HTTP de entrada para aplicações em execução em Planos de Serviço de Aplicações em um Ambiente de Serviço de Aplicações.  Numa ASE ativada pelo ILB, esta porta está ligada ao endereço ILB da ASE.
* 443: Porta padrão para tráfego TLS de entrada para aplicações em execução em Planos de Serviço de Aplicações em um Ambiente de Serviço de Aplicações.  Numa ASE ativada pelo ILB, esta porta está ligada ao endereço ILB da ASE.
* 21: Canal de controlo para FTP.  Esta porta pode ser bloqueada com segurança se o FTP não estiver a ser utilizado.  Numa ASE ativada pelo ILB, esta porta pode ser ligada ao endereço ILB para uma ASE.
* 990: Canal de controlo para FTPS.  Esta porta pode ser bloqueada com segurança se o FTPS não estiver a ser utilizado.  Numa ASE ativada pelo ILB, esta porta pode ser ligada ao endereço ILB para uma ASE.
* 10001-10020: Canais de dados para FTP.  Tal como acontece com o canal de controlo, estas portas podem ser bloqueadas com segurança se o FTP não estiver a ser utilizado.  Numa ASE ativada pelo ILB, esta porta pode ser ligada ao endereço ILB da ASE.
* 4016: Usado para depuração remota com O Estúdio Visual 2012.  Esta porta pode ser bloqueada com segurança se a funcionalidade não estiver a ser utilizada.  Numa ASE ativada pelo ILB, esta porta está ligada ao endereço ILB da ASE.
* 4018: Usado para depuração remota com o Visual Studio 2013.  Esta porta pode ser bloqueada com segurança se a funcionalidade não estiver a ser utilizada.  Numa ASE ativada pelo ILB, esta porta está ligada ao endereço ILB da ASE.
* 4020: Usado para depuração remota com o Visual Studio 2015.  Esta porta pode ser bloqueada com segurança se a funcionalidade não estiver a ser utilizada.  Numa ASE ativada pelo ILB, esta porta está ligada ao endereço ILB da ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividade de Saída e Requisitos de DNS
Para que um Ambiente de Serviço de Aplicações funcione corretamente, também requer acesso de saída a vários pontos finais. Uma lista completa dos pontos finais externos utilizados por uma ASE está na secção "Conectividade de Rede Necessária" da Configuração da Rede para artigo [ExpressRoute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

Os Ambientes de Serviço de Aplicações requerem uma infraestrutura DNS válida configurada para a rede virtual.  Se, por alguma razão, a configuração do DNS for alterada após a criação de um Ambiente de Serviço de Aplicações, os desenvolvedores podem forçar um Ambiente de Serviço de Aplicação a captar a nova configuração DNS.  Desencadear um reboot ambiente rolante utilizando o ícone "Restart" localizado no topo da lâmina de gestão do ambiente do serviço de aplicações no [portal Azure][NewPortal] fará com que o ambiente apanhe a nova configuração DNS.

Recomenda-se também que quaisquer servidores DNS personalizados no vnet sejam configurados antes do tempo antes de criar um Ambiente de Serviço de Aplicações.  Se a configuração dNS de uma rede virtual for alterada enquanto um Ambiente de Serviço de Aplicações está a ser criado, isso resultará na falha do processo de criação do App Service Environment.  Na mesma linha, se um servidor DNS personalizado existir na outra extremidade de um gateway VPN, e o servidor DNS estiver inacessível ou indisponível, o processo de criação do App Service Environment também falhará.

## <a name="creating-a-network-security-group"></a>Criar um Grupo de Segurança de Rede
Para obter mais detalhes sobre como os grupos de segurança da rede funcionam, consulte as [seguintes informações][NetworkSecurityGroups].  O exemplo de Gestão de Serviços Azure abaixo toca em destaques de grupos de segurança de rede, com foco em configurar e aplicar um grupo de segurança de rede a uma subnet que contém um App Service Environment.

**Nota:** Os grupos de segurança da rede podem ser configurados graficamente utilizando o [Portal Azure](https://portal.azure.com) ou através do Azure PowerShell.

Os grupos de segurança da rede são criados pela primeira vez como uma entidade autónoma associada a uma subscrição. Uma vez que os grupos de segurança da rede são criados numa região do Azure, asseguram que o grupo de segurança da rede é criado na mesma região que o App Service Environment.

O seguinte demonstra a criação de um grupo de segurança de rede:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Uma vez criado um grupo de segurança de rede, uma ou mais regras de segurança da rede são adicionadas a ele.  Uma vez que o conjunto de regras pode mudar ao longo do tempo, recomenda-se que se espaça o regime de numeração utilizado para as prioridades de regras, a fim de facilitar a inserção de regras adicionais ao longo do tempo.

O exemplo abaixo mostra uma regra que concede explicitamente o acesso aos portos de gestão necessários pela infraestrutura Azure para gerir e manter um Ambiente de Serviço de Aplicações.  Note que todo o tráfego de gestão flui sobre TLS e é garantido por certificados de cliente, pelo que, embora os portos sejam abertos, são inacessíveis por qualquer outra entidade que não a infraestrutura de gestão Azure.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Ao bloquear o acesso aos portes 80 e 443 para "esconder" um Ambiente de Serviço de Aplicações atrás de dispositivos ou serviços a montante, terá de conhecer o endereço IP a montante.  Por exemplo, se estiver a utilizar uma firewall de aplicação web (WAF), o WAF terá o seu próprio endereço IP (ou endereços) que utiliza ao procurar tráfego para um ambiente de serviço de aplicações a jusante.  Terá de utilizar este endereço IP no parâmetro *SourceAddressPrefix* de uma regra de segurança da rede.

No exemplo abaixo, o tráfego de entrada a partir de um endereço IP específico a montante é explicitamente permitido.  O endereço *1.2.3.4* é utilizado como espaço reservado para o endereço IP de um WAF a montante.  Altere o valor para corresponder ao endereço utilizado pelo seu dispositivo ou serviço a montante.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Se o suporte ftp for desejado, as seguintes regras podem ser utilizadas como modelo para permitir o acesso à porta de controlo ftp e às portas dos canais de dados.  Uma vez que o FTP é um protocolo imponente, pode não ser capaz de encaminhar o tráfego FTP através de uma firewall tradicional HTTP/HTTPS ou dispositivo proxy.  Neste caso, terá de definir o *SourceAddressPrefix* para um valor diferente - por exemplo, a gama de endereços IP de desenvolvedor ou máquinas de implantação em que os clientes FTP estão a funcionar. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

**(Nota:** a gama de portas do canal de dados pode mudar durante o período de pré-visualização.)

Se for utilizada uma depuração remota com o Visual Studio, as seguintes regras demonstram como conceder acesso.  Existe uma regra separada para cada versão suportada do Visual Studio, uma vez que cada versão utiliza uma porta diferente para depuração remota.  Tal como acontece com o acesso ftp, o tráfego de depuração remoto não pode fluir corretamente através de um dispositivo waf tradicional ou proxy.  O *SourceAddressPrefix* pode, em vez disso, ser definido para a gama de endereços IP das máquinas de desenvolvimento que executam o Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Atribuir um Grupo de Segurança de Rede a uma Subnet
Um grupo de segurança de rede tem uma regra de segurança padrão que nega o acesso a todo o tráfego externo.  O resultado da combinação das regras de segurança da rede acima descritas, e da regra de segurança padrão que bloqueia o tráfego de entrada, é que apenas o tráfego das gamas de endereços de origem associadas a uma ação *de permitir* será capaz de enviar tráfego para aplicações que executam um App Service Environment.

Depois de um grupo de segurança de rede ser preenchido com regras de segurança, este tem de ser atribuído à subnet que contém o Ambiente de Serviço de Aplicações.  O comando de atribuição refere tanto o nome da rede virtual onde reside o App Service Environment, como o nome da subnet onde foi criado o Ambiente de Serviço de Aplicações.  

O exemplo abaixo mostra um grupo de segurança da rede a ser atribuído a uma rede de sub-rede e virtual:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Uma vez que a atribuição do grupo de segurança da rede seja bem sucedida (a atribuição é uma operação de longo prazo e pode demorar alguns minutos a ser concluída), apenas a correspondência de tráfego de entrada *Permitir* regras chegará com sucesso a aplicações no App Service Environment.

Para completar, o exemplo seguinte mostra como remover e, assim, dissociar o grupo de segurança da rede da sub-rede:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerações especiais para IP-SSL explícito
Se uma aplicação for configurada com um endereço IP-SSL explícito (aplicável *apenas* às ASEs que possuem um VIP público), em vez de utilizar o endereço IP padrão do Ambiente de Serviço de Aplicações, tanto o tráfego HTTP como o HTTPS fluem para a subrede sobre um conjunto diferente das portas 80 e 443.

O par individual de portas utilizadas por cada endereço IP-SSL pode ser encontrado na interface de utilizador do portal a partir dos detalhes do App Service Environment.  Selecione "Todas as definições" --> "endereços IP".  A lâmina "endereços IP" mostra uma tabela de todos os endereços IP-SSL explicitamente configurados para o Ambiente de Serviço de Aplicações, juntamente com o par de porta especial que é usado para direcionar o tráfego HTTP e HTTPS associado a cada endereço IP-SSL.  É este par de portas que precisa de ser usado para os parâmetros DestinationPortRange ao configurar regras num grupo de segurança de rede.

Quando uma aplicação numa ASE é configurada para utilizar IP-SSL, os clientes externos não verão e não precisam de se preocupar com o mapeamento especial do par de portas.  O tráfego para as aplicações fluirá normalmente para o endereço IP-SSL configurado.  A tradução para o par de portas especial ocorre automaticamente internamente durante a última etapa do tráfego de encaminhamento para a subrede que contém a ASE. 

## <a name="getting-started"></a>Introdução
Para começar com ambientes de serviço de aplicações, consulte [Introdução ao Ambiente de Serviço de Aplicações][IntroToAppServiceEnvironment]

Para mais detalhes em torno de apps que se conectam de forma segura para apoiar o recurso a partir de um Ambiente de Serviço de Aplicações, consulte [a ligação segura aos recursos Backend a partir de um Ambiente de Serviço][SecurelyConnecttoBackend] de Aplicações

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

