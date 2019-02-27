---
title: Seleção de algoritmo ML automatizado e Otimização
titleSuffix: Azure Machine Learning service
description: Saiba como serviço Azure Machine Learning pode automaticamente escolher um algoritmo para e gerar um modelo a partir do mesmo a economizar tempo, usando os parâmetros e os critérios fornecidos para selecionar o melhor algoritmo para o seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: bf010e33a5ef77fcfde2506bfef9760a09667a9d
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867257"
---
# <a name="what-is-automated-machine-learning"></a>O que é automatizado aprendizagem automática?

Aprendizagem automática é o processo de mudança dos dados de treinamento com um recurso de destino definidos e fazendo a iteração por meio de combinações de algoritmos e seleções de funcionalidade para selecionar automaticamente o melhor modelo para os seus dados com base nas pontuações de treinamento. A processo de desenvolvimento do modelo de aprendizagem tradicional é altamente com muitos recursos e requer investimento de dados de conhecimento e a hora de domínio significativo para executar e comparar os resultados de dezenas de modelos. Aprendizagem automática simplifica este processo através da geração de modelos ajustados de objetivos e restrições definidas para a experimentação, como o tempo para a experimentação para execução ou que modela à lista de bloqueio.

## <a name="how-it-works"></a>Como funciona

1. Configurar o tipo de problema de aprendizado de máquina que está a tentar resolver. Categorias de aprendizagem supervisionada são suportadas:
   + Classificação
   + Regressão
   + Previsão

   Enquanto a aprendizagem automática está disponível em geral, **a previsão funcionalidade ainda está em pré-visualização pública.**

   Consulte a [lista de modelos](how-to-configure-auto-train.md#select-your-experiment-type) pode experimentar o Azure Machine Learning ao treinar.

1. Especifique a origem e o formato dos dados de treinamento. Os dados devem ser o nome e podem ser armazenados no seu ambiente de desenvolvimento ou no armazenamento de Blobs do Azure. Se os dados são armazenados no seu ambiente de desenvolvimento, tem de estar no mesmo diretório que seus scripts de treinamento. Este diretório é copiado para o destino de computação que selecionar para treinamento.

    No seu script de treinamento, os dados podem ser lidos em matrizes de Numpy ou um Pandas dataframe.

    Pode configurar opções de divisão para selecionar dados de treinamento e validação, ou pode especificar os conjuntos de dados de treinamento e validação separados.

1. Configurar o [destino de computação](how-to-set-up-training-targets.md) que serve para preparar o modelo.

1. Configure a configuração de aprendizado de máquina automatizada. Esse item controla os parâmetros utilizados como o Azure Machine Learning é iterada em modelos diferentes, as definições de hiper-parâmetros, e que métricas para ver quando determinar o melhor modelo

1. Submeta um treinamento ser executado.

Durante o treinamento, o serviço Azure Machine Learning cria um número de pipelines que tente parâmetros e algoritmos diferentes. Ele irá parar quando atingir o limite de iteração que é fornecer ou quando ela atinge o valor de destino para a métrica que especificar.

[![Aprendizagem automática](./media/how-to-automated-ml/automated-machine-learning.png)](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Pode inspecionar as informações de execução com sessão iniciada, que contém a métricas recolhidas durante a execução. A execução de treinamento também produz um objeto de Python serializados (`.pkl` ficheiro) que contém o modelo e, em seguida, o pré-processamento de dados.

## <a name="model-explainability"></a>Modelo explainability

Uma armadilha comum da aprendizagem automática é uma incapacidade de ver o processo de ponto-a-ponto. O Azure Machine Learning permite-lhe ver informações detalhadas sobre os modelos para aumentar a transparência no que está sendo executado no back-end. Alguns modelos, como a regressão linear, são considerados como bastante simples e, portanto, fácil de entender. Mas, tal como podemos adicionar mais recursos e utilizar modelos mais complicada de aprendizagem, compreendê-los para obtém mais difícil. Existem dois aspetos fundamentais a transparência de processos no machine learning:

1. Reconhecimento de pipeline e todos os passos de aprendizagem automática envolvidos incluindo o pré-processamento de dados/featurization e valores de hiper-parâmetros.
1. Compreender a relação entre a variáveis de entrada (também conhecido como "recursos") e a saída de modelo.  Saber a magnitude e a direção do impacto de cada funcionalidade no valor previsto ajuda a compreender melhor e a explicar o modelo. Isso é conhecido como importância de funcionalidade.

Pode ativar formação a pedido post de importância de recurso global para o pipeline da sua preferência ou ativá-la para todos os pipelines como parte de treinamento de aprendizado de máquina automatizada. Em setores altamente regulamentados, como cuidados de saúde e serviços bancários, isto é fundamental para estar em conformidade com normas e práticas recomendadas.  Seguem-se alguns cenários de mundo real para ilustrar:

1. Uma empresa industrial com machine learning para prever falhas instrumento futuras, por isso, proativamente realizarem a atividade de manutenção. Sabe que um instrumento está prestes a falhar, o que é a causa mais provável vai ser, para que manutenção preventiva pode ser realizada rapidamente?
1. Uma instituição financeira com o machine learning para aplicações de cartão de crédito ou de crédito de processo. Como sabe se o modelo está fazendo a coisa certa e se um cliente pede para obter mais detalhes sobre por que seu aplicativo foi rejeitado, como responde a eles?
1. Um retalhista online ou um fornecedor de fabricantes independentes de software através de machine learning para prever o abandono de clientes. Quais são os contribuintes principais para o cliente de alterações e, como impedir que os clientes pleno funcionamento?

Esta é uma funcionalidade de pré-visualização e Vamos continuar a investir em fornecendo informações mais detalhadas para o ajudar a compreender melhor os seus modelos de machine learning. Siga este [bloco de notas do exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/model-explanation/auto-ml-model-explanation.ipynb) fazer experiências com explicações de modelo no Azure Machine Learning.

## <a name="next-steps"></a>Passos Seguintes

Ver exemplos e saiba como criar modelos com o Machine Learning automatizada:
+ [Exemplos: Utilizar blocos de notas do Jupyter para explorar o serviço Azure Machine Learning](samples-notebooks.md#automated-ml-setup)

+ [Tutorial: Preparar automaticamente um modelo de classificação com o Azure automatizada Machine Learning](tutorial-auto-train-models.md)

+ [Utilizar o treinamento automática num recurso remoto](how-to-auto-train-remote.md)

+ [Configurar as definições para treinamento automática](how-to-configure-auto-train.md)
