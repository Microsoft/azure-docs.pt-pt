---
title: Tutorial de Design de Unidade Acústica do Projeto
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve o fluxo de trabalho de design para o Projeto Acústica em Unidade.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fd00e4105ce4edae9d014df2a83c5ae3aaf778da
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68854270"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Tutorial de Design de Unidade Acústica do Projeto
Este tutorial descreve as ferramentas de design e fluxo de trabalho para o Projeto Acústica em Unidade.

Pré-requisitos:
* Unidade 2018.2+ para Windows
* Uma cena de unidade com um ativo acústico assado

Para este tutorial, você pode obter uma cena de Unidade com um ativo de acústica assado de duas maneiras:
* Adicione o [Project Acoustics ao seu projeto de Unidade,](unity-integration.md)em seguida, [obtenha uma conta De Lote Azure,](create-azure-account.md)em [seguida, asse a sua cena de Unidade](unity-baking.md)
* Ou, use o conteúdo da [amostra de Unidade Acústica do Projeto](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Conceitos de processo de design de revisão
O Project Acoustics utiliza métodos comuns de processamento de sinal digital áudio (DSP) para processar as suas fontes, e dá-lhe controlo sobre propriedades acústicas familiares, incluindo oclusão, mistura molhada/seca e comprimento da cauda de reverberação (RT60). Mas o conceito de processo de design core [Project Acoustics](design-process.md) é que, em vez de definir estas propriedades diretamente, você controla como os resultados de simulação são usados para impulsionar estas propriedades. As definições predefinidas para cada controlo representam acústica fisicamente precisa.

## <a name="design-acoustics-for-each-source"></a>Design de acústica para cada fonte
O Project Acoustics fornece uma série de controlos de design de acústica específicos de origem. Isto permite controlar a mistura numa cena, enfatizando algumas fontes e desvalorizando outras.

### <a name="adjust-distance-based-attenuation"></a>Ajustar a atenuação à base de distância
O DSP áudio fornecido pelo plugin espacial **Project Acoustics** Unitity respeita a atenuação baseada na distância por fonte incorporada no Editor de Unidade. Os controlos para a atenuação à distância encontram-se no componente **Fonte de Áudio** encontrado no painel **inspetor** de fontes sonoras, em definições de **som 3D:**

![Painel de opções de atenuação da distância de distância de screenshot de unidade](media/distance-attenuation.png)

A acústica realiza a computação numa caixa de "região de simulação" centrada na localização do jogador. Se uma fonte sonora estiver distante do leitor, localizado fora desta região de simulação, apenas a geometria dentro da caixa afetará a propagação sonora (como causar oclusão) que funciona razoavelmente bem quando os oclusores estão nas proximidades do leitor. No entanto, nos casos em que o jogador está em espaço aberto, mas os oclusos estão perto da fonte sonora distante, o som pode tornar-se irrealistamente disociludido. A nossa seleção sugerida é garantir, nesses casos, que a atenuação sonora cai para 0 a cerca de 45 m, a distância horizontal padrão do leitor até à borda da caixa.

![Screenshot do painel de opções do SpeakerMode de Unidade](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Ajustar a oclusão e a transmissão
A fixação do script **AcousticsAdjust** a uma fonte permite a finação dos parâmetros dessa fonte. Para fixar o script, clique em **Adicionar Componente** na parte inferior do painel **do Inspetor** e navegar para **scripts > Ajuste de Acústica**. O guião tem seis controlos:

![Screenshot do script Acústico de Unidade](media/acoustics-adjust.png)

* **Ativar a Acústica** - Controla se a acústica é aplicada a esta fonte. Quando não for controlada, a fonte será espacializada com HRTFs ou panorâmica, mas não haverá acústica. Isto significa que não há obstrução, oclusão ou parâmetros dinâmicos de reverberação, tais como o nível e o tempo de decomposição. A reverberação ainda é aplicada com um nível fixo e tempo de decomposição.
* **Oclusão** - Aplique um multiplicador ao nível dB de oclusão calculado pelo sistema de acústica. Se este multiplicador for superior a 1, a oclusão será exagerada, enquanto os valores inferiores a 1 tornam o efeito de oclusão mais subtil, e um valor de 0 desativa a oclusão.
* **Transmissão dB -** Descoloque a atenuação (em dB) causada pela transmissão através da geometria. Coloque este deslizador no seu nível mais baixo para desativar a transmissão. A acústica espacializa o áudio seco inicial como chegando em torno da geometria da cena (portaling). A transmissão proporciona uma chegada seca adicional que é espacializada na direção da linha de visão. Note que a curva de atenuação da distância para a fonte também é aplicada.

### <a name="adjust-reverberation"></a>Ajustar a reverberação
* **Wetness (dB)** - Ajusta a potência reverb, em dB, de acordo com a distância da fonte. Os valores positivos tornam um som mais reverberante, enquanto os valores negativos tornam um som mais seco. Clique no controlo de curvas (linha verde) para elevar o editor de curvas. Modifique a curva clicando à esquerda para adicionar pontos e arrastando esses pontos para formar a função que deseja. O eixo x está a distância da fonte e o eixo y é o ajuste reverb no dB. Para obter mais informações sobre as curvas de edição, consulte este [Manual de Unidade](https://docs.unity3d.com/Manual/EditingCurves.html). Para redefinir a curva para o padrão, clique à direita na **Wetness** e selecione **Reset**.
* **Escala de tempo** de deterioração - Ajusta um multiplicador para o tempo de decadência. Por exemplo, se o resultado do bolo especificar um tempo de decomposição de 750 milissegundos, mas este valor é definido para 1,5, o tempo de decomposição aplicado à fonte é de 1.125 milissegundos.
* **Outdoorness** - Um ajuste aditivo na estimativa do sistema de acústica de como "exterior" a reverberação numa fonte deve soar. Definir este valor para 1 fará com que uma fonte soe sempre completamente ao ar livre, enquanto a definição para -1 fará com que uma fonte soe completamente dentro de casa.

A fixação do script **AcousticsAdjustExperimental** a uma fonte permite parâmetros de afinação experimental adicionais para essa fonte. Para fixar o script, clique em **Adicionar Componente** na parte inferior do painel **do Inspetor** e navegar para **scripts > Acústica Ajustar Experimental**. Existe atualmente um controlo experimental:

![Screenshot do script Acústico De UnidadeAdjustExperimental](media/acoustics-adjust-experimental.png)

* **Dobra da distância percetual** - Aplique uma distorção exponencial à distância usada para calcular a relação seca-molhada. O sistema de acústica calcula níveis húmidos em todo o espaço, que variam suavemente com a distância e fornecem pistas de distância percetuais. Os valores de distorção superiores a 1 exageram este efeito aumentando os níveis de reverberação relacionados com a distância, tornando o som "distante". Os valores de distorção inferiores a 1 tornam a reverberação à distância mais subtil, tornando o som mais "presente".

## <a name="design-acoustics-for-all-sources"></a>Design de acústica para todas as fontes
Para ajustar os parâmetros para todas as fontes, clique na faixa do canal no **Audio Mixer**da Unity e ajuste os parâmetros no efeito Mixer Project **Acoustics.**

![Screenshot do painel de personalização do Project Acoustics Unitity Mixer](media/mixer-parameters.png)

* **Ajuste húmido** - Ajusta a potência reverb, em dB, através de todas as fontes da cena com base na distância do ouvinte de origem. Os valores positivos tornam um som mais reverberante, enquanto os valores negativos tornam um som mais seco.
* **Escala RT60** - Escalar multiplicativo para o tempo de reverberação.
* **Utilização Panning** - Controla se o áudio é saída como binaural (0) ou panorâmica multicanal (1). Qualquer valor além de 1 indica binaural. A saída binaural é espacializada com HRTFs para utilização com auscultadores e a saída multicanal é espacializada com VBAP para utilização com sistemas de altifalantes surround multicanal. Se utilizar o painel multicanal, certifique-se de que seleciona o modo de altifalante que corresponde às definições do dispositivo, encontrado em **definições** > de projeto**Áudio**.

## <a name="check-proper-sound-source-placement"></a>Verifique a colocação adequada da fonte de som
Fontes sonoras colocadas dentro de voxels ocupados não receberão tratamento acústico. Como os voxels se estendem para além da geometria da cena visível, é possível colocar uma fonte dentro de um voxel enquanto parece unocida pela geometria visual. Pode ver o Project Acoustics voxels toggling a caixa de verificação da grelha voxel no menu **Gizmos,** na parte superior direita da vista **Cena.**

![Screenshot do menu Unity Gizmos](media/gizmos-menu.png)  

O ecrã voxel também pode ajudar a determinar se os componentes visuais do jogo têm uma transformação aplicada a eles. Em caso afirmativo, aplique a mesma transformação no GameObject que acolhe o **Gestor de Acústica**.

### <a name="bake-time-vs-run-time-voxels"></a>Tempo de cozedura vs. tempo de execução voxels
É possível ver voxels na janela do editor na hora do design do jogo e na janela do jogo em tempo de execução. O tamanho dos voxels é diferente nestas vistas. Isto porque a interpolação do tempo de funcionamento da acústica utiliza uma grelha voxel mais fina para obter resultados de interpolação mais suaves. A colocação da fonte de som deve ser verificada utilizando os voxels de tempo de execução.

Tempo de design voxels:

![Screenshot do Projeto Acoustics voxels durante o tempo de design](media/voxels-design-time.png)

Voxels runtime:

![Screenshot do Projeto Acoustics voxels durante o tempo de execução](media/voxels-runtime.png)

## <a name="next-steps"></a>Passos seguintes
* Explore estudos de caso destacando os conceitos por trás do processo de [design](design-process.md)

