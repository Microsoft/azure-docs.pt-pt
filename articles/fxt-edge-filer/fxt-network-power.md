---
title: 'Tutorial: Conecte os cabos a um Azure FXT Edge Filer'
description: Como cabo as portas de rede e anexar energia para hardware Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 80458d99ed6ad19c8a837f0989798d4dcc156b61
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219535"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Tutorial: Faça ligações de rede e forneça energia ao nó de Filer de Borda Azure FXT

Este tutorial ensina-lhe a cabo as ligações de rede para um nó de hardware Azure FXT Edge Filer.

Neste tutorial, irá aprender:

> [!div class="checklist"]
>
> * Como escolher o tipo de cabo de rede para o seu ambiente
> * Como ligar um nó de ficheiro Azure FXT Edge filer à sua rede de datacenter
> * Como encaminhar os cabos através do braço de gestão do cabo (CMA)
> * Como ligar a energia ao dispositivo a cremado e ligá-la

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, o Azure FXT Edge Filer deve ser instalado num rack de equipamento padrão. O CMA deve ser instalado no nó de ficheiro.

## <a name="identify-ports"></a>Identificar portas

Identifique as várias portas na parte de trás do seu Azure FXT Edge Filer.

![Parte de trás de um dispositivo por cabo](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Ligar o dispositivo

* Ligue as portas RJ-45 à fonte de rede do seu centro de dados, conforme descrito nas [portas da Rede](#network-ports).  
* Ligue de forma segura a [porta iDRAC](#idrac-port) a uma rede separada com um servidor DHCP seguro.
* Utilize as portas USB e a porta VGA para ligar um teclado e monitor ao nó para a configuração inicial. Tem de iniciar o nó e [definir uma palavra-passe inicial](fxt-node-password.md) para ativar as outras portas do nó. Leia [as palavras-passe iniciais](fxt-node-password.md) para mais detalhes.

Este artigo também descreve como ligar a [energia CA](#connect-power-cables) para o nó.

Este artigo também explica como ligar à [porta de série](#serial-port-only-when-necessary)do nó, se necessário para a resolução de problemas especializados.

### <a name="network-ports"></a>Portas de rede

Cada nó de filete de borda Azure FXT inclui as seguintes portas de rede:

* Seis portas de dados de alta velocidade de 25GbE/10GbE:

  * Quatro portas fornecidas por dois adaptadores de rede plug-in de duas portas
  * Duas portas fornecidas pelo adaptador de rede mezanino de motherboard

* Duas portas de 1GbE fornecidas pelo adaptador de rede mezanino de motherboard

As portas de dados de alta velocidade de 25GbE/10GbE têm gaiolas compatíveis com SFP28 padrão. Para utilizar cabos óticos, deve instalar módulos de transceptore ótico SFP28 (não fornecidos).

As portas de 1GbE têm conectores RJ-45 padrão.

Para obter uma lista completa de cabos, interruptores e transceptores suportados, consulte a [Matriz de Interoperabilidade da Série Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

O tipo de ligações a utilizar para o seu sistema depende do ambiente do seu data center.

* Se ligar a uma rede de 25GbE, por cabo cada uma das portas de dados de alta velocidade com um dos seguintes tipos de cabo:

  * Cabo ótico e transceptor ótico SFP28 com capacidade de 25GbE/10GbE de 25GbE ou de dupla taxa
  * Cabo twinaxial de ligação gexial do tipo SFP28 25GbE

* Se ligar a uma rede de 10GbE, por cabo cada uma das portas de dados de alta velocidade com uma das seguintes:

  * Cabo ótico e transceptor ótico SFP28 com capacidade de 10GbE ou de dupla taxa 25GbE/10GbE.
  * Cabo twinaxial de ligação gexial do tipo SFP28 25GbE
  * Cabo twinaxial de ligação gexial do tipo SFP28 tipo 10GbE

* As portas de rede de 1GbE são utilizadas para o tráfego de gestão de clusters. Verifique a opção **de rede Use 1Gb mgmt** ao criar o cluster se pretender criar uma rede fisicamente separada para configuração do cluster (descrita em [Configurar a rede de gestão).](fxt-cluster-create.md#configure-the-management-network) Cabo as portas com cat5 padrão ou cabo melhor, conforme descrito na lista de cabos suportados.

  Pode deixar as portas de 1GbE sem visibilidade se pretender utilizar as portas de alta velocidade para todo o tráfego. Por predefinição, as portas de rede de 1GbE não são utilizadas se estiver disponível uma porta de dados de maior velocidade.  

### <a name="idrac-port"></a>porta iDRAC  

O iDRAC com a etiqueta da porta é uma ligação de 1Gb que permite a comunicação com um controlador de acesso remoto utilizado para a gestão e monitorização de hardware. O software FXT utiliza a Interface de Gestão de Plataformas Inteligentes (IPMI) com este controlador para resolução e recuperação de problemas. Pode utilizar a [interface iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) incorporada para monitorizar o hardware através desta porta. O acesso iDRAC e IPMI são ativados por padrão.

> [!Note]
> A porta iDRAC pode contornar o sistema operativo e interagir diretamente com hardware no nó.

Utilize estas estratégias de segurança ao ligar e configurar a porta iDRAC:

* Apenas ligue as portas iDRAC a uma rede que esteja fisicamente separada da rede de dados utilizada para aceder ao cluster.
* Desagure uma senha de administrador iDRAC segura em cada nó. Tem de definir esta palavra-passe para ativar o hardware - siga as instruções em [set hardware passwords](fxt-node-password.md).
* A configuração padrão da porta iDRAC utiliza DHCP e IPv4 para atribuição de endereço IP. Certifique-se de que o seu ambiente DHCP está bem protegido e que as ligações são restritas entre os clientes DHCP e o servidor DHCP. (O painel de controlo do cluster inclui definições para alterar o método de configuração do endereço dos nós após a criação do cluster.)
* Deixe a porta iDRAC definida como "modo dedicado" (o padrão), que restringe o tráfego de rede iDRAC/IPMI à porta dedicada RJ-45.

A porta iDRAC não necessita de uma ligação de rede de alta velocidade.
  
### <a name="serial-port-only-when-necessary"></a>Porta em série (apenas quando necessário)

Em algumas situações, o Microsoft Service and Support pode dizer-lhe para ligar um terminal à porta de série de um nó para diagnosticar um problema.

Para fixar a consola:

1. Localize a porta de série (COM1) na parte traseira do nó de ficheiro de borda FXT.
1. Utilize um cabo de modem nulo para ligar a porta de série a um terminal configurado para ANSI-115200-8N1.
1. Inscreva-se na consola e tome outros passos como indicado pela equipa de apoio.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Cabos de rota no braço de gestão do cabo (CMA)

Cada nó de filete de borda Azure FXT vem com um braço de gestão de cabo opcional. O CMA simplifica o encaminhamento do cabo e proporciona um acesso mais fácil à parte de trás do chassis sem necessidade de desligar os cabos.

Siga estas instruções para encaminhar os cabos através da CMA:

1. Utilizando os invólucros fornecidos, junte os cabos à medida que entram e saem dos cestos para que não interfiram com os sistemas adjacentes (1).
1. Com o CMA na posição de serviço, encaminhe o feixe de cabo através dos cestos internos e externos (2).
1. Utilize as correias pré-instaladas em ambos os lados dos cestos para fixar os cabos (3).
1. Volte a colocar a CMA no lugar na bandeja (4).
1. Instale o cabo indicador de estado na parte de trás do sistema e fixe o cabo encaminhando-o através da CMA. Fixe a outra extremidade do cabo ao canto do cesto exterior da CMA (5).

   > [!CAUTION]
   > Para evitar potenciais danos causados por cabos salientes, fixe qualquer folga no cabo indicador de estado depois de encaminhar este cabo através da CMA.

![Ilustração de CMA com cabos instalados](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
> Se não instalou o CMA, utilize as duas correias de gancho e de laço fornecidas no kit ferroviário para encaminhar os cabos na parte de trás do seu sistema.
>
>  1. Localize os suportes exteriores da CMA nos lados interiores de ambos os flanges de cremalheira.
>  2. Junte os cabos suavemente, puxando-os para fora dos conectores do sistema para os lados esquerdo e direito.
>  3. Passe as correias do gancho e do laço através das ranhuras ferramentadas nos suportes exteriores da CMA de cada lado do sistema para fixar os feixes de cabo.
>
>     ![Cabos encaminhados sem CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Sobre os requisitos de endereço IP

Para nós de hardware numa cache de armazenamento híbrido Azure FXT Edge Filer, os endereços IP são geridos pelo software de cluster.

Cada nó requer pelo menos um endereço IP, mas os endereços de nó são atribuídos quando os nós são adicionados ou removidos do cluster.

O número total de endereços IP necessários depende do número de nós que compõem o seu cache.

Configure a gama de endereços IP utilizando o software do Painel de Controlo após a instalação dos nós. Para saber mais, leia [Recolher informações para o cluster.](fxt-cluster-create.md#gather-information-for-the-cluster)  

## <a name="connect-power-cables"></a>Ligar cabos de alimentação

Cada nó de Filete de Borda Azure FXT utiliza duas unidades de alimentação (PSUs).

> [!TIP]
> Para tirar partido das duas PSUs redundantes, ligue cada cabo de alimentação CA a uma unidade de distribuição de energia (PDU) num circuito de ramificação independente.
>
> Pode utilizar um UPS para alimentar os PDUs para uma proteção extra.

1. Ligue os cabos de alimentação incluídos às PSUs no chassis. Certifique-se de que os cabos e as PSUs estão completamente sentados.
1. Fixe os cabos de alimentação às unidades de distribuição de energia do cesto do equipamento. Se possível, utilize duas fontes de alimentação separadas para os dois cabos.

### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Alimentação num nó de filete de borda Azure FXT

Para ligar o nó, pressione o botão de alimentação na parte frontal do sistema. O botão está no painel de controlo do lado direito.

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Desligar um nó de filete de borda Azure FXT

O botão de alimentação pode ser utilizado para desligar o sistema durante os testes e antes de o adicionar a um cluster. No entanto, depois de um nó Azure FXT Edge Filer ser utilizado como parte de um cluster, deve utilizar o software do painel de controlo do cluster para desligar o hardware. Leia Como desligar com segurança o [hardware do Azure FXT Edge Filer](fxt-power-off.md) para obter mais detalhes.

## <a name="next-steps"></a>Passos seguintes

Depois de terminar de ligar o hardware, ligue cada um dos nós e inicialize-os definindo as suas palavras-passe de raiz.
> [!div class="nextstepaction"]
> [Definir senhas iniciais](fxt-node-password.md)
