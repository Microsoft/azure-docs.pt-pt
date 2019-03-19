---
title: Tutorial de Design do projeto Acoustics Unity
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve o fluxo de trabalho de design do projeto Acoustics no Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: kegodin
ms.openlocfilehash: 7f079c511a32cfcf0fa018d40abb737ad08f3821
ms.sourcegitcommit: f68b0e128f0478444740172f54e92b453df696be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58137963"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Tutorial de Design do projeto Acoustics Unity
Este tutorial descreve as ferramentas de design e o fluxo de trabalho do projeto Acoustics no Unity.

Pré-requisitos:
* Unity 2018.2 + para Windows
* Uma cena Unity com um recurso de acoustics incorporadas

Para este tutorial, pode obter uma cena Unity com um recurso de acoustics incorporadas de duas formas:
* [Adicionar projeto Acoustics ao seu projeto Unity](unity-integration.md), em seguida, [obter uma conta do Azure Batch](create-azure-account.md), em seguida, [inserir sua cena Unity](unity-baking.md)
* Em alternativa, utilizar o [conteúdo de exemplo de projeto Acoustics Unity](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Reveja conceitos de design do processo
Projeto Acoustics usa o métodos de processamento de sinais (DSP) de digital de áudio comuns para processar suas origens e permite que controle propriedades de acoustics familiares, incluindo oclusão, a mistura de wet/dry e comprimento de cauda reverberation (RT60). Mas o núcleo [conceito de processo de design de projeto Acoustics](design-process.md) é que em vez de definir essas propriedades diretamente, controlar a forma como os resultados da simulação são usados para orientar essas propriedades. As definições predefinidas para cada controle representam acoustics fisicamente precisos.

## <a name="design-acoustics-for-each-source"></a>Acoustics de design para cada origem
Projeto Acoustics fornece uma série de controles de design de acoustics específicos da fonte. Isto permite-lhe controlar a combinação de uma cena, que realçam algumas origens e eliminação emphasizing outras pessoas.

### <a name="adjust-distance-based-attenuation"></a>Ajustar com base na distância atenuação
O áudio DSP fornecida pelos **projeto Acoustics** Plug-in do Unity spatializer respeita a atenuação de com base na distância do código-fonte criada no Editor do Unity. Controlos de atenuação com base na distância estão no **origem de áudio** componente encontrado no **Inspetor** origens de painel de som, em **definições de som 3D**:

![Atenuação de distância](media/distance-attenuation.png)

Acoustics executa o cálculo numa caixa "região de simulação" centrada a localização de player. Se uma origem de som é distante do jogador, localizado fora desta região de simulação, apenas a geometria na caixa de afetará a propagação de som (por exemplo, fazendo com que oclusão) que funciona razoavelmente bem quando occluders estão perto o jogador. No entanto, em casos quando o jogador está no espaço aberto, mas os occluders estão próximos da origem de som distante, o som pode se tornar inacreditavelmente disoccluded. Nossa solução sugerida é Certifique-se nestes casos que a atenuação de som recai como 0 em cerca de 45 m, a distância de horizontal padrão do player na periferia da caixa.

![SpeakerMode](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Ajuste oclusão e a transmissão
A anexar o **AcousticsAdjust** script para uma origem permite parâmetros de ajuste para essa origem. Para anexar o script, clique em **Add Component** na parte inferior dos **Inspetor** painel e navegue para **Scripts > Acoustics ajustar**. O script tem seis controles:

![AcousticsAdjust](media/acoustics-adjust.png)

* **Ativar Acoustics** -controla se acoustics se aplica a esta origem. Quando desmarcado, a origem irá ser spatialized com HRTFs ou de movimento panorâmico mas não haverá nenhuma acoustics. Isso significa que nenhum obstrução, oclusão ou parâmetros de reverberation dinâmica, como o tempo de nível e Win32/decay. Reverberation ainda está a ser aplicada com um nível fixo e tempo de Win32/decay.
* **Oclusão** -aplicar um multiplicador para o nível de dB oclusão calculado pelo sistema acoustics. Se este multiplicador for superior a 1, irá ser exagerada oclusão, enquanto valores inferior a 1 fazer o efeito de oclusão mais sutil e um valor de 0 desativa oclusão.
* **Transmissão (dB)** -definir a atenuação (no dB) causada por transmissão por meio de geometria. Defina este controlo de deslize para o nível mais baixo para desativar a transmissão. Acoustics spatializes o áudio de dry inicial como que chegam em torno de geometria da cena (portaling). Transmissão fornece uma chegada de dry adicional que é spatialized na direção de linha de visão. Tenha em atenção que a curva de atenuação de distância para a origem também é aplicada.

### <a name="adjust-reverberation"></a>Ajustar reverberation
* **Umidade (dB)** -ajusta o poder de reverberação no dB, de acordo com a distância da origem. Valores positivos emitir um som mais reverberant, enquanto valores negativos emitir um som mais dry. Clique no controle de curva (linha verde) para abrir o editor de curva. Modificar a curva left-clicking para adicionar pontos e arrastando esses pontos para formar a função que pretende. O eixo x é a distância de origem e o eixo y é o ajuste de reverberação no dB. Para obter mais informações sobre a edição de curvas, consulte esta [Manual do Unity](https://docs.unity3d.com/Manual/EditingCurves.html). Repor a curva predefinido, clique em **Umidade** e selecione **repor**.
* **Win32/decay escala de tempo** -ajusta um multiplicador para a hora de Win32/decay. Por exemplo, se o resultado de criar Especifica um tempo de Win32/decay de 750 milissegundos, mas este valor é definido como 1,5, o tempo de Win32/decay aplicado para a origem é 1.125 milissegundos.
* **Outdoorness** -um ajuste aditiva na estimativa do sistema acoustics do como "pessoas" reverberation numa origem deve parecer. A definição deste valor como 1 fará com que uma origem sempre som completamente pessoas, enquanto a defini-la como -1 fará com que uma origem de som indoors completamente.

A anexar o **AcousticsAdjustExperimental** script para uma origem permite parâmetros de ajuste experimentais adicionais para essa origem. Para anexar o script, clique em **Add Component** na parte inferior dos **Inspetor** painel e navegue para **Scripts > Acoustics ajustar Experimental**. Atualmente, existe um controlo experimental:

![AcousticsAdjustExperimental](media/acoustics-adjust-experimental.png)

* **Warp de distância percetual** -aplicar um distorcendo exponencial para a distância usada para calcular o rácio de dry wet. O sistema de acoustics calcula wet níveis por todo o espaço, que variam sem problemas com a distância e proporcionam indicações de percepção de distância. Distorcendo valores maiores que 1 exaggerate esse efeito, aumentando os níveis de reverberation relacionados com a distância, tornando o som "distante". Distorcendo valores de marca de menos de 1 a reverberation com base na distância alterar mais sutil, garantindo o som mais "presente".

## <a name="design-acoustics-for-all-sources"></a>Acoustics de design para todas as origens
Para ajustar os parâmetros para todas as origens, clique na faixa do canal do Unity **Mixer de áudio**e ajustar os parâmetros na **projeto Acoustics Mixer** efeito.

![Personalização do Mixer](media/mixer-parameters.png)

* **Ajustar umidade** -ajusta o poder de reverberação no dB, em todas as origens da cena com base na distância do serviço de escuta de origem. Valores positivos emitir um som mais reverberant, enquanto valores negativos emitir um som mais dry.
* **Dimensionamento RT60** - multiplicadora escalar para o tempo de reverberação.
* **Utilizar Panning** -controles se áudio é saída como binaural (0) ou multicanal movimento panorâmico (1). Qualquer valor, além de 1 indica binaural. Binaural saída é spatialized com HRTFs para utilização com auscultadores e multicanal de saída é spatialized com VBAP para utilização com sistemas de orador surround multicanal. Se utilizar o panner multicanal, não se esqueça de selecionar o modo de altifalante que corresponde a definições do dispositivo, encontra-se em **definições do projeto** > **áudio**.

## <a name="check-proper-sound-source-placement"></a>Colocação de som de origem correto de verificação
Origens de som umístit voxels ocupados não obterá acústico tratamento. Como voxels ultrapassar a geometria da cena visível, é possível colocar uma origem de dentro de um voxel enquanto ele é exibido unoccluded por visual geometry. Pode ver o projeto Acoustics voxels, Ativando a caixa de verificação de grade voxel no **Gizmos** menu, no canto superior direito dos **cena** vista.

![Menu de gizmos](media/gizmos-menu.png)  

A exibição de voxel também pode ajudar a determinar se os componentes visuais no jogo tem uma transformação aplicada às mesmas. Se assim for, aplicam-se a mesma transformação para a hospedagem de GameObject a **Acoustics Manager**.

### <a name="bake-time-vs-run-time-voxels"></a>Inserir hora vs. tempo de execução voxels
É possível ver voxels na janela do editor durante o design de jogos e na janela de jogo em tempo de execução. O tamanho do voxels é diferente nesses modos de exibição. Isto acontece porque a interpolação de tempo de execução acoustics utiliza uma grade voxel mais para resultados de interpolação mais suaves. Colocação de som de origem deve ser verificada usando o voxels de tempo de execução.

Voxels de tempo de design:

![VoxelsDesignTime](media/voxels-design-time.png)

Voxels de tempo de execução:

![VoxelsRuntime](media/voxels-runtime.png)

## <a name="next-steps"></a>Passos Seguintes
* Estudos de caso, realce os conceitos por trás de explorar o [criar processo](design-process.md)

