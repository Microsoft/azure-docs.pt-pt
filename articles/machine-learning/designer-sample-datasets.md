---
title: Utilize os conjuntos de dados da amostra
titleSuffix: Azure Machine Learning
description: Descrições dos conjuntos de dados utilizados em modelos de amostra incluídos no designer de Machine Learning. Pode utilizar estes conjuntos de dados de amostra para os seus oleodutos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 14e514f0025d474b3afb45524753583b7c2e8a7d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165065"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Utilize os conjuntos de dados da amostra no designer de machine learning Azure (pré-visualização)

Quando cria um novo pipeline no designer de Machine Learning Azure (pré-visualização), vários conjuntos de dados de amostra saem incluídos por padrão. Muitos destes conjuntos de dados de amostra são utilizados pelos modelos de amostra na página inicial do designer. Outros são incluídos como exemplos de vários tipos de dados normalmente usados na aprendizagem automática.

Alguns destes conjuntos de dados estão disponíveis no armazenamento da Blob Azure. Para estes conjuntos de dados, a tabela seguinte fornece uma ligação direta. Pode utilizar estes conjuntos de dados nos seus oleodutos utilizando o módulo [dados de importação.](./algorithm-module-reference/import-data.md)

Os restantes conjuntos de dados da amostra estão disponíveis na categoria **Datasets**-**Samples.** Pode encontrá-lo na paleta de módulos à esquerda da tela no designer. Pode utilizar qualquer um destes conjuntos de dados no seu próprio oleoduto arrastando-os para a tela.

## <a name="datasets"></a>Conjuntos de dados

<table>

<tr>
  <th>Nome do conjunto de dados</th>
  <th>Descrição do conjunto de dados</th>
</tr>

<tr>
  <td>Conjunto de dados de classificação binária de rendimento do recenseamento adulto</td>
  <td>
Um subconjunto da base de dados do Census de 1994, utilizando adultos que trabalham com mais de 16 anos com um índice de rendimento ajustado de > 100.
<p></p>
<b>Utilização:</b> Classifique as pessoas usando a demografia para prever se uma pessoa ganha mais de 50 mil por ano.
<p></p>
<b>Investigação Relacionada:</b> Kohavi, R., Becker, B., (1996). Repositório de Aprendizagem automática <a href="https://archive.ics.uci.edu/ml">UCI https://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação </td>
</tr>

<tr>
  <td>Dados sobre o preço do automóvel (Cru)</td>
  <td>
Informações sobre automóveis por marca e modelo, incluindo o preço, características como o número de cilindros e MPG, bem como uma pontuação de risco de seguro.
<p></p>
A pontuação de risco está inicialmente associada ao preço automóvel. É então ajustado para o risco real num processo conhecido pelos atuadores como símbolo. Um valor de +3 indica que o automóvel é arriscado, e um valor de -3 que provavelmente é seguro.
<p></p>
<b>Utilização:</b> Preveja a pontuação de risco por características, utilizando a classificação de regressão ou multivariado. 
<p></p>
<b>Investigação Relacionada:</b> Schlimmer, J.C. (1987). Repositório de Aprendizagem automática <a href="https://archive.ics.uci.edu/ml">UCI https://archive.ics.uci.edu/ml</a>. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação </td>
</tr>


<tr>
  <td>Etiquetas de appetence CRM partilhadas</td>
  <td>
Etiquetas do desafio de previsão da relação com o cliente da KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">(orange_small_train_appetency.labels).</a>
  </td>
</tr>

<tr>
  <td>Etiquetas CRM Churn partilhadas</td>
  <td>
Etiquetas do desafio de previsão da relação com o cliente da KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">(orange_small_train_churn.labels).</a>
  </td>
</tr>

<tr>
  <td>Conjunto de dados CRM partilhado</td>
  <td>
Estes dados provêm do desafio de previsão da relação com o cliente da KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">(orange_small_train.data.zip</a>).
<p></p>
O conjunto de dados contém clientes de 50K da empresa francesa de telecomunicações Orange. Cada cliente tem 230 características anoonizadas, das quais 190 são numéricas e 40 são categóricas. As características são muito escassas.
  </td>
</tr>

<tr>
  <td>Rótulos de upselling crm partilhados</td>
  <td>
Etiquetas do desafio de previsão da relação com o cliente da KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">(orange_large_train_upselling.labels).</a>
  </td>
</tr>

<tr>
  <td>Dados de atrasos de voo</td>
  <td>
Dados de desempenho de voo de passageiros a tempo recolhidos da recolha de dados do TranStats do Departamento de Transportes dos EUA (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">On-Time).</a>
<p></p>
O conjunto de dados abrange o período de abril a outubro de 2013. Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma:
<ul>
  <li>O conjunto de dados foi filtrado para cobrir apenas os 70 aeroportos mais movimentados do continente americano</li>
  <li>Voos cancelados foram rotulados como atrasados em mais de 15 minutos</li>
  <li>Voos desviados foram filtrados</li>
  <li>Foram selecionadas as seguintes colunas: Ano, Mês, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Cancelado</li>
</ul>
</td>
</tr>

<tr>
  <td>Conjunto de dados UCI do Cartão de Crédito Alemão</td>
  <td>
O conjunto de dados DA UCI Statlog (Cartão de Crédito Alemão)<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">(Statlog+German+Credit+Data),</a>utilizando o ficheiro german.data.
<p></p>
O conjunto de dados classifica as pessoas, descritas por um conjunto de atributos, como riscos de crédito baixos ou elevados. Cada exemplo representa uma pessoa. Existem 20 características, tanto numéricas como categóricas, e uma etiqueta binária (o valor do risco de crédito). As entradas de alto risco de crédito têm rótulo = 2, as entradas de baixo risco de crédito têm etiqueta = 1. O custo de classificar mal um exemplo de baixo risco como elevado é 1, enquanto o custo de classificar mal um exemplo de alto risco como baixo é 5.
  </td>
</tr>

<tr>
  <td>Títulos de filme iMDB</td>
  <td>
O conjunto de dados contém informações sobre filmes que foram classificados em tweets do Twitter: IMDB movie ID, nome do filme, género e ano de produção. Há filmes de 17K no conjunto de dados. O conjunto de dados foi introduzido no jornal "S. Dooms, T. De Pessemier e L. Martens. MovieTweetings: um conjunto de dados de classificação de filmes recolhido do Twitter. Workshop sobre Crowdsourcing e Computação Humana para Sistemas Recomendadores, CrowdRec na RecSys 2013."
  </td>
</tr>

<tr>
  <td>Classificações de filmes</td>
  <td>
O conjunto de dados é uma versão estendida do conjunto de dados movie Tweetings. O conjunto de dados tem classificações de 170K para filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa um tweet e é um tuple: ID do utilizador, ID de filme IMDB, classificação, carimbo de tempo, número de favoritos para este tweet, e número de retweets deste tweet. O conjunto de dados foi disponibilizado por A. Said, S. Dooms, B. Loni e D. Tikk para recommender Systems Challenge 2014.
  </td>
</tr>


<tr>
  <td>Conjunto de Dados meteorológicos</td>
  <td>
Observações meteorológicas por hora da NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">dados fundidos de 201304 a 201310).</a>
<p></p>
Os dados meteorológicos abrangem observações feitas a partir de estações meteorológicas do aeroporto, abrangendo o período de abril a outubro de 2013. Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma:
<ul>
  <li>Os IDs da estação meteorológica foram mapeados para as iDs do aeroporto correspondentes.</li>
  <li>Estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram filtradas</li>
  <li>A coluna Data foi dividida em colunas separadas ano, mês e dia</li>
  <li>Foram selecionadas as seguintes colunas: AirportID, Year, Month, Day, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 Dataset</td>
  <td>
Os dados são derivados da Wikipédia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) com base em artigos de cada empresa S&P 500, armazenados como dados XML.
<p></p>
Antes de enviar para o designer, o conjunto de dados foi processado da seguinte forma:
<ul>
  <li>Extrair conteúdo de texto para cada empresa específica</li>
  <li>Remover a formatação wiki</li>
  <li>Remover caracteres não alfanuméricos</li>
  <li>Converter todo o texto em minúscula</li>
  <li>Foram adicionadas categorias de empresas conhecidas</li>
</ul>
<p></p>
Note-se que para algumas empresas não foi possível encontrar um artigo, pelo que o número de registos é inferior a 500.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Passos seguintes

* Conheça o básico da análise preditiva e da aprendizagem automática com [tutorial: Preveja](tutorial-designer-automobile-price-train-score.md) o preço do automóvel com o designer

* Utilizar módulo [de dados de importação](./algorithm-module-reference/import-data.md) para importar conjuntos de dados de amostras
