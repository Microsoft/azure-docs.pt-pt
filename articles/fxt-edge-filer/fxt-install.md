---
title: 'Tutorial: Instale um dispositivo físico Azure FXT Edge Filer'
description: Como desembalar, rack e cabo o componente do dispositivo físico do cache de armazenamento híbrido Microsoft Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7c77e25d0dcabc49f2e6672645c6bc41e8662ec8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75551034"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Tutorial: Instale o Ficheiro de Borda Azure FXT 

Este tutorial descreve como instalar um nó de hardware para o cache de armazenamento híbrido Azure FXT Edge Filer. É necessário instalar pelo menos três nós de hardware para criar um cluster Azure FXT Edge Filer.

O procedimento de instalação envolve desembalar e montar o dispositivo e prender o braço de gestão do cabo (CMA) e o molde dianteiro. Um tutorial separado explica a fixação de cabos de rede e a potência de ligação. 

Leva aproximadamente uma hora para instalar um nó de filete de borda Azure FXT. 

Este tutorial inclui estes passos de configuração: 

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Monte o dispositivo numa prateleira
> * Instale o molde frontal (opcional)

## <a name="installation-prerequisites"></a>Pré-requisitos de instalação 

Antes de começar, certifique-se de que o centro de dados e a cremalheira que utilizará tem estas funcionalidades:

* Uma ranhura de 1U disponível na prateleira onde pretende montar o dispositivo.
* Sistemas de alimentação e arrefecimento caC que atendam às necessidades do Ficheiro de Borda Azure FXT. (Leia [as especificações de energia e térmica](fxt-specs.md#power-and-thermal-specifications) para ajudar a planear e dimensionar a instalação.)  

  > [!NOTE] 
  > Para tirar o máximo partido das duas unidades de alimentação redundantes (PSUs), utilize unidades de distribuição de energia em dois circuitos de ramificação diferentes ao fixar a potência CA. Leia Ligue os cabos de [alimentação](fxt-network-power.md#connect-power-cables) para obter mais detalhes.  

## <a name="unpack-the-hardware-node"></a>Desembalar o nó de hardware 

Cada nó de Filer de Borda Azure FXT é enviado numa única caixa. Complete estes passos para desembalar um dispositivo.

1. Coloque a caixa numa superfície plana e uniforme.

2. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou a embalagem estiverem severamente danificadas, não a abra. Contacte o Suporte da Microsoft para ajudar a avaliar se o dispositivo está funcional.

3. Desembale a caixa. Certifique-se de que inclui os seguintes itens:
   * Um único dispositivo FXT de recinto
   * Dois cabos de alimentação
   * Um molde frontal e chave
   * Um conjunto de kit ferroviário
   * Um braço de gestão de cabos (CMA)
   * Folheto de instruções de instalação cma
   * Folheto de instruções de instalação de rack
   * Folheto de Informação de Segurança, Ambiente e Regulação

Se não recebeu todos os itens listados, contacte o fornecedor do dispositivo para obter suporte. 

Certifique-se de que o dispositivo teve tempo suficiente para atingir a mesma temperatura que a sala antes de o instalar ou de o ligar. Se notar condensação em qualquer parte do aparelho, aguarde pelo menos 24 horas antes de instalar.

O próximo passo é montar o seu dispositivo.

## <a name="rack-the-device"></a>Montar o dispositivo em bastidor

O dispositivo Azure FXT Edge Filer deve ser instalado num rack padrão de 19 polegadas. 

A cache de armazenamento híbrido Azure FXT Edge Filer é composta por três ou mais dispositivos Azure FXT Edge Filer. Repita os passos de instalação da cremalheira para cada dispositivo que faça parte do seu sistema. 

### <a name="rack-install-prerequisites"></a>Rack instalar pré-requisitos

* Antes de começar, leia as instruções de segurança no folheto informação de segurança, ambiente e regulação que foi enviado com o seu dispositivo.

  > [!NOTE]
  > Utilize sempre duas pessoas ao levantar o nó, incluindo quando o instalar numa prateleira ou removê-lo da cremalheira. 

* Identifique o tipo de instalação ferroviária utilizada com o seu equipamento. 
  * Para obter prateleiras quadradas ou redondas, siga as instruções de transporte ferroviário sem ferramentas.
  * Para os cestos de furos roscados, siga as instruções do trilho. 
  
    Para a configuração de montagem do trilho, é necessário fornecer oito parafusos, tipo 10-32, 12-24, M5 ou M6. O diâmetro da cabeça dos parafusos deve ser inferior a 10 mm (0,4").

### <a name="identify-the-rail-kit-contents"></a>Identifique o conteúdo do kit ferroviário

Localize os componentes para a instalação do conjunto do kit ferroviário:

1. Dois conjuntos ferroviários Deslizantes A7 Dell ReadyRails II (1)
1. Duas correias de gancho e laço (2)

![Desenho numerado de conteúdo do kit ferroviário](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Montagem ferroviária - trilhos sem ferramentas (furos quadrados ou prateleiras redondas)

Para prateleiras com furos quadrados ou redondos, siga este procedimento para montar e instalar os trilhos. 

1. Posicione as peças de extremidade direita e direita da esquerda e direita rotuladas **em frente** para dentro. Posicione cada peça final de modo a que se coloque nos orifícios do lado frontal das flanges verticais. (1)

2. Alinhe cada peça final no fundo e nos orifícios superiores do rack no espaço que pretende montá-la. (2)

3. Engatar a parte de trás do trilho até que se encaixe completamente na flange vertical da cremalheira e o trinco se encaixe no lugar. Repita estes passos para posicionar e coloque a peça frontal na flange vertical do cesto. (3)

> [!TIP]
> Para retirar os trilhos, puxe o botão de desbloqueio do trinco no ponto médio da peça final (4) e desencaixe cada trilho.

![Diagrama de instalação e remoção de trilhos sem ferramentas, com passos numerados](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Montagem ferroviária - trilhos ferramentas (prateleiras de furos roscados)

Para prateleiras com orifícios roscados, siga este procedimento para montar e instalar os trilhos.

1. Retire os pinos dos suportes de montagem dianteiro e traseiro com uma chave de fendas de ponta plana. (1)
1. Puxe e rode os subconjuntos do trinco ferroviário para os retirar dos suportes de montagem. (2)
1. Fixe os trilhos de montagem esquerdo e direita às flanges da cremalheira vertical dianteira utilizando dois pares de parafusos. (3)
1. Deslize os suportes traseiros esquerdo e direito para a frente contra as flanges verticais traseiras e prenda-os utilizando dois pares de parafusos. (4)

![Diagrama de instalação e remoção de trilhos tooled, com passos numerados](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Instale o sistema na prateleira

Siga estes passos para montar o dispositivo Azure FXT Edge Filer na prateleira.

1. Puxe os trilhos de deslizamento interno para fora da prateleira até que se bloqueiem no lugar. (1)
1. Localize o impasse do trilho traseiro em cada lado do dispositivo e baixe-os para as ranhuras J traseiras nos conjuntos de diapositivos. (2) 
1. Rode o dispositivo para baixo até que todos os impasses do trilho estejam sentados nas ranhuras J. (3)
1. Empurre o dispositivo para dentro até que as alavancas de bloqueio clicem no lugar.
1. Pressione os botões de bloqueio de desbloqueio de diapositivos em ambos os trilhos (4) e deslize o dispositivo para dentro da prateleira.

![Instale o sistema num diagrama de rack, com passos numerados](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Retire o sistema da prateleira

Para retirar o dispositivo da prateleira, siga este procedimento. 

1. Localize as alavancas de bloqueio nas laterais dos trilhos internos (1).
2. Desbloqueie cada alavanca rodando-a até à sua posição de desbloqueio (2).
3. Segure os lados do sistema firmemente e puxe-o para a frente até que os impasses ferroviários estejam na frente das ranhuras J. Levante o sistema para cima e para longe da cremalheira e coloque-o sobre uma superfície nivelada (3).

![Ilustração de remover um sistema da prateleira, com passos numerados](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Engatar o trinco slam

1. Virado para a frente, localize o trinco de bater (1) em ambos os lados do sistema.
2. Os fechos atam-se automaticamente à medida que o sistema é empurrado para a cremalheira. 

Para libertar os fechos ao retirar o sistema, puxe-os para cima (2).

São fornecidos parafusos de montagem dura opcional para fixar o sistema à cremalheira para envio ou em outros ambientes instáveis. Encontre o parafuso debaixo de cada trinco e aperte-os com uma chave de fendas #2 Phillips (3).

![Ilustração numerada de engatar e soltar o trinco slam](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Instale o braço de gestão do cabo 

Um braço de gestão de cabos opcional (CMA) é fornecido com o FILER de Borda FXT. As instruções impressas para a sua instalação são fornecidas na embalagem. 

1. Desfaça as malas e identifique os componentes do kit de braço de gestão do cabo:
   * Bandeja CMA (1)
   * CMA (2)
   * Embrulhos de laço de cabo de nylon (3)
   * Suportes de fixação CMA (4)
   * Cabo indicador de estado (5) 

   > [!TIP] 
   > Para fixar o CMA para envio no cesto, enrole o laço em torno dos cestos e da bandeja e aconselhe-os com firmeza. A segurança da CMA desta forma também irá proteger o seu sistema em ambientes instáveis.

   ![Ilustração de peças CMA](media/fxt-install/cma-kit-400.png)

2. Instale a bandeja CMA.

   A bandeja cma fornece suporte e atua como um retentor para a CMA. 

   1. Alinhe e envolva cada lado da bandeja com os suportes recetores nas bordas internas dos trilhos. 
   1. Empurre a bandeja para a frente até que esta se coloque no lugar. (1)
   1. Para retirar a bandeja, aperte os botões de desbloqueio do trinco para o centro e puxe a bandeja para fora dos suportes recetores (2).

   ![Ilustração da instalação da bandeja CMA](media/fxt-install/cma-tray-install-400.png)

3. Instale os suportes de fixação CMA. 

   > [!NOTE]
   >
   > * Pode ligar o CMA ao trilho de montagem direito ou esquerdo, dependendo da forma como pretende desviar os cabos do sistema. 
   > * Por conveniência, monte o CMA no lado oposto às fontes de alimentação (lado A). Se for montado no lado B, o CMA deve ser desligado para remover a alimentação exterior. 
   > * Retire sempre a bandeja antes de retirar as fontes de alimentação. 

   ![Ilustração da instalação do suporte CMA](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Selecione o suporte de fixação CMA apropriado para o lado onde pretende montar o CMA (lado B ou lado A).
   1. Instale o suporte de fixação CMA com a marcação lateral A ou lateral correspondente na parte de trás do trilho de deslizamento.
   1. Alinhe os orifícios do suporte com os pinos do trilho de deslizamento. Empurre o suporte para baixo até que bloqueie no lugar. 

4. Instale o CMA.

   1. Na parte de trás do sistema, coloque o trinco na extremidade frontal do CMA no suporte interno do conjunto de diapositivos até que o fecho se encaixe (1). 
   1. Coloque o outro trinco na extremidade do suporte exterior até que o trinco se encaixe (2). 
   1. Para remover o CMA, desencaixe ambos os fechos premindo os botões de desbloqueio cma na parte superior das casas do fecho interno e externo (3).

   ![Ilustração da instalação principal da CMA](media/fxt-install/cma-install-400.png)

   A CMA pode ser rodada para longe do sistema de acesso e serviço. Na extremidade articulada, levante o CMA para longe da bandeja para desatar (1). Depois de dessentar da bandeja, afaste o CMA do sistema (2).

   ![Ilustração de CMA rodada aberta para o serviço](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Instale o molde frontal (opcional)

Esta secção explica como instalar e remover o molde frontal (placa frontal) para o hardware De Ficheiro de Borda Azure FXT. 

Para instalar o molde frontal: 

1. Localize e retire a chave de moldura, que está fornecida no pacote de moldura. 
1. Alinhe o molde com a frente do chassis e insira os pinos do lado direito do molde nos orifícios do suporte do nó direito. 
1. Coloque a extremidade esquerda do molde no chassis. Pressione o molde até que o botão do lado esquerdo clice no lugar.
1. Tranque o molde com a chave.

Para remover o molde dianteiro: 
1. Desbloqueie o molde utilizando a chave de moldura.
1. Pressione o botão de libertação no lado esquerdo e puxe a extremidade esquerda do molde para longe do chassis.
1. Desajuste a extremidade direita e retire o molde.
   
   ![Imagem mostrando o botão de libertação à esquerda do molde e como removê-lo puxando para fora do lado esquerdo](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Passos seguintes

Depois de ter desembalado e acoplado o dispositivo, continue a configurar, ligando cabos de rede e ligando a energia CA ao Ficheiro de Borda Azure FXT.

> [!div class="nextstepaction"]
> [Cabo as portas de rede e alimentação](fxt-network-power.md)