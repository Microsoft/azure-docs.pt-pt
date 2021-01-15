---
title: Aprendizagem profunda vs. aprendizagem automática
titleSuffix: Azure Machine Learning
description: Saiba como a aprendizagem profunda se relaciona com a aprendizagem automática e a IA. Em Azure Machine Learning, utilize modelos de aprendizagem profunda para deteção de fraudes, deteção de objetos e muito mais.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 01/14/2020
ms.custom: contperf-fy21q1,contperfq1
ms.openlocfilehash: 61305e4f9ba45cb7c13a96bee9d1aab81108bb4d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220699"
---
# <a name="deep-learning-vs-machine-learning-in-azure-machine-learning"></a>Aprendizagem profunda vs. aprendizagem automática em Azure Machine Learning

Este artigo explica a aprendizagem profunda vs. aprendizagem automática e como se enquadram na categoria mais ampla de inteligência artificial. Saiba mais sobre soluções de aprendizagem profunda que pode construir no Azure Machine Learning, como deteção de fraudes, reconhecimento de voz e facial, análise de sentimentos e previsão de séries de tempo.

Para obter orientações sobre a escolha de algoritmos para as suas soluções, consulte a [Folha de Batota do Algoritmo de Aprendizagem automática.](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri)

## <a name="deep-learning-machine-learning-and-ai"></a>Aprendizagem profunda, aprendizagem automática e IA

![Diagrama de relacionamento: IA vs. machine learning vs. deep learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Considere as seguintes definições para compreender a aprendizagem profunda vs. aprendizagem automática vs.AI:

- **A aprendizagem profunda** é um subconjunto de aprendizagem automática que é baseado em redes neuronais artificiais. O _processo de aprendizagem_ é _profundo_ porque a estrutura das redes neurais artificiais consiste em múltiplas entradas, saídas e camadas ocultas. Cada camada contém unidades que transformam os dados de entrada em informações que a próxima camada pode utilizar para uma determinada tarefa preditiva. Graças a esta estrutura, uma máquina pode aprender através do seu próprio processamento de dados.

- **Machine learning** é um subconjunto de inteligência artificial que usa técnicas (como a aprendizagem profunda) que permitem às máquinas usar a experiência para melhorar em tarefas. O _processo de aprendizagem_ baseia-se nos seguintes passos:

   1. Introduza os dados num algoritmo. (Neste passo pode fornecer informações adicionais ao modelo, por exemplo, através da realização da extração de recursos.)
   1. Use estes dados para treinar um modelo.
   1. Teste e implemente o modelo.
   1. Consumir o modelo implantado para fazer uma tarefa preditiva automatizada. (Por outras palavras, ligue e utilize o modelo implantado para receber as previsões devolvidas pelo modelo.)

- **A inteligência artificial (IA)** é uma técnica que permite aos computadores imitar a inteligência humana. Inclui aprendizagem automática. 
 
Ao utilizar técnicas de aprendizagem automática e aprendizagem profunda, pode construir sistemas e aplicações informáticas que façam tarefas que estão normalmente associadas à inteligência humana. Estas tarefas incluem reconhecimento de imagem, reconhecimento de voz e tradução linguística.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Técnicas de aprendizagem profunda vs. aprendizagem automática 

Agora que tens a visão geral da aprendizagem automática vs. aprendizagem profunda, vamos comparar as duas técnicas. Na aprendizagem automática, o algoritmo precisa de ser dito como fazer uma previsão precisa consumindo mais informação (por exemplo, através da extração de recursos). Em aprendizagem profunda, o algoritmo pode aprender a fazer uma previsão precisa através do seu próprio processamento de dados, graças à estrutura da rede neural artificial.

A tabela a seguir compara mais detalhadamente as duas técnicas:

| |Toda a aprendizagem automática |Apenas aprendizagem profunda|
|---|---|---|
|  **Número de pontos de dados** | Pode usar pequenas quantidades de dados para fazer previsões. | Precisa de usar grandes quantidades de dados de treino para fazer previsões. |
|  **Dependências de hardware** | Pode funcionar em máquinas de gama baixa. Não precisa de uma grande quantidade de poder computacional. | Depende de máquinas de alta qualidade. Faz inerentemente um grande número de operações de multiplicação de matrizes. Uma GPU pode otimizar eficientemente estas operações. |
|  **Processo de exibição** | Requer funcionalidades para serem identificadas com precisão e criadas pelos utilizadores. | Aprende funcionalidades de alto nível a partir de dados e cria novas funcionalidades por si só. |
|  **Abordagem de aprendizagem** | Divide o processo de aprendizagem em passos menores. Em seguida, combina os resultados de cada passo numa única saída. | Passa pelo processo de aprendizagem, resolvendo o problema de ponta a ponta. |
|  **Tempo de execução** | Leva relativamente pouco tempo para treinar, variando de alguns segundos a algumas horas. | Normalmente demora muito tempo a treinar porque um algoritmo de aprendizagem profunda envolve muitas camadas. |
|  **Saída** | A saída é geralmente um valor numérico, como uma pontuação ou uma classificação. | A saída pode ter vários formatos, como um texto, uma partitura ou um som. |

## <a name="what-is-transfer-learning"></a>O que é a aprendizagem de transferência

A formação de modelos de aprendizagem profunda requer frequentemente grandes quantidades de dados de formação, recursos computacionais de alta qualidade (GPU, TPU) e um tempo de treino mais longo. Em cenários em que não tenha nenhum destes disponíveis, pode encurtar o processo de treino usando uma técnica conhecida como *aprendizagem de transferência.*

A aprendizagem de transferência é uma técnica que aplica conhecimentos adquiridos desde a resolução de um problema para um problema diferente, mas relacionado.

Devido à estrutura das redes neurais, o primeiro conjunto de camadas geralmente contém características de nível inferior, enquanto o conjunto final de camadas contém uma característica de nível superior que está mais próxima do domínio em questão. Ao repor as camadas finais para uso num novo domínio ou problema, pode reduzir significativamente a quantidade de tempo, dados e recursos de cálculo necessários para treinar o novo modelo. Por exemplo, se já tiver um modelo que reconheça carros, pode reutilizar esse modelo usando a aprendizagem de transferência para reconhecer também camiões, motociclos e outros tipos de veículos.

Aprenda a aplicar a aprendizagem de transferência para a classificação de imagem utilizando um quadro de código aberto em Azure Machine Learning : [Treine um modelo PyTorch de aprendizagem profunda utilizando a aprendizagem de transferências.](./how-to-train-pytorch.md?WT.mc_id=docs-article-lazzeri)

## <a name="deep-learning-use-cases"></a>Casos de uso de aprendizagem profunda

Devido à estrutura artificial da rede neural, a aprendizagem profunda prima pela identificação de padrões em dados não estruturados como imagens, som, vídeo e texto. Por esta razão, a aprendizagem profunda está a transformar rapidamente muitas indústrias, incluindo os cuidados de saúde, a energia, as finanças e os transportes. Estas indústrias estão agora a repensar os processos comerciais tradicionais. 

Algumas das aplicações mais comuns para a aprendizagem profunda são descritas nos parágrafos seguintes. No Azure Machine Learning, pode utilizar um modelo a partir de si construir a partir de uma estrutura de código aberto ou construir o modelo utilizando as ferramentas fornecidas.

### <a name="named-entity-recognition"></a>Reconhecimento de entidades nomeadas

O reconhecimento de entidades nomeadas é um método de aprendizagem profunda que pega num pedaço de texto como entrada e o transforma numa classe pré-especificada. Esta nova informação pode ser um código postal, uma data, uma identificação do produto. A informação pode então ser armazenada num esquema estruturado para construir uma lista de endereços ou servir de referência para um motor de validação de identidade.

### <a name="object-detection"></a>Deteção de objetos

A aprendizagem profunda tem sido aplicada em muitos casos de deteção de objetos. A deteção de objetos compreende duas partes: classificação de imagem e, em seguida, localização de imagem. A _classificação da imagem_ identifica os objetos da imagem, como carros ou pessoas. A _localização_ da imagem fornece a localização específica destes objetos. 

A deteção de objetos já é usada em indústrias como jogos, retalho, turismo e carros automobilísticos.

### <a name="image-caption-generation"></a>Geração de legendas de imagem

Tal como o reconhecimento de imagem, na legenda da imagem, para uma determinada imagem, o sistema deve gerar uma legenda que descreva o conteúdo da imagem. Quando é possível detetar e rotular objetos em fotografias, o próximo passo é transformar esses rótulos em frases descritivas. 

Normalmente, as aplicações de legendagem de imagem usam redes neuronais convolucionais para identificar objetos numa imagem e, em seguida, usar uma rede neural recorrente para transformar os rótulos em frases consistentes.

### <a name="machine-translation"></a>Tradução automática

A tradução automática pega em palavras ou frases de uma língua e traduz-as automaticamente noutra língua. A tradução automática existe há muito tempo, mas a aprendizagem profunda consegue resultados impressionantes em duas áreas específicas: tradução automática de texto (e tradução da fala para texto) e tradução automática de imagens.

Com a transformação adequada de dados, uma rede neural pode compreender sinais de texto, áudio e visual. A tradução automática pode ser usada para identificar fragmentos de som em ficheiros de áudio maiores e transcrever a palavra falada ou imagem como texto.

### <a name="text-analytics"></a>Análise de texto

A análise de texto baseada em métodos de aprendizagem profunda envolve analisar grandes quantidades de dados de texto (por exemplo, documentos médicos ou recibos de despesas), reconhecer padrões e criar informações organizadas e concisas a partir dele.

As empresas usam aprendizagem profunda para realizar análises de texto para detetar o trading de insider e o cumprimento dos regulamentos governamentais. Outro exemplo comum é a fraude dos seguros: a análise de textos tem sido frequentemente utilizada para analisar grandes quantidades de documentos para reconhecer as hipóteses de um pedido de seguro ser fraude. 

## <a name="artificial-neural-networks"></a>Redes neuronais artificiais

As redes neuronais artificiais são formadas por camadas de nós conectados. Os modelos de aprendizagem profunda usam redes neurais que têm um grande número de camadas. 

As secções seguintes exploram as tipologias de rede neural artificial mais populares.

### <a name="feedforward-neural-network"></a>Rede neural de feedforward

A rede neural de feedforward é o tipo mais simples de rede neural artificial. Numa rede de feedforward, a informação move-se apenas numa direção, desde a camada de entrada até à camada de saída. As redes neuronais de feedforward transformam uma entrada colocando-a através de uma série de camadas escondidas. Cada camada é composta por um conjunto de neurónios, e cada camada está totalmente ligada a todos os neurónios da camada anterior. A última camada totalmente ligada (a camada de saída) representa as previsões geradas.

### <a name="recurrent-neural-network"></a>Rede neuronal recorrente

As redes neuronais recorrentes são uma rede neural artificial amplamente utilizada. Estas redes poupam a saída de uma camada e alimentam-na de volta à camada de entrada para ajudar a prever o resultado da camada. As redes neuronais recorrentes têm grandes capacidades de aprendizagem. São amplamente utilizados para tarefas complexas, como previsão de séries de tempo, aprendizagem de caligrafia e reconhecimento da linguagem.

### <a name="convolutional-neural-network"></a>Rede neuronal convolucional

Uma rede neural convolucional é uma rede neural artificial particularmente eficaz, e apresenta uma arquitetura única. As camadas são organizadas em três dimensões: largura, altura e profundidade. Os neurónios numa camada ligam-se não a todos os neurónios da camada seguinte, mas apenas a uma pequena região dos neurónios da camada. A saída final é reduzida a um único vetor de pontuações de probabilidade, organizadas ao longo da dimensão de profundidade. 

As redes neuronais convolucionais têm sido utilizadas em áreas como reconhecimento de vídeo, reconhecimento de imagem e sistemas de recomendação.

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos mostram-lhe mais opções para a utilização de modelos de aprendizagem profunda de código aberto em [Azure Machine Learning:](./index.yml?WT.mc_id=docs-article-lazzeri)


- [Classifique os dígitos manuscritos utilizando um modelo TensorFlow](./how-to-train-tensorflow.md?WT.mc_id=docs-article-lazzeri) 

- [Classifique os dígitos manuscritos utilizando um estimador de TensorFlow e Keras](./how-to-train-keras.md?WT.mc_id=docs-article-lazzeri)

- [Classifique os dígitos manuscritos utilizando um modelo Chainer](./how-to-set-up-training-targets.md)
