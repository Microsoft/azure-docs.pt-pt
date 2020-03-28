---
title: 'Tutorial: Ligue cabos a um Ficheiro de Borda Azure FXT'
description: Como cabo das portas de rede e anexar energia para hardware De Ficheiro selada Azure FXT Edge
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 1e3c60fd955bd899955c46b7670acd3f088d0f86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239785"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Tutorial: Faça ligações de rede e alimentação ao nó De arquivo de borda Azure FXT

Este tutorial ensina-o a cabo as ligações de rede para um nó de hardware De Hardware Azure FXT Edge Filer.

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * Como escolher o tipo de cabo de rede para o seu ambiente
> * Como ligar um nó de Filer de Borda Azure FXT à sua rede datacenter
> * Como encaminhar cabos através do braço de gestão do cabo (CMA)
> * Como ligar a energia ao dispositivo amassado e ligá-lo

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, o Ficheiro de Borda Azure FXT deve ser instalado num rack de equipamento padrão. A CMA deve ser instalada no nó do filer. 

## <a name="identify-ports"></a>Identificar portas

Identifique as várias portas na parte de trás do seu Ficheiro de Borda Azure FXT. 
 
![Parte de trás de um dispositivo por cabo](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Ligar o dispositivo

* Ligue as portas RJ-45 à fonte de rede do seu centro de dados, conforme descrito nas [portas da Rede](#network-ports).  
* Ligue a [porta iDRAC](#idrac-port) de forma segura a uma rede separada com um servidor DHCP seguro. 
* Utilize as portas USB e a porta VGA para ligar um teclado e monitorizar ao nó para a configuração inicial. Deve iniciar o nó e [definir uma senha inicial](fxt-node-password.md) para ativar as outras portas do nó. Leia [Definir as palavras-passe iniciais](fxt-node-password.md) para mais detalhes. 

Este artigo também descreve como ligar a [energia CA](#connect-power-cables) para o nó. 

Este artigo também explica como se conectar à [porta de série](#serial-port-only-when-necessary)do nó, se necessário para resolução de problemas especializados. 

### <a name="network-ports"></a>Portas de rede 

Cada nó de Filer de Borda Azure FXT inclui as seguintes portas de rede: 

* Seis portas de dados de taxa dupla de alta velocidade 25GbE/10GbE: 

  * Quatro portas fornecidas por dois adaptadores de rede plug-in de duas portas
  * Dois portos fornecidos pelo adaptador de rede de mezanino motherboard 

* Duas portas 1GbE fornecidas pelo adaptador de rede de mezanino motherboard 

As portas de dados de alta velocidade 25GbE/10GbE têm gaiolas compatíveis com SFP28 padrão. Para utilizar cabos óticos, deve instalar módulos de transceptor ótico SFP28 (não fornecidos).

As portas 1GbE têm conectores RJ-45 padrão.

Para obter uma lista completa de cabos, interruptores e transceptores suportados, consulte a Matriz de [Interoperabilidade da Série Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

O tipo de ligações a utilizar para o seu sistema depende do ambiente do seu data center.

* Se ligar a uma rede de 25Gbe, cabo cada uma das portas de dados de alta velocidade com um dos seguintes tipos de cabo:

  * Cabo ótico e transceptor ótico SFP28 com capacidade de 25GbE ou de dupla taxa 25GbE/10GbE
  * SFP28 tipo 25GbE cabo twinaxial capaz de ligar

* Se ligar a uma rede de 10GbE, cabo cada uma das portas de dados de alta velocidade com uma das seguintes: 

  * Cabo ótico e transceptor ótico SFP28 com capacidade de 10GbE ou de taxa dupla 25GbE/10GbE.
  * SFP28 tipo 25GbE cabo twinaxial capaz de ligar
  * SFP28 tipo 10GbE cabo twinaxial capaz de ligar

* As portas de rede 1GbE são utilizadas para o tráfego de gestão de clusters. Verifique a opção **de rede Use 1Gb mgmt** ao criar o cluster se pretender criar uma rede fisicamente separada para a configuração do cluster (descrita no [Configure a rede](fxt-cluster-create.md#configure-the-management-network)de gestão ). Cabo as portas com cat5 padrão ou melhor cabo conforme descrito na lista de cabos suportados.

  Pode deixar as portas 1GbE sem cabo se pretender utilizar as portas de alta velocidade para todo o tráfego. Por predefinição, as portas de rede 1GbE não são utilizadas se estiver disponível uma porta de dados de maior velocidade.  

### <a name="idrac-port"></a>porta iDRAC  

A porta com o rótulo iDRAC é uma ligação de 1Gb que permite a comunicação com um controlador de acesso remoto utilizado para a gestão e monitorização de hardware. O software FXT utiliza a Interface inteligente de gestão de plataformas (IPMI) com este controlador para resolução e recuperação de problemas. Pode utilizar a [interface iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) incorporada para monitorizar o hardware através desta porta. O acesso iDRAC e IPMI são ativados por defeito. 

> [!Note]
> A porta iDRAC pode contornar o sistema operativo e interagir diretamente com hardware no nó. 

Utilize estas estratégias de segurança ao ligar e configurar a porta iDRAC:

* Apenas ligue as portas iDRAC a uma rede fisicamente separada da rede de dados utilizada para aceder ao cluster.
* Detete uma senha de administrador iDRAC segura em cada nó. Tem de definir esta palavra-passe para ativar o hardware - siga as instruções nas [palavras-passe do hardware .](fxt-node-password.md)
* A configuração da porta iDRAC predefinida utiliza DHCP e IPv4 para a atribuição de endereços IP. Certifique-se de que o seu ambiente DHCP está bem protegido e que as ligações são restritas entre os clientes DHCP e o servidor DHCP. (O painel de controlo do cluster inclui definições para alterar o método de configuração do endereço dos nós após a criação do cluster.)
* Deixe a porta iDRAC definida como "modo dedicado" (o predefinido), que restringe o tráfego da rede iDRAC/IPMI à porta rJ-45 dedicada.

A porta iDRAC não requer uma ligação de rede de alta velocidade.
  
### <a name="serial-port-only-when-necessary"></a>Porta de série (apenas quando necessário)

Em algumas situações, o Microsoft Service and Support pode dizer-lhe para ligar um terminal à porta de série de um nó para diagnosticar um problema.  

Para fixar a consola:

1. Localize a porta de série (COM1) na parte traseira do nó FXT Edge Filer.
1. Utilize um cabo de modem nulo para ligar a porta de série a um terminal configurado para ANSI-115200-8N1.
1. Inscreva-se na consola e tome outras etapas, conforme orientado pela equipa de suporte.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Cabos de rota no braço de gestão do cabo (CMA)

Cada nó de Filer de Borda Azure FXT vem com um braço de gestão de cabo opcional. A CMA simplifica o encaminhamento por cabo e proporciona um acesso mais fácil à parte de trás do chassis sem necessidade de desligar os cabos. 

Siga estas instruções para encaminhar os cabos através da CMA: 

1. Utilizando os invólucros de gravata fornecidos, junte os cabos à medida que entram e saem dos cestos para que não interfiram com os sistemas adjacentes (1).
1. Com o CMA na posição de serviço, encaminhe o pacote do cabo através dos cestos internos e externos (2).
1. Utilize as correias de gancho e laço pré-instaladas em ambas as extremidades dos cestos para fixar os cabos (3).
1. Volte a colocar o CMA no lugar na bandeja (4).
1. Instale o cabo indicador de estado na parte de trás do sistema e fixe o cabo encaminhando-o através do CMA. Fixe a outra extremidade do cabo ao canto do cesto exterior cma (5). 

   > [!CAUTION]
   > Para evitar potenciais danos causados por cabos salientes, fixe qualquer folga no cabo indicador de estado após o encaminhamento deste cabo através do CMA. 

![Ilustração de CMA com cabos instalados](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Se não instalou o CMA, utilize as duas correias de gancho e de laço fornecidas no kit ferroviário para encaminhar os cabos na parte de trás do seu sistema.
> 
>  1. Localize os suportes exteriores da CMA nos lados internos de ambas as flanges de cremalheira.
>  2. Embrulhe os cabos suavemente, puxando-os para fora dos conectores do sistema para os lados esquerdo e direito.
>  3. Passe as correias do gancho e do laço através das ranhuras ferramentas nos suportes CMA externos de cada lado do sistema para fixar os feixes de cabo.
> 
>     ![Cabos encaminhados sem CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Sobre os requisitos de endereço IP

Para nós de hardware numa cache de armazenamento híbrido Azure FXT Edge Filer, os endereços IP são geridos pelo software de cluster.

Cada nó requer pelo menos um endereço IP, mas os endereços do nó são atribuídos quando os nós são adicionados ou removidos do cluster. 

O número total de endereços IP necessários depende do número de nós que compõem a sua cache. 

Configure a gama de endereços IP utilizando o software do Painel de Controlo depois de instalados os nódosos. Para saber mais, leia [Recolher informações para o cluster](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Ligar cabos de alimentação

Cada nó de Filer de Borda Azure FXT utiliza duas unidades de alimentação (PSUs). 

> [!TIP] 
> Para tirar partido das duas PSUs redundantes, prenda cada cabo de alimentação AC a uma unidade de distribuição de energia (PDU) num circuito de ramificação independente.  
> 
> Pode utilizar um UPS para alimentar os PDUs para proteção extra. 

1. Ligue os cabos de alimentação incluídos às OPNo no chassis. Certifique-se de que os cabos e as PSUs estão completamente sentados. 
1. Fixe os cabos de alimentação às unidades de distribuição de energia no porta-equipamentos. Se possível, utilize duas fontes de alimentação separadas para os dois cabos. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Potência num nó de Filer de Borda Azure FXT

Para ligar o nó, pressione o botão de alimentação na parte frontal do sistema. O botão está no painel de controlo do lado direito. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Desligar um nó de Filer de Borda Azure FXT

O botão de alimentação pode ser utilizado para desligar o sistema durante os testes e antes de o adicionar a um cluster. No entanto, depois de um nó de Filer de Borda Azure FXT estar a ser utilizado como parte de um cluster, deve utilizar o software do painel de controlo de cluster para desligar o hardware. Leia Como desligar com segurança o hardware do Ficheiro de [Borda Azure FXT](fxt-power-off.md) para obter mais detalhes. 

## <a name="next-steps"></a>Passos seguintes

Depois de terminar de ligar o hardware, deligar cada um dos nós e insetá-los definindo as suas palavras-passe de raiz. 
> [!div class="nextstepaction"]
> [Definir senhas iniciais](fxt-node-password.md)
