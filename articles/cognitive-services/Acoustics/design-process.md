---
title: Conceitos de Design com Simulação de Acústica
titlesuffix: Azure Cognitive Services
description: Esta visão geral conceitual explica como o projeto acústica incorpora a simulação acústica ao processo de design de som.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854299"
---
# <a name="project-acoustics-design-process-concepts"></a>Conceitos do processo de design acústicos do projeto

Esta visão geral conceitual explica como o projeto acústica incorpora a simulação de acústica física no processo de design de som.

## <a name="sound-design-with-audio-dsp-parameters"></a>Design de som com parâmetros do DSP de áudio

os títulos interativos 3D atingem seu som específico usando blocos DSP (processamento de sinal digital) de áudio hospedados em um mecanismo de áudio. Esses blocos variam em complexidade desde a simples combinação, até reverberation, eco, atraso, equalização, compactação e limitação, além de outros efeitos. A seleção, a organização e a definição de parâmetros desses efeitos é responsabilidade do designer de som, que cria um grafo de áudio que atinge as metas estética e cheia da experiência.

Em um título interativo, como os sons e o ouvinte se movem ao longo do espaço 3D, como esses parâmetros se adaptam às condições em constante mudança? O designer de som geralmente organizará volumes em todo o espaço programado para disparar alterações de parâmetro para obter alterações em efeitos reverberation, por exemplo, ou para os sons patos na combinação, à medida que o ouvinte se mover de uma parte da cena para outra. Os sistemas acústicos também estão disponíveis e podem automatizar alguns desses efeitos.

os títulos 3D usam sistemas de física de iluminação e cinemática que são motivados por física, mas que se ajustam ao designer para alcançar uma mistura de metas imersãos e de jogos. Um designer visual não define valores de pixel individuais, mas ajusta modelos 3D, materiais e sistemas de transporte leves que são todos fisicamente baseados para compensar as estética visuais e os custos de CPU. Qual seria o processo equivalente para áudio? O projeto acústica é uma primeira etapa na exploração dessa pergunta. Primeiro, vamos abordar o que significa transportar energia acústica por um espaço.

![Captura de tela da cena AltSpace sobrepostas com zonas de reverberação](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Respostas de impulso: Conectando dois pontos no espaço de uma acústica

Se você estiver familiarizado com o design de áudio, talvez esteja familiarizado com respostas de impulso acústicos. Uma resposta de impulso acústico modela o transporte de um som de uma origem a um ouvinte. Portanto, uma resposta de impulso pode capturar cada efeito interessante de acústica de sala, como oclusão e reverberation. As respostas de impulso também têm certas propriedades poderosas que permitem que os efeitos do DSP de áudio sejam dimensionados. Adicionar dois sinais de áudio juntos e processar com uma resposta de impulso fornece o mesmo resultado que aplicar a resposta de impulso separadamente a cada sinal e adicionar os resultados. A propagação acústica e as respostas de impulso também não dependem do processamento de áudio, somente da cena que está sendo modelada e dos locais de origem e de ouvinte. Em suma, uma resposta de impulso ainda disumi o efeito da cena na propagação de som.

Uma resposta de impulso captura cada efeito acústico de sala interessante e podemos aplicá-lo a áudio com eficiência com um filtro, e podemos obter respostas de impulso de medição ou simulação. Mas e se não quisermos que a acústica coincida exatamente com a física, mas, em vez disso, moldar isso para corresponder às demandas emocional de uma cena? Mas, como valores de pixel, uma resposta de impulso é apenas uma lista de milhares de números, como é possível ajustá-la para atender às necessidades estética? E se quisermos que o oclusão/obstrução varie sem problemas ao passar pelo doorways ou por trás dos obstáculos, quantas respostas de impulso precisamos para obter um efeito suave? E se a fonte se mover rapidamente? Como fazemos a interpolação?

Parece difícil usar a simulação e as respostas de impulso para alguns aspectos da acústica em títulos interativos. Mas ainda podemos criar um sistema de transporte de áudio que dá suporte a ajustes de designer se pudermos conectar nossas respostas de impulso de simulação com nossos parâmetros de efeito de DSP de áudio conhecidos.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Conectando a simulação ao DSP de áudio com parâmetros

Uma resposta de impulso contém todos os efeitos acústicos interessantes (e todos os ininteressantes). Blocos de DSP de áudio, quando seus parâmetros são definidos corretamente, podem renderizar um efeito acústico interessante. Usar a simulação acústica para direcionar um bloco de DSP de áudio para automatizar o transporte de áudio em uma cena 3D é apenas uma questão de medir os parâmetros do DSP de áudio de uma resposta de impulso. Essa medida é bem compreendida para determinados efeitos acústicos comuns e importantes, incluindo oclusão, obstrução, portal e reverberation.

Mas se a simulação estiver conectada diretamente aos parâmetros do DSP de áudio, onde é o ajuste do designer? O que obtemos? Bem, obtemos uma quantidade significativa de memória de volta, descartando respostas de impulso e retendo alguns parâmetros do DSP. E para dar ao designer algum poder sobre o resultado final, precisamos encontrar apenas uma maneira de inserir o designer entre a simulação e o DSP de áudio.

![Grafo com resposta de impulso estilizado com parâmetros sobrepostos](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Design de som transformando parâmetros do DSP de áudio da simulação

Considere o efeito que sua óculos tem em sua visão do mundo. Em um dia brilhante, os óculos podem reduzir o brilho para algo mais confortável. Em uma sala escura, talvez você não consiga ver nada. Os óculos não definem um certo nível de brilho em todas as situações; Eles apenas tornam tudo mais escuro.

Se usarmos a simulação para direcionar nosso DSP de áudio usando parâmetros oclusão e reverberation, podemos adicionar um filtro após o simulador para ajustar os parâmetros que o DSP ' vê '. O filtro não imusará um determinado nível de oclusão ou o comprimento da parte final do verbo de reverberação, de forma que o óculos não deixe cada sala o mesmo brilho. O filtro pode simplesmente fazer com que cada occluder occlude menos. Ou occlude mais. Ao adicionar e ajustar um filtro de parâmetro oclusão "escurecer", grandes salas abertas ainda terão pouco efeito de oclusão, enquanto o doorways aumentaria de um efeito de oclusão médio para um forte e, ao mesmo tempo, mantém a suavidade em transições de efeito que a simulação fornece.

Nesse paradigma, a tarefa do designer muda de escolher parâmetros acústicos para cada uma das situações, para selecionar e ajustar filtros para aplicar aos parâmetros de DSP mais importantes provenientes da simulação. Ele eleva as atividades do designer das preocupações estreitas de configurar transições suaves para as preocupações mais altas da intensidade dos efeitos oclusão e reverberation e a presença de fontes na combinação. É claro que, quando a situação exige, um filtro sempre disponível é simplesmente voltar a escolher os parâmetros do DSP para uma fonte específica em uma situação específica.

## <a name="sound-design-in-project-acoustics"></a>Design de som em acústica do projeto

O pacote acústicos do projeto integra cada um dos componentes descritos acima: um simulador, um codificador que extrai parâmetros e cria o ativo acústico, o DSP de áudio e uma seleção de filtros. O design de som com acústicas do projeto envolve a escolha de parâmetros para os filtros que ajustam os parâmetros oclusão e reverberation derivados da simulação e aplicados ao DSP de áudio, com controles dinâmicos expostos no editor de jogos e no mecanismo de áudio.

## <a name="next-steps"></a>Passos Seguintes
* Experimente o paradigma de design usando o guia de [início rápido do projeto acústicos para o Unity](unity-quickstart.md) ou o guia de [início rápido do projeto como inreal](unreal-quickstart.md)
* Explore os [controles de design acústicos do projeto para o Unity](unity-workflow.md) ou os [controles de design acústicos do projeto para inreal](unreal-workflow.md)

