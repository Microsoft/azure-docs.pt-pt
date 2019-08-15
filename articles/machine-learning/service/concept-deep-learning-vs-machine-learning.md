---
title: Introdução ao aprendizado profundo versus aprendizado de máquina
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre a relação entre aprendizado profundo versus aprendizado de máquina e como eles se encaixam na categoria de inteligência artificial. Este artigo descreve como o aprendizado profundo resolve cenários, como detecção de fraudes, reconhecimento de voz e facial, análise de opiniões e previsão de séries temporais.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 186ba03142e01c4b9340fc5b492621cc17575104
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934281"
---
# <a name="introduction-to-deep-learning-vs-machine-learning"></a>Introdução ao aprendizado profundo versus aprendizado de máquina

Este artigo ajuda você a entender a relação entre aprendizado profundo versus aprendizado de máquina. Você aprenderá como os dois conceitos se comparam e como eles se encaixam na categoria mais ampla de inteligência artificial. Por fim, o artigo descreve como o aprendizado profundo pode ser aplicado a cenários do mundo real, como detecção de fraudes, reconhecimento de voz e facial, análise de opiniões e previsão de séries temporais.

## <a name="how-do-deep-learning-machine-learning-and-ai-relate-to-one-another"></a>Como o aprendizado profundo, aprendizado de máquina e ia se relacionam entre si?

![Diagrama de relações: IA versus aprendizado de máquina versus aprendizado profundo](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Para entender a diferença entre aprendizado profundo versus aprendizado de máquina versus IA, saiba mais sobre cada um desses campos de estudo:

- O **aprendizado profundo** é um subconjunto de aprendizado de máquina baseado em redes neurais artificiais que permitem que um computador se treine. Nesse caso, o _processo de aprendizado_ é chamado de _profundidade_, porque a estrutura de redes neurais artificiais consiste em várias camadas de entrada, saída e ocultas. Cada camada contém unidades que transformam os dados de entrada em informações que a próxima camada pode usar para executar uma determinada tarefa de previsão. Graças a essa estrutura, uma máquina é capaz de aprender por meio de seu próprio processamento de dados.

- O **Machine Learning** é um subconjunto de inteligência artificial que inclui técnicas (como aprendizado profundo) que permitem que as máquinas aprimorem as tarefas com experiência. Nesse caso, o _processo de aprendizado_ é baseado nas seguintes etapas:

    1. Alimentar um algoritmo com dados fornecendo-o com mais informações (por exemplo, executando extração de recursos).
    2. Use esses dados para treinar um modelo.
    3. Teste e implante o modelo.
    4. Consuma o modelo implantado para executar uma determinada tarefa de previsão automatizada.

- O **ia (inteligência artificial)** é uma técnica que permite aos computadores imitar a inteligência humana. Ele inclui aprendizado de máquina. É importante entender a relação entre _ia versus aprendizado de máquina versus aprendizado profundo_: O Machine Learning é uma maneira de obter inteligência artificial, o que significa que, pelo uso de aprendizado de máquina e técnicas de aprendizado profundo, você pode criar sistemas de computadores e aplicativos que podem executar tarefas normalmente associadas à inteligência humana, como percepção visual, reconhecimento de fala, tomada de decisão e conversão entre linguagens.

## <a name="comparison-of-deep-learning-vs-machine-learning"></a>Comparação de aprendizado profundo versus aprendizado de máquina

Agora que você tem a visão geral do aprendizado de máquina versus aprendizado profundo, compare as duas técnicas. No caso do aprendizado de máquina, o algoritmo precisa ser informado sobre como fazer uma previsão precisa fornecendo a ela mais informações; enquanto, no caso de aprendizado profundo, o algoritmo é capaz de aprender isso por meio de seu próprio processamento de dados graças à estrutura de rede neural artificial.

A tabela a seguir compara as duas técnicas mais detalhadamente:

| |Todos os aprendizado de máquina |Somente aprendizado profundo|
|---|---|---|
|  **Número de pontos de dados** | Pode usar valores de dados pequenos fornecidos por usuários | Requer uma grande quantidade de dados de treinamento para fazer conclusões concisas |
|  **Dependências de hardware** | Pode funcionar em máquinas de low-end. Ele não precisa de uma grande quantidade de energia computacional | Depende de computadores avançados. Ele faz inerentemente um grande número de operações de multiplicação de matriz. Essas operações podem ser otimizadas com eficiência usando uma GPU |
|  **Processo de personalização** | Requer que os recursos sejam identificados e criados com precisão pelos usuários | Ele aprende recursos de alto nível de dados e cria novos recursos por si só |
|  **Abordagem de aprendizagem** | Divide as tarefas em pequenas partes e, em seguida, combina resultados recebidos em uma conclusão | Resolve o problema de uma base de ponta a ponta |
|  **Tempo de execução** | Economiza comparativamente menos tempo para treinar, variando de alguns segundos a algumas horas | Leva um tempo muito longo para treinar, pois há muitos parâmetros em um algoritmo de aprendizado profundo |
|  **Saída** | A saída geralmente é um valor numérico, como uma pontuação ou uma classificação | A saída pode ser qualquer coisa de uma pontuação, um texto, um elemento ou um som |

## <a name="deep-learning-use-cases-what-problems-does-it-solve"></a>Casos de uso de aprendizado profundo: Quais problemas ele resolve?

Devido à estrutura de rede neural artificial, o profundo aprendizado de excels na identificação de padrões em dados não estruturados, como imagens, som, vídeo e texto. Por esse motivo, ele está transformando rapidamente muitos setores, incluindo saúde, energia, fintech, transporte e outros, para reconsiderar os processos de negócios tradicionais. Alguns dos aplicativos mais comuns para aprendizado profundo são descritos nos parágrafos a seguir.

### <a name="named-entity-recognition"></a>Reconhecimento de entidade nomeada

Um uso de redes de aprendizado profundo é o reconhecimento de entidade nomeada, que é uma maneira de extrair dados não estruturados e não rotulados determinados tipos de informações, como pessoas, lugares, empresas ou coisas. Essas informações podem então ser armazenadas em um esquema estruturado para criar uma lista de endereços ou servir como um parâmetro de comparação para um mecanismo de validação de identidade.

### <a name="object-detection"></a>Deteção de objetos

O aprendizado profundo foi aplicado em muitos casos de uso de detecção de objetos. A detecção de objeto é, na verdade, um processo de duas partes: classificação de imagem e localização de imagem. A classificação de imagem determina o que os objetos na imagem são, como um carro ou uma pessoa, enquanto a localização da imagem fornece o local específico desses objetos. A detecção de objetos já está sendo usada em indústrias de jogos, varejo, turismo e carros de autoria.

### <a name="image-caption-generation"></a>Geração de legenda de imagem

Semelhante à tarefa de reconhecimento de imagem, a legenda da imagem é a tarefa em que, para uma determinada imagem, o sistema deve gerar uma legenda que descreve o conteúdo da imagem. Depois que você puder detectar objetos em fotografias e gerar rótulos para esses objetos, poderá ver que a próxima etapa é transformar esses rótulos em uma descrição de frase coerente. Em geral, os sistemas envolvem o uso de redes neurais de revisores muito grandes para a detecção de objeto nas fotografias e, em seguida, uma rede neural recorrente (RNN) para transformar os rótulos em uma frase coerente.

### <a name="machine-translation"></a>Tradução automática

A tradução automática usa palavras, frases ou sentenças de um idioma e as converte automaticamente em outro idioma. A tradução automática do computador já existe há muito tempo, mas o aprendizado profundo está obtendo resultados principais em duas áreas específicas: tradução automática de texto (e fala a texto) e tradução automática de imagens. Com a transformação de dados apropriada, uma rede profunda é capaz de compreender os sinais de texto, áudio e visuais. A tradução automática pode ser usada para identificar trechos de som em arquivos de áudio maiores e transcrever a palavra ou imagem falada como texto.

### <a name="text-analytics"></a>Análise de texto

Uma tarefa importante que o aprendizado profundo pode realizar é a e-Discovery. As empresas estão usando análise de texto com base em aprendizado profundo para detecção de insider trading e conformidade regulatória governamental. Os fundos de cobertura usam a análise de texto para fazer uma busca detalhada em repositórios de documentos maciços para obter informações sobre o desempenho do investimento futuro e as opiniões do mercado. O caso de uso para análise de texto com base em aprendizado profundo gira em volta da sua capacidade de analisar grandes quantidades de dados de texto para executar a análise ou produzir agregações.

## <a name="what-are-artificial-neural-networks"></a>O que são redes neurais artificiais?

Redes neurais artificial são formadas por camadas de nós conectados. Os modelos de aprendizado profundo usam redes neurais com um número muito grande de camadas. Os typologies de rede neural artificial mais populares são discutidos abaixo.

### <a name="feedforward-neural-network"></a>Rede neural Feedforward

A rede neural Feedforward é o tipo mais básico de rede neural artificial, no qual as informações viajam em apenas uma direção da camada de entrada para a camada de saída. As redes neurais Feedforward transformam uma entrada colocando-a em uma série de camadas ocultas. Cada camada é composta de um conjunto de neurônios, em que cada camada é totalmente conectada a todos os neurônios na camada antes. Por fim, há uma última camada totalmente conectada — a camada de saída — que representa as previsões geradas.

### <a name="recurrent-neural-network"></a>Rede neural recorrente

As redes neurais recorrentes são um tipo mais amplamente usado de rede neural artificial, que trabalha no princípio de salvar a saída de uma camada e alimentar-a de volta à camada de entrada para ajudar a prever o resultado da camada. Essas redes neurais têm mais recursos de aprendizado e são amplamente empregadas para tarefas mais complexas, como aprender manuscrito ou reconhecimento de idioma.

### <a name="convolutional-neural-networks"></a>Redes neurais de revolução

Uma rede neural de revolução é um tipo particularmente eficaz de rede neural artificial, que apresenta uma arquitetura exclusiva. Primeiro, as camadas são organizadas em três dimensões: largura, altura e profundidade. Além disso, os neurônios em uma camada não se conectam a todos os neurônios na próxima camada, mas apenas a uma pequena região dele. Por fim, a saída final será reduzida a um único vetor de pontuações de probabilidade, organizadas ao longo da dimensão de profundidade. Essas redes neurais foram usadas em áreas como reconhecimento e classificação de imagens.

## <a name="next-steps"></a>Passos Seguintes

Os artigos a seguir mostram como usar a tecnologia de aprendizado profundo no [serviço de Azure Machine Learning](/azure/machine-learning/service/):

- [Classificar dígitos manuscritos com um modelo TensorFlow](how-to-train-tensorflow.md)
- [Classificar dígitos manuscritos com um estimador TensorFlow e Keras](how-to-train-keras.md)
- [Classificar imagens com um modelo Pytorch](how-to-train-pytorch.md)
- [Classificar dígitos manuscritos com um modelo de encadeamento](how-to-train-pytorch.md)
