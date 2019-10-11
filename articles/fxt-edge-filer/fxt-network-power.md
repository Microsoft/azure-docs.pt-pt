---
title: Microsoft Azure conexões de rede do FXT Edge filer e fonte de energia
description: Como conectar as portas de rede e ligar a energia para o hardware de arquivo de borda do Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 474172284383bc9ba0e5b5c11c66e1b990010184
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254867"
---
# <a name="tutorial-make-network-connections-and-supply-power-to-the-azure-fxt-edge-filer-node"></a>Tutorial: fazer conexões de rede e fornecer energia para o nó de filer do Azure FXT Edge

Este tutorial ensina como cabear as conexões de rede para um nó de hardware do Azure FXT Edge Filer.

Neste tutorial, irá aprender: 

> [!div class="checklist"]
> * Como escolher o tipo de cabo de rede para o seu ambiente
> * Como conectar um nó de filer do Azure FXT Edge à sua rede de datacenter
> * Como rotear cabos por meio do gerenciamento de cabo do ARM (CMA)
> * Como conectar a energia ao dispositivo em rack e ligá-lo

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, o filer do Azure FXT Edge deve ser instalado em um rack de equipamento padrão. O CMA deve ser instalado no nó do Filer. 

## <a name="identify-ports"></a>Identificar portas

Identifique as várias portas na parte posterior do seu Filer do Azure FXT Edge. 
 
![Trás de um dispositivo cabeado](media/fxt-back-annotated.png)

## <a name="cable-the-device"></a>Ligar o dispositivo

* Conecte as portas RJ-45 à fonte de rede de seu data center, conforme descrito em [portas de rede](#network-ports).  
* Conecte com segurança a [porta iDRAC](#idrac-port) a uma rede separada com um servidor DHCP seguro. 
* Use as portas USB e a porta VGA para conectar um teclado e um monitor ao nó para a configuração inicial. Você deve inicializar o nó e [definir uma senha inicial](fxt-node-password.md) para ativar as outras portas do nó. Leia [definir senhas iniciais](fxt-node-password.md) para obter detalhes. 

Este artigo também descreve como [conectar a energia CA](#connect-power-cables) para o nó. 

Este artigo também explica como se conectar à [porta serial](#serial-port-only-when-necessary)do nó, se necessário para a solução de problemas especializada. 

### <a name="network-ports"></a>Portas de rede 

Cada nó de arquivo de borda do Azure FXT inclui as seguintes portas de rede: 

* Seis portas de dados de taxa dupla de 25GbE/10Gbe de alta velocidade: 

  * Quatro portas fornecidas por dois adaptadores de rede de plug-in de porta dupla
  * Duas portas fornecidas pelo adaptador de rede de mezanino de placa-mãe 

* Duas portas 1 GbE fornecidas pelo adaptador de rede de mezanino de placa-mãe 

As portas de dados 25GbE/10Gbe de alta velocidade têm compartimentos compatíveis com SFP28 padrão. Para usar cabos ópticos, você deve instalar módulos de transceptor ótico SFP28 (não fornecidos).

As portas 1 GbE têm conectores RJ-45 padrão.

Para obter uma lista completa de cabos, comutadores e transceptores com suporte, consulte a [matriz de interoperabilidade da série Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).

O tipo de conexões a ser usado para o seu sistema depende do ambiente de data center.

* Se estiver se conectando a uma rede 25GbE, conecte cada uma das portas de dados de alta velocidade com um dos seguintes tipos de cabo:

  * Cabo óptico e transceptor óptico SFP28 com capacidade de 25GbE ou 25GbE de taxa dupla/10Gbe
  * SFP28 tipo de cabo twinaxial de conexão direta com capacidade 25GbE

* Se estiver se conectando a uma rede de 10 GB, conecte cada uma das portas de dados de alta velocidade com uma das seguintes opções: 

  * Cabo óptico e transceptor óptico SFP28 com capacidade de 10 GB ou 25GbE/10Gbe de taxa dupla.
  * SFP28 tipo de cabo twinaxial de conexão direta com capacidade 25GbE
  * Cabo twinaxial de conexão direta com capacidade de 10Gbe do tipo SFP28

* As portas de rede 1 GbE são usadas para o tráfego de gerenciamento de cluster. Marque a opção **usar rede de gerenciamento de 1GB** ao criar o cluster se você quiser criar uma rede fisicamente separada para a configuração de cluster (descrita em [Configurar a rede de gerenciamento](fxt-cluster-create.md#configure-the-management-network)). Conecte as portas com um cabo padrão Cat5 ou melhor, conforme descrito na lista de cabos com suporte.

  Você pode deixar as portas 1 GbE desconectadas se planeja usar as portas de alta velocidade para todo o tráfego. Por padrão, as portas de rede 1 GbE não são usadas se uma porta de dados de velocidade mais alta estiver disponível.  

### <a name="idrac-port"></a>porta iDRAC  

A porta rotulada iDRAC é uma conexão de 1 GB que permite a comunicação com um controlador de acesso remoto usado para gerenciamento e monitoramento de hardware. O FXT software usa a IPMI (interface de gerenciamento de plataforma inteligente) com esse controlador para solução de problemas e recuperação. Você pode usar a [interface iDRAC](https://www.dell.com/support/manuals/idrac9-lifecycle-controller-v3.30.30.30/idrac_3.30.30.30_ug/) interna para monitorar o hardware por essa porta. o acesso a iDRAC e IPMI são habilitados por padrão. 

> [!Note]
> A porta iDRAC pode ignorar o sistema operacional e interagir diretamente com o hardware no nó. 

Use essas estratégias de segurança ao conectar e configurar a porta iDRAC:

* Conecte somente as portas iDRAC a uma rede separada fisicamente da rede de dados usada para acessar o cluster.
* Defina uma senha de administrador do iDRAC segura em cada nó. Você deve definir essa senha para ativar o hardware – siga as instruções em [definir senhas de hardware](fxt-node-password.md).
* A configuração padrão da porta iDRAC usa DHCP e IPv4 para atribuição de endereço IP. Verifique se o ambiente DHCP está bem protegido e se as conexões são restritas entre os clientes DHCP e o servidor DHCP. (O painel de controle de cluster inclui configurações para alterar o método de configuração de endereço dos nós depois de criar o cluster.)
* Deixe a porta iDRAC definida como "modo dedicado" (o padrão), que restringe o tráfego de rede iDRAC/IPMI para a porta RJ-45 dedicada.

A porta iDRAC não requer uma conexão de rede de alta velocidade.
  
### <a name="serial-port-only-when-necessary"></a>Porta serial (somente quando necessário)

Em algumas situações, o serviço e o suporte da Microsoft podem informá-lo para conectar um terminal à porta serial de um nó para diagnosticar um problema.  

Para anexar o console:

1. Localize a porta serial (COM1) na parte traseira do nó Filer do FXT Edge.
1. Use um cabo de modem nulo para conectar a porta serial a um terminal configurado para ANSI-115200-8N1.
1. Entre no console do e execute outras etapas conforme orientado pela equipe de suporte.

## <a name="route-cables-in-the-cable-management-arm-cma"></a>Rotear cabos no braço de gerenciamento de cabo (CMA)

Cada nó de arquivo de borda do Azure FXT é fornecido com um ARM de gerenciamento de cabo opcional. O CMA simplifica o roteamento de cabos e fornece acesso mais fácil à parte posterior do chassi sem a necessidade de desconectar os cabos. 

Siga estas instruções para rotear os cabos por meio do CMA: 

1. Usando os encapsulamentos de ligação fornecidos, agrupe os cabos enquanto eles inserem e saem das cestas para que eles não interfiram em sistemas adjacentes (1).
1. Com o CMA na posição do serviço, encaminhe o grupo de cabos pelas cestas internas e externas (2).
1. Use o gancho pré-instalado e as pulseiras de loop em qualquer extremidade das cestas para proteger os cabos (3).
1. Gire o CMA de volta para o lugar na bandeja (4).
1. Instale o cabo indicador de status na parte posterior do sistema e proteja o cabo encaminhando-o por meio do CMA. Anexe a outra extremidade do cabo ao canto da cesta de CMA externa (5). 

   > [!CAUTION]
   > Para evitar possíveis danos causados por cabos de folga, proteja qualquer margem de atraso no cabo indicador de status depois de rotear esse cabo por meio do CMA. 

![Ilustração do CMA com cabos instalados](media/fxt-install/cma-cabling-400.png)

> [!NOTE]
>  Se você não instalou o CMA, use as duas pulseiras de gancho e loop fornecidas no kit de trilhos para rotear os cabos na parte traseira do sistema.
> 
>  1. Localize os colchetes de CMA externos nos lados interiores de ambas as bordas do rack.
>  2. Agrupe os cabos com cuidado, puxando-os claros dos conectores do sistema para os lados esquerdo e direito.
>  3. Threade as pulseiras do gancho e do loop por meio dos slots de ferramentas nos colchetes de CMA externos em cada lado do sistema para proteger os pacotes de cabos.
> 
>     ![Cabos roteados sem um CMA](media/fxt-install/fxt-route-cables-no-cma-400.png)

## <a name="about-ip-address-requirements"></a>Sobre os requisitos de endereço IP

Para nós de hardware em um cache de armazenamento híbrido do Azure FXT Edge, os endereços IP são gerenciados pelo software de cluster.

Cada nó requer pelo menos um endereço IP, mas os endereços de nó são atribuídos quando nós são adicionados ou removidos do cluster. 

O número total de endereços IP necessários depende do número de nós no que compõem o cache. 

Configure o intervalo de endereços IP usando o software do painel de controle depois que os nós tiverem sido instalados. Para saber mais, leia [coletar informações para o cluster](fxt-cluster-create.md#gather-information-for-the-cluster).  

## <a name="connect-power-cables"></a>Conectar cabos de alimentação

Cada nó de arquivo de borda do Azure FXT usa duas unidades de fonte de alimentação (PSUs). 

> [!TIP] 
> Para aproveitar os dois PSUs redundantes, conecte cada cabo de alimentação CA a uma PDU (unidade de distribuição de energia) em um circuito de ramificação independente.  
> 
> Você pode usar um UPS para ligar a PDUs para proteção extra. 

1. Conecte os cabos de alimentação incluídos ao PSUs no chassi. Verifique se os cabos e PSUs estão totalmente encaixados. 
1. Conecte os cabos de alimentação às unidades de distribuição de energia no rack do equipamento. Se possível, use duas fontes de alimentação separadas para os dois cabos. 
 
### <a name="power-on-an-azure-fxt-edge-filer-node"></a>Ligar um nó de filer do Azure FXT Edge

Para ligar o nó, pressione o botão de energia na frente do sistema. O botão está no painel de controle do lado direito. 

### <a name="power-off-an-azure-fxt-edge-filer-node"></a>Desligar um nó de filer do Azure FXT Edge

O botão de energia pode ser usado para desligar o sistema durante o teste e antes de adicioná-lo a um cluster. No entanto, depois que um nó de filer do Azure FXT Edge estiver em uso como parte de um cluster, você deverá usar o software do painel de controle de cluster para desligar o hardware. Leia [como desligar com segurança o hardware do Azure FXT Edge Filer](fxt-power-off.md) para obter detalhes. 

## <a name="next-steps"></a>Passos seguintes

Depois de concluir o cabeamento do hardware, ligue cada um dos nós e inicialize-os definindo suas senhas raiz. 
> [!div class="nextstepaction"]
> [Definir senhas iniciais](fxt-node-password.md)
