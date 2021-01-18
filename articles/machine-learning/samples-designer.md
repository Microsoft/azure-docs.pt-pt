---
title: Exemplo de oleodutos & conjuntos de dados para o designer
titleSuffix: Azure Machine Learning
description: Aprenda a usar amostras no designer de Aprendizagem automática Azure para iniciar os seus oleodutos de aprendizagem automática.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 10/14/2020
ms.custom: designer
ms.openlocfilehash: 4d594115139321c8a4ad2b3aa6b065474bd426fa
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556176"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer"></a>Pipelines de exemplo & conjuntos de dados para o designer de aprendizagem de máquinas Azure

Use os exemplos incorporados no designer de Aprendizagem automática Azure para começar rapidamente a construir os seus próprios oleodutos de aprendizagem automática. O [repositório gitHub](https://github.com/Azure/MachineLearningDesigner) do designer de machine learning Azure contém documentação detalhada para ajudá-lo a entender alguns cenários comuns de aprendizagem automática.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree)
* Uma área de trabalho do Azure Machine Learning 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-sample-pipelines"></a>Utilize gasodutos de amostra

O designer guarda uma cópia dos oleodutos de amostra para o seu espaço de trabalho de estúdio. Pode editar o pipeline para adaptá-lo às suas necessidades e guardá-lo como seu. Use-os como ponto de partida para iniciar os seus projetos.

Eis como usar uma amostra de designer:

1. Inscreva-se para <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>e selecione o espaço de trabalho com o quais pretende trabalhar.

1. Selecione **Designer**.

1. Selecione um gasoduto de amostra sob a secção **do novo gasoduto.**

    Selecione **Mostrar mais amostras** para uma lista completa de amostras.

1. Para executar um oleoduto, primeiro tem de definir o alvo de computação padrão para executar o oleoduto.

   1. No painel **de Definições** à direita da tela, **selecione Selecione o alvo de cálculo**.

   1. No diálogo que aparece, selecione um alvo de computação existente ou crie um novo. Selecione **Guardar**.

   1. **Selecione Submeter** na parte superior da tela para submeter uma corrida de gasoduto.

   Dependendo do gasoduto de amostra e das definições de cálculo, as corridas podem demorar algum tempo a ser concluídas. As definições de computação padrão têm um tamanho mínimo de nó de 0, o que significa que o designer deve alocar recursos depois de estar inativo. As repetidas operações de gasoduto levarão menos tempo, uma vez que os recursos de computação já estão atribuídos. Além disso, o designer utiliza resultados em cache para cada módulo para melhorar ainda mais a eficiência.


1. Depois de o gasoduto terminar em funcionamento, pode rever o oleoduto e ver a saída de cada módulo para saber mais. Utilize os seguintes passos para visualizar as saídas dos módulos:

   1. Clique com o botão direito no módulo na tela cuja saída gostaria de ver.
   1. **Selecione Visualizar**.


   Utilize as amostras como pontos de partida para alguns dos cenários mais comuns de aprendizagem automática.

## <a name="regression"></a>Regressão

Explore estas amostras de regressão incorporadas.

| Título de exemplo | Description | 
| --- | --- |
| [Regressão - Previsão dos Preços do Automóvel (Básico)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-basic.md) | Prever os preços dos carros usando a regressão linear. |
| [Regressão - Previsão do Preço do Automóvel (Avançado)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/regression-automobile-price-prediction-compare-algorithms.md) | Prever os preços dos automóveis usando a floresta de decisão e impulsionar os regressores das árvores de decisão. Compare os modelos para encontrar o melhor algoritmo.

## <a name="classification"></a>Classificação

Explore estas amostras de classificação incorporadas. Você pode aprender mais sobre as amostras abrindo as amostras e visualizando os comentários do módulo no designer.

| Título de exemplo | Description | 
| --- | --- |
| [Classificação Binária com Seleção de Recursos - Previsão de Rendimento](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-feature-selection-income-prediction.md) | Prever o rendimento tão alto ou baixo, usando uma árvore de decisão reforçada de duas classes. Use a correlação Pearson para selecionar funcionalidades.
| [Classificação binária com script pitão personalizado - Previsão do Risco de Crédito](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-python-credit-prediction.md) | Classificar os pedidos de crédito como de alto ou baixo risco. Utilize o módulo de script de Python executar para pesar os seus dados.
| [Classificação Binária - Previsão da Relação do Cliente](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/binary-classification-customer-relationship-prediction.md) | Prever o churn do cliente usando árvores de decisão reforçadas de duas classes. Utilize o SMOTE para recolher amostras de dados tendenciosos.
| [Classificação de texto - Wikipedia SP 500 Dataset](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/text-classification-wiki.md) | Classifique os tipos de empresas de artigos da Wikipédia com regressão logística multiclasse. |
| Classificação Multiclasse - Reconhecimento de Letras | Crie um conjunto de classificadores binários para classificar letras escritas. |

## <a name="computer-vision"></a>Imagem digitalizada

Explore estas amostras de visão computacional incorporadas. Você pode aprender mais sobre as amostras abrindo as amostras e visualizando os comentários do módulo no designer.

| Título de exemplo | Description | 
| --- | --- |
| Classificação de imagem usando DenseNet | Utilize módulos de visão computacional para construir um modelo de classificação de imagem baseado no PyTorch DenseNet.| 

## <a name="recommender"></a>Recomendador

Explore estas amostras de recomendadores incorporadas. Você pode aprender mais sobre as amostras abrindo as amostras e visualizando os comentários do módulo no designer.

| Título de exemplo | Description | 
| --- | --- |
| Recomendação de grande & baseada em profundidade - Previsão de Classificação de Restaurantes | Construa um motor de recomendação de restaurante a partir de funcionalidades e classificações de restaurante/utilizador.|
| Recomendação - Tweets de Classificação de Filmes | Construa um motor de recomendação de filme a partir de funcionalidades de filme/utilizador e classificações.|

## <a name="utility"></a>Utilitário

Saiba mais sobre as amostras que demonstram serviços e funcionalidades de aprendizagem automática. Você pode aprender mais sobre as amostras abrindo as amostras e visualizando os comentários do módulo no designer.

| Título de exemplo | Description | 
| --- | --- |
| Classificação Binária usando Modelo de Wabbit Vowpal - Previsão do Rendimento de Adultos | Vowpal Wabbit é um sistema de aprendizagem automática que empurra a fronteira da aprendizagem automática com técnicas como online, hashing, allreduce, reduções, learning2search, ative, e interactive learning. Esta amostra mostra como usar o modelo Vowpal Wabbit para construir um modelo de classificação binária. 
| [Use script R personalizado - Previsão do atraso de voo](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/r-script-flight-delay-prediction.md) | Use o script R personalizado para prever se um voo de passageiros programado será adiado por mais de 15 minutos.
| Validação cruzada para classificação binária - Previsão do Rendimento de Adultos | Utilize validação cruzada para construir um classificador binário para o rendimento de adultos.
| Importância da funcionalidade de permutação | Utilizar a importância da funcionalidade de permutação para calcular as pontuações de importância para o conjunto de dados do teste. 
| Parâmetros de sintonização para classificação binária - Previsão do Rendimento de Adultos | Utilize hiperparmetros tune model para encontrar hiperparímetros ideais para construir um classificador binário. |

## <a name="datasets"></a>Conjuntos de dados

Quando cria um novo oleoduto no designer Azure Machine Learning, uma série de conjuntos de dados da amostra são incluídos por padrão. Estes conjuntos de dados da amostra são utilizados pelos gasodutos de amostragem na página inicial do designer. 

Os conjuntos de dados da amostra estão disponíveis na categoria **Datasets** - **Samples.** Pode encontrar isto na paleta do módulo à esquerda da tela no designer. Pode utilizar qualquer um destes conjuntos de dados no seu próprio oleoduto arrastando-o para a tela.

| Nome do conjunto de &nbsp; dados&nbsp;&nbsp;&nbsp;&nbsp;| Descrição do conjunto de dados |
|-------------|:--------------------|
| Conjunto de dados de classificação binária de rendimento do rendimento do rendimento de adultos | Um subconjunto da base de dados do Censos de 1994, utilizando adultos que trabalham com mais de 16 anos com um índice de rendimento ajustado de > 100.<br/>**Utilização**: Classifique as pessoas que usam a demografia para prever se uma pessoa ganha mais de 50 mil por ano.<br/> **Investigação Relacionada**: Kohavi, R., Becker, B., (1996). [Repositório de Aprendizagem automática da UCI.](https://archive.ics.uci.edu/ml) Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação|
|Dados sobre os preços do automóvel (Raw)|Informação sobre automóveis por marca e modelo, incluindo o preço, características como o número de cilindros e MPG, bem como uma pontuação de risco de seguro.<br/> A pontuação de risco está inicialmente associada ao preço automóvel. É então ajustado para o risco real num processo conhecido pelos atuadores como símbolo. Um valor de +3 indica que o automóvel é arriscado, e um valor de -3 que provavelmente é seguro.<br/>**Utilização**: Prever a pontuação de risco por características, utilizando a regressão ou a classificação multivariada.<br/>**Investigação Relacionada**: Schlimmer, J.C. (1987). [Repositório de Aprendizagem automática da UCI.](https://archive.ics.uci.edu/ml) Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação. |
| Rótulos de Appetency CRM compartilhados |Etiquetas do desafio de previsão da relação com o cliente KDD Cup 2009[(orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|Etiquetas CRM Churn Compartilhadas|Etiquetas do desafio de previsão da relação com o cliente KDD Cup 2009[(orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|Conjunto de dados crm compartilhado | Estes dados provêm do desafio de previsão da relação com o cliente da KDD Cup 2009[ (orange_small_train.data.zip). ](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip) <br/>O conjunto de dados contém 50 mil clientes da empresa francesa de telecomunicações Orange. Cada cliente tem 230 funcionalidades anonimizadas, 190 das quais numéricas e 40 são categóricas. As características são muito escassas. |
|Etiquetas de upselling CRM partilhadas|Rótulos do desafio de previsão da relação com o cliente KDD Cup 2009[(orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Dados de atrasos de voo|Dados de desempenho de voo de passageiros retirados da recolha de dados tranStats do Departamento de Transportes dos EUA[(On-Time).](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)<br/>O conjunto de dados cobre o período de tempo April-October 2013. Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma: <br/>- O conjunto de dados foi filtrado para cobrir apenas os 70 aeroportos mais movimentados dos EUA <br/>- Voos cancelados foram rotulados como atrasados por mais de 15 minutos <br/>- Voos desviados foram filtrados <br/>- Foram selecionadas as seguintes colunas: Ano, Mês, Dia deMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Cancelado|
|Conjunto de dados do Cartão de Crédito Alemão UCI|O conjunto de dados da UCI Statlog (Cartão de Crédito Alemão)[(Statlog+German+Credit+Data),](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))utilizando o ficheiro german.data.<br/>O conjunto de dados classifica as pessoas, descritas por um conjunto de atributos, como riscos de crédito baixos ou elevados. Cada exemplo representa uma pessoa. Existem 20 características, tanto numéricas como categóricas, e uma etiqueta binária (o valor do risco de crédito). As entradas de alto risco de crédito têm etiqueta = 2, entradas de baixo risco de crédito têm etiqueta = 1. O custo de classificar mal um exemplo de baixo risco como elevado é 1, enquanto o custo de classificação errada de um exemplo de alto risco como baixo é 5.|
|Títulos de filme do IMDB|O conjunto de dados contém informações sobre filmes que foram classificados em tweets do Twitter: ID filme IMDB, nome do filme, género e ano de produção. Há filmes de 17K no conjunto de dados. O conjunto de dados foi introduzido no artigo "S. Dooms, T. De Pessemier e L. Martens. MovieTweetings: um conjunto de dados de classificação de filme recolhidos do Twitter. Workshop de Crowdsourcing e Computação Humana para Sistemas Recomendadores, CrowdRec na RecSys 2013."|
|Classificações de Filmes|O conjunto de dados é uma versão estendida do conjunto de dados do Movie Tweetings. O conjunto de dados tem classificações de 170K para filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa um tweet e é um tuple: ID do utilizador, ID de filme IMDB, classificação, timetamp, número de favoritos para este tweet, e número de retweets deste tweet. O conjunto de dados foi disponibilizado por A. Said, S. Dooms, B. Loni e D. Tikk para o Recommender Systems Challenge 2014.|
|Conjunto de dados meteorológicos|Observações meteorológicas por hora da NOAA[(dados fundidos de 201304 a 201310).](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)<br/>Os dados meteorológicos cobrem observações feitas a partir de estações meteorológicas do aeroporto, abrangendo o período April-October 2013. Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma:    <br/> - Os IDs da estação meteorológica foram mapeados para os iDs do aeroporto correspondentes    <br/> - Estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram filtradas    <br/> - A coluna Date foi dividida em colunas separadas de ano, mês e dia    <br/> - Foram selecionadas as seguintes colunas: AirportID, Ano, Mês, Dia, Tempo, TimeZone, SkyCondition, Visibilidade, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, Hourciply, Alprettime, Alpretertime, Alpretertime,|
|Conjunto de dados Wikipedia SP 500|Os dados são derivados da Wikipédia ( https://www.wikipedia.org/) com base em artigos de cada empresa S&P 500, armazenados como dados XML.    <br/>Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma:    <br/> - Extrair conteúdo de texto para cada empresa específica    <br/> - Remover a formatação wiki    <br/> - Remover caracteres não alfanuméricos    <br/> - Converter todo o texto em minúsculas    <br/> - Foram adicionadas categorias de empresas conhecidas    <br/>Note-se que para algumas empresas não foi possível encontrar um artigo, pelo que o número de registos é inferior a 500.|
|Dados de Características do Restaurante| Um conjunto de metadados sobre restaurantes e suas características, como tipo de comida, estilo de jantar e localização. <br/>**Utilização**: Utilize este conjunto de dados, em combinação com os outros dois conjuntos de dados do restaurante, para treinar e testar um sistema de recomendadores.<br/> **Investigação Relacionada**: Bache, K. e Lichman, M. (2013). [Repositório de Aprendizagem automática da UCI.](https://archive.ics.uci.edu/ml) Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação.|
|Classificações de Restaurantes| Contém classificações dadas pelos utilizadores a restaurantes numa escala de 0 a 2.<br/>**Utilização**: Utilize este conjunto de dados, em combinação com os outros dois conjuntos de dados do restaurante, para treinar e testar um sistema de recomendadores. <br/>**Investigação Relacionada**: Bache, K. e Lichman, M. (2013). [Repositório de Aprendizagem automática da UCI.](https://archive.ics.uci.edu/ml) Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação.|
|Dados do Cliente do Restaurante| Um conjunto de metadados sobre clientes, incluindo demografia e preferências. <br/>**Utilização**: Utilize este conjunto de dados, em combinação com os outros dois conjuntos de dados do restaurante, para treinar e testar um sistema de recomendadores. <br/> **Investigação Relacionada**: Bache, K. e Lichman, M. (2013). [Repositório de Aprendizagem automática da UCI](https://archive.ics.uci.edu/ml) Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação.|

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Aprenda os fundamentos da análise preditiva e machine learning com [Tutorial: Predict automobile price with the designer](tutorial-designer-automobile-price-train-score.md)

