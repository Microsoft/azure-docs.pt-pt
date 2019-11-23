---
title: Utilizar os conjuntos de dados de exemplo
titleSuffix: ML Studio (classic) - Azure
description: Descrições dos conjuntos de valores usados em modelos de exemplo incluídos no Machine Learning Studio (clássico). Você pode usar esses conjuntos de valores de exemplo para seus experimentos.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 84ac2b43c9ec2e46bcd2967cf411f1bb1f60f42a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839398"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>Usar os conjuntos de valores de exemplo no Azure Machine Learning Studio (clássico)
[top]: #machine-learning-sample-datasets

Quando você cria um novo espaço de trabalho no Azure Machine Learning Studio (clássico), vários testes de exemplo e experimentos são incluídos por padrão. Muitos desses conjuntos de exemplo de conjunto de exemplos são usados pelos modelos de exemplo no [Galeria de ia do Azure](https://gallery.azure.ai/). Outras são incluídas como exemplos de vários tipos de dados normalmente usados no aprendizado de máquina.

Alguns desses conjuntos de itens estão disponíveis no armazenamento de BLOBs do Azure. Para esses conjuntos de tabelas, a tabela a seguir fornece um link direto. Você pode usar esses conjuntos de dados em seus experimentos usando o módulo [importar data][import-data] .

O restante desses conjuntos de exemplos de exemplo está disponível em seu espaço de trabalho em **DataSets salvos**. Você pode encontrar isso na paleta de módulo à esquerda da tela do experimento no Machine Learning Studio (clássico).
Você pode usar qualquer um desses conjuntos de testes em seu próprio experimento arrastando-o para a tela do experimento.

## <a name="datasets"></a>Conjuntos de dados

<table>

<tr>
  <th>Nome do conjunto de dados</th>
  <th>Descrição do conjunto de um</th>
</tr>

<tr>
  <td>Conjunto de censo de classificação binária de renda de adulto</td>
  <td>
Um subconjunto do banco de dados 1994 censo, usando adultos de trabalho durante a idade de 16 com um índice de renda ajustado de > 100.
<p></p>
<b>Uso:</b> Classifique pessoas usando dados demográficos para prever se uma pessoa recebe mais de 50 mil por ano.
<p></p>
<b>Pesquisa relacionada:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação </td>
</tr>

<tr>
  <td>Conjunto de código do Aeroporto</td>
  <td>
Códigos de Aeroporto dos EUA.
<p></p>
Esse conjunto de conteúdo contém uma linha para cada aeroporto dos EUA, fornecendo o número de ID do aeroporto e o nome, juntamente com a cidade e o estado do local.
  </td>
</tr>

<tr>
  <td>Dados de preço de automóvel (bruto)</td>
  <td>
Informações sobre automóveis por marca e modelo, incluindo o preço, recursos como o número de cilindros e MPG, bem como uma pontuação de risco de seguro.
<p></p>
A pontuação de risco é inicialmente associada ao preço automático. Em seguida, ele é ajustado para o risco real em um processo conhecido por avaliadores como símbolos. Um valor de + 3 indica que o auto é arriscado e um valor de-3 que provavelmente é seguro.
<p></p>
<b>Uso:</b> Prever a pontuação de risco por recursos, usando a regressão ou classificação multivariada. 
<p></p>
<b>Pesquisa relacionada:</b> Schlimmer, J.C. (1987). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação </td>
</tr>

<tr>
  <td>Conjunto de conjuntos de o relocação de bicicletas</td>
  <td>
O conjunto de dados de aluguel de bicicletas de UCI que é baseado em reais, da empresa Bikeshare de capital que mantém uma rede de aluguel de bicicletas em Washington, D.c.
<p></p>
O conjunto de registros tem uma linha para cada hora de cada dia em 2011 e 2012, para um total de 17.379 linhas. O intervalo de locações de bicicletas por hora é de 1 a 977.

  </td>
</tr>

<tr>
  <td>Imagem RGB de Bill Gates</td>
  <td>
Arquivo de imagem disponível publicamente convertido em dados CSV.
<p></p>
O código para converter a imagem é fornecido na página de detalhes de <strong>quantização de cor usando o modelo de clustering K-</strong> means.
  </td>
</tr>

<tr>
  <td>Dados de doação de sangue</td>
  <td>
Um subconjunto de dados do doador de sangue do centro de serviço de transfusão de sangue da cidade Hsin-Chu, Taiwan.
<p></p>
Os dados do doador incluem os meses desde a última doação) e a frequência, ou o número total de doações, o tempo desde a última doação e a quantidade de sangue.
<p></p>
<b>Uso:</b> O objetivo é prever por meio da classificação se o doador doadorer sangue em março de 2007, em que 1 indica um patrocinador durante o período de destino e 0 um não doador. 
<p></p>
<b>Pesquisa relacionada:</b> Yeh, I.C., (2008). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação
<p></p>
Yeh, I-Cheng, Yang, King-Jang e Ting, tao-Ming, "descoberta de conhecimento no modelo RFM usando a sequência de Bernoulli", sistemas especialistas com aplicativos, 2008 <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Mama câncer data</td>
  <td>
Um dos três conjuntos de câncer de itens relacionados ao Oncologia Institute que aparece frequentemente na literatura de aprendizado de máquina. Combina informações de diagnóstico com recursos da análise de laboratório de aproximadamente 300 exemplos de tecido.
<p></p>
<b>Uso:</b> Classifique o tipo de câncer, com base em 9 atributos, alguns dos quais são lineares e outros são categóricos. 
<p></p>
<b>Pesquisa relacionada:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação </td>
</tr>

<tr>
  <td>Recursos do mama câncer <td>
O conjunto de dados contém informações para 102 mil de regiões suspeitas (candidatos) de imagens de raios X, cada uma descrita por 117 recursos. Os recursos são proprietários e seu significado não é revelado pelos criadores do conjunto de recursos (Siemens Healthcare). 
  </td>
</tr>

<tr>
  <td>Mama câncer info</td>
  <td>
O conjunto de dados contém informações adicionais para cada região suspeita da imagem X-Ray. Cada exemplo fornece informações (por exemplo, rótulo, ID do paciente, coordenadas do patch em relação à imagem inteira) sobre o número de linha correspondente no conjunto de dados de recursos do mama câncer. Cada paciente tem vários exemplos. Para pacientes que têm um câncer, alguns exemplos são positivos e alguns são negativos. Para pacientes que não têm um câncer, todos os exemplos são negativos. O DataSet tem exemplos de 102 mil. O conjunto de os é polarizado, 0,6% dos pontos são positivos, o restante é negativo. O conjunto de alterações foi disponibilizado pela Siemens Healthcare.
  </td>
</tr>

<tr>
  <td>Rótulos de desejo de CRM compartilhados</td>
  <td>
Rótulos do desafio de previsão de relacionamento com o cliente do KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency. Labels</a>).
  </td>
</tr>

<tr>
  <td>Rótulos de rotatividade de CRM compartilhados</td>
  <td>
Rótulos do desafio de previsão de relacionamento com o cliente do KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn. Labels</a>).
  </td>
</tr>

<tr>
  <td>DataSet do CRM compartilhado</td>
  <td>
Esses dados são provenientes do desafio de previsão de relacionamento com o cliente do KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train. Data. zip</a>).
<p></p>
O conjunto de conteúdo contém clientes do 50 mil da empresa de telecomunicações francesa laranja. Cada cliente tem 230 recursos anônimos, 190 dos quais são numéricos e 40 são categóricos. Os recursos são muito esparsos.
  </td>
</tr>

<tr>
  <td>Rótulos de venda de CRM compartilhados</td>
  <td>
Rótulos do desafio de previsão de relacionamento com o cliente do KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling. Labels</a>).
  </td>
</tr>

<tr>
  <td>Dados de regressão de eficiência de energia</td>
  <td>
Uma coleção de perfis de energia simulados, com base em 12 formas de construção diferentes. Os edifícios são diferenciados por oito recursos. Isso inclui a área envidraçada, a distribuição da área envidraçada e a orientação.
<p></p>
<b>Uso:</b> Use a regressão ou a classificação para prever a classificação de eficiência de energia com base em uma das duas respostas de valor real. Para classificação de várias classes, é arredondar a variável de resposta para o número inteiro mais próximo. 
<p></p>
<b>Pesquisa relacionada:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação </td>
</tr>

<tr>
  <td>Dados de atrasos de voo</td>
  <td>
Dados de desempenho no horário do vôo em tempo hábil obtidos da coleta de dados TranStats do departamento de transporte dos EUA (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">no momento</a>).
<p></p>
O conjunto de data cobre o período de abril a 2013 de outubro. Antes de carregar para a versão clássica do Azure Machine Learning Studio, o conjunto de um foi processado da seguinte maneira:
<ul>
  <li>O conjunto de um foi filtrado para cobrir somente os aeroportos 70 mais ocupados no continental dos EUA</li>
  <li>Os vôos cancelados foram rotulados como atrasados em mais de 15 minutos</li>
  <li>Vôos de diversões foram filtrados</li>
  <li>As seguintes colunas foram selecionadas: ano, mês, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, cancelado</li>
</ul>
</td>
</tr>

<tr>
  <td>Desempenho pontual (bruto)</td>
  <td>
Registros de entradas de voo de avião e divergências dentro de Estados Unidos de outubro de 2011.
<p></p>
<b>Uso:</b> Prever atrasos de voo. 
<p></p>
<b>Pesquisa relacionada:</b> Do departamento dos EUA. <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>de transporte.
  </td>
</tr>

<tr>
  <td>Dados de incêndio de floresta</td>
  <td>
Contém dados meteorológicos, como índices de temperatura e umidade e velocidade do vento. Os dados são tirados de uma área do nordeste de Portugal, combinados com os registros de incêndios da floresta.
<p></p>
<b>Uso:</b> Essa é uma tarefa de regressão difícil, em que o objetivo é prever a área gravada de incêndios da floresta. 
<p></p>
<b>Pesquisa relacionada:</b> Cortez, P., & morais, A. (2008). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação
<p></p>
[Cortez e morais, 2007] P. Cortez e A. morais. Uma abordagem de mineração de dados para prever a floresta é acionada usando dados do aeródromo. Em J. Neves, M. F. Santos e J. Machado EDS., novas tendências em inteligência artificial, procedimentos da conferência 13 EPIA 2007-Português em inteligência artificial, dezembro, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponível em: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr>
  <td>Conjunto de pontos de crédito do alemão</td>
  <td>
O conjunto de dados de Statlog (cartão de crédito alemão) de UCI (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + alemão + credit + data</a>), usando o arquivo alemão. Data.
<p></p>
O conjunto de um classifica as pessoas, descritas por um conjunto de atributos, como baixos ou altos riscos de crédito. Cada exemplo representa uma pessoa. Há 20 recursos, numéricos e categóricos, e um rótulo binário (o valor de risco de crédito). As entradas de risco de crédito alto têm rótulo = 2, entradas de risco de crédito baixo têm rótulo = 1. O custo de classificar incorretamente um exemplo de baixo risco como alto é 1, enquanto o custo de classificar incorretamente um exemplo de alto risco como baixo é 5.
  </td>
</tr>

<tr>
  <td>Títulos de filmes do IMDB</td>
  <td>
O conjunto de dados contém informações sobre filmes que foram classificados em tweets do Twitter: ID de filme do IMDB, nome do filme, gênero e ano de produção. Há filmes 17K no conjunto de conjuntos. O conjunto de entrada foi introduzido no papel "S. Dooms, T. de Pessemier e L. Martens. MovieTweetings: um conjunto de uma classificação de filme coletado do Twitter. Workshop sobre computação crowdsourcing e humana para sistemas de recomendação, CrowdRec em RecSys 2013. "
  </td>
</tr>

<tr>
  <td>Dados da íris de duas classes</td>
  <td>
Esse é talvez o melhor banco de dados conhecido seja encontrado na literatura de reconhecimento do padrão. O conjunto de armazenamento é relativamente pequeno, contendo 50 exemplos de cada uma das medidas de pétala de três variedades de íris.
<p></p>
<b>Uso:</b> Prever o tipo de íris das medições.  
<p></p>
<b>Pesquisa relacionada:</b> Fisher, R.A. (1988). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação </td>
</tr>

<tr>
  <td>Tweets de filmes</td>
  <td>
O DataSet é uma versão estendida do conjunto de e de tweets de filmes. O conjunto de valores tem classificações 170 mil para filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa um tweet e é uma tupla: ID de usuário, ID de filme do IMDB, classificação, carimbo de data/hora, número de favoritos para este tweet e número de retweets desse tweet. O conjunto de teste foi disponibilizado por um. disse, S. Dooms, B. Loni e D. tikk para os sistemas de recomendação desafio 2014.
  </td>
</tr>

<tr>
  <td>Dados MPG para vários automóveis</td>
  <td>
Este DataSet é uma versão ligeiramente modificada do conjunto de conjuntos fornecido pela biblioteca StatLib do Carnegie Mellon University. O conjunto de entrada foi usado no exposição de associação estatística americana 1983.
<p></p>
Os dados listam o consumo de combustível para vários automóveis em milhas por galão. Ele também inclui informações como o número de cilindros, o deslocamento do motor, a potência, o peso total e a aceleração.
<p></p>
<b>Uso:</b> Prever a economia de combustível com base em três atributos discretos com valores diferentes e cinco atributos contínuos. 
<p></p>
<b>Pesquisa relacionada:</b> StatLib, Carnegie Mellon University, (1993). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação </td>
</tr>

<tr>
  <td>Conjunto de Pima Indians diabetes de classificação binária</td>
  <td>
Um subconjunto de dados do Instituto Nacional de diabetes e renais doenças. O conjunto de um foi filtrado para se concentrar em pacientes fêmea do patrimônio indiano Pima. Os dados incluem dados médicos, como os níveis glicose e insulin, bem como fatores de estilo de vida.
<p></p>
<b>Uso:</b> Preveja se o assunto tem diabetes (classificação binária). 
<p></p>
<b>Pesquisa relacionada:</b> Sigillito, V. (1990). UCI Machine Learning repositório <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml"</a>. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação </td>
</tr>

<tr>
  <td>Dados do cliente do restaurante</td>
  <td>
Um conjunto de metadados sobre clientes, incluindo dados demográficos e preferências.
<p></p>
<b>Uso:</b> Use este conjunto de DataSet, em combinação com os outros dois conjuntos de datarestaurante, para treinar e testar um sistema de recomendação. 
<p></p>
<b>Pesquisa relacionada:</b> Bache, K. e Lichman, M. (2013). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação.
  </td>
</tr>

<tr>
  <td>Dados de recurso do restaurante</td>
  <td>
Um conjunto de metadados sobre restaurantes e seus recursos, como tipo de alimentos, estilo de jantar e localização.
<p></p>
<b>Uso:</b> Use este conjunto de DataSet, em combinação com os outros dois conjuntos de datarestaurante, para treinar e testar um sistema de recomendação. 
<p></p>
<b>Pesquisa relacionada:</b> Bache, K. e Lichman, M. (2013). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação.
  </td>
</tr>

<tr>
  <td>Classificações de restaurante</td>
  <td>
Contém classificações dadas por usuários a restaurantes em uma escala de 0 a 2.
<p></p>
<b>Uso:</b> Use este conjunto de DataSet, em combinação com os outros dois conjuntos de datarestaurante, para treinar e testar um sistema de recomendação. 
<p></p>
<b>Pesquisa relacionada:</b> Bache, K. e Lichman, M. (2013). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação.
  </td>
</tr>

<tr>
  <td>Conjunto de recozimento de aço de várias classes</td>
  <td>
Esse conjunto de recursos contém uma série de registros de tentativas de recozimento de aço. Ele contém os atributos físicos (largura, espessura, tipo (bobina, planilha, etc.) dos tipos de aço resultantes.
<p></p>
<b>Uso:</b> Prever qualquer um dos dois atributos de classe numéricos; dureza ou força. Você também pode analisar correlações entre atributos.
<p></p>
As notas de aço seguem um conjunto padrão, definido por SAE e outras organizações. Você está procurando um ' nível ' específico (a variável de classe) e deseja entender os valores necessários. 
<p></p>
<b>Pesquisa relacionada:</b> Esterlina, D. & Buntine, W. (NA). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações e ciência da computação
<p></p>
Um guia útil para as notas de aço pode ser encontrado aqui: <a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Dados de teleescopo</td>
  <td>
Registro de intermitências de partícula gama de alta energia juntamente com ruídos de fundo, ambos simulados usando um processo Monte Carlo.
<p></p>
A intenção da simulação era melhorar a precisão dos teleescopos de atmosférica Cherenkov gama baseados em aterramento. Isso é feito usando métodos estatísticos para diferenciar entre o sinal desejado (Cherenkov de radiação hadrônicos) e o ruído de fundo (hadrônicos Hadron iniciado por raios raios cósmicos na atmosfera superior).
<p></p>
Os dados foram processados previamente para criar um cluster alongado com o eixo longo, orientado para o centro da câmera. As características dessa elipse (geralmente chamadas de parâmetros de Hillas) estão entre os parâmetros de imagem que podem ser usados para discriminação.
<p></p>
<b>Uso:</b> Prever se a imagem de um chá representa ruído de sinal ou de fundo.
<p></p>
<b>Observações:</b> A precisão da classificação simples não é significativa para esses dados, já que classificar um evento em segundo plano como sinal é pior do que classificar um evento de sinal como plano de fundo. Para comparação de classificadores diferentes, o gráfico ROC deve ser usado. A probabilidade de aceitar um evento de segundo plano como sinal deve estar abaixo de um dos seguintes limites: 0, 1, 0, 2, 0, 5, 0,1 ou 0,2.
<p></p>
Além disso, observe que o número de eventos em segundo plano (h, para hadrônicos Hadron) é subestimado. Em medidas reais, a classe h ou nois representa a maioria dos eventos. 
<p></p>
<b>Pesquisa relacionada:</b> Bock, R.K. (1995). UCI Machine Learning <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>do repositório. Irvine, CA: Universidade da Califórnia, escola de informações </td>
</tr>

<tr>
  <td>Conjunto de tempo</td>
  <td>
Observações de clima baseado em horário de hora de NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">dados mesclados de 201304 para 201310</a>).
<p></p>
Os dados meteorológicos abrangem observações feitas nas estações de clima do aeroporto, cobrindo o período de abril a 2013 de outubro. Antes de carregar para a versão clássica do Azure Machine Learning Studio, o conjunto de um foi processado da seguinte maneira:
<ul>
  <li>As IDs da estação do tempo foram mapeadas para as IDs correspondentes do Aeroporto</li>
  <li>As estações de clima não associadas a 70 aeroportos mais ocupados foram filtradas</li>
  <li>A coluna de data foi dividida em colunas de ano, mês e dia separadas</li>
  <li>As seguintes colunas foram selecionadas: Aeroportoid, ano, mês, dia, hora, fuso horário, SkyCondition, visibilidade, Climatype, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, altímetro</li>
</ul>
  </td>
</tr>

<tr>
  <td>Conjunto de DataSet do Wikipédia SP 500</td>
  <td>
Os dados são derivados da Wikipédia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) com base nos artigos de cada S & empresa P 500, armazenados como dados XML.
<p></p>
Antes de carregar para a versão clássica do Azure Machine Learning Studio, o conjunto de um foi processado da seguinte maneira:
<ul>
  <li>Extraia o conteúdo de texto para cada empresa específica</li>
  <li>Remover formatação wiki</li>
  <li>Remover caracteres não alfanuméricos</li>
  <li>Converter todo o texto em minúsculas</li>
  <li>Foram adicionadas categorias de empresa conhecidas</li>
</ul>
<p></p>
Observe que, para algumas empresas, não foi possível encontrar um artigo, portanto, o número de registros é menor que 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
O DataSet contém dados do cliente e indicações sobre sua resposta a uma campanha de mala direta. Cada linha representa um cliente. O conjunto de dados contém nove recursos sobre dados demográficos do usuário e comportamento passado e três colunas de rótulo (visita, conversão e gasto).  Visite é uma coluna binária que indica que um cliente visitou após a campanha de marketing. Conversão indica que um cliente comprou algo. Gastos é o valor que foi gasto.  O conjunto de dados foi disponibilizado por Kevin Hillstrom para análise de email MineThatData e desafio de data mining.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Recursos dos exemplos de teste no conjunto de RCV1-v2 Reuters News. O conjunto de 781K tem artigos de notícias em conjunto com suas IDs (primeira coluna do conjunto de um). Cada artigo é indexado, stopworded e originado. O conjunto de alterações foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Recursos de exemplos de treinamento no conjunto de notícias RCV1-v2 Reuters News. O conjunto de 23K tem artigos de notícias em conjunto com suas IDs (primeira coluna do conjunto de um). Cada artigo é indexado, stopworded e originado. O conjunto de alterações foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Conjunto de dados da competição do KDD Cup 1999 e das ferramentas de Data Mining (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99. html</a>).
<p></p>
O conjunto de testes foi baixado e armazenado no armazenamento de BLOBs do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection. csv</a>) e inclui conjuntos de testes de treinamento e de teste. O conjunto de registros de treinamento tem aproximadamente 126 mil linhas e 43 colunas, incluindo os rótulos. Três colunas fazem parte das informações do rótulo e 40 colunas, que consistem em recursos numéricos e de cadeia de caracteres/categóricos, estão disponíveis para treinar o modelo. Os dados de teste têm aproximadamente 22,5 exemplos de teste com as mesmas colunas de 43 que os dados de treinamento.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Atribuições de tópico para artigos de notícias no conjunto de RCV1-v2 Reuters News. Um artigo de notícias pode ser atribuído a vários tópicos. O formato de cada linha é "&lt;nome do tópico&gt; &lt;ID do documento&gt; 1". O conjunto de conteúdo contém atribuições de tópico 2.6 M. O conjunto de alterações foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Esses dados vêm do desafio de avaliação de desempenho do KDD Cup 2010 Student (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">avaliação de desempenho do aluno</a>). Os dados usados são o conjunto de treinamento Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Conjunto de dados de desafio do desafio do KDD Cup 2010 Education Data Mining. Encontre-o em <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads. jsp</a>.
<p></p>
O conjunto de registros foi baixado e armazenado no armazenamento de BLOBs do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance. txt</a>) e contém arquivos de log de um sistema de tutorial de estudante. Os recursos fornecidos incluem a ID do problema e sua breve descrição, ID do estudante, carimbo de data/hora e quantas tentativas o aluno fez antes de resolver o problema da maneira certa. O conjunto de recursos original tem registros M 8.9; Este conjunto de registros foi reduzido para as primeiras 100 mil linhas. O conjunto de data tem 23 colunas separadas por tabulação de vários tipos: Numeric, categórico e timestamp.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Início rápido seus experimentos com exemplos](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
