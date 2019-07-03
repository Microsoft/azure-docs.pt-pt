---
title: Tutorial sobre como instalar um dispositivo físico do filtro de borda de FXT do Azure | Documentos da Microsoft
description: Como descompactar, montar e instalar o componente de dispositivo físico da cache de armazenamento do filtro de borda do Microsoft Azure FXT híbrida
services: ''
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: ed9eca88e5ccc386b25acb95fa729a3cfb95cbd0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543481"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Tutorial: Instalar o filtro de borda FXT do Azure 

Este tutorial descreve como instalar um nó de hardware para a cache de armazenamento híbrido do Azure FXT Edge filtro. Tem de instalar pelo menos três nós de hardware para criar um cluster de ficheiros do Azure FXT Edge.

O procedimento de instalação envolve descompactar e rack montar o dispositivo e anexando-o braço de gestão de cabo (CMA) e front-bezel. Um tutorial separado explica anexar cabos de rede e o poder de ligação. 

Demora aproximadamente uma hora para instalar um nó de filtro de borda de FXT do Azure. 

Este tutorial inclui estes passos de configuração: 

> [!div class="checklist"]
> * Desembalar o dispositivo
> * Montar o dispositivo num bastidor
> * Instalar o bezel front-(opcional)

## <a name="installation-prerequisites"></a>Pré-requisitos de instalação 

Antes de começar, certifique-se de que o Centro de dados e rack utilizará tem estas funcionalidades:

* Um slot de 1U disponíveis no rack onde pretende montar o dispositivo.
* Fonte de alimentação AC e refrigeração sistemas que atendem às necessidades de filtro de borda de FXT do Azure. (Leia [potência e especificações térmicos](fxt-specs.md#power-and-thermal-specifications) para ajudar a planear e a instalação de tamanho.)  

  > [!NOTE] 
  > Para aproveitar ao máximo de duas unidades de fonte de alimentação redundantes (PSUs), utilize unidades de distribuição de energia em dois circuitos do ramo diferente ao anexar a energia CA. Leia [ligue os cabos de power](fxt-network-power.md#connect-power-cables) para obter detalhes.  

## <a name="unpack-the-hardware-node"></a>Descompactar o nó de hardware 

Cada nó de filtro de borda de FXT do Azure é fornecido numa única caixa. Conclua estes passos para descompactar um dispositivo.

1. Coloque a caixa numa superfície plana e uniforme.

2. Inspecione a caixa e a espuma de empacotamento para verificar se existem partes esmagadas, cortes, danos causados por água ou quaisquer outros danos óbvios. Se a caixa ou empacotamento gravemente está danificado, não abri-lo. Contacte o Suporte da Microsoft para ajudar a avaliar se o dispositivo está funcional.

3. Desembale a caixa. Certifique-se de que inclui os seguintes itens:
   * Um dispositivo FXT bastidor único
   * Dois cabos de alimentação
   * Um front-bezel e a chave
   * Assembly do um rail kit
   * Braço de gestão de um cabo (CMA)
   * Livreto de instruções de instalação CMA
   * Livreto de instruções de instalação de bastidor
   * Segurança, ambientais e informações de regulamentação livreto

Se não tiver recebido todos os itens listados, contacte o fabricante do dispositivo para obter suporte. 

Certifique-se de que o dispositivo tenha tido tempo suficiente para atingir a mesma temperatura como a sala de antes de instalá-lo ou ligá-lo na. Se observar condensação em qualquer parte do dispositivo, aguarde pelo menos de 24 horas antes de instalar.

O passo seguinte consiste em rack montar o seu dispositivo.

## <a name="rack-the-device"></a>Montar o dispositivo em bastidor

O dispositivo de filtro de borda de FXT do Azure tem de ser instalado num bastidor 19 polegadas padrão. 

A cache de ficheiros do Azure FXT Edge de armazenamento híbrida é constituído por três ou mais dispositivos de ficheiros do Azure FXT Edge. Repita os passos de instalação de rack para cada dispositivo que faz parte do seu sistema. 

### <a name="rack-install-prerequisites"></a>Pré-requisitos de instalação de bastidor

* Antes de começar, leia as instruções de segurança no livreto de segurança, ambientais e informações de normas que foi lançado com o seu dispositivo.

  > [!NOTE]
  > Utilize sempre a duas pessoas quando a elevação de nó, incluindo quando instalá-lo num bastidor ou removê-lo do rack. 

* Identifica o tipo de instalação do rail utilizado com o bastidor de equipamento. 
  * Para quadrado ou redondo buraco snap-in racks, siga as instruções de ferramenta sem rail.
  * Para racks buraco com threads, siga as instruções do tooled rail. 
  
    Para a grade tooled montar a configuração, terá de fornecer oito screws, tipo de 10 32, 12 a 24, M5 ou M6. O diâmetro principal dos screws tem de ser inferior a 10 mm (0.4").

### <a name="identify-the-rail-kit-contents"></a>Identificar o conteúdo do kit rail

Localize os componentes para instalar o assembly do rail kit:

1. II de ReadyRails Dell dois A7 deslizante assemblies do rail (1)
1. Dois conectar e fazer um loop straps (2)

![Desenho numerado de conteúdo do kit de rail](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Assembly do Rail - ferramenta sem rails (buraco quadrado ou buraco redondo racks)

Para racks com brechas snap-in do quadrado ou executar repetidamente, siga este procedimento para montar e instalar os rails. 

1. Posicionar as partes de fim de left e right rail rotuladas **front-** inward a enfrentar. Posicione cada peça final para que ele postos de trabalho os buracos no lado do front-de flanges o bastidor vertical. (1)

2. Alinhe cada peça final os buracos na parte inferior e superior de bastidor no espaço que pretende montá-la. (2)

3. Interagir com o back-end do rail até ele totalmente o flange vertical rack e os cliques de bloqueio temporário no lugar certo. Repita estes passos para posicionar e a parte de front-end no flange bastidor vertical de usuários. (3)

> [!TIP]
> Para remover os rails, extrair o botão de versão do bloqueio temporário no ponto médio peça final (4) e unseat cada rail.

![Diagrama de instalação e remoção de ferramenta sem rails, com os passos numerados](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Assembly do Rail - rails tooled (Threaded buraco racks)

Para racks com falhas com threads, siga este procedimento para montar e instalar os rails.

1. Remova os pins de frente e traseiro montar Retos com um screwdriver colocado para fixa. (1)
1. Extrair e girar os subassemblies de bloqueio temporário do rail para removê-los dos colchetes de montagem. (2)
1. Anexe à esquerda e direita montar rails para as flanges de front-rack vertical através de dois pares de screws. (3)
1. Deslize o lado esquerda e direita de cópia de parênteses Retos reencaminhar contra os flanges rear vertical rack e anexe-os através de dois pares de screws. (4)

![Diagrama de instalação e remoção de tooled rails, com os passos numerados](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Instalar o sistema no rack

Siga estes passos para montar o dispositivo de ficheiros do Azure FXT Edge no rack.

1. Extrair os rails slide interna fora do rack até que eles bloquear no lugar certo. (1)
1. Localize o standoff rear rail em cada lado do dispositivo e reduzi-los para o traseiro J ranhuras nos assemblies de slide. (2) 
1. Roda o dispositivo para baixo até que todos os standoffs de rail são sentados nas ranhuras-J. (3)
1. Envie o dispositivo inward até que o alavancas de bloqueio, clique no lugar certo.
1. Pressione os botões de bloqueio de slide lançamento em ambos os rails (4) e deslize o dispositivo em rack.

![Instalar o sistema num diagrama de rack, com os passos numerados](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Remover o sistema de bastidor

Para remover o dispositivo de rack, siga este procedimento. 

1. Localize as alavancas de bloqueio nos lados dos rails internas (1).
2. Desbloquear cada alavanca por girando-o até sua posição de versão (2).
3. Entender os lados do sistema firmemente e recebê-los para frente até que o standoffs da grade são na frente da J-ranhuras. Leve o sistema de cima e para fora do rack e coloque-o numa superfície de nível (3).

![Ilustração da remoção de um sistema de rack, com os passos numerados](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Interaja com a trava mais

1. Com acesso à frente, localize o bloqueio temporário de mais (1) em ambos os lados do sistema.
2. As travas envolver-se automaticamente à medida que o sistema é enviado para o bastidor. 

Para disponibilizar as travas ao remover o sistema, selecioná-los a cópia de segurança (2).

Opcionais screws de disco rígido montagem são fornecidas para proteger o sistema de rack para envio ou em outros ambientes instáveis. Encontre o screw em cada bloqueio temporário e reforçá-los com um #2 screwdriver de Phillips (3).

![Ilustração de como utilizar e liberar o bloqueio temporário de mais de numerados](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Instalar o braço de gestão do cabo 

Um braço de gestão de cabo opcional (CMA) é fornecido com o filtro de borda FXT. São fornecidas instruções impressas para instalá-lo no pacote. 

1. Descompactar e identificar os componentes do kit de arm de gestão de cabo:
   * Bandeja de CMA (1)
   * CMA (2)
   * Empate de cabo nylon encapsula (3)
   * Colchetes de anexo de CMA (4)
   * Cabo de indicador de estado (5) 

   > [!TIP] 
   > Para proteger o CMA para envio no rack, inclui o empate em torno baskets e Bandeja de loop e cinch-los firmemente. Proteger o CMA desta forma também irá proteger o seu sistema em ambientes instáveis.

   ![Ilustração de partes CMA](media/fxt-install/cma-kit-400.png)

2. Instale a Bandeja do CMA.

   O tabuleiro CMA fornece suporte e age como um retainer para o CMA. 

   1. Alinhar e interagir com cada lado da bandeja do Retos recetor nas margens internas dos rails. 
   1. Enviar por push o tabuleiro para a frente até que ele clica no lugar certo. (1)
   1. Para remover o tabuleiro, compactar os botões de versão do bloqueio temporário em direção ao centro e extrair a Bandeja fora os colchetes de destinatário (2).

   ![Ilustração da instalação de tabuleiro de CMA](media/fxt-install/cma-tray-install-400.png)

3. Instale os colchetes de anexo de CMA. 

   > [!NOTE]
   >
   > * Pode anexar o CMA para a direita ou esquerda montar rail, dependendo da forma como pretende cabos de rota do sistema. 
   > * Para sua comodidade, Monte o CMA no lado do oposta as fontes de alimentação (A-lado). Se este está montado no lado B, o CMA deve ser desligada para remover a fonte de alimentação externo. 
   > * Sempre remova a Bandeja antes de remover o poder fornece. 

   ![Ilustração da instalação de colchete CMA](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Selecione o colchete de anexo de CMA apropriado para o lado onde pretende montar o CMA (lado B ou lado A).
   1. Instale o colchete de anexo de CMA com o correspondente A de lado ou de marcação de lado B remissivo rail o slide.
   1. Alinhe os buracos no colchete com os pins no rail slide. Empurrar o colchete até que ele bloqueia no lugar certo. 

4. Instale o CMA.

   1. Parte traseira desse sistema, ajustar o colchete mais interno do slide assembly bloqueio temporário no front-end do CMA até que o bloqueio temporário é ativado (1). 
   1. Ajustar o bloqueio temporário no final do Reto de abertura mais externo até que o bloqueio temporário é ativado (2). 
   1. Para remover o CMA, se desligue ambas as travas pressionando os botões de versão CMA na parte superior do housings bloqueio temporário interna e externa (3).

   ![Ilustração da instalação de CMA principal](media/fxt-install/cma-install-400.png)

   O CMA pode ser girado para fora do sistema para o acesso e o serviço. No final hinged, migrar o CMA na direção oposta a Bandeja para unseat-lo (1). Quando estiver unseated da bandeja do, girar o CMA longe do sistema (2).

   ![Ilustração da CMA girado aberta para o serviço](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Instalar o bezel front-(opcional)

Esta secção explica como instalar e remover o front-bezel (faceplate) para o hardware de ficheiros do Azure FXT Edge. 

Para instalar o front-bezel: 

1. Localizar e remover a chave de bezel, que é fornecida no pacote de bezel. 
1. Alinhar bezel na parte frontal do chassi e inserir os pins no lado direito do bezel os buracos em flange de montagem de rack do nó do lado direito. 
1. Ajustar a extremidade esquerda da bezel para os chassis. Prima o bezel até que o botão do lado esquerdo clica no lugar certo.
1. Bloquear o bezel com a chave.

Para remover o front-bezel: 
1. Desbloqueie o bezel utilizando a chave de bezel.
1. Prima o botão de versão no lado esquerdo e extrair a extremidade esquerda da bezel na direção oposta chassi.
1. Unhook extremidade direita e remova o bezel.
   
   ![Imagem que mostra o botão de versão à esquerda do bezel e como removê-lo ao extrair exterior do lado esquerdo](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Passos Seguintes

Depois de ter descompactado e montou em Bastidor o dispositivo, continue a configuração ao anexar cabos de rede e ligar a energia CA para o filtro de borda de FXT do Azure.

> [!div class="nextstepaction"]
> [Ligar as portas de rede e fornecer energia](fxt-network-power.md)