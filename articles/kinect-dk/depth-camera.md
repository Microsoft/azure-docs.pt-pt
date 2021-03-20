---
title: Câmera de profundidade Azure Kinect DK
description: Compreenda os princípios de funcionamento e as principais características da câmara de profundidade no seu Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azul, sensor, sdk, câmara de profundidade, tof, princípios, desempenho, invalidação
ms.openlocfilehash: 22f04b983ed7c6a2ab19a5c1c709621655ee31c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277510"
---
# <a name="azure-kinect-dk-depth-camera"></a>Câmera de profundidade Azure Kinect DK

Esta página cobre como utilizar a câmara de profundidade no seu Azure Kinect DK. A câmara de profundidade é a segunda das duas câmaras. Como coberto em secções anteriores, a outra câmara é a câmara RGB.  

## <a name="operating-principles"></a>Princípios de funcionamento

A câmara de profundidade Azure Kinect DK implementa o princípio de Tempo de Voo (ToF) de Amplitude Kinect (AMCW). A câmara lança iluminação modulada no espectro quase IR (NIR) para a cena. Em seguida, grava uma medição indireta do tempo que leva a luz a viajar da câmara para a cena e para trás.

Estas medições são processadas para gerar um mapa de profundidade. Um mapa de profundidade é um conjunto de valores de coordenadas Z para cada pixel da imagem, medido em unidades de milímetros.

Juntamente com um mapa de profundidade, também obtemos uma chamada leitura ir limpa. O valor dos pixels na leitura iv limpa é proporcional à quantidade de luz devolvida da cena do crime. A imagem parece-se com uma imagem normal do IR. A figura abaixo mostra um mapa de profundidade de exemplo (à esquerda) e uma imagem iv limpa correspondente (direita).

![Profundidade e IR lado a lado](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>Principais funcionalidades

As características técnicas da câmara de profundidade incluem:

- Chip de imagem ToF de 1-Megapixel com tecnologia de pixel avançada que permite maiores frequências de modulação e precisão de profundidade.
- Dois díodos laser NIR que permitem modos de profundidade próximos e largos do campo de visão (FoV).
- O pixel ToF mais pequeno do mundo, a 3,5μm por 3,5μm.
- Seleção automática por pixel ganhando permitindo uma grande gama dinâmica permitindo que objetos próximos e distantes sejam capturados de forma limpa.
- Obturador global que permite um melhor desempenho à luz do sol.
- Método de cálculo de profundidade em várias fases que permite uma precisão robusta mesmo na presença de chip, laser e variação de alimentação.
- Erros sistemáticos e aleatórios baixos.

![Módulo de Profundidade](./media/concepts/depth-camera-depth-module.jpg)

A câmara de profundidade transmite imagens iv moduladas cruas para o PC anfitrião. No PC, o software acelerado do motor de profundidade da GPU converte o sinal bruto em mapas de profundidade.A câmara de profundidade suporta vários modos. Os modos **de campo de visão estreito (FoV)** são ideais para cenas com menores extensões nas dimensões X e Y, mas maiores extensões na dimensão Z. Se a cena tem grandes extensões X e Y, mas gamas Z mais pequenas, os **modos FoV largos** são mais adequados.

A câmara de profundidade suporta **modos de ligação 2x2** para alargar a gama Z em comparação com os **modos não encadernados correspondentes**. O binário é feito à custa da redução da resolução de imagem. Todos os modos podem ser executados até 30 fotogramas por segundo (fps), com exceção do modo de 1 megapixel (MP) que funciona a uma taxa máxima de fotogramas de 15 fps. A câmara de profundidade também fornece um **modo IR passivo**. Neste modo, os iluminadores da câmara não estão ativos e apenas é observada a iluminação ambiente.

## <a name="camera-performance"></a>Desempenho da câmera

O desempenho da câmara é medido como erros sistemáticos e aleatórios.

### <a name="systematic-error"></a>Erro Sistemático

O erro sistemático é definido como a diferença entre a profundidade medida após a remoção do ruído e a profundidade correta (verdade do solo). Calculamos a média temporal sobre muitos quadros de uma cena estática para eliminar o ruído de profundidade tanto quanto possível. Mais precisamente, o erro sistemático é definido como:

![Erro sistemático de profundidade](./media/concepts/depth-camera-systematic-error.png)

Onde *d <sub>t</sub>* denota a profundidade da medida no tempo *t*, *N* é o número de quadros usados no procedimento médio e *d <sub>gt</sub>* é a profundidade da verdade no solo.

A especificação de erro sistemática da câmara de profundidade exclui interferências multi-caminhos (MPI). MPI é quando um pixel sensor integra luz que é refletida por mais de um objeto. O MPI é parcialmente atenuado na nossa câmara de profundidade usando frequências de modulação mais altas, juntamente com a invalidação de profundidade, que introduziremos mais tarde.

### <a name="random-error"></a>Erro aleatório

Vamos supor que tiramos 100 imagens do mesmo objeto sem mover a câmara. A profundidade do objeto será ligeiramente diferente em cada uma das 100 imagens. Esta diferença é causada pelo ruído de tiro. O ruído de tiro é o número de fotões que atingem o sensor varia por um fator aleatório ao longo do tempo. Definimos este erro aleatório numa cena estática como o desvio padrão de profundidade ao longo do tempo calculado como:

![Erro aleatório de profundidade](./media/concepts/depth-camera-random-error.png)

Quando *N* denota o número de medições de profundidade, *d <sub>t</sub>* representa a medição de profundidade no momento *t* e *d* denota o valor médio calculado sobre todas as medições de profundidade *d <sub>t</sub>*.

## <a name="invalidation"></a>Invalidação

Em determinadas situações, a câmara de profundidade pode não fornecer valores corretos para alguns pixels. Nestas situações, os pixels de profundidade são invalidados. Os pixels inválidos são indicados pelo valor de profundidade igual a 0. As razões para que o motor de profundidade não seja capaz de produzir valores corretos incluem:

- Fora da máscara ativa de iluminação ir
- Sinal de IR saturado
- Sinal de IR baixo
- Filtrar fora
- Interferência multi-caminhos

### <a name="illumination-mask"></a>Máscara de iluminação

Os pixels são invalidados quando estão fora da máscara de iluminação ir ativa. Não recomendamos a utilização do sinal de tais pixels para calcular a profundidade. A figura abaixo mostra o exemplo de invalidação por máscara de iluminação. Os pixels invalidados são os pixels de cor preta fora do círculo nos modos FoV largos (à esquerda) e o hexágono nos modos FoV estreitos (à direita).

![Nulidade Fora máscara de iluminação](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>Força do sinal

Os pixels são invalidados quando contêm um sinal iv saturado. Quando os pixels estão saturados, a informação de fase é perdida. A imagem abaixo mostra o exemplo de invalidação por um sinal de IR saturado. Veja as setas apontadas para os pixels de exemplo tanto na profundidade como nas imagens IV.

![Saturação de invalidação](./media/concepts/depth-camera-invalidation-saturation.png)

A invalidação também pode ocorrer quando o sinal de INFRAVERMELHO não é forte o suficiente para gerar profundidade. O valor abaixo mostra o exemplo de invalidação por um sinal de IR baixo. Veja as setas apontadas para os pixels de exemplo tanto na profundidade como nas imagens irais.

![Sinal baixo de invalidação](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>Profundidade ambígua

Os pixels também podem ser invalidados se receberem sinais de mais de um objeto na cena. Um caso comum em que este tipo de invalidação pode ser visto está em cantos.  Por causa da geometria da cena, a luz do IR da câmara refletia-se numa parede e na outra. Esta luz refletida causa ambiguidade na profundidade medida do pixel. Filtros no algoritmo de profundidade detetam estes sinais ambíguos e invalidam os pixels.

Os números abaixo mostram exemplos de invalidação por deteção de vários caminhos. Também pode ver como a mesma área de superfície que foi invalidada a partir de uma vista de câmara (linha superior) pode aparecer novamente a partir de uma visão de câmara diferente (linha inferior). Esta imagem demonstra que superfícies invalidadas de uma perspetiva podem ser visíveis de outra.

![Invalidação Multipath - Canto](./media/concepts/depth-camera-invalidation-multipath.png)

Outro caso comum de multipata são os pixels que contêm o sinal misto de primeiro plano e fundo (como em torno das bordas dos objetos). Durante o movimento rápido, pode ver mais pixels invalidados em torno das bordas. Os pixels invalidados adicionais devem-se ao intervalo de exposição da captura de profundidade bruta,

![Invalidação MultiPath - Bordas](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>Passos seguintes

[Sistemas de coordenadas](coordinate-systems.md)
