---
title: Conceitos de Design com Simulação de Acústica
titlesuffix: Azure Cognitive Services
description: Esta descrição geral conceptual explica como o projeto Acoustics incorpora acústica simulação para o processo de design de som.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: dd27b660dfdd1f4bcec89291b10fd87750ad4c49
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58136161"
---
# <a name="project-acoustics-design-process-concepts"></a>Conceitos de processo de Design do projeto Acoustics

Esta descrição geral conceptual explica como o projeto Acoustics incorpora simulação acústica física no processo de design de som.

## <a name="sound-design-with-audio-dsp-parameters"></a>Estrutura sólida com parâmetros DSP áudio

Títulos interativos 3D atingir seus determinado som com áudio sinal digital, processamento de blocos (DSP) hospedados num mecanismo de áudio. Estes variam de blocos em complexidade de misturar simples, para reverberation, eco, atraso, equalization, compressão e a limitação e outros efeitos. Selecionar, organizar e definir os parâmetros nesses efeitos são da responsabilidade do estruturador de som, que cria um gráfico de áudio que atinja os objetivos estética e jogabilidade da experiência.

Num título de interativo, como os sons e o serviço de escuta de passar por todo o espaço 3D, como esses parâmetros adaptar às mudanças nas condições? O designer de som, muitas vezes, assume a responsabilidade volumes por todo o espaço que são programadas para acionar as alterações do parâmetro para alcançar as alterações no efeitos reverberation, por exemplo, ou para sons de pato em sua composição conforme o serviço de escuta se move de uma parte da cena para outro. Sistemas de Acoustics também estão disponíveis que pode automatizar alguns desses efeitos.

Títulos 3D utilizam sistemas de física iluminação e kinematic que estão motivados de física, mas designer ajustado para atingir uma combinação dos objetivos de imersão e o jogo. Um designer visual não define os valores de pixel individuais, mas em vez disso, se ajusta modelos 3D, materiais e sistemas de transporte leves tudo fisicamente baseadas para trade estética visual e os custos de CPU. O que seria o processo equivalente para áudio? Projeto Acoustics é um primeiro passo para a exploração dessa pergunta. Em primeiro lugar mencionaremos o que significa acoustical energia por meio de um espaço de transporte.

![zonas de reverberação](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Respostas de impulso: Acoustically ligar dois pontos no espaço

Se estiver familiarizado com o design de áudio, talvez esteja familiarizado com as respostas de impulso acústico. Uma resposta de impulso acústico modela o transporte de um som de uma origem para um serviço de escuta. Por conseguinte, uma resposta de impulso pode capturar cada efeito interessante de acoustics sala como oclusão e reverberation. Respostas de impulso também tem determinadas propriedades poderosas que permitem que os efeitos de DSP áudio de dimensionar. Somar dois sinais de áudio e de processamento com uma resposta de impulso fornece o mesmo resultado que aplicar a resposta de impulso em separado para cada sinal e adicionar os resultados. Propagação acústica e respostas de impulso também não dependem de áudio a ser processado apenas em cena a ser modelada e as localizações de origem e o serviço de escuta. Em resumo, uma resposta de impulso distila o efeito da cena na propagação de som.

Captura de uma resposta de impulso cada efeito acústico sala interessante e podemos aplicá-lo para áudio com eficiência com um filtro e pode obter respostas de impulso medida ou simulação. Mas e se podemos não bastante desejam acoustics para corresponder exatamente a física, mas em vez disso, moldá-lo de acordo com as demandas emocional de uma cena? Mas muito como valores de pixel, uma resposta de impulso é apenas uma lista de milhares de números, como estamos possivelmente ajustar para atender às necessidades estética? E, e se quiséssemos oclusão/obstrução varia suavemente ao passar por meio de doorways ou por trás de obstáculos, quantos respostas de impulso que é necessário obter um efeito uniforme? E se a origem move rápida? Como podemos interpolar?

Isso parece difícil de utilizar a simulação e respostas de impulso para alguns aspectos do acoustics em títulos interativos. Mas ainda é possível criar um sistema de transporte de áudio que suporte a designers ajustes se podemos ligar as nossas respostas de impulso de simulação com nossos parâmetros de efeito DSP áudio familiares.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Ligar a simulação para áudio DSP com parâmetros

Uma resposta de impulso contém todos os interessantes (e cada desinteressante) acoustical efeito. Blocos DSP áudio, quando seus parâmetros estão definidos corretamente, podem processar efeito acoustical interessante. Usando a simulação acoustical para orientar um bloco DSP áudio para automatizar o transporte de áudio numa cena 3D é apenas uma questão de medir os parâmetros DSP áudio de uma resposta de impulso. Esta medida é bem compreendida determinados efeitos acoustical importantes e comuns incluindo oclusão, obstrução, portalling e reverberation.

Mas se a simulação é ligada diretamente para os parâmetros DSP áudio, em que é o ajuste de designer? O que obtemos de? Bem, obtemos uma quantidade significativa de memória volta ao descartar as respostas de impulso e manter alguns parâmetros DSP. E para dar o designer de algum poder sobre o resultado final, tem de localizar apenas uma forma de inserir o designer entre a simulação e o áudio DSP.

![parâmetros de resposta de impulso](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Estrutura sólida, transformando os parâmetros DSP áudio de simulação

Considere o efeito de que sua óculos de sol tem na sua visão de mundo. Num bom dia, os óculos podem reduzir a brilhar para algo mais à vontade. Numa sala escura, poderá não conseguir ver nada em todos os. Os óculos não definir um certo nível de luminosidade em todas as situações; eles apenas tornam tudo mais escura.

Se usarmos a simulação para orientar o nosso DSP áudio usando parâmetros oclusão e reverberation, podemos adicionar um filtro depois do simulador para ajustar os parâmetros que o DSP 'vê". O filtro não impor um determinado nível de oclusão ou reverberação cauda comprimento, muito como óculos de sol não disponibilizar cada espaço o brilho do mesmo. O filtro poderá apenas fazer cada occluder occlude menor. Ou occlude muito mais. Ao adicionar e ajustar um filtro de parâmetro oclusão "darkening", os ambientes de grandes dimensões, abra ainda não teria pouco ou nenhum efeito oclusão, enquanto doorways aumentaria uma média para um efeito de oclusão forte, retendo os smoothness em vigor transições que fornece a simulação.

Esse paradigma, tarefas do designer muda escolham acústicos parâmetros para cada situação, filtros para selecionar e ajuste a aplicar aos parâmetros mais importantes de DSP proveniente de simulação. Ela eleva atividades do designer das preocupações estreitas de configuração de transições uniformes para as preocupações mais elevadas de intensidade dos efeitos oclusão e reverberation e a presença de origens em sua composição. É claro, quando exige a situação, um filtro sempre disponível é simplesmente voltar para escolher os parâmetros DSP para uma origem específica numa situação específica.

## <a name="sound-design-in-project-acoustics"></a>Design de som no projeto Acoustics

O pacote de projeto Acoustics integra-se cada um dos componentes descritos acima: um simulador, um codificador que extrai os parâmetros e cria o elemento de acoustics DSP áudio e uma seleção de filtros. Design de som com o projeto Acoustics implica escolher parâmetros para os filtros que ajustar os parâmetros oclusão e reverberation derivada de simulação e aplicada para o áudio DSP, com controles dinâmicos expostos dentro do editor de jogo e o mecanismo de áudio.

## <a name="next-steps"></a>Passos Seguintes
* Experimente o paradigma de design utilizando o [início rápido de Acoustics de projeto para Unity](unity-quickstart.md) ou o [Acoustics de projeto manual de início rápido para Unreal](unreal-quickstart.md)
* Explore os [controles de design Acoustics de projeto para Unity](unity-workflow.md) ou o [Acoustics de projeto de desenvolver controles para Unreal](unreal-workflow.md)

