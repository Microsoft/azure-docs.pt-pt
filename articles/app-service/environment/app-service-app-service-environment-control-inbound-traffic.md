---
title: Controlo do tráfego de entrada v1
description: Saiba como controlar o tráfego de entrada para um Ambiente de Serviço de Aplicações. Este doc é fornecido apenas para clientes que usam o legado v1 ASE.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962065"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Como controlar o tráfego de entrada para um ambiente de serviço de aplicações
## <a name="overview"></a>Descrição Geral
Um Ambiente de Serviço de Aplicações pode ser criado **numa** rede virtual Azure Resource Manager **ou** numa [rede virtual][virtualnetwork]de modelos de implementação clássica.  Uma nova rede virtual e uma nova sub-rede podem ser definidas no momento em que um Ambiente de Serviço de Aplicações é criado. Em vez disso, um Ambiente de Serviço de Aplicações pode ser criado numa rede virtual pré-existente e numa sub-rede pré-existente.  A partir de junho de 2016, as ASEs também podem ser implantadas em redes virtuais que utilizam tanto as gamas de endereços públicos como os espaços de endereços RFC1918 (endereços privados).  Para obter mais informações, consulte [Como Criar um Ambiente de Serviço de Aplicações.][HowToCreateAnAppServiceEnvironment]

Crie sempre um Ambiente de Serviço de Aplicações dentro de uma sub-rede. Uma sub-rede fornece uma fronteira de rede que pode ser usada para bloquear o tráfego de entrada por trás de dispositivos e serviços a montante. Esta configuração permite apenas endereços IP a montante específicos para aceitar tráfego HTTP e HTTPS.

O tráfego de rede de entrada e saída numa sub-rede é controlado através de um [grupo de segurança de rede][NetworkSecurityGroups]. Para controlar o tráfego de entrada, crie regras de segurança de rede num grupo de segurança de rede. Em seguida, atribua ao grupo de segurança da rede a sub-rede que contém o Ambiente de Serviço de Aplicações.

Uma vez que atribua um grupo de segurança de rede a uma sub-rede, o tráfego de entrada para aplicações no Ambiente de Serviço de Aplicações é permitido ou bloqueado com base nas regras de permitir e negar que são definidas no grupo de segurança da rede.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Portas de rede de entrada utilizadas num ambiente de serviço de aplicações
Antes de bloquear o tráfego de rede de entrada com um grupo de segurança de rede, conheça o conjunto de portas de rede necessárias e opcionais utilizadas por um Ambiente de Serviço de Aplicações.  Fechar acidentalmente o tráfego em algumas portas pode resultar em perda de funcionalidade num Ambiente de Serviço de Aplicações.

A lista a seguir contém as portas utilizadas por um Ambiente de Serviço de Aplicações. Todas as portas são **TCP,** salvo indicação em contrário:

* 454:  **Porto necessário** utilizado pela infraestrutura Azure para gerir e manter ambientes de serviço de aplicações via TLS.  Não bloqueie o tráfego para este porto.  Este porto está sempre ligado ao PÚBLICO VIP de um ASE.
* 455:  **Porto necessário** utilizado pela infraestrutura Azure para gerir e manter ambientes de serviço de aplicações via TLS.  Não bloqueie o tráfego para este porto.  Este porto está sempre ligado ao PÚBLICO VIP de um ASE.
* 80: Porta padrão para tráfego HTTP de entrada para aplicações em execução em Planos de Serviço de Aplicações num Ambiente de Serviço de Aplicações.  Num ASE ativado pelo ILB, esta porta está ligada ao endereço ILB da ASE.
* 443: Porta padrão para tráfego TLS de entrada para aplicações que funcionam em Planos de Serviço de Aplicações num Ambiente de Serviço de Aplicações.  Num ASE ativado pelo ILB, esta porta está ligada ao endereço ILB da ASE.
* 21: Canal de controlo para FTP.  Esta porta pode ser bloqueada com segurança se a FTP não estiver a ser utilizada.  Num ASE ativado pelo ILB, esta porta pode ser ligada ao endereço ILB para um ASE.
* 990: Canal de controlo para FTPS.  Esta porta pode ser bloqueada com segurança se o FTPS não estiver a ser utilizado.  Num ASE ativado pelo ILB, esta porta pode ser ligada ao endereço ILB para um ASE.
* 10001-10020: Canais de dados para FTP.  Tal como acontece com o canal de controlo, estas portas podem ser bloqueadas com segurança se a FTP não estiver a ser utilizada.  Num ASE ativado pelo ILB, esta porta pode ser ligada ao endereço ILB da ASE.
* 4016: Usado para depurar remotamente com Visual Studio 2012.  Esta porta pode ser bloqueada com segurança se a funcionalidade não estiver a ser utilizada.  Num ASE ativado pelo ILB, esta porta está ligada ao endereço ILB da ASE.
* 4018: Usado para depurar remotamente com Visual Studio 2013.  Esta porta pode ser bloqueada com segurança se a funcionalidade não estiver a ser utilizada.  Num ASE ativado pelo ILB, esta porta está ligada ao endereço ILB da ASE.
* 4020: Usado para depurar remotamente com o Visual Studio 2015.  Esta porta pode ser bloqueada com segurança se a funcionalidade não estiver a ser utilizada.  Num ASE ativado pelo ILB, esta porta está ligada ao endereço ILB da ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Conectividade de Saída e Requisitos de DNS
Para que um Ambiente de Serviço de Aplicações funcione corretamente, também requer acesso de saída a vários pontos finais. Uma lista completa dos pontos finais externos utilizados por um ASE está na secção "Conectividade de rede obrigatória" da Configuração de Rede para o artigo [ExpressRoute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

Os Ambientes de Serviço de Aplicações requerem uma infraestrutura de DNS válida configurada para a rede virtual.  Se a configuração do DNS for alterada após a criação de um Ambiente de Serviço de Aplicações, os desenvolvedores podem forçar um Ambiente de Serviço de Aplicações a captar a nova configuração de DNS.  Se ativar um reboot de ambiente rolante utilizando o ícone **Restart,** o ambiente capta a nova configuração de DNS. (O ícone **Restart** está localizado no topo da lâmina de gestão do Ambiente do Serviço de Aplicações, no [portal Azure][NewPortal].)

Também é recomendado que quaisquer servidores DNS personalizados na vnet sejam configurado antes de criar um Ambiente de Serviço de Aplicações.  Se a configuração de DNS de uma rede virtual for alterada durante a criação de um Ambiente de Serviço de Aplicações, o processo de criação do App Service Environment falhará.  Da mesma forma, se houver um servidor DNS personalizado que seja inacessível ou indisponível na outra extremidade de um gateway VPN, o processo de criação do App Service Environment também falhará.

## <a name="creating-a-network-security-group"></a>Criar um Grupo de Segurança de Rede
Para obter informações completas sobre o funcionação dos grupos de segurança da rede, consulte as [seguintes informações.][NetworkSecurityGroups]  O exemplo de Gestão de Serviços Azure abaixo toca em destaques de grupos de segurança de rede. O exemplo configura e aplica um grupo de segurança de rede a uma sub-rede que contém um Ambiente de Serviço de Aplicações.

**Nota:** Os grupos de segurança da rede podem ser configurados graficamente utilizando o [portal Azure](https://portal.azure.com) ou através do Azure PowerShell.

Os grupos de segurança de rede são criados pela primeira vez como uma entidade autónoma associada a uma subscrição. Uma vez que os grupos de segurança de rede são criados numa região do Azure, crie o grupo de segurança da rede na mesma região que o App Service Environment.

O seguinte comando demonstra a criação de um grupo de segurança de rede:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Uma vez criado um grupo de segurança de rede, uma ou mais regras de segurança da rede são adicionadas ao mesmo.  Uma vez que o conjunto de regras pode mudar ao longo do tempo, deve espaçar o esquema de numeração utilizado para as prioridades de regras. Esta prática facilita a inserção de regras adicionais ao longo do tempo.

No exemplo abaixo, uma regra concede explicitamente acesso aos portos de gestão necessários pela infraestrutura Azure para gerir e manter um Ambiente de Serviço de Aplicações.  Todo o tráfego de gestão flui sobre o TLS e é garantido por certificados de cliente. Mesmo que os portos estejam abertos, são inacessíveis por qualquer entidade que não a infraestrutura de gestão Azure.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Quando bloquear o acesso à porta 80 e 443 para "esconder" um Ambiente de Serviço de Aplicações por trás de dispositivos ou serviços a montante, lembre-se do endereço IP a montante.  Por exemplo, se estiver a utilizar uma firewall de aplicação web (WAF), o WAF terá o seu próprio endereço IP ou endereços. O WAF utiliza-os quando procura o tráfego para um Ambiente de Serviço de Aplicações a jusante.  Terá de utilizar este endereço IP no parâmetro *SourceAddressPrefix* de uma regra de segurança de rede.

No exemplo abaixo, o tráfego de entrada a partir de um endereço IP a montante específico é explicitamente permitido.  O endereço *1.2.3.4* é utilizado como espaço reservado para o endereço IP de um WAF a montante.  Altere o valor para corresponder ao endereço utilizado pelo seu dispositivo ou serviço a montante.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Se o suporte FTP for desejado, utilize as seguintes regras como modelo para conceder acesso à porta de controlo FTP e às portas dos canais de dados.  Uma vez que a FTP é um protocolo imponente, pode não ser capaz de encaminhar o tráfego FTP através de uma firewall http/HTTPS tradicional ou dispositivo de procuração.  Neste caso, terá de definir o *SourceAddressPrefix* para um valor diferente, como a gama de endereços IP de programadores ou máquinas de implantação em que os clientes FTP estão a executar. 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

**(Nota:** a gama de portas do canal de dados pode mudar durante o período de pré-visualização.)

Se for utilizada a depuragem remota com o Visual Studio, as seguintes regras demonstram como conceder acesso.  Há uma regra separada para cada versão suportada do Visual Studio uma vez que cada versão usa uma porta diferente para depurar remotamente.  Tal como acontece com o acesso FTP, o tráfego de depuragem remota pode não fluir corretamente através de um dispositivo tradicional waf ou proxy.  O *SourceAddressPrefix* pode, em vez disso, ser definido para a gama de endereços IP de máquinas desenvolvedoras que executam o Visual Studio.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Atribuir um Grupo de Segurança de Rede a uma Sub-rede
Um grupo de segurança de rede tem uma regra de segurança padrão que nega o acesso a todo o tráfego externo. Quando combinar esta regra com as regras de segurança da rede acima, apenas o tráfego das gamas de endereços de origem associadas a uma ação *Allow* será capaz de enviar tráfego para aplicações que funcionam num Ambiente de Serviço de Aplicações.

Depois de um grupo de segurança de rede ser preenchido com regras de segurança, atribua-o à sub-rede que contém o Ambiente de Serviço de Aplicações.  O comando de atribuição refere dois nomes: o nome da rede virtual onde se encontra o Ambiente de Serviço de Aplicações e o nome da sub-rede onde foi criado o Ambiente de Serviço de Aplicações.  

O exemplo abaixo mostra um grupo de segurança de rede a ser atribuído a uma rede de sub-redes e virtual:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

A missão é uma operação de longa duração, e pode levar alguns minutos para ser concluída. Uma vez que a atribuição do grupo de segurança da rede tenha sucesso, apenas o tráfego de entrada que *corresponda As* regras permitirão que as regras cheguem com sucesso a aplicações no Ambiente de Serviço de Aplicações.

Para a completude, o exemplo a seguir mostra como remover e dissociar o grupo de segurança da rede da sub-rede:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considerações Especiais para IP-SSL explícito
Se uma aplicação for configurada com um endereço IP-SSL explícito (aplicável *apenas* às ASEs que têm um VIP público), em vez de utilizar o endereço IP predefinido do Ambiente de Serviço de Aplicações, tanto o tráfego HTTP como HTTPS flui para a sub-rede sobre portas que não as portas 80 e 443.

Para encontrar o par individual de portas que é usado por cada endereço IP-SSL, vá ao portal e veja os detalhes do Ambiente de Serviço de Aplicação.  Selecione **Todos os**  >  **endereços IP** de definições .  A lâmina **de endereços IP** mostra uma tabela de todos os endereços IP-SSL explicitamente configurados para o Ambiente de Serviço de Aplicações. A lâmina também mostra o par de portas especial que é usado para o tráfego HTTP e HTTPS associado a cada endereço IP-SSL.  Utilize este par de portas para os parâmetros DestinationPortRange ao configurar as regras num grupo de segurança de rede.

Quando uma aplicação num ASE é configurada para usar IP-SSL, os clientes externos não verão ou precisam de se preocupar com o mapeamento especial do par de portas.  O tráfego para as aplicações fluirá normalmente para o endereço IP-SSL configurado.  A tradução para o par especial da porta ocorre automaticamente internamente, durante a última etapa do tráfego de encaminhamento para a sub-rede que contém o ASE. 

## <a name="getting-started"></a>Introdução
Para começar com os Ambientes de Serviço de Aplicações, consulte [Introdução ao Ambiente de Serviço de Aplicações.][IntroToAppServiceEnvironment]

Para obter mais informações, consulte [a ligação segura aos recursos backend de um Ambiente de Serviço de Aplicações.][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->