---
title: Exemplo de pipelines de design & conjuntos de dados
titleSuffix: Azure Machine Learning
description: Utilize amostras no designer de Machine Learning Azure para iniciar os seus oleodutos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.custom: designer
ms.openlocfilehash: e40c49cdcaae6fa84a72c645f5fea4bac99ee03f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654565"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer"></a>Exemplo de oleodutos & conjuntos de dados para Azure Machine Learning Designer

Use os exemplos incorporados no designer de Machine Learning Azure para começar rapidamente a construir os seus próprios oleodutos de aprendizagem automática. O [repositório GitHub,](https://github.com/Azure/MachineLearningDesigner) designer de machine learning azure, contém documentação detalhada para o ajudar a compreender alguns cenários comuns de aprendizagem automática.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://aka.ms/AMLFree)
* Um espaço de trabalho azure machine learning com a Enterprise SKU.

## <a name="use-sample-pipelines"></a>Utilizar gasodutos de amostra

O designer guarda uma cópia dos gasodutos de amostra para o seu espaço de trabalho no estúdio. Pode editar o oleoduto para adaptá-lo às suas necessidades e guardá-lo como seu. Use-os como ponto de partida para iniciar os seus projetos.

Aqui está como usar uma amostra de designer:

1. Inscreva-se na <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>e selecione o espaço de trabalho com que pretende trabalhar.

1. Selecione **Designer**.

1. Selecione um gasoduto de amostra sob a secção **new pipeline.**

    Selecione **Mostrar mais amostras** para uma lista completa de amostras.

1. Para executar um oleoduto, primeiro tem de definir o alvo de computação padrão para executar o gasoduto.

   1. No painel **Definições** à direita da tela, **selecione Selecione o alvo computacional**.

   1. No diálogo que aparece, selecione um alvo computacional existente ou crie um novo. Selecione **Guardar**.

   1. Selecione **Submeter** na parte superior da tela para submeter uma execução de gasoduto.

   Dependendo do pipeline da amostra e das definições de cálculo, as execuções podem demorar algum tempo a ser concluídas. As definições de computação padrão têm um tamanho mínimo de nó de 0, o que significa que o designer deve alocar recursos depois de estar inativo. As repetidas corridas de gasodutos demorarão menos tempo, uma vez que os recursos da computação já estão atribuídos. Além disso, o designer utiliza resultados em cache para cada módulo para melhorar ainda mais a eficiência.


1. Depois de o gasoduto terminar de funcionar, pode rever o gasoduto e ver a saída de cada módulo para saber mais. Utilize os seguintes passos para visualizar as saídas do módulo:

   1. Selecione um módulo na tela.

   1. No módulo os detalhes painelam à direita da tela, selecione **Saídas + troncos**. Selecione o ícone de visualização do ícone do gráfico ![ ](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) para ver os resultados de cada módulo. 

   Use as amostras como pontos de partida para alguns dos cenários mais comuns de aprendizagem automática.

## <a name="regression"></a>Regressão

Explore estas amostras de regressão incorporadas.

| Título de exemplo | Descrição | 
| --- | --- |
| [Amostra 1: Regressão - Previsão do Preço do Automóvel (Básico)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Preveja os preços dos carros usando regressão linear. |
| [Amostra 2: Regressão - Previsão de Preço do Automóvel (Avançada)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Preveja os preços dos automóveis usando a floresta de decisão e impulsionou os regressos das árvores de decisão. Compare modelos para encontrar o melhor algoritmo.

## <a name="classification"></a>Classificação

Explore estas amostras de classificação incorporadas. Pode saber mais sobre as amostras sem ligações de documentação, abrindo as amostras e visualizando os comentários do módulo.

| Título de exemplo | Descrição | 
| --- | --- |
| [Amostra 3: Classificação binária com seleção de características - Previsão de Rendimento](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Preveja o rendimento tão alto ou baixo, usando uma árvore de decisão impulsionada por duas classes. Use a correlação pearson para selecionar funcionalidades.
| [Amostra 4: Classificação binária com script python personalizado - Previsão de Risco de Crédito](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Classifique os pedidos de crédito como de alto ou baixo risco. Utilize o módulo execute python script para pesar os seus dados.
| [Amostra 5: Classificação Binária - Previsão da Relação com o Cliente](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Preveja o rebuliço do cliente usando árvores de decisão impulsionadas por duas classes. Utilize o SMOTE para recolher dados tendenciosos.
| [Amostra 7: Classificação de Texto - Wikipedia SP 500 Dataset](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Classifique os tipos de empresas de artigos da Wikipédia com regressão logística multiclasse. |
| Amostra 12: Classificação multiclasse - Reconhecimento de cartas | Crie um conjunto de classificadores binários para classificar as letras escritas. |

## <a name="recommender"></a>Recomendador

Explore estas amostras de recomendação incorporadas. Pode saber mais sobre as amostras sem ligações de documentação, abrindo as amostras e visualizando os comentários do módulo.

| Título de exemplo | Descrição | 
| --- | --- |
| Amostra 10: Recomendação - Tweets de classificação de filmes | Construa um motor de recomendação de filme a partir de títulos de filme e classificação. |

## <a name="utility"></a>Utility

Saiba mais sobre as amostras que demonstram utilitários e funcionalidades de aprendizagem automática. Pode saber mais sobre as amostras sem ligações de documentação, abrindo as amostras e visualizando os comentários do módulo.

| Título de exemplo | Descrição | 
| --- | --- |
| [Amostra 6: Utilize script R personalizado - Previsão de atraso de voo](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Amostra 8: Validação cruzada para classificação binária - Previsão do Rendimento de Adultos | Utilize validação cruzada para construir um classificador binário para o rendimento adulto.
| Amostra 9: Importância da característica permutação | Use a importância da funcionalidade de permutação para calcular as pontuações de importância para o conjunto de dados do teste. 
| Amostra 11: Parâmetros de sintonia para classificação binária - Previsão do Rendimento adulto | Utilize hiperparâmetros do Modelo tune para encontrar hiperparâmetros ideais para construir um classificador binário. |

## <a name="datasets"></a>Conjuntos de dados

Quando se cria um novo pipeline no designer de Machine Learning Azure, vários conjuntos de dados de amostras são incluídos por padrão. Estes conjuntos de dados de amostra são utilizados pelos gasodutos de amostra na página inicial do designer. 

Os conjuntos de dados da amostra estão disponíveis na categoria **datasets** - **Samples.** Pode encontrá-lo na paleta de módulos à esquerda da tela no designer. Pode utilizar qualquer um destes conjuntos de dados no seu próprio oleoduto arrastando-os para a tela.

| Nome do conjunto de dados &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Descrição do conjunto de dados |
|-------------|:--------------------|
| Conjunto de dados de classificação binária de rendimento do recenseamento adulto | Um subconjunto da base de dados do Censos de 1994, utilizando adultos que trabalham com mais de 16 anos com um índice de rendimento ajustado de > 100.<br/>**Utilização**: Classifique as pessoas que usam demografia para prever se uma pessoa ganha mais de 50 mil por ano.<br/> **Investigação Relacionada**: Kohavi, R., Becker, B., (1996). [Repositório de Aprendizagem automática UCI.](https://archive.ics.uci.edu/ml) Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação|
|Dados sobre o preço do automóvel (Cru)|Informações sobre automóveis por marca e modelo, incluindo o preço, características como o número de cilindros e MPG, bem como uma pontuação de risco de seguro.<br/> A pontuação de risco está inicialmente associada ao preço automóvel. É então ajustado para o risco real num processo conhecido pelos atuadores como símbolo. Um valor de +3 indica que o automóvel é arriscado, e um valor de -3 que provavelmente é seguro.<br/>**Utilização**: </b> Prever a pontuação de risco por características, utilizando a classificação de regressão ou multivariado.<br/>**Investigação Relacionada**: </b> Schlimmer, J.C. (1987). [Repositório de Aprendizagem automática UCI.](https://archive.ics.uci.edu/ml) Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação. |
| Etiquetas de appetence CRM partilhadas |Etiquetas do desafio de previsão da relação com o cliente da KDD Cup 2009[(orange_small_train_appetency.labels).](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)|
|Etiquetas CRM Churn partilhadas|Etiquetas do desafio de previsão da relação com o cliente da KDD Cup 2009[(orange_small_train_churn.labels).](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)|
|Conjunto de dados CRM partilhado | Estes dados provêm do desafio de previsão da relação com o cliente da KDD Cup 2009[(orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>O conjunto de dados contém clientes de 50K da empresa francesa de telecomunicações Orange. Cada cliente tem 230 características anoonizadas, das quais 190 são numéricas e 40 são categóricas. As características são muito escassas. |
|Rótulos de upselling crm partilhados|Etiquetas do desafio de previsão da relação com o cliente da KDD Cup 2009[(orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Dados de atrasos de voo|Dados de desempenho de voo de passageiros a tempo recolhidos da recolha de dados do TranStats do Departamento de Transportes dos EUA ([On-Time).](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)<br/>O conjunto de dados abrange o período de abril a outubro de 2013. Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma: <br/>- O conjunto de dados foi filtrado para cobrir apenas os 70 aeroportos mais movimentados do continente americano <br/>- Voos cancelados foram rotulados como atrasados por mais de 15 minutos <br/>- Voos desviados foram filtrados <br/>- Foram selecionadas as seguintes colunas: Ano, Mês, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Cancelado|
|Conjunto de dados UCI do Cartão de Crédito Alemão|O conjunto de dados DA UCI Statlog (Cartão de Crédito Alemão)[(Statlog+German+Credit+Data),](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))utilizando o ficheiro german.data.<br/>O conjunto de dados classifica as pessoas, descritas por um conjunto de atributos, como riscos de crédito baixos ou elevados. Cada exemplo representa uma pessoa. Existem 20 características, tanto numéricas como categóricas, e uma etiqueta binária (o valor do risco de crédito). As entradas de alto risco de crédito têm rótulo = 2, as entradas de baixo risco de crédito têm etiqueta = 1. O custo de classificar mal um exemplo de baixo risco como elevado é 1, enquanto o custo de classificar mal um exemplo de alto risco como baixo é 5.|
|Títulos de filme iMDB|O conjunto de dados contém informações sobre filmes que foram classificados em tweets do Twitter: IMDB movie ID, nome do filme, género e ano de produção. Há filmes de 17K no conjunto de dados. O conjunto de dados foi introduzido no jornal "S. Dooms, T. De Pessemier e L. Martens. MovieTweetings: um conjunto de dados de classificação de filmes recolhido do Twitter. Workshop sobre Crowdsourcing e Computação Humana para Sistemas Recomendadores, CrowdRec na RecSys 2013."|
|Classificações de filmes|O conjunto de dados é uma versão estendida do conjunto de dados movie Tweetings. O conjunto de dados tem classificações de 170K para filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa um tweet e é um tuple: ID do utilizador, ID de filme IMDB, classificação, carimbo de tempo, número de favoritos para este tweet, e número de retweets deste tweet. O conjunto de dados foi disponibilizado por A. Said, S. Dooms, B. Loni e D. Tikk para recommender Systems Challenge 2014.|
|Conjunto de Dados meteorológicos|Observações meteorológicas por hora da NOAA ([dados fundidos de 201304 a 201310).](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)<br/>Os dados meteorológicos abrangem observações feitas a partir de estações meteorológicas do aeroporto, abrangendo o período de abril a outubro de 2013. Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma:    <br/> - As iDs da estação meteorológica foram mapeadas para as iDs do aeroporto correspondentes    <br/> - As estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram filtradas    <br/> - A coluna Data foi dividida em colunas separadas ano, mês e dia    <br/> - Foram selecionadas as seguintes colunas: AirportID, Ano, mês, dia, tempo, TimeZone, SkyCondition, Visibilidade, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Wikipedia SP 500 Dataset|Os dados são derivados da Wikipédia ( https://www.wikipedia.org/) com base em artigos de cada empresa S&P 500, armazenados como dados XML.    <br/>Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma:    <br/> - Extrair conteúdo de texto para cada empresa específica    <br/> - Remover a formatação wiki    <br/> - Remover caracteres não alfanuméricos    <br/> - Converter todo o texto em minúscula    <br/> - Foram adicionadas categorias de empresas conhecidas    <br/>Note-se que para algumas empresas não foi possível encontrar um artigo, pelo que o número de registos é inferior a 500.|


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximos passos

Conheça o básico da análise preditiva e da aprendizagem automática com [tutorial: Preveja](tutorial-designer-automobile-price-train-score.md) o preço do automóvel com o designer

