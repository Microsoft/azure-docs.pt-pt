---
title: Tutorial sobre a instalação de um dispositivo físico do Filer do Azure FXT Edge | Microsoft Docs
description: Como desempacotar, colocar em rack e conectar o componente do dispositivo físico do Microsoft Azure o cache de armazenamento híbrido do Filer do FXT Edge
services: ''
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 0f3c7b01ee9d4a62ec0d563af55f2086894081be
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256042"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Tutorial: instalar o filer do Azure FXT Edge 

Este tutorial descreve como instalar um nó de hardware para o cache de armazenamento híbrido do Filer do Azure FXT Edge. Você precisa instalar pelo menos três nós de hardware para criar um cluster de arquivos do Azure FXT Edge.

O procedimento de instalação envolve o desempacotamento e a montagem de rack do dispositivo e a anexação do CMA (braço de gerenciamento de cabo) e do painel frontal. Um tutorial separado explica como anexar cabos de rede e conectar a energia. 

Leva aproximadamente uma hora para instalar um nó de filer do Azure FXT Edge. 

Este tutorial inclui estas etapas de instalação: 

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montar o dispositivo em um rack
> * Instalar o painel frontal (opcional)

## <a name="installation-prerequisites"></a>Pré-requisitos de instalação 

Antes de começar, verifique se o data center e o rack que você usará têm estes recursos:

* Um slot de 1U disponível no rack onde você pretende montar o dispositivo.
* Fonte de energia CA e sistemas de resfriamento que atendem às necessidades do Filer do Azure FXT Edge. (Leia as [especificações de energia e térmica](fxt-specs.md#power-and-thermal-specifications) para obter ajuda sobre como planejar e dimensionar a instalação.)  

  > [!NOTE] 
  > Para aproveitar ao máximo as duas PSUs (unidades de fonte de alimentação) redundantes, use unidades de distribuição de energia em dois circuitos de ramificação diferentes ao conectar a energia AC. Leia [cabos de alimentação de conexão](fxt-network-power.md#connect-power-cables) para obter detalhes.  

## <a name="unpack-the-hardware-node"></a>Desempacotar o nó de hardware 

Cada nó de arquivo de borda do Azure FXT é fornecido em uma única caixa. Conclua estas etapas para desempacotar um dispositivo.

1. Coloque a caixa numa superfície plana e uniforme.

2. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou o empacotamento estiver seriamente danificado, não o abra. Contacte o Suporte da Microsoft para ajudar a avaliar se o dispositivo está funcional.

3. Desembale a caixa. Certifique-se de que ele inclui os seguintes itens:
   * Um dispositivo FXT único de compartimento
   * Dois cabos de alimentação
   * Um painel frontal e uma chave
   * Assembly de kit de um trilho
   * Um braço de gerenciamento de cabo (CMA)
   * Folheto de instruções de instalação do CMA
   * Folheto de instruções de instalação do rack
   * Folheto de informações sobre segurança, ambiente e normas

Se você não recebeu todos os itens listados, contate o fornecedor do dispositivo para obter suporte. 

Verifique se o dispositivo teve tempo suficiente para alcançar a mesma temperatura que o espaço antes de instalá-lo ou ligá-lo. Se você notar a condensação em qualquer parte do dispositivo, aguarde pelo menos 24 horas antes de instalar o.

A próxima etapa é montar o dispositivo em rack.

## <a name="rack-the-device"></a>Montar o dispositivo em bastidor

O dispositivo Filer do Azure FXT Edge deve ser instalado em um rack padrão de 19 polegadas. 

O cache de armazenamento híbrido do Filer do Azure FXT Edge é composto de três ou mais dispositivos de arquivo de borda do Azure FXT. Repita as etapas de instalação do rack para cada dispositivo que faz parte do seu sistema. 

### <a name="rack-install-prerequisites"></a>Pré-requisitos de instalação do rack

* Antes de começar, leia as instruções de segurança no livreto de segurança, ambiente e informações regulatórias que foi enviado com seu dispositivo.

  > [!NOTE]
  > Sempre use duas pessoas ao levantar o nó, incluindo ao instalá-lo em um rack ou removê-lo do rack. 

* Identifique o tipo de instalação do trilho usado com seu rack de equipamento. 
  * Para racks de snap-in de orifícios quadrados ou redondos, siga as instruções do trilho sem ferramentas.
  * Para racks de orifícios segmentados, siga as instruções do trilho de ferramentas. 
  
    Para a configuração de montagem do trilho de ferramentas, você precisa fornecer oito parafusos, digite 10-32, 12-24, M5 ou M6. O diâmetro de cabeça dos parafusos deve ser inferior a 10 mm (0,4 ").

### <a name="identify-the-rail-kit-contents"></a>Identificar o conteúdo do kit do trilho

Localize os componentes para instalar o assembly do kit do trilho:

1. Dois assemblies de trilho deslizante do Dell ReadyRails II a7 (1)
1. Duas pulseiras de gancho e loop (2)

![Desenho numerado de conteúdo do kit do trilho](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Assembly do trilho – trilhos sem ferramentas (orifício quadrado ou racks de orifícios redondos)

Para racks com orifícios quadrados ou redondos, siga este procedimento para montar e instalar os trilhos. 

1. Posicione as peças de extremidade do trilho esquerda e direita rotuladas como **front** face para dentro. Posicione cada peça final para que ela se encaixe nos orifícios no lado frontal dos flanges verticais do rack. uma

2. Alinhe cada parte final nos orifícios inferior e superior do rack no espaço em que você deseja montá-lo. 2

3. Envolva o back-end do trilho até que ele se encaixe totalmente no flange vertical do rack e que os cliques da trava estejam no lugar. Repita essas etapas para posicionar e colocar a peça de front-end no flange vertical do rack. Beta

> [!TIP]
> Para remover os trilhos, puxe o botão de liberação de trava no ponto médio da parte final (4) e descoloque cada trilho.

![Diagrama de instalação e remoção de trilhos sem ferramentas, com etapas numeradas](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Assembly de trilho – trilhos de ferramentas (racks de orifícios segmentados)

Para racks com buracos threaded, siga este procedimento para montar e instalar os trilhos.

1. Remova os PINs dos colchetes de montagem frontal e traseira por uma chave de fenda inclinada simples. uma
1. Puxe e gire os subconjuntos de travas do trilho para removê-los dos colchetes de montagem. 2
1. Anexe os trilhos de montagem esquerdo e direito aos flangeos do rack vertical frontal usando dois pares de parafusos. Beta
1. Deslize os colchetes à esquerda e à direita para frente nos flanges verticais do rack traseiro e anexe-os usando dois pares de parafusos. quatro

![Diagrama de instalação e remoção de trilhos de ferramentas, com etapas numeradas](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Instalar o sistema no rack

Siga estas etapas para montar o dispositivo de filer do Azure FXT Edge no rack.

1. Puxe os trilhos do slide interno para fora do rack até que eles travem no local. uma
1. Localize o ressalto do trilho traseiro em cada lado do dispositivo e abaixe-o nos slots J traseiros nos assemblies do slide. 2 
1. Gire o dispositivo para baixo até que todos os espaçadores do trilho estejam encaixados nos slots J. Beta
1. Envie o dispositivo por push para dentro até que as alavancas de bloqueio cliquem no lugar.
1. Pressione os botões de bloqueio de liberação de slide em ambos os trilhos (4) e deslize o dispositivo para o rack.

![Instalar o sistema em um diagrama de rack, com as etapas numeradas](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Remover o sistema do rack

Para remover o dispositivo do rack, siga este procedimento. 

1. Localize as alavancas de bloqueio nos lados dos trilhos internos (1).
2. Desbloqueie cada alavanca girando-a para sua posição de liberação (2).
3. Segure os lados do sistema firmemente e empurre-os para frente até que os espaçadores do trilho estejam na frente dos J-slots. Levante o sistema para cima e para fora do rack e coloque-o em uma superfície de nível (3).

![Ilustração da remoção de um sistema do rack, com as etapas numeradas](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Envolver a trava térrea

1. Voltado para a frente, localize a trava do térrea (1) em qualquer lado do sistema.
2. As travas são acionadas automaticamente à medida que o sistema é empurrado para o rack. 

Para liberar as travas ao remover o sistema, puxe-os para cima (2).

Os parafusos de montagem de hardware opcionais são fornecidos para proteger o sistema ao rack para envio ou em outros ambientes instáveis. Localize o parafuso sob cada trava e aperte-os com um #2 chave Phillips (3).

![Ilustração numerada de envolver e liberar a trava térrea](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Instalar o ARM de gerenciamento de cabo 

Um braço de gerenciamento de cabo opcional (CMA) é fornecido com o Gerenciador de borda de FXT. As instruções impressas para instalá-lo são fornecidas no pacote. 

1. Descompacte e identifique os componentes do kit de ARM do gerenciamento de cabo:
   * Bandeja CMA (1)
   * CMA (2)
   * Encapsulamentos de gravata do cabo de nylon (3)
   * Colchetes de anexo CMA (4)
   * Cabo do indicador de status (5) 

   > [!TIP] 
   > Para proteger o CMA para remessa no rack, faça um loop com a ligação entre as cestas e a bandeja e fácil-as firmemente. Proteger o CMA dessa maneira também protegerá seu sistema em ambientes instáveis.

   ![Ilustração de partes do CMA](media/fxt-install/cma-kit-400.png)

2. Instale a bandeja CMA.

   A bandeja CMA fornece suporte e atua como um retentor para o CMA. 

   1. Alinhe e envolva cada lado da bandeja com os colchetes do destinatário nas bordas internas dos trilhos. 
   1. Pressione a bandeja para frente até que ela clique no local. uma
   1. Para remover a bandeja, aperte os botões de liberação da trava em direção ao centro e puxe a bandeja para fora dos colchetes do receptor (2).

   ![Ilustração da instalação da bandeja do CMA](media/fxt-install/cma-tray-install-400.png)

3. Instale os colchetes de anexo do CMA. 

   > [!NOTE]
   >
   > * Você pode anexar o CMA ao trilho de montagem à direita ou à esquerda, dependendo de como você pretende rotear os cabos do sistema. 
   > * Para sua conveniência, monte o CMA no lado oposto às fontes de alimentação (lado A). Se ele estiver montado no lado B, o CMA deverá ser desconectado para remover a fonte de energia externa. 
   > * Sempre remova a bandeja antes de remover as fontes de alimentação. 

   ![Ilustração de instalação de colchete CMA](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Selecione o colchete de anexo CMA apropriado para o lado em que você deseja montar o CMA (lado B ou lado A).
   1. Instale o colchete de anexo do CMA com a marca correspondente do lado a ou B na parte posterior do trilho do slide.
   1. Alinhe os orifícios no colchete com os Pins no trilho do slide. Pressione o colchete até que ele trave no local. 

4. Instale o CMA.

   1. Na parte posterior do sistema, ajuste a trava no front-end do CMA no colchete interno do assembly de slides até que a trava trave (1). 
   1. Ajuste a outra trava no final do colchete mais externo até que a trava trave (2). 
   1. Para remover o CMA, solte as travas pressionando os botões de versão do CMA na parte superior dos invólucros de trava interna e externa (3).

   ![Ilustração da instalação principal do CMA](media/fxt-install/cma-install-400.png)

   O CMA pode ser girado para fora do sistema para acesso e serviço. No fim da dobradiça, levante o CMA para fora da bandeja para desencaixá-lo (1). Depois de ser desinstalado da bandeja, gire o CMA para fora do sistema (2).

   ![Ilustração do CMA girado aberto para o serviço](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Instalar o painel frontal (opcional)

Esta seção explica como instalar e remover o painel frontal (faceplate) para o hardware de servidor de borda do Azure FXT. 

Para instalar o painel frontal: 

1. Localize e remova a chave do painel, que é fornecida no pacote do painel. 
1. Alinhe o painel com a frente do chassi e insira os pinos no lado direito do painel nos orifícios no flange de montagem do rack do lado direito do nó. 
1. Ajuste a extremidade esquerda do painel no chassi. Pressione o painel até que o botão no lado esquerdo clique no local.
1. Bloqueie o painel com a chave.

Para remover o painel frontal: 
1. Desbloqueie o painel usando a chave do painel.
1. Pressione o botão de liberação no lado esquerdo e puxe a extremidade esquerda do painel para fora do chassi.
1. Desconecte a extremidade direita e remova o painel.
   
   ![Imagem mostrando o botão de liberação à esquerda do painel e como removê-lo puxando para fora do lado esquerdo](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Passos seguintes

Depois de desempacotar e colocar o dispositivo em rack, continue a instalação anexando os cabos de rede e conectando a alimentação de CA ao Filer do Azure FXT Edge.

> [!div class="nextstepaction"]
> [Cabear portas de rede e poder de fornecimento](fxt-network-power.md)