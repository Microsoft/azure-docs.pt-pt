---
title: Conceitos de Design com Simulação de Acústica
titlesuffix: Azure Cognitive Services
description: Esta visão geral conceptual explica como o Project Acoustics incorpora simulação acústica ao processo de design de som.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854299"
---
# <a name="project-acoustics-design-process-concepts"></a>Conceitos de processo de design de acústica do projeto

Esta visão geral conceptual explica como o Project Acoustics incorpora simulação acústica física no processo de design de som.

## <a name="sound-design-with-audio-dsp-parameters"></a>Design de som com parâmetros DSP áudio

Os títulos interativos 3D obtêm o seu som particular utilizando blocos de processamento de sinal digital áudio (DSP) hospedados num motor de áudio. Estes blocos vão desde a simples mistura, à reverberação, eco, atraso, equalização, compressão e limitação, e outros efeitos. Selecionar, organizar e definir parâmetros sobre estes efeitos é da responsabilidade do designer de som, que constrói um gráfico áudio que alcança os objetivos estéticos e de gameplay da experiência.

Num título interativo, à medida que os sons e ouvintes se movem pelo espaço 3D, como é que estes parâmetros se adaptam às condições de mudança? O designer de som muitas vezes organiza volumes em todo o espaço que são programados para desencadear alterações de parâmetros para obter alterações nos efeitos de reverberação, por exemplo, ou para evitar sons na mistura à medida que o ouvinte se move de uma parte da cena para outra. Estão também disponíveis sistemas de acústica que podem automatizar alguns destes efeitos.

Os títulos 3D usam sistemas de iluminação e física cinética que são motivados pela física, mas ajustados por designers para alcançar uma mistura de objetivos de imersão e gameplay. Um designer visual não define valores individuais de pixels, mas ajusta modelos 3D, materiais e sistemas de transporte leve que são baseados fisicamente para trocar estética visual e custos de CPU. Qual seria o processo equivalente para áudio? O Projeto Acústica é um primeiro passo na exploração desta questão. Primeiro vamos tocar no que significa transportar energia acústica através de um espaço.

![Screenshot da cena do AltSpace sobreposto com zonas de reverberação](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Respostas de impulso: Acústico conectando dois pontos no espaço

Se estiver familiarizado com o design de áudio, pode estar familiarizado com as respostas acústicas de impulso. Uma resposta acústica de impulso modela o transporte de um som de uma fonte para um ouvinte. Portanto, uma resposta de impulso pode capturar todos os efeitos interessantes da acústica da sala, como oclusão e reverberação. As respostas de impulso também têm certas propriedades poderosas que permitem a escala de efeitos dSP áudio. Adicionar dois sinais de áudio e processar com uma resposta de impulso dá o mesmo resultado que aplicar a resposta de impulso separadamente a cada sinal e adicionar os resultados. A propagação acústica e as respostas de impulso também não dependem do áudio que está a ser processado, apenas da cena que está a ser modelada, e das localizações de origem e ouvintes. Em suma, uma resposta por impulso destila o efeito da cena na propagação sonora.

Uma resposta de impulso captura todos os efeitos acústicos da sala interessantes, e podemos aplicá-lo ao áudio de forma eficiente com um filtro, e podemos obter respostas de impulso a partir da medição ou simulação. Mas e se não quisermos que a acústica combine com a física exatamente, mas que moldá-la corresponda às exigências emocionais de uma cena? Mas, tal como os valores dos pixels, uma resposta impulsiva é apenas uma lista de milhares de números, como podemos ajustá-lo para atender às necessidades estéticas? E se quisermos ter oclusão/obstrução que varia suavemente ao passar por portas ou por detrás de obstáculos, quantas respostas de impulso precisamos para obter um efeito suave? E se a fonte se mover depressa? Como interpolamos?

Parece difícil usar simulação e respostas de impulso para alguns aspetos da acústica em títulos interativos. Mas ainda podemos construir um sistema de transporte de áudio que suporta ajustes de designers se conseguirmos ligar as nossas respostas de impulso a partir da simulação com os nossos parâmetros de efeito DSP áudio familiares.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Ligar a simulação ao DSP áudio com parâmetros

Uma resposta de impulso contém todos os efeitos acústicos interessantes (e desinteressantes). Os blocos DSP áudio, quando os seus parâmetros estão corretamente definidos, podem produzir um efeito acústico interessante. Usar simulação acústica para conduzir um bloco DSP áudio para automatizar o transporte áudio numa cena 3D é apenas uma questão de medir os parâmetros DSP áudio de uma resposta de impulso. Esta medição é bem entendida para certos efeitos acústicos comuns e importantes, incluindo oclusão, obstrução, portalismo e reverberação.

Mas se a simulação estiver ligada diretamente aos parâmetros DSP áudio, onde está o ajuste do designer? O que ganhamos? Bem, recuperamos uma quantidade significativa de memória descartando respostas de impulsos e mantendo alguns parâmetros DSP. E para dar ao designer algum poder sobre o resultado final, só precisamos de encontrar uma maneira de inserir o designer entre a simulação e o DSP áudio.

![Gráfico com resposta de impulso estilizada com parâmetros sobrepostos](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Design de som transformando parâmetros dSP áudio da simulação

Considere o efeito que os seus óculos de sol têm na sua visão do mundo. Num dia brilhante, os óculos podem reduzir o brilho a algo mais confortável. Numa sala escura, talvez não consigas ver nada. Os óculos não estabelecem um certo nível de brilho em todas as situações; só tornam tudo mais escuro.

Se utilizarmos simulação para conduzir o nosso DSP áudio utilizando parâmetros de oclusão e reverberação, podemos adicionar um filtro após o simulador para ajustar os parâmetros que o DSP 'vê'. O filtro não imporia um certo nível de oclusão ou comprimento revertido da cauda, tal como os óculos de sol não fazem de cada sala o mesmo brilho. O filtro pode fazer com que cada oclusão oclusse menos. Ou oclum mais. Ao adicionar e ajustar um filtro de parâmetro de oclusão 'escurecido', grandes salas abertas ainda teriam pouco ou nenhum efeito de oclusão, enquanto as portas aumentariam de um meio para um forte efeito de oclusão, mantendo a suavidade nas transições efetivas que a simulação fornece.

Neste paradigma, a tarefa do designer passa desde a escolha de parâmetros acústicos para cada situação, até selecionar e ajustar filtros para aplicar aos parâmetros DSP mais importantes provenientes da simulação. Eleva as atividades do designer das preocupações estreitas de criar transições suaves para as preocupações mais elevadas da intensidade dos efeitos de oclusão e reverberação e da presença de fontes na mistura. Claro que, quando a situação exige, um filtro sempre disponível é simplesmente voltar a escolher os parâmetros do DSP para uma fonte específica numa situação específica.

## <a name="sound-design-in-project-acoustics"></a>Design de som no Projeto Acústica

O pacote Project Acoustics integra cada um dos componentes acima descritos: um simulador, um codificador que extrai parâmetros e constrói o ativo de acústica, dSP áudio e uma seleção de filtros. O design de som com o Project Acoustics implica escolher parâmetros para os filtros que ajustam os parâmetros de oclusão e reverberação derivados da simulação e aplicados ao DSP áudio, com controlos dinâmicos expostos dentro do editor de jogos e do motor de áudio.

## <a name="next-steps"></a>Passos seguintes
* Experimente o paradigma de design usando o [quickstart project Acoustics para a Unidade](unity-quickstart.md) ou o [quickstart do Projeto Acoustics para Unreal](unreal-quickstart.md)
* Explore os [controlos](unity-workflow.md) de design do Projeto Acústica para a Unidade ou os controlos de design do Projeto Acústica [para Unreal](unreal-workflow.md)

