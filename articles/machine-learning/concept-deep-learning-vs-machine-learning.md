---
title: Aprendizagem profunda vs. aprendizagem automática
titleSuffix: Azure
description: Saiba como a aprendizagem profunda se relaciona com a aprendizagem automática e a inteligência artificial. A aprendizagem profunda é usada em cenários como deteção de fraudes, reconhecimento de voz e facial, análise de sentimentos e previsão de séries de tempo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 03/05/2020
ms.openlocfilehash: a930dc3974536047096912505b9a786cf53c41b8
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78328596"
---
# <a name="deep-learning-vs-machine-learning"></a>Aprendizagem profunda vs. aprendizagem automática

Este artigo ajuda-o a comparar a aprendizagem profunda vs. machine learning. Vais aprender como os dois conceitos se comparam e como se encaixam na categoria mais ampla da inteligência artificial. O artigo também descreve como a aprendizagem profunda pode ser aplicada a cenários do mundo real, tais como deteção de fraudes, reconhecimento de voz e rosto, análise de sentimentos e previsão de séries de tempo.

## <a name="deep-learning-machine-learning-and-ai"></a>Aprendizagem profunda, aprendizagem automática e IA

![Diagrama de relacionamento: IA vs. machine learning vs. aprendizagem profunda](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Considere as seguintes definições para compreender a aprendizagem profunda vs. machine learning vs. IA:

- **A aprendizagem profunda** é um subconjunto de aprendizagem automática que é baseado em redes neurais artificiais. O _processo de aprendizagem_ é _profundo_ porque a estrutura das redes neurais artificiais consiste em múltiplas camadas de entrada, saída e ocultos. Cada camada contém unidades que transformam os dados de entrada em informações que a próxima camada pode usar para uma determinada tarefa preditiva. Graças a esta estrutura, uma máquina pode aprender através do seu próprio processamento de dados.

- **Machine learning** é um subconjunto de inteligência artificial que utiliza técnicas (como a aprendizagem profunda) que permitem que as máquinas utilizem a experiência para melhorar as tarefas. O _processo de aprendizagem baseia-se_ nos seguintes passos:

   1. Insera dados num algoritmo. (Neste passo pode fornecer informações adicionais ao modelo, por exemplo, através da realização da extração de recurso.)
   1. Use estes dados para treinar um modelo.
   1. Teste e implante o modelo.
   1. Consumir o modelo implantado para fazer uma tarefa preditiva automatizada. (Por outras palavras, ligue e utilize o modelo implantado para receber as previsões devolvidas pelo modelo.)

- **A inteligência artificial (IA)** é uma técnica que permite aos computadores imitar a inteligência humana. Inclui aprendizagem automática. 
 
É importante entender a relação entre AI, aprendizagem automática e aprendizagem profunda. Machine learning é uma forma de alcançar a inteligência artificial. Utilizando técnicas de aprendizagem automática e de aprendizagem profunda, pode construir sistemas informáticos e aplicações que façam tarefas que são geralmente associadas à inteligência humana. Estas tarefas incluem reconhecimento de imagem, reconhecimento de voz e tradução de linguagem.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Técnicas de aprendizagem profunda vs. aprendizagem automática 

Agora que tens a visão geral da aprendizagem automática vs. aprendizagem profunda, vamos comparar as duas técnicas. Na aprendizagem automática, o algoritmo precisa de ser dito como fazer uma previsão precisa consumindo mais informação (por exemplo, executando a extração de recursos). Na aprendizagem profunda, o algoritmo pode aprender a fazer uma previsão precisa através do seu próprio processamento de dados, graças à estrutura da rede neural artificial.

A tabela seguinte compara as duas técnicas com mais detalhes:

| |Todas as aprendizagens automáticas |Apenas aprendizagem profunda|
|---|---|---|
|  **Número de pontos de dados** | Pode usar pequenas quantidades de dados para fazer previsões. | Precisa de usar grandes quantidades de dados de treino para fazer previsões. |
|  **Dependências de hardware** | Pode funcionar em máquinas de gama baixa. Não precisa de uma grande quantidade de poder computacional. | Depende de máquinas de alta qualidade. Faz, inerentemente, um grande número de operações de multiplicação matriz. Uma GPU pode otimizar eficazmente estas operações. |
|  **Processo de caracterização** | Requer que as funcionalidades sejam identificadas e criadas com precisão pelos utilizadores. | Aprende funcionalidades de alto nível a partir de dados e cria novas funcionalidades por si só. |
|  **Abordagem de aprendizagem** | Divide o processo de aprendizagem em passos menores. Em seguida, combina os resultados de cada passo numa saída. | Passa pelo processo de aprendizagem resolvendo o problema de ponta a ponta. |
|  **Tempo de execução** | Demora relativamente pouco tempo a treinar, variando de alguns segundos a algumas horas. | Normalmente demora muito tempo a treinar porque um algoritmo de aprendizagem profunda envolve muitas camadas. |
|  **Saída** | A saída é geralmente um valor numérico, como uma pontuação ou uma classificação. | A saída pode ter vários formatos, como um texto, uma pontuação ou um som. |

## <a name="deep-learning-use-cases"></a>Casos de uso de aprendizagem profunda

Devido à estrutura artificial da rede neural, a aprendizagem profunda prima pela identificação de padrões em dados não estruturados, tais como imagens, som, vídeo e texto. Por esta razão, a aprendizagem profunda está rapidamente a transformar muitas indústrias, incluindo os cuidados de saúde, a energia, as finanças e os transportes. Estas indústrias estão agora a repensar os processos de negócio tradicionais. 

Algumas das aplicações mais comuns para a aprendizagem profunda são descritas nos parágrafos seguintes.

### <a name="named-entity-recognition"></a>Reconhecimento de entidades nomeadas

O reconhecimento de entidades nomeadas é um método de aprendizagem profunda que toma um pedaço de texto como entrada e o transforma numa classe pré-especificada. Esta nova informação pode ser um código postal, uma data, uma identificação do produto. A informação pode então ser armazenada num esquema estruturado para construir uma lista de endereços ou servir de referência para um motor de validação de identidade.

### <a name="object-detection"></a>Deteção de objetos

A aprendizagem profunda tem sido aplicada em muitos casos de deteção de objetos. A deteção de objetos compreende duas partes: classificação de imagem e, em seguida, localização de imagem. A _classificação da imagem_ identifica os objetos da imagem, como carros ou pessoas. A _localização da imagem_ fornece a localização específica destes objetos. 

A deteção de objetos já é utilizada em indústrias como jogos, retalho, turismo e carros automobilísticos.

### <a name="image-caption-generation"></a>Geração de legendas de imagem

Tal como o reconhecimento de imagem, na legenda da imagem, para uma determinada imagem, o sistema deve gerar uma legenda que descreva o conteúdo da imagem. Quando se pode detetar e rotular objetos em fotografias, o próximo passo é transformar esses rótulos em frases descritivas. 

Normalmente, as aplicações de legendagem de imagem usam redes neuronais convolutivas para identificar objetos numa imagem e, em seguida, usar uma rede neural recorrente para transformar os rótulos em frases consistentes.

### <a name="machine-translation"></a>Tradução automática

A tradução automática pega em palavras ou frases de uma língua e traduz-as automaticamente noutra língua. A tradução automática existe há muito tempo, mas a aprendizagem profunda consegue resultados impressionantes em duas áreas específicas: tradução automática de texto (e tradução da fala para texto) e tradução automática de imagens.

Com a transformação adequada de dados, uma rede neural pode entender sinais de texto, áudio e visuais. A tradução automática pode ser usada para identificar fragmentos de som em ficheiros áudio maiores e transcrever a palavra ou imagem falada como texto.

### <a name="text-analytics"></a>Análise de texto

A análise de texto baseada em métodos de aprendizagem profunda envolve analisar grandes quantidades de dados de texto (por exemplo, documentos médicos ou recibos de despesas), reconhecer padrões e criar informações organizadas e concisas a partir dos mesmos.

As empresas usam aprendizagem profunda para realizar análises de texto para detetar informações privilegiadas e o cumprimento dos regulamentos governamentais. Outro exemplo comum é a fraude dos seguros: a análise de textotem sido frequentemente utilizada para analisar grandes quantidades de documentos para reconhecer as hipóteses de um pedido de seguro ser fraude. 

## <a name="artificial-neural-networks"></a>Redes neuronais artificiais

As redes neuronais artificiais são formadas por camadas de nódulos conectados. Os modelos de aprendizagem profunda usam redes neurais que têm um grande número de camadas. 

As seguintes secções exploram as tipologias da rede neural artificial mais populares.

### <a name="feedforward-neural-network"></a>Rede neural feedforward

A rede neural feedforward é o tipo mais básico de rede neural artificial. Numa rede de feedforward, a informação move-se numa única direção, desde a camada de entrada até à camada de saída. As redes neurais feedforward transformam uma entrada colocando-a através de uma série de camadas escondidas. Cada camada é composta por um conjunto de neurónios, e cada camada está totalmente ligada a todos os neurónios da camada anterior. A última camada totalmente ligada (a camada de saída) representa as previsões geradas.

### <a name="recurrent-neural-network"></a>Rede neural recorrente

Redes neurais recorrentes são uma rede neural artificial amplamente utilizada. Estas redes poupam a saída de uma camada e alimentam-na de volta à camada de entrada para ajudar a prever o resultado da camada. Redes neuronais recorrentes têm grandes capacidades de aprendizagem. São amplamente utilizados para tarefas complexas, como previsão de séries de tempo, aprendizagem da caligrafia e reconhecimento da linguagem.

### <a name="convolutional-neural-networks"></a>Redes neuronais convolucional

Uma rede neural convolucional é uma rede neural artificial particularmente eficaz, e apresenta uma arquitetura única. As camadas são organizadas em três dimensões: largura, altura e profundidade. Os neurónios numa camada ligam-se não a todos os neurónios na camada seguinte, mas apenas a uma pequena região dos neurónios da camada. A saída final é reduzida a um único vetor de pontuações de probabilidade, organizada ao longo da dimensão da profundidade. 

Redes neuronais convolucionais têm sido utilizadas em áreas como reconhecimento de vídeo, reconhecimento de imagem e sistemas de recomendação.

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos mostram-lhe como usar tecnologia de aprendizagem profunda em [Azure Machine Learning:](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=docs-article-lazzeri)

- [Classifique os dígitos manuscritos utilizando um modelo TensorFlow](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)

- [Classifique os dígitos manuscritos utilizando um estimador TensorFlow e Keras](https://docs.microsoft.com/azure/machine-learning/how-to-train-keras?WT.mc_id=docs-article-lazzeri)

- [Classifique as imagens usando um modelo de Pircha](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)

- [Classifique os dígitos manuscritos utilizando um modelo Chainer](https://docs.microsoft.com/azure/machine-learning/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)

Além disso, utilize a Folha de Batota do [Algoritmo de Aprendizagem automática](algorithm-cheat-sheet.md) para escolher algoritmos para o seu modelo.
