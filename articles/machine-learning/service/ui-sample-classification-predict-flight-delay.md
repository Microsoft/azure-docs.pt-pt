---
title: 'Classificação: Prever atrasos de voos'
titleSuffix: Azure Machine Learning service
description: Este artigo mostra-lhe como criar um modelo de machine learning para prever a atrasos de voos usando a interface visual de arrastar e soltar e o código R personalizado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: peterlu
ms.date: 07/02/2019
ms.openlocfilehash: 773e55fe4b5ca5acf27ba1765e5a16075f625187
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607640"
---
# <a name="sample-6---classification-predict-flight-delays-using-r"></a>Exemplo 6 - classificação: Prever os atrasos de voos através da linguagem R

Esta experiência utiliza voo histórico e os dados meteorológicos para prever se um voo comercial agendado será adiado mais de 15 minutos.

Esse problema pode ser abordado como um problema de classificação, prever duas classes – atrasadas, ou no tempo. Para criar um classificador, esse modelo com um grande número de exemplos de dados do histórico de voo.

Este é o gráfico de experimentação final:

[![Gráfico da experimentação](media/ui-sample-classification-predict-flight-delay/experiment-graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Selecione o **aberto** botão para a experimentação de exemplo 6:

    ![Abra a experimentação](media/ui-sample-classification-predict-flight-delay/open-sample6.png)

## <a name="get-the-data"></a>Obter os dados

Experimente isto utiliza o **dados de atrasos de voos** conjunto de dados. Ele 's parte da coleta de dados TranStats dos EUA Departamento de transportes. O conjunto de dados contém informações de atraso de voo de Abril a Outubro de 2013. Antes de carregar os dados para a interface visual, ele ter sido previamente processado da seguinte forma:

* Filtrado para incluir os 70 aeroportos mais ocupados no território continental dos Estados Unidos.
* Para cancelar os voos, relabeled como atrasado mais de 15 minutos.
* Filtrado desviados voos.
* Colunas de 14 selecionadas.

Para complementar os dados de voo, os **conjunto de dados de Meteorologia** é utilizado. Os dados meteorológicos contém observações por hora de com base em ' s land meteorologia da NOAA e representa as observações de estações de Meteorologia do aeroporto, que abrange o mesmo período de tempo de Abril de Outubro de 2013. Antes de carregar a interface visual do Azure ML, ele ter sido previamente processado da seguinte forma:

* IDs de estação meteorológica foram mapeados para IDs de aeroporto correspondente.
* Estações de Meteorologia não associadas os 70 aeroportos mais ocupados foram removidas.
* A coluna de data foi dividida em colunas separadas: Ano, mês e dia.
* Colunas de 26 selecionadas.

## <a name="pre-process-the-data"></a>Pré-processar os dados

Um conjunto de dados normalmente, requer algumas de pré-processamento antes de que podem ser analisados.

![processo de dados](media/ui-sample-classification-predict-flight-delay/data-process.png)

### <a name="flight-data"></a>Dados de voos

As colunas **operadora**, **OriginAirportID**, e **DestAirportID** são guardadas como números inteiros. No entanto, eles são atributos categóricos, utilize o **Editar metadados** módulo convertê-las em categóricos.

![edit-metadata](media/ui-sample-classification-predict-flight-delay/edit-metadata.png)

Em seguida, utilize o **Select Columns** no módulo de conjunto de dados para impedir que as colunas do conjunto de dados que são leakers destino possível: **DepDelay**, **DepDel15**, **ArrDelay**, **Canceled**, **Year**. 

Para associar os registos de voo com os registos de Meteorologia por hora, utilize o tempo de mudança agendada como uma das chaves de associação. Para fazer a associação, a coluna de CSRDepTime tem de ser arredondados por defeito para a hora mais próxima, o que é feita no **executar Script do R** módulo. 

### <a name="weather-data"></a>Dados meteorológicos

Colunas que têm uma grande proporção de valores em falta são excluídas com o **colunas do projeto** módulo. Estas colunas incluem todas as colunas de valor de cadeia de caracteres: **ValueForWindCharacter**, **WetBulbFarenheit**, **WetBulbCelsius**, **PressureTendency**, **PressureChange**, **SeaLevelPressure**, and **StationPressure**.

O **Clean Missing Data** módulo, em seguida, é aplicado às colunas restantes para remover linhas com dados em falta.

Tempos de observação de Meteorologia são arredondados para a hora completa mais próxima. Tempos de avião com aterragem e os tempos de observação de Meteorologia são arredondados nas direções opostas para garantir que o modelo utiliza apenas Meteorologia antes da hora de voo. 

Uma vez que os dados meteorológicos são reportados na hora local, diferenças de fuso horário são consideradas ao subtrair as colunas de fuso horário do tempo de mudança agendada e o tempo de observação de Meteorologia. Estas operações são efetuadas utilizando o **executar Script do R** módulo.

### <a name="joining-datasets"></a>Ingressar em conjuntos de dados

Registos de voo estão associados com os dados meteorológicos na origem do voo (**OriginAirportID**) com o **associar dados** módulo.

 ![Junte-se de voo e meteorologia por origem](media/ui-sample-classification-predict-flight-delay/join-origin.png)


Registos de voo estão associados com os dados de Meteorologia com o destino do voo (**DestAirportID**).

 ![Junte-se de voo e meteorologia por destino](media/ui-sample-classification-predict-flight-delay/join-destination.png)

### <a name="preparing-training-and-test-samples"></a>Preparar o treinamento e exemplos de teste

O **Split Data** módulo divide os dados em Abril, através de registos de Setembro de treinamento e Outubro dos registos do teste.

 ![Dividir a formação e testar dados](media/ui-sample-classification-predict-flight-delay/split.png)

Ano, mês e colunas de fuso horário são removidas do conjunto de dados de treinamento utilizando o módulo de colunas selecionadas.

## <a name="define-features"></a>Definir funcionalidades

Machine learning, os recursos são propriedades mensuráveis individuais de algo que está interessado. Encontrar um sólido conjunto de recursos requer experimentação e conhecimento do domínio. Algumas funcionalidades são melhores para prever num destino do que outras. Além disso, algumas funcionalidades podem ter uma correlação forte com outras funcionalidades e não adicionará novas informações para o modelo. Estas funcionalidades podem ser removidas.

Para criar um modelo, pode usar todos os recursos disponíveis ou selecionar um subconjunto dos recursos.

## <a name="choose-and-apply-a-learning-algorithm"></a>Escolher e aplicar um algoritmo de aprendizagem

Criar um modelo com o **regressão logística de duas classes** módulo e formá-lo no conjunto de dados de treinamento. 

O resultado do **Train Model** módulo é um modelo de classificação de preparação que pode ser utilizado para pontuação amostras novo para fazer previsões. Utilize o teste definido para gerar as pontuações de modelos de formação. Em seguida, utilize o **Evaluate Model** módulo para analisar e comparar a qualidade dos modelos.

Depois de executar a experimentação, pode ver o resultado do **modelo de pontuação** módulo ao clicar na porta de saída e selecionando **Visualize**. A saída inclui as etiquetas classificadas e as probabilidades para as etiquetas.

Por fim, para testar a qualidade dos resultados, adicione a **Evaluate Model** módulo para a experimentação baseadas em telas e ligue a porta de entrada à esquerda à saída do módulo do modelo de pontuação. Execute a experimentação e ver o resultado do **Evaluate Model** módulo, ao clicar na porta de saída e selecionando **Visualize**.

## <a name="evaluate"></a>Avaliar
O modelo de regressão logística tem AUC de 0.631 de teste definido.

 ![avaliar](media/ui-sample-classification-predict-flight-delay/evaluate.png)

## <a name="next-steps"></a>Passos Seguintes

Explore os outros exemplos disponíveis para a interface visual:

- [Exemplo 1 - regressão: Prever o preço de um automóvel](ui-sample-regression-predict-automobile-price-basic.md)
- [Exemplo 2 - regressão: Compare os algoritmos de previsão de preços de automóveis](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Exemplo 3 - classificação: Prever o risco de crédito](ui-sample-classification-predict-credit-risk-basic.md)
- [Exemplo 4 - classificação: Prever o risco de crédito (custo confidencial)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Exemplo 5 - classificação: Prever o volume de alterações](ui-sample-classification-predict-churn.md)
