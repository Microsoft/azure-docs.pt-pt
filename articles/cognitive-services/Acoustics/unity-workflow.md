---
title: Tutorial de design do Unity do projeto acústicos
titlesuffix: Azure Cognitive Services
description: Este tutorial descreve o fluxo de trabalho de design para acústicas do projeto no Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: f9ff4225e7e855ed666d3554631015b8ce51df37
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706596"
---
# <a name="project-acoustics-unity-design-tutorial"></a>Tutorial de design do Unity do projeto acústicos
Este tutorial descreve as ferramentas de design e o fluxo de trabalho para acústicas do projeto no Unity.

Pré-requisitos:
* Unity 2018.2 + para Windows
* Uma cena do Unity com um ativo de inclusas acústicos

Para este tutorial, você pode obter uma cena do Unity com um ativo de inclusas acústicos de duas maneiras:
* [Adicionar acústica do projeto ao seu projeto do Unity](unity-integration.md), [obter uma conta do lote do Azure](create-azure-account.md)e, em seguida, distortar [sua cena do Unity](unity-baking.md)
* Ou use o [conteúdo de exemplo do Project acústicas Unity](unity-quickstart.md).

## <a name="review-design-process-concepts"></a>Examinar os conceitos de processo de design
Os acústicos do projeto usam métodos de DSP (processamento de sinal digital) de áudio comuns para processar suas fontes e oferece controle sobre as propriedades acústicas familiares, incluindo oclusão, mistura úmida/seca e reverberation (RT60). Mas o [conceito de processo de design acústicos do projeto](design-process.md) principal é que, em vez de definir essas propriedades diretamente, você controla como os resultados da simulação são usados para orientar essas propriedades. As configurações padrão para cada controle representam acústicos fisicamente precisos.

## <a name="design-acoustics-for-each-source"></a>Criar acústicas para cada fonte
A acústica do projeto fornece vários controles de design acústicos específicos de origem. Isso permite que você controle a combinação em uma cena enfatizando algumas fontes e enfatizando outras.

### <a name="adjust-distance-based-attenuation"></a>Ajustar atenuação baseada em distância
O DSP de áudio fornecido pelo plug-in spatializer do **projeto acústicas** do Unity respeita a atenuação baseada em distância por origem incorporada ao editor do Unity. Os controles para atenuação baseada em distância estão no componente **fonte de áudio** encontrado no painel do **Inspetor** de fontes de som, em **configurações de som 3D**:

![Captura de tela do painel de opções de atenuação de distância do Unity](media/distance-attenuation.png)

O acústica executa a computação em uma caixa de "região de simulação" centralizada no local do jogador. Se uma fonte de som estiver distante do jogador, localizada fora dessa região de simulação, somente a geometria dentro da caixa afetará a propagação do som (como a causa de oclusão), que funciona razoavelmente bem quando occluders estão na proximidade do jogador. No entanto, nos casos em que o player está em espaço aberto, mas o occluders está próximo da fonte de som distante, o som pode se tornar disoccluded inrealistamente. Nossa solução alternativa sugerida é garantir que, em tais casos, a atenuação do som caia em 0 a cerca de 45 m, a distância horizontal padrão do jogador até a borda da caixa.

![Captura de tela do painel de opções de Palestramode do Unity](media/speaker-mode.png)

### <a name="adjust-occlusion-and-transmission"></a>Ajustar oclusão e transmissão
Anexar o script **AcousticsAdjust** a uma fonte habilita parâmetros de ajuste para essa fonte. Para anexar o script, clique em **Adicionar componente** na parte inferior do painel do **Inspetor** e navegue até **scripts > ajuste acústicos**. O script tem seis controles:

![Captura de tela do script do Unity AcousticsAdjust](media/acoustics-adjust.png)

* **Habilitar acústicos** – controla se os acústicos são aplicados a essa origem. Quando desmarcada, a fonte será espacial com HRTFs ou panorâmica, mas não haverá acústica. Isso significa que não há obstrução, oclusão ou parâmetros reverberation dinâmicos, como o nível e o tempo de decaimento. Reverberation ainda é aplicado com um nível fixo e um tempo de decaimento.
* **Oclusão** -aplicar um multiplicador ao nível do oclusão DB computado pelo sistema acústicos. Se esse multiplicador for maior que 1, oclusão será exagerado, enquanto valores menores que 1 tornam o efeito de oclusão mais sutil e um valor de 0 desabilita oclusão.
* **Transmissão (DB)** – defina a atenuação (no banco de BD) causada pela transmissão por meio de geometry. Defina esse controle deslizante como seu nível mais baixo para desabilitar a transmissão. Os acústicos spatializesm o áudio seco inicial como chega em relação à geometria da cena (Portal). A transmissão fornece uma chegada seca adicional que é espacial na direção da linha de visão. Observe que a curva de atenuação de distância para a origem também é aplicada.

### <a name="adjust-reverberation"></a>Ajustar reverberation
* **Umidade (DB)** – ajusta a potência de reverberação, no BD, de acordo com a distância da origem. Os valores positivos tornam um som mais reverberant, enquanto valores negativos tornam um som mais seco. Clique no controle de curva (linha verde) para abrir o editor de curva. Modifique a curva clicando com o botão esquerdo do mouse para adicionar pontos e arrastar os pontos para formar a função desejada. O eixo x é a distância da origem e o eixo y é o ajuste de reverberação no BD. Para obter mais informações sobre como editar curvas, consulte este [manual do Unity](https://docs.unity3d.com/Manual/EditingCurves.html). Para redefinir a curva de volta para o padrão, clique com o botão direito do mouse em **umidade** e selecione **Redefinir**.
* **Escala de tempo de decaimento** -ajusta um multiplicador para o tempo de decaimento. Por exemplo, se o resultado do torta especifica um tempo de decaimento de 750 milissegundos, mas esse valor é definido como 1,5, o tempo de decaimento aplicado à fonte é de 1.125 milissegundos.
* **Portabilidade** – um ajuste aditivo na estimativa do sistema acústicos de como "em casa" o reverberation em uma fonte deve soar. Definir esse valor como 1 fará com que uma fonte sempre pareça completamente em todo o ar, ao defini-lo como-1 fará com que um som de origem seja totalmente inportado.

Anexar o script **AcousticsAdjustExperimental** a uma fonte permite parâmetros de ajuste experimental adicionais para essa fonte. Para anexar o script, clique em **Adicionar componente** na parte inferior do painel **Inspetor** e navegue até **scripts > acústicas ajustar experimentalmente**. Atualmente, há um controle experimental:

![Captura de tela do script do Unity AcousticsAdjustExperimental](media/acoustics-adjust-experimental.png)

* **Distorção de distância perceptiva** – aplique uma detorção exponencial à distância usada para calcular a taxa seca. O sistema acústico computa níveis úmida em todo o espaço, o que varia suavemente com distância e fornece indicações de distância perceptiva. Valores de detorção maiores que 1 exageram esse efeito ao aumentar os níveis de reverberation relacionados à distância, tornando o som "distante". Valores de detorção menores que 1 tornam a alteração reverberation baseada em distância mais sutil, tornando o som mais "presente".

## <a name="design-acoustics-for-all-sources"></a>Criar acústicos para todas as fontes
Para ajustar os parâmetros para todas as fontes, clique na faixa de canais no **mixer de áudio**do Unity e ajuste os parâmetros no efeito de **mixer de acústica do projeto** .

![Captura de tela do painel de personalização do mixer da unidade acústica do projeto](media/mixer-parameters.png)

* **Ajuste de umidade** -ajusta a potência de reverberação, no BD, em todas as fontes na cena com base na distância do ouvinte de origem. Os valores positivos tornam um som mais reverberant, enquanto valores negativos tornam um som mais seco.
* **RT60 Scale** -multiplicativa escalar para tempo de reverberação.
* **Usar movimento panorâmico** – controla se o áudio é de saída como Binaural (0) ou panorâmica de multicanal (1). Qualquer valor além de 1 indica Binaural. A saída Binaural é espacialada com HRTFs para uso com fones de ouvido e a saída multicanal é espacial com VBAP para uso com sistemas de alto-falante monochannel. Se estiver usando o movimento panorâmico multicanal, selecione o modo de alto-falante que corresponde às suas configurações de dispositivo, encontrado em **configurações** > do projeto**áudio**.

## <a name="check-proper-sound-source-placement"></a>Verificar posicionamento de origem de som apropriado
Fontes de som colocadas dentro de voxels ocupadas não terão tratamento acústico. Como voxels ultrapassa a geometria da cena visível, é possível posicionar uma fonte dentro de um VOXEL enquanto ele aparece unoccluded por geometria Visual. Você pode exibir os voxels acústicos do projeto alternando a caixa de seleção grade VOXEL no menu **utensílios** , no canto superior direito da exibição de **cena** .

![Captura de tela do menu utensílios do Unity](media/gizmos-menu.png)  

A exibição VOXEL também pode ajudar a determinar se os componentes visuais no jogo têm uma transformação aplicada a eles. Nesse caso, aplique a mesma transformação ao gameobject que hospeda o **Gerenciador acústicos**.

### <a name="bake-time-vs-run-time-voxels"></a>Tempo de torta versus voxels de tempo de execução
É possível exibir voxels na janela do editor em tempo de design do jogo e na janela do jogo em tempo de execução. O tamanho do voxels é diferente nessas exibições. Isso ocorre porque a interpolação de tempo de execução acústica usa uma grade VOXEL mais fina para resultados de interpolação mais suaves. O posicionamento de origem do som deve ser verificado usando o tempo de execução voxels.

Voxels do tempo de design:

![Captura de tela do projeto acústica voxels durante o tempo de design](media/voxels-design-time.png)

Voxels de tempo de execução:

![Captura de tela do projeto acústicos voxels durante o tempo de execução](media/voxels-runtime.png)

## <a name="next-steps"></a>Passos seguintes
* Explore os estudos de caso destacando os conceitos por trás do [processo de design](design-process.md)

