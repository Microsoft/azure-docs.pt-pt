---
title: Ligações de rede do Microsoft Azure FXT Edge filtro e a fonte de alimentação
description: Como ligar as portas de rede e ligar a energia para o hardware de ficheiros do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 444835a94559a352bfd749cfa1cb2cd8c3a39373
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450311"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Tutorial: Fazer conexões de rede e fornecer energia para o nó de filtro de borda de FXT do Azure

Este tutorial ensina a instalar os cabos as conexões de rede para um nó de hardware de ficheiros do Azure FXT Edge.

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * Como escolher o tipo de cabo de rede para o seu ambiente
> * Como ligar um nó de filtro de borda de FXT do Azure à sua rede de centro de dados
> * Como rotear cabos através o braço de gestão de cabo (CMA)
> * Como ligar a energia ao dispositivo racked e ligue-o

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, o filtro de borda do Azure FXT deve ser instalado num bastidor equipamento padrão. O CMA deve ser instalado no nó de filtro. 

## <a name="identify-ports"></a>Identificar portas

Identifica as várias portas na parte de trás de seu filtro de borda de FXT do Azure. 
 
![Inverso de um dispositivo cabled](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Ligar o dispositivo

* Ligar as portas de RJ-45 a origem de rede do seu centro de dados, conforme descrito em [portas de rede](#network-ports).  
* Ligar de forma segura a [porta do iDRAC](#idrac-port) a uma rede separada com um servidor DHCP seguro. 
* Utilize as portas USB e a porta VGA para ligar um teclado e um monitor para o nó para a configuração inicial. Tem de iniciar o nó e [definir uma palavra-passe inicial](fxt-node-password.md) para ativar o nó do outras portas. Leia [definir palavras-passe inicial](fxt-node-password.md) para obter detalhes. 

Este artigo também descreve como [ligar alternada](#connect-power-cables) para o nó. 

Este artigo também explica como se pode ligar para o nó [porta serial](#serial-port-only-when-necessary), mas a porta serial só é utilizada para a resolução de problemas avançada. 

### <a name="network-ports"></a>Portas de rede 

Cada nó de filtro de borda de FXT do Azure inclui as seguintes portas de rede: 

* Seis portas de dados de alta velocidade 25GbE/10 gbe taxa de dupla: 

  * Quatro portas fornecidas por dois adaptadores de rede de plug-in de porta dupla
  * Duas portas fornecidas pelo adaptador de rede de placa-mãe mezanino 

* Duas portas de 1 gbe fornecidas pelo adaptador de rede de placa-mãe mezanino 

As portas de dados de alta velocidade 25GbE/10 gbe têm compartimentos de SFP28 compatível com padrão. Para utilizar cabos óticas, tem de instalar módulos de transceiver óptica SFP28 (não fornecidos).

As portas de 1 gbe tem conectores de RJ-45 padrão.

Para obter uma lista completa de consulta suportada, cabos, comutadores e transcetores, o [Cavium FastlinQ 41000 série interoperabilidade matriz](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

O tipo de conexões para utilizar para o seu sistema depende de seu ambiente de centro de dados.

* Se ligar a uma rede de 25GbE, instalar cada uma das portas de dados de alta velocidade com um dos seguintes tipos de cabo:

  * Cabo óptica e SFP28 transceiver óptica com 25GbE ou capacidade de 25GbE/10 gbe duplos de taxa
  * Direct 25GbE capaz de tipo SFP28 anexar twinaxial cabo

* Se ligar a uma rede de 10 gbe, instalar os cabos cada um das portas de dados de alta velocidade com um dos seguintes: 

  * Cabo óptica e SFP28 transceiver óptica com 10 gbe ou capacidade de 25GbE/10 gbe duplos taxa.
  * Direct 25GbE capaz de tipo SFP28 anexar twinaxial cabo
  * Direct com capacidade 10 gbe do tipo SFP28 anexar twinaxial cabo

* As portas de rede de 1 gbe são utilizadas para tráfego de gestão do cluster. Verifique os **utilize a rede de gestão de 1Gb** opção ao criar o cluster (descrito na [configurar a rede de gestão](fxt-cluster-create.md#configure-the-management-network)). Ligar as portas com Cat5 padrão ou cabo melhor, conforme descrito na lista de cabos suportados.

  Pode deixar as portas de 1 gbe uncabled se planeja usar as portas de alta velocidade para todo o tráfego. Por predefinição, as portas de rede de 1 gbe não são utilizadas se uma porta de dados de velocidade superior está disponível.  

### <a name="idrac-port"></a>porta do iDRAC  

A porta rotulado como iDRAC é uma ligação de 1Gb que permite a comunicação com um controlador de acesso remoto utilizado para gestão de hardware e monitorização. FXT software usa o Intelligent Platform Management Interface (IPMI) com esse controlador para resolução de problemas e recuperação. Pode usar o incorporado [interface do iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) para monitorizar o hardware por essa porta. iDRAC e acesso IPMI estão ativadas por predefinição. 

> [!Note]
> A porta do iDRAC pode ignorar o sistema operacional e interagir diretamente com o hardware no nó. 

Use essas estratégias de segurança ao ligar e configurar a porta do iDRAC:

* Ligue-se apenas iDRAC portas a uma rede que está fisicamente separada da rede de dados utilizada para aceder ao cluster.
* Defina uma palavra-passe de administrador do iDRAC seguro em cada nó. Tem de definir esta palavra-passe para ativar o hardware – siga as instruções em [definir palavras-passe de hardware](fxt-node-password.md).
* A configuração de porta predefinida do iDRAC utiliza o DHCP e IPv4 para atribuição de endereços IP. Certifique-se de que o seu ambiente de DHCP está bem protegida e que as ligações estão limitadas entre clientes DHCP e o servidor DHCP. (O painel de controlo do cluster inclui definições para alterar o método de configuração de endereço de nós depois de criar o cluster.)
* Deixe a porta do iDRAC definida como "modo dedicado" (predefinição), que restringe o tráfego de rede do iDRAC/IPMI à porta RJ-45 dedicada.

A porta do iDRAC não requer uma ligação de rede de alta velocidade.
  
### <a name="serial-port-only-when-necessary"></a>Porta Serial (apenas quando necessário)

Em algumas situações e suporte do Microsoft Service podem eventualmente indicar para ligar um terminal a porta serial de um nó para diagnosticar um problema.  

Para anexar a consola:

1. Localize a porta (COM1) serial em atrás do nó de filtro de borda FXT.
1. Utilize um cabo de modem nulo para ligar a porta serial para um terminal configurado para ANSI-115200-8N1.
1. Inicie sessão na consola e efetuar outros passos conforme indicado pela equipe de suporte.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Cabos de rota no braço de gestão de cabo (CMA)

Cada nó de filtro de borda do Azure FXT vem com um braço de gestão de cabo opcional. O CMA simplifica o encaminhamento de cabo e fornece acesso mais fácil para trás os chassis sem a necessidade de cabos de desligar. 

Siga estas instruções para encaminhar os cabos por meio do CMA: 

1. Utilizar o inclui empate fornecido, agrupar os cabos conforme eles entrar e sair os baskets, para que eles não interferem com sistemas adjacentes (1).
1. Com o CMA na posição de serviço, encaminhe o pacote de cabo através de baskets internas e externas (2).
1. Utilize os pré-instalados straps hook e loop em cada extremidade os baskets para proteger os cabos (3).
1. Girar o CMA novamente no local na Bandeja do (4).
1. Utilize o cabo de indicador de estado pré-instalados parte traseira desse sistema e proteger, o cabo ao encaminhamento-lo através do CMA. Anexe outro extremo do cabo para o canto do cesto CMA externo (5). 

   > [!CAUTION]
   > Para evitar potenciais danos de protruding cabos, proteger qualquer slack, o cabo de indicador de estado após o encaminhamento neste cabo por meio do CMA. 

![Ilustração da CMA com cabos instalado](media/cma-cabling-scan-400.png)

> [!NOTE]
>  Se não tiver instalado o CMA, usar o gancho de duas e fazer um loop straps fornecidos no kit do rail para encaminhar os cabos remissivo seu sistema.
> 
>  1. Localize os colchetes CMA externos nos lados interior de ambos os flanges rack.
>  2. Agrupar os cabos moderadamente, colocá-los limpar dos conectores do sistema para as laterais esquerda e direita.
>  3. Os straps hook e o loop por meio de ranhuras tooled nos colchetes CMA externas de cada lado do sistema para proteger os pacotes de cabo de thread.
> 
>     ![Cabos encaminhados sem um CMA](media/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Sobre os requisitos de endereço IP

Para nós de hardware num cache de ficheiros do Azure FXT Edge de armazenamento híbridas, os endereços IP são geridos pelo software do cluster.

Cada nó requer, pelo menos, um endereço IP, mas os endereços de nó são atribuídos quando nós são adicionados ou removidos do cluster. 

O número total de endereços IP necessários depende o número de nós em que constituem a cache. 

Configure o intervalo de endereços IP com o software de painel de controlo, após a instalação os nós. Para saber mais, leia [recolher informações para o cluster](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Ligue os cabos de energia

Cada nó de filtro de borda do Azure FXT usa duas unidades de fonte de alimentação de energia (PSUs). 

> [!TIP] 
> Para aproveitar os dois PSUs redundantes, anexe o cabo de alimentação cada AC para uma unidade de distribuição de energia (PDU) num circuito do ramo independentes.  
> 
> Pode usar no-BREAK para criar os PDUs para proteção adicional. 

1. Ligue os cabos de energia incluídos a PSUs no chassi. Certifique-se de que os cabos e PSUs são sentados totalmente. 
1. Anexe os cabos de energia para as unidades de distribuição de energia em rack equipamento. Se possível, utilize duas fontes de alimentação separados para os dois cabos. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Ligar um nó de filtro de borda de FXT do Azure

Para ativar o nó, prima o botão de energia na frente do sistema. O botão é no painel de controle do lado direito. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Desligar um nó de filtro de borda de FXT do Azure

O botão de energia pode ser utilizado para encerrar o sistema durante o teste e antes de adicionar um cluster. No entanto, depois de um nó de ficheiros do Azure FXT Edge está a ser utilizado como parte de um cluster, deve utilizar o software de painel de controlo do cluster para encerrar o hardware. Leia [como desligar com segurança de hardware de ficheiros do Azure FXT Edge](fxt-power-off.md) para obter detalhes. 

## <a name="next-steps"></a>Passos Seguintes

Depois de concluir a cablagem o hardware, cada um de nós de ativação e inicializá-las ao definir as respetivas palavras-passe de raiz. 
> [!div class="nextstepaction"]
> [Conjunto inicial de palavras-passe](fxt-node-password.md)
