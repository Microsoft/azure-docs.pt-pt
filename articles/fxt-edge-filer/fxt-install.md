---
title: 'Tutorial: Instale um dispositivo físico Azure FXT Edge Filer'
description: Como desempacotar, cremalheira e cabo o componente do dispositivo físico da cache híbrida de armazenamento híbrido Microsoft Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 48ee6de28bcd76d4c484b77c981062bad1a3754d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219756"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Tutorial: Instalar filete de borda Azure FXT

Este tutorial descreve como instalar um nó de hardware para a cache de armazenamento híbrido Azure FXT Edge Filer. É necessário instalar pelo menos três nós de hardware para criar um cluster Azure FXT Edge Filer.

O procedimento de instalação envolve desembalar e montar o dispositivo e fixar o braço de gestão do cabo (CMA) e o molde dianteiro. Um tutorial separado explica a fixação de cabos de rede e a alimentação de ligação.

Demora aproximadamente uma hora a instalar um nó Azure FXT Edge Filer.

Este tutorial inclui estes passos de configuração:

> [!div class="checklist"]
>
> * Desembalar o dispositivo
> * Monte o dispositivo num cesto
> * Instale o molde frontal (opcional)

## <a name="installation-prerequisites"></a>Pré-requisitos de instalação

Antes de começar, certifique-se de que o data center e rack que utilizará tem estas funcionalidades:

* Uma ranhura 1U disponível na prateleira onde pretende montar o dispositivo.
* Sistemas de alimentação e arrefecimento CA que atendam às necessidades do Azure FXT Edge Filer. (Ler [Energia e especificações térmicas](fxt-specs.md#power-and-thermal-specifications) para ajudar a planear e dimensionar a instalação.)  

  > [!NOTE]
  > Para tirar o máximo partido das duas unidades de alimentação redundantes (PSUs), utilize unidades de distribuição de energia em dois circuitos de ramificação diferentes ao ligar a potência CA. Leia [os cabos de alimentação](fxt-network-power.md#connect-power-cables) de ligação para mais detalhes.  

## <a name="unpack-the-hardware-node"></a>Desembalar o nó de hardware

Cada nó de filete de borda Azure FXT é enviado numa única caixa. Complete estes passos para desempacotar um dispositivo.

1. Coloque a caixa numa superfície plana e uniforme.

2. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou a embalagem estiverem gravemente danificadas, não a abra. Contacte o Suporte da Microsoft para ajudar a avaliar se o dispositivo está funcional.

3. Desembale a caixa. Certifique-se de que inclui os seguintes itens:
   * Um único dispositivo FXT de recinto
   * Dois cabos de alimentação
   * Um molde frontal e chave
   * Um conjunto de kit de trilho
   * Um braço de gestão de cabos (CMA)
   * Folheto de instruções de instalação da CMA
   * Folheto de instruções de instalação do rack
   * Folheto de Informação sobre Segurança, Ambiente e Regulação

Se não recebeu todos os itens listados, contacte o fornecedor do dispositivo para obter suporte.

Certifique-se de que o aparelho teve tempo suficiente para atingir a mesma temperatura que a sala antes de o instalar ou ligar. Se notar condensação em qualquer parte do aparelho, aguarde pelo menos 24 horas antes de instalar.

O próximo passo é montar o seu dispositivo.

## <a name="rack-the-device"></a>Montar o dispositivo em bastidor

O dispositivo Azure FXT Edge Filer deve ser instalado num rack standard de 19 polegadas.

A cache de armazenamento híbrido Azure FXT Edge Filer é composta por três ou mais dispositivos Azure FXT Edge Filer. Repita os passos de instalação da cremalheira para cada dispositivo que faça parte do seu sistema.

### <a name="rack-install-prerequisites"></a>Rack instalar pré-requisitos

* Antes de começar, leia as instruções de segurança no folheto de Informação de Segurança, Ambiente e Regulação que foi enviado com o seu dispositivo.

  > [!NOTE]
  > Utilize sempre duas pessoas ao levantar o nó, incluindo quando o instale numa prateleira ou o retire da cremalheira.

* Identifique o tipo de instalação ferroviária utilizada com o seu suporte de equipamento.
  * Para grelhar o encaixe quadrado ou redondo, siga as instruções do trilho sem ferramentas.
  * Para grelhar orifícios roscados, siga as instruções do trilho.
  
    Para a configuração de montagem do carril com ferramentas, é necessário fornecer oito parafusos, tipo 10-32, 12-24, M5 ou M6. O diâmetro da cabeça dos parafusos deve ser inferior a 10 mm (0,4").

### <a name="identify-the-rail-kit-contents"></a>Identifique o conteúdo do kit ferroviário

Localize os componentes para a instalação do conjunto do kit ferroviário:

1. Dois conjuntos ferroviários de correr A7 Dell ReadyRails II (1)
1. Duas correias de gancho e laço (2)

![Desenho numerado do conteúdo do kit ferroviário](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Conjunto ferroviário - trilhos sem ferramentas (furo quadrado ou racks redondos)

Para grelhadores com furos quadrados ou redondos, siga este procedimento para montar e instalar os trilhos.

1. Posicione as peças finais do trilho esquerdo e direito rotuladas **frontalmente** viradas para dentro. Posicione cada peça final de modo a que se coloque nos orifícios do lado frontal das flanges de cremalheira vertical. (1)

2. Alinhe cada peça final nos orifícios inferiores e superiores da cremalheira no espaço que pretende montá-la. (2)

3. Engate a parte de trás do trilho até que se coloque completamente sobre a flange da cremalheira vertical e o fecho clica no lugar. Repita estes passos para a posição e coloque a peça frontal na flange vertical da cremalheira. (3)

> [!TIP]
> Para retirar os carris, puxe o botão de desbloqueio do trinco no ponto médio da peça final (4) e desative cada trilho.

![Diagrama de instalação e remoção de trilhos sem ferramentas, com passos numerados](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Conjunto ferroviário - trilhos com ferramentas (cabides de furos roscados)

Para grelhadores com furos roscados, siga este procedimento para montar e instalar os trilhos.

1. Retire os pinos dos suportes de montagem dianteiro e traseiro com uma chave de fendas de ponta plana. (1)
1. Puxe e rode as submontagens do trinco para removê-las dos suportes de montagem. (2)
1. Fixe os trilhos de montagem esquerdo e direito às flanges de cremalheira vertical dianteiras utilizando dois pares de parafusos. (3)
1. Deslize os suportes traseiros esquerdo e direito para a frente contra as flanges de cremalheira vertical traseira e prenda-os com dois pares de parafusos. (4)

![Diagrama de instalação e remoção de trilhos com trilhos com passos numerados](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Instale o sistema na cremalheira

Siga estes passos para montar o dispositivo Azure FXT Edge Filer na cremalheira.

1. Retire os trilhos internos do cesto até que se tranquem no lugar. (1)
1. Localize o impasse do trilho traseiro em cada lado do dispositivo e baixe-os nas ranhuras traseiras J nos conjuntos de diapositivos. (2)
1. Rode o dispositivo para baixo até que todos os impasses ferroviários estejam sentados nas ranhuras J. (3)
1. Empurre o dispositivo para dentro até que as alavancas de bloqueio cliquem no lugar.
1. Pressione os botões de bloqueio de desbloqueio de diapositivos em ambos os carris (4) e deslize o dispositivo para dentro da cremalheira.

![Instale o sistema num diagrama de cremalheira, com passos numerados](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Retire o sistema da cremalheira

Para retirar o aparelho da cremalheira, siga este procedimento.

1. Localizar as alavancas de bloqueio nos lados dos trilhos internos (1).
2. Desbloqueie cada alavanca rodando-a até à sua posição de desbloqueio (2).
3. Segure firmemente os lados do sistema e puxe-o para a frente até que os impasses ferroviários estejam na frente das ranhuras J. Levante o sistema para cima e para longe da cremalheira e coloque-o sobre uma superfície nivelada (3).

![Ilustração de remover um sistema da cremalheira, com passos numerados](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Engate o trinco do slam

1. Virado para a frente, localize o fecho do bate-lo (1) em ambos os lados do sistema.
2. Os fechos ligam-se automaticamente à medida que o sistema é empurrado para dentro da cremalheira.

Para soltar os fechos ao remover o sistema, puxe-os para cima (2).

São fornecidos parafusos de montagem rígido opcionais para fixar o sistema à cremalheira para envio ou em outros ambientes instáveis. Encontre o parafuso debaixo de cada trinco e aperte-os com uma chave de fendas #2 Phillips (3).

![Ilustração numerada de envolver e soltar o trinco slam](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Instale o braço de gestão do cabo

Um braço de gestão de cabo opcional (CMA) é fornecido com o FxT Edge Filer. As instruções impressas para a sua instalação são fornecidas na embalagem.

1. Desembalar e identificar os componentes do kit de braço de gestão do cabo:
   * Bandeja CMA (1)
   * CMA (2)
   * Embrulhos de gravata de cabo de nylon (3)
   * Suportes de fixação CMA (4)
   * Cabo indicador de estado (5)

   > [!TIP]
   > Para fixar o CMA para envio na cremalheira, enrole os laços em torno dos cestos e da bandeja e aperte-os firmemente. A segurança da CMA desta forma também irá proteger o seu sistema em ambientes instáveis.

   ![Ilustração de peças de CMA](media/fxt-install/cma-kit-400.png)

2. Instale a bandeja CMA.

   A bandeja CMA fornece suporte e atua como um retentor para a CMA.

   1. Alinhe e engate cada lado da bandeja com os suportes do recetor nas bordas internas dos trilhos.
   1. Empurre a bandeja para a frente até que se clique no lugar. (1)
   1. Para retirar a bandeja, aperte os botões de desbloqueio do trinco em direção ao centro e puxe a bandeja para fora dos suportes do recetor (2).

   ![Ilustração da instalação de bandeja cma](media/fxt-install/cma-tray-install-400.png)

3. Instale os suportes de fixação CMA.

   > [!NOTE]
   >
   > * Pode fixar o CMA ao trilho de montagem direito ou esquerdo, dependendo da forma como pretende encaminhar os cabos do sistema.
   > * Por conveniência, monte o CMA no lado oposto às fontes de alimentação (lado A). Se estiver montado no lado B, o CMA deve ser desligado para remover a alimentação exterior.
   > * Retire sempre a bandeja antes de retirar as fontes de alimentação.

   ![Ilustração da instalação do suporte da CMA](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Selecione o suporte de fixação CMA apropriado para o lado onde pretende montar o CMA (lado B ou lado A).
   1. Instale o suporte de fixação CMA com a marcação A ou lateral B correspondente na parte de trás do trilho de diapositivos.
   1. Alinhe os orifícios no suporte com os pinos no trilho de diapositivos. Empurre o suporte para baixo até que se encaixe no lugar.

4. Instale o CMA.

   1. Na parte de trás do sistema, coloque o trinco na extremidade frontal da CMA no suporte mais interno do conjunto de diapositivos até que o trinco se encaixe (1).
   1. Coloque o outro trinco na extremidade do suporte mais exterior até que o trinco se encaixe (2).
   1. Para remover a CMA, desenrem ambos os fechos premindo os botões de desbloqueio CMA na parte superior das caixas interiores e exteriores (3).

   ![Ilustração da instalação principal da CMA](media/fxt-install/cma-install-400.png)

   O CMA pode ser rodado para longe do sistema de acesso e serviço. Na extremidade articulada, levante o CMA para longe da bandeja para a descolorê (1). Depois de dessentado da bandeja, afaste a CMA do sistema (2).

   ![Ilustração de CMA rodada aberta para serviço](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Instale o molde frontal (opcional)

Esta secção explica como instalar e remover o molde frontal (faceplate) para o hardware Azure FXT Edge Filer.

Para instalar o molde frontal:

1. Localize e retire a chave de moldura, que está fornecida na embalagem de moldura.
1. Alinhe o molde com a parte frontal do chassis e insira os pinos do lado direito do molde nos orifícios do suporte do suporte do nó direito.
1. Coloque a extremidade esquerda do molde no chassis. Pressione o molde até que o botão do lado esquerdo clique no lugar.
1. Tranque o molde com a chave.

Para remover o molde frontal:

1. Desbloqueie o molde utilizando a chave de moldes.
1. Pressione o botão de desbloqueio no lado esquerdo e puxe a extremidade esquerda do molde para longe do chassis.
1. Desaperte a extremidade direita e retire o molde.

   ![Imagem mostrando o botão de desbloqueio à esquerda do molde e como removê-lo puxando para fora do lado esquerdo](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Passos seguintes

Depois de desempacotar e empacotar o dispositivo, continue a configurar, ligando cabos de rede e ligando a energia CA ao Azure FXT Edge Filer.

> [!div class="nextstepaction"]
> [Cabo das portas da rede e da alimentação](fxt-network-power.md)
