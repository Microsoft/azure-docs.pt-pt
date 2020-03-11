---
title: Utilize conjuntos de dados de amostras em designer de aprendizagem automática Azure
titleSuffix: Azure Machine Learning
description: Saiba mais sobre os conjuntos de dados da amostra incluídos no designer de Machine Learning Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037308"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Utilize os conjuntos de dados da amostra no designer de machine learning Azure (pré-visualização)

Quando cria um novo pipeline no designer de Machine Learning Azure (pré-visualização), vários conjuntos de dados de amostra saem incluídos por padrão. Estes conjuntos de dados de amostra são utilizados pelos gasodutos de amostra na página inicial do designer. 

Os conjuntos de dados da amostra estão disponíveis na categoria **Datasets**-**Samples.** Pode encontrá-lo na paleta de módulos à esquerda da tela no designer. Pode utilizar qualquer um destes conjuntos de dados no seu próprio oleoduto arrastando-os para a tela.

## <a name="datasets"></a>Conjuntos de dados


| Dataset&nbsp;nome&nbsp;&nbsp;&nbsp;&nbsp;| Descrição do conjunto de dados |
|-------------|:--------------------|
| Conjunto de dados de classificação binária de rendimento do recenseamento adulto | Um subconjunto da base de dados do Census de 1994, utilizando adultos que trabalham com mais de 16 anos com um índice de rendimento ajustado de > 100.<br/>**Utilização**: Classifique as pessoas que usam demografia para prever se uma pessoa ganha mais de 50 mil por ano.<br/> **Investigação Relacionada**: Kohavi, R., Becker, B., (1996). [Repositório de Aprendizagem automática UCI.](https://archive.ics.uci.edu/ml) Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação|
|Dados sobre o preço do automóvel (Cru)|Informações sobre automóveis por marca e modelo, incluindo o preço, características como o número de cilindros e MPG, bem como uma pontuação de risco de seguro.<br/> A pontuação de risco está inicialmente associada ao preço automóvel. É então ajustado para o risco real num processo conhecido pelos atuadores como símbolo. Um valor de +3 indica que o automóvel é arriscado, e um valor de -3 que provavelmente é seguro.<br/>**Utilização**:</b> Prever a pontuação de risco por características, utilizando a classificação de regressão ou multivariado.<br/>**Investigação Relacionada**:</b> Schlimmer, J.C. (1987). [Repositório de Aprendizagem automática UCI.](https://archive.ics.uci.edu/ml) Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação. |
| Etiquetas de appetence CRM partilhadas |Etiquetas do desafio de previsão da relação com o cliente da KDD Cup 2009[(orange_small_train_appetency.labels).](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)|
|Etiquetas CRM Churn partilhadas|Etiquetas do desafio de previsão da relação com o cliente da KDD Cup 2009[(orange_small_train_churn.labels).](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)|
|Conjunto de dados CRM partilhado | Estes dados provêm do desafio de previsão da relação com o cliente da KDD Cup 2009[(orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>O conjunto de dados contém clientes de 50K da empresa francesa de telecomunicações Orange. Cada cliente tem 230 características anoonizadas, das quais 190 são numéricas e 40 são categóricas. As características são muito escassas. |
|Rótulos de upselling crm partilhados|Etiquetas do desafio de previsão da relação com o cliente da KDD Cup 2009[(orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Dados de atrasos de voo|Dados de desempenho de voo de passageiros a tempo recolhidos da recolha de dados do TranStats do Departamento de Transportes dos EUA ([On-Time).](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)<br/>O conjunto de dados abrange o período de abril a outubro de 2013. Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma: <br/>- O conjunto de dados foi filtrado para cobrir apenas os 70 aeroportos mais movimentados do continente americano <br/>- Voos cancelados foram rotulados como atrasados por mais de 15 minutos <br/>- Voos desviados foram filtrados <br/>- Foram selecionadas as seguintes colunas: Ano, Mês, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Cancelado|
|Conjunto de dados UCI do Cartão de Crédito Alemão|O conjunto de dados DA UCI Statlog (Cartão de Crédito Alemão)[(Statlog+German+Credit+Data),](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))utilizando o ficheiro german.data.<br/>O conjunto de dados classifica as pessoas, descritas por um conjunto de atributos, como riscos de crédito baixos ou elevados. Cada exemplo representa uma pessoa. Existem 20 características, tanto numéricas como categóricas, e uma etiqueta binária (o valor do risco de crédito). As entradas de alto risco de crédito têm rótulo = 2, as entradas de baixo risco de crédito têm etiqueta = 1. O custo de classificar mal um exemplo de baixo risco como elevado é 1, enquanto o custo de classificar mal um exemplo de alto risco como baixo é 5.|
|Títulos de filme iMDB|O conjunto de dados contém informações sobre filmes que foram classificados em tweets do Twitter: IMDB movie ID, nome do filme, género e ano de produção. Há filmes de 17K no conjunto de dados. O conjunto de dados foi introduzido no jornal "S. Dooms, T. De Pessemier e L. Martens. MovieTweetings: um conjunto de dados de classificação de filmes recolhido do Twitter. Workshop sobre Crowdsourcing e Computação Humana para Sistemas Recomendadores, CrowdRec na RecSys 2013."|
|Classificações de filmes|O conjunto de dados é uma versão estendida do conjunto de dados movie Tweetings. O conjunto de dados tem classificações de 170K para filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa um tweet e é um tuple: ID do utilizador, ID de filme IMDB, classificação, carimbo de tempo, número de favoritos para este tweet, e número de retweets deste tweet. O conjunto de dados foi disponibilizado por A. Said, S. Dooms, B. Loni e D. Tikk para recommender Systems Challenge 2014.|
|Conjunto de Dados meteorológicos|Observações meteorológicas por hora da NOAA ([dados fundidos de 201304 a 201310).](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)<br/>Os dados meteorológicos abrangem observações feitas a partir de estações meteorológicas do aeroporto, abrangendo o período de abril a outubro de 2013. Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma:    <br/> - As iDs da estação meteorológica foram mapeadas para as iDs do aeroporto correspondentes    <br/> - As estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram filtradas    <br/> - A coluna Data foi dividida em colunas separadas ano, mês e dia    <br/> - Foram selecionadas as seguintes colunas: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Wikipedia SP 500 Dataset|Os dados são derivados da Wikipédia (https://www.wikipedia.org/) com base em artigos de cada empresa S&P 500, armazenados como dados XML.    <br/>Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma:    <br/> - Extrair conteúdo de texto para cada empresa específica    <br/> - Remover a formatação wiki    <br/> - Remover caracteres não alfanuméricos    <br/> - Converter todo o texto em minúscula    <br/> - Foram adicionadas categorias de empresas conhecidas    <br/>Note-se que para algumas empresas não foi possível encontrar um artigo, pelo que o número de registos é inferior a 500.|

## <a name="next-steps"></a>Passos seguintes

* Conheça o básico da análise preditiva e da aprendizagem automática com [tutorial: Preveja](tutorial-designer-automobile-price-train-score.md) o preço do automóvel com o designer

* Aprenda a modificar as amostras de [designer existentes](samples-designer.md) para adaptá-las às suas necessidades.
