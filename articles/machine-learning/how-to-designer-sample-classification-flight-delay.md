---
title: 'Designer: exemplo de previsão de atraso de voo'
titleSuffix: Azure Machine Learning
description: Crie um classificador e use código R personalizado para prever atrasos de voo com o designer de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: d459350572d68cc5dcbbfd56933483404b94f918
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963286"
---
# <a name="build-a-classifier--use-r-to-predict-flight-delays-with-azure-machine-learning-designer"></a>Criar um classificador & usar R para prever atrasos de voo com Azure Machine Learning designer

**Designer (visualização) exemplo 6**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Esse pipeline usa dados históricos de vôo e clima para prever se um vôo de passageiro agendado será atrasado em mais de 15 minutos. Esse problema pode ser abordado como um problema de classificação, prevendo duas classes: atrasadas ou no prazo.

Este é o grafo de pipeline final para este exemplo:

[gráfico ![do oleoduto](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png)](media/how-to-designer-sample-classification-flight-delay/pipeline-graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Clique em exemplo 6 para abri-lo.

## <a name="get-the-data"></a>Obter os dados

Este exemplo usa o conjunto de **dados de atrasos de voo** . Faz parte da coleta de dados TranStats do departamento de transporte dos EUA. O conjunto de dados contém informações de atraso de voo de abril a 2013 de outubro. O conjunto de um foi processado previamente da seguinte maneira:

* Filtrado para incluir os aeroportos 70 mais ocupados no Estados Unidos continental.
* Rerotulado vôos cancelados como atrasados em mais de 15 minutos.
* Filtro de vôos de diversões filtrados.
* 14 colunas selecionadas.

Para complementar os dados de vôo, o conjunto de dado **meteorológico** é usado. Os dados meteorológicos contêm observações de clima com base em hora, de NOAA e representam observações das estações de clima do aeroporto, cobrindo o mesmo período de tempo que o conjunto de dados de vôos. Ele foi processado previamente da seguinte maneira:

* As IDs da estação do tempo foram mapeadas para as IDs correspondentes do aeroporto.
* As estações de clima não associadas a 70 aeroportos mais ocupados foram removidas.
* A coluna de data foi dividida em colunas separadas: ano, mês e dia.
* As 26 colunas selecionadas.

## <a name="pre-process-the-data"></a>Pré-processar os dados

Um conjunto de um DataSet geralmente requer algum pré-processamento antes que possa ser analisado.

![processo de dados](./media/how-to-designer-sample-classification-flight-delay/data-process.png)

### <a name="flight-data"></a>Dados de vôo

As colunas **Carrier**, **OriginAirportID**e **DestAirportID** são salvas como inteiros. No entanto, eles são atributos categóricos, use o módulo **Editar metadados** para convertê-los em categóricos.

![editar metadados](./media/how-to-designer-sample-classification-flight-delay/edit-metadata.png)

Em seguida, use o módulo **selecionar colunas** no conjunto de DataSet para excluir das colunas do conjunto de linhas que são possíveis vazamentores de destino: **DepDelay**, **DepDel15**, **ArrDelay**, **cancelado**, **year**. 

Para unir os registros de vôo com os registros meteorológicos por hora, use a hora de partida agendada como uma das chaves de junção. Para fazer a junção, a coluna CSRDepTime deve ser arredondada para a hora mais próxima, que é feita pelo no módulo **Executar script R** . 

### <a name="weather-data"></a>Dados meteorológicos

Colunas que têm uma grande proporção de valores ausentes são excluídas usando o módulo **colunas do projeto** . Essas colunas incluem todas as colunas com valor de cadeia de caracteres: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**e **StationPressure**.

O módulo **limpar dados ausentes** é aplicado às colunas restantes para remover linhas com dados ausentes.

Os tempos de observação do clima são arredondados para a hora completa mais próxima. Os tempos de vôo agendados e os tempos de observação do clima são arredondados em direções opostas para garantir que o modelo Use apenas o clima antes do tempo de vôo. 

Como os dados meteorológicos são relatados na hora local, as diferenças de fuso horário são contabilizadas subtraindo-se as colunas de fuso horário do horário de partida agendado e o tempo de observação do clima. Essas operações são feitas usando o módulo **Executar script R** .

### <a name="joining-datasets"></a>Unindo conjuntos de os

Os registros de voo são Unidos com dados meteorológicos na origem do vôo (**OriginAirportID**) usando o módulo **unir dados** .

 ![Junte-se a voo e clima por origem](./media/how-to-designer-sample-classification-flight-delay/join-origin.png)


Os registros de voo são Unidos com dados meteorológicos usando o destino do vôo (**DestAirportID**).

 ![Unir voo e clima por destino](./media/how-to-designer-sample-classification-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Preparando exemplos de treinamento e teste

O módulo **dividir dados** divide os dados em abril até registros de setembro para treinamento e registros de outubro para teste.

 ![Dividir dados de treinamento e teste](./media/how-to-designer-sample-classification-flight-delay/split.png)

As colunas year, month e TimeZone são removidas do conjunto de módulos de treinamento usando o módulo selecionar colunas.

## <a name="define-features"></a>Definir recursos

No aprendizado de máquina, os recursos são propriedades mensuráveis individuais de algo no qual você está interessado. Encontrar um conjunto forte de recursos requer experimentação e conhecimento do domínio. Algumas funcionalidades são melhores para prever num destino do que outras. Além disso, alguns recursos podem ter uma correlação forte com outros recursos e não adicionarão novas informações ao modelo. Esses recursos podem ser removidos.

Para criar um modelo, você pode usar todos os recursos disponíveis ou selecionar um subconjunto dos recursos.

## <a name="choose-and-apply-a-learning-algorithm"></a>Escolher e aplicar um algoritmo de aprendizado

Crie um modelo usando o módulo **regressão logística de duas classes** e treine-o no conjunto de módulos de treinamento. 

O resultado do módulo **modelo de treinamento** é um modelo de classificação treinado que pode ser usado para pontuar novas amostras para fazer previsões. Use o conjunto de teste para gerar pontuações dos modelos treinados. Em seguida, use o módulo **avaliar modelo** para analisar e comparar a qualidade dos modelos.
pipeline depois de executar o pipeline, você pode exibir a saída do módulo **modelo de Pontuação** clicando na porta de saída e selecionando **Visualizar**. A saída inclui os rótulos pontuados e as probabilidades para os rótulos.

Por fim, para testar a qualidade dos resultados, adicione o módulo **avaliar modelo** à tela do pipeline e conecte a porta de entrada à esquerda à saída do módulo modelo de pontuação. Execute o pipeline e exiba a saída do módulo **modelo de avaliação** , clicando na porta de saída e selecionando **Visualizar**.

## <a name="evaluate"></a>Avaliar
O modelo de regressão logística tem AUC de 0,631 no conjunto de teste.

 ![avaliar](media/how-to-designer-sample-classification-flight-delay/sample6-evaluate-1225.png)

## <a name="next-steps"></a>Passos seguintes

Explore os outros exemplos disponíveis para o designer:

- [Exemplo 1-regressão: prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Exemplo 2-regressão: comparar algoritmos para previsão de preço de automóvel](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Exemplo 3-classificação com seleção de recursos: Previsão de renda](how-to-designer-sample-classification-predict-income.md)
- [Exemplo 4-classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Exemplo 5-classificação: Previsão de rotatividade](how-to-designer-sample-classification-churn.md)
- [Exemplo de classificação de texto 7: conjunto de teste da Wikipédia SP 500](how-to-designer-sample-text-classification.md)