---
title: Utilizar os conjuntos de dados de exemplo
titleSuffix: Azure Machine Learning Studio
description: Descrições dos conjuntos de dados utilizadas em modelos de exemplo incluídos no Machine Learning Studio. Pode utilizar estes conjuntos de dados de exemplo para suas experimentações.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 22b3480093b85507123cab886340713747433928
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818362"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Utilizar os conjuntos de dados de exemplo no Azure Machine Learning Studio
[top]: #machine-learning-sample-datasets

Quando cria uma nova área de trabalho no Azure Machine Learning Studio, um número de conjuntos de dados de exemplo e experimentações está incluído por predefinição. Muitos destes conjuntos de dados de exemplo são utilizados pelos modelos de exemplo no [Galeria de IA do Azure](http://gallery.azure.ai/). Outras pessoas estão incluídas como exemplos de vários tipos de dados normalmente utilizados na machine learning.

Algumas destes conjuntos de dados estão disponíveis no armazenamento de Blobs do Azure. Para estes conjuntos de dados, a tabela seguinte fornece uma ligação direta. Pode utilizar estes conjuntos de dados em suas experiências utilizando o [importar dados] [ import-data] módulo.

O resto destes conjuntos de dados de exemplo estão disponíveis na sua área de trabalho sob **conjuntos de dados guardado**. Pode encontrar isto na paleta do módulo para a esquerda da tela de experimentação no Machine Learning Studio.
Pode utilizar qualquer um destes conjuntos de dados na sua própria experiência ao arrastá-la à tela de experimentação.




<table>

<tr>
  <th>Nome do conjunto de dados</th>
  <th>Descrição do conjunto de dados</th>
</tr>

<tr>
  <td>Conjunto de dados de classificação binária de receitas de censo para adultos</td>
  <td>
Um subconjunto de 1994 recenseamento banco de dados, usando os adultos de trabalhar mais de 16 com um índice de renda ajustada de > 100.
<p></p>
<b>Utilização:</b> Classificar as pessoas que utilizam dados demográficos para prever se uma pessoa recebe mais de 50 mil por ano.
<p></p>
<b>Pesquisas relacionadas:</b> Kohavi, R., Becker, B., (1996). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação </td>
</tr>

<tr>
  <td>O conjunto de dados do aeroporto códigos</td>
  <td>
Códigos de aeroporto dos EUA.
<p></p>
Este conjunto de dados contém uma linha para cada aeroporto dos EUA, fornecendo o número de identificação do aeroporto e o nome, juntamente com a cidade de localização e o estado.
  </td>
</tr>

<tr>
  <td>Dados do preço automóvel (bruto)</td>
  <td>
Informações sobre automóveis por marca e modelo, incluindo o preço, funcionalidades, tais como o número de cilindros e MPG, bem como uma classificação de risco de seguros.
<p></p>
A classificação de risco está inicialmente associada a preço automática. Em seguida, ele será ajustado por risco real num processo conhecido para atuários como symboling. Um valor de +3 indica que o automática é arriscada e um valor de -3 que ele é provavelmente seguro.
<p></p>
<b>Utilização:</b> Prever a classificação de risco por recursos, com a regressão ou classificação multivariate. 
<p></p>
<b>Pesquisas relacionadas:</b> Schlimmer, J.C. (1987). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação </td>
</tr>

<tr>
  <td>Conjunto de dados de UCI de aluguer de bicicletas</td>
  <td>
Aluguer de bicicletas de UCI conjunto de dados com base em dados reais anónimos da empresa de Capital Bikeshare que mantém uma rede de aluguer de bicicletas em Washington DC.
<p></p>
O conjunto de dados tem uma linha para cada hora de cada dia em 2011 e 2012, para um total de 17,379 linhas. O intervalo de hora a hora de bicicletas alugadas é entre 1 e 977.

  </td>
</tr>

<tr>
  <td>Bill Gates RGB imagem</td>
  <td>
Ficheiro de imagem disponíveis publicamente convertido para dados do CSV.
<p></p>
O código para converter a imagem é fornecido no <strong>cor quantização usando o clustering de K-Means</strong> página de detalhes do modelo.
  </td>
</tr>

<tr>
  <td>Dados de doação de sangue</td>
  <td>
Um subconjunto dos dados da base de dados de Doador sangue do Centro de Hsin Chu de serviço a sangue Transfusion cidade, Taiwan.
<p></p>
Dados de Doador incluem os meses desde a última doação) e frequência ou o número total de doações, tempo desde a última doação e a quantidade de sangue doou.
<p></p>
<b>Utilização:</b> O objetivo é prever por meio de classificação, se o doador doou sangue em Março de 2007, onde 1 indica um doador durante o período de destino e 0 um não-doador. 
<p></p>
<b>Pesquisas relacionadas:</b> Yeh I.C., (2008). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação
<p></p>
Yeh, eu-Cheng, Yang, King-Jang e Ting, etiqueta-Ming de VPN, "descoberta de dados de conhecimento no modelo RFM usando a seqüência de Bernoulli,"especialista em sistemas com aplicativos, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Dados de câncer breast</td>
  <td>
Um dos três relacionados com o cancro conjuntos de dados fornecidos pelo Institute Oncology que aparece com frequência na literatura de aprendizado de máquina. Combina informações de diagnóstico com as funcionalidades de análise de laboratório de cerca de 300 exemplos de cicatrização.
<p></p>
<b>Utilização:</b> Classificar o tipo de cancro, com base em 9 atributos, alguns dos quais estão lineares e alguns são categóricos. 
<p></p>
<b>Pesquisas relacionadas:</b> O.L. Wohlberg, W.H., rua, W.N. e Mangasarian, (1995). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação </td>
</tr>

<tr>
  <td>Funcionalidades de Câncer breast <td>
O conjunto de dados contém informações para regiões suspeita de 102K (candidatos) das imagens de raio-x, cada descrito por 117 recursos. Os recursos são proprietários e seu significado não será revelado pelos criadores de conjunto de dados (Siemens cuidados de saúde). 
  </td>
</tr>

<tr>
  <td>Informações de Câncer breast</td>
  <td>
O conjunto de dados contém informações adicionais para cada região suspeita da imagem de raio-x. Cada exemplo fornece informações (por exemplo, etiqueta, pacientes ID, coordenadas de patch em relação a imagem inteira) sobre o número de linha correspondente no conjunto de dados Breast cancro funcionalidades. Cada paciente tem um número de exemplos. Para os pacientes que têm um cancro, alguns exemplos são positivos e algumas são negativas. Para os pacientes que não têm um cancro, todos os exemplos são negativos. O conjunto de dados tem 102K exemplos. O conjunto de dados é medido, 0,6% dos pontos são positivo, o restante é negativo. O conjunto de dados foi disponibilizado por Siemens cuidados de saúde.
  </td>
</tr>

<tr>
  <td>CRM Apetência etiquetas partilhadas</td>
  <td>
Etiquetas do desafio de predição de relação de cliente do concurso KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>Etiquetas de alterações a dados CRM partilhadas</td>
  <td>
Etiquetas do desafio de predição de relação de cliente do concurso KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>O conjunto de dados CRM partilhado</td>
  <td>
O desafio de predição de relação de cliente do concurso KDD Cup 2009 vêm os dados (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
O conjunto de dados contém 50 mil clientes da empresa de telecomunicações francês laranja. Cada cliente tem 230 recursos anónimos, 190 dos quais são numéricos e 40 são categóricos. Os recursos são muito dispersos.
  </td>
</tr>

<tr>
  <td>CRM Upselling etiquetas partilhadas</td>
  <td>
Etiquetas do desafio de predição de relação de cliente do concurso KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Dados de regressão de eficiência energética</td>
  <td>
Um conjunto de perfis de energia simulado, com base em 12 criar diferentes formas. Os prédios são diferenciados por funcionalidades de oito. Isto inclui glazing área, a distribuição da área de glazing e orientação.
<p></p>
<b>Utilização:</b> Utilize regressão ou classificação para prever a classificação de eficiência energética com base em como uma das duas respostas reais com valores. Para a classificação de Roc, é arredondar a variável de resposta para o número inteiro mais próximo. 
<p></p>
<b>Pesquisas relacionadas:</b> Xifara, A. & Tsanas, A. (2012). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação </td>
</tr>

<tr>
  <td>Voo atrasa a dados</td>
  <td>
Voo comercial no tempo de dados de desempenho obtidos a partir da recolha de dados de TranStats dos EUA Departamento de transportes (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">de forma atempada</a>).
<p></p>
O conjunto de dados abrange o período de tempo de Abril de Outubro de 2013. Antes de carregar para o Azure Machine Learning Studio, o conjunto de dados foi processado da seguinte forma:
<ul>
  <li>O conjunto de dados foi filtrado para abranger apenas os 70 aeroportos mais ocupados no território continental dos EUA</li>
  <li>Cancelado voos foram etiquetados como atrasado mais de 15 minutos</li>
  <li>Foram filtrados desviados voos</li>
  <li>Foram selecionadas as seguintes colunas: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>Desempenho de chegadas de voo (Raw)</td>
  <td>
Registos de entradas de voo do avião e departures dentro dos Estados Unidos de Outubro de 2011.
<p></p>
<b>Utilização:</b> Prever a atrasos de voos. 
<p></p>
<b>Pesquisas relacionadas:</b> Dos E.U.A. dodep. of Transportation <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time"> http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time </a>.
  </td>
</tr>

<tr>
  <td>Dados de incêndios florestais</td>
  <td>
Contém dados de informações sobre o clima, como índices de temperatura e humidade e velocidade do vento. Os dados está a ser utilizados de uma área de Nordeste de Portugal, combinado com os registos de floresta desencadeado.
<p></p>
<b>Utilização:</b> Esta é uma tarefa de regressão difícil, em que o objetivo é prever a área burned de floresta desencadeado. 
<p></p>
<b>Pesquisas relacionadas:</b> Cortez, P. & Morais, A. (2008). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação
<p></p>
[Cortez e Morais, 2007] P. Cortez e Morais de r. Uma abordagem de mineração de dados para prever com dados Meteorological floresta desencadeado. In J. Neves, M. F. Santos e J. Machado Eds., novas tendências em Inteligência Artificial, judiciais 2007 EPIA 13 - português conferência em Inteligência Artificial, Dezembro, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponível em: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf"> http://www.dsi.uminho.pt/~pcortez/fires.pdf </a>.
  </td>
</tr>

<tr>
  <td>Conjunto de dados alemão UCI de cartão de crédito</td>
  <td>
O conjunto de dados de UCI Statlog (cartão de crédito alemão) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + alemão + crédito + dados</a>), usando o arquivo german.data.
<p></p>
O conjunto de dados classifica as pessoas, descritas por um conjunto de atributos, como os riscos de crédito baixa ou alta. Cada exemplo representa uma pessoa. Há 20 recursos, numéricos e categóricos e uma etiqueta de binária (o valor de risco de crédito). Entradas de risco elevado crédito tem etiqueta = 2, entradas de risco de crédito baixa tem etiqueta = 1. O custo de misclassifying um exemplo de baixo risco tão elevado é 1, ao passo que o custo de misclassifying um exemplo de alto risco como baixa é 5.
  </td>
</tr>

<tr>
  <td>Títulos de filmes IMDB</td>
  <td>
O conjunto de dados contém informações sobre filmes que foram avaliadas nos tweets do Twitter: ID de filme IMDB, o nome do filme, o gênero e o ano de produção. Há 17 mil filmes no conjunto de dados. O conjunto de dados foi introduzido no documento "S. Dooms, T. Alemanha Pessemier e Martens L. MovieTweetings: um conjunto de dados de classificação de filme recolhido do Twitter. Workshop sobre Crowdsourcing e a computação humana para os sistemas de Recomendador, CrowdRec na RecSys 2013."
  </td>
</tr>

<tr>
  <td>Dados de classe de íris dois</td>
  <td>
Isto é, talvez, a base de dados mais conhecido para ser encontrados na literatura de reconhecimento do padrão. O conjunto de dados é relativamente pequeno, que contêm 50 exemplos de medidas de petal de três variedades de íris.
<p></p>
<b>Utilização:</b> Prever o tipo de íris de medidas.  
<p></p>
<b>Pesquisas relacionadas:</b> Fisher, R.A. (1988). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação </td>
</tr>

<tr>
  <td>Tweets do filme</td>
  <td>
O conjunto de dados é uma versão expandida do conjunto de dados do filme Tweetings. O conjunto de dados tem classificações de 170K de filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa um tweet e é uma cadeia de identificação: ID de utilizador, ID de filme IMDB, classificação, timestamp, numer dos favoritos para esta tweet e o número de retweets deste tweet. O conjunto de dados foi disponibilizado por disse A., S. Dooms, Loni B. e D. Tikk para sistemas de Recomendador desafio 2014.
  </td>
</tr>

<tr>
  <td>Dados MPG para vários automóveis</td>
  <td>
Este conjunto de dados é uma versão ligeiramente modificada do conjunto de dados fornecido pela biblioteca StatLib de Carnegie Mellon University. O conjunto de dados foi utilizado no 1983 American estatísticas associação Exposition.
<p></p>
Os dados de lista de consumo de combustível para vários automóveis em quilómetros por litro. Também inclui informações como o número de cilindros, deslocamento do motor, potência, peso total e aceleração.
<p></p>
<b>Utilização:</b> Prever a economia de combustível com base em atributos discretos de valor múltiplos três e cinco atributos contínuos. 
<p></p>
<b>Pesquisas relacionadas:</b> StatLib, Carnegie Mellon University, (1993). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação </td>
</tr>

<tr>
  <td>Conjunto de dados de classificação binária do Pima Indians Diabetes</td>
  <td>
Um subconjunto de dados do Instituto nacional de Diabetes e da base de dados Digestive e Kidney doenças. O conjunto de dados foi filtrado concentrar-se em female pacientes de herança de indianos Pima. Os dados incluem dados médicos como glicose e níveis de insulin, bem como os fatores de estilo de vida.
<p></p>
<b>Utilização:</b> Preveja se o requerente tem diabetes (classificação binária). 
<p></p>
<b>Pesquisas relacionadas:</b> Sigillito, V. (1990). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml"</a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação </td>
</tr>

<tr>
  <td>Dados de clientes do restaurante</td>
  <td>
Um conjunto de metadados sobre clientes, incluindo dados demográficos e preferências.
<p></p>
<b>Utilização:</b> Utilize este conjunto de dados, em combinação com os outros dois restaurante conjuntos de dados, para treinar e testar um sistema de recomendador. 
<p></p>
<b>Pesquisas relacionadas:</b> Bache, K. e Lichman, M. (2013). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação.
  </td>
</tr>

<tr>
  <td>Dados de funcionalidade do restaurante</td>
  <td>
Um conjunto de metadados sobre restaurantes e as respetivas funcionalidades, como o tipo de comida, estilo jantar e localização.
<p></p>
<b>Utilização:</b> Utilize este conjunto de dados, em combinação com os outros dois restaurante conjuntos de dados, para treinar e testar um sistema de recomendador. 
<p></p>
<b>Pesquisas relacionadas:</b> Bache, K. e Lichman, M. (2013). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação.
  </td>
</tr>

<tr>
  <td>Classificações de restaurante</td>
  <td>
Contém as classificações dadas por usuários para restaurantes numa escala de 0 a 2.
<p></p>
<b>Utilização:</b> Utilize este conjunto de dados, em combinação com os outros dois restaurante conjuntos de dados, para treinar e testar um sistema de recomendador. 
<p></p>
<b>Pesquisas relacionadas:</b> Bache, K. e Lichman, M. (2013). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação.
  </td>
</tr>

<tr>
  <td>Conjunto de dados de Roc aço Annealing</td>
  <td>
Este conjunto de dados contém uma série de registos a partir do acinzentado recozimento versões de avaliação. Contém os atributos físicos (largura, espessura, tipos de tipo (coil, folha, etc.) de resultante aço.
<p></p>
<b>Utilização:</b> Prever a qualquer um dos dois atributos numéricos de classe; hardness ou força. Também pode analisar correlações entre atributos.
<p></p>
As notas de aço siga um conjunto padrão, definido pela SAE e outras organizações. Está procurando uma específicas de "nível" (a variável de classe) e quiser compreender os valores necessários. 
<p></p>
<b>Pesquisas relacionadas:</b> Sterling, D. & Buntine, W. (NA). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola e ciência da computação
<p></p>
Um guia útil para acinzentado notas pode ser encontrado aqui: <a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Dados de telescope</td>
  <td>
Registo da partícula de gama alta de energia o aumento, juntamente com o barulho de fundo, ambos simulados através de um processo de Monte Carlo.
<p></p>
O objetivo da simulação era melhorar a precisão de telescopes atmospheric de gama Cherenkov baseado em zero. Isso é feito através de métodos estatísticos para diferenciar entre o sinal desejado (Cherenkov radiation showers) e o barulho de fundo (hadronic showers iniciadas pelo cosmic rays na atmosfera do canto superior).
<p></p>
Os dados terem sido previamente processados para criar um cluster elongated com a longa eixo está orientado para o Centro de câmara. As características dessa elipse (muitas vezes chamado de parâmetros de Hillas) estão entre os parâmetros de imagem que podem ser utilizados para discrimination.
<p></p>
<b>Utilização:</b> Prever se a imagem de um shower representa o ruído de sinal ou em segundo plano.
<p></p>
<b>Notas:</b> Precisão de classificação simples não é significativo para estes dados, desde classificar um evento em segundo plano, como o sinal é pior do que classificar um evento de sinal como em segundo plano. Para comparação de classificadores diferentes, deve ser utilizado o gráfico ROC. A probabilidade de aceitação de um evento de plano de fundo como sinal tem de ser abaixo de um dos seguintes limiares: 0,01, 0,02, 0,05, 0,1 ou 0,2.
<p></p>
Além disso, tenha em atenção que o número de eventos de segundo plano (h, para hadronic showers) é subestimado. Medidas reais, a classe de h ou ruído representa a maioria dos eventos. 
<p></p>
<b>Pesquisas relacionadas:</b> Bock, R.K. (1995). Repositório de aprendizagem UCI <a href="http://archive.ics.uci.edu/ml"> http://archive.ics.uci.edu/ml </a>. Irvine, CA: Universidade da Califórnia, de informações sobre a escola </td>
</tr>

<tr>
  <td>Conjunto de dados de Meteorologia</td>
  <td>
Por hora observações de com base em ' s land meteorologia da NOAA (<a href="http://az754797.vo.msecnd.net/data/WeatherDataset.csv">intercalar dados de 201304 para 201310</a>).
<p></p>
Os dados meteorológicos abrange observações feitas a partir de estações de Meteorologia do aeroporto, que abrange o período de tempo de Abril de Outubro de 2013. Antes de carregar para o Azure Machine Learning Studio, o conjunto de dados foi processado da seguinte forma:
<ul>
  <li>IDs de estação meteorológica foram mapeados para IDs de aeroporto correspondente</li>
  <li>Estações de Meteorologia não associadas os 70 aeroportos mais ocupados foram filtradas</li>
  <li>A coluna de data foi dividida em colunas separadas, ano, mês e dia</li>
  <li>Foram selecionadas as seguintes colunas: AirportID, ano, mês, dia, hora, fuso horário, SkyCondition, visibilidade, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 conjunto de dados</td>
  <td>
Dados são derivados da Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) com base nos artigos de cada empresa S & P 500, armazenadas como dados XML.
<p></p>
Antes de carregar para o Azure Machine Learning Studio, o conjunto de dados foi processado da seguinte forma:
<ul>
  <li>Extrair o conteúdo de texto para cada empresa específica</li>
  <li>Remover formatação do wiki</li>
  <li>Remover os carateres não alfanuméricos</li>
  <li>Converter todo o texto em minúsculas</li>
  <li>Categorias de conhecidas de empresas foram adicionadas</li>
</ul>
<p></p>
Tenha em atenção que, para algumas empresas um artigo não foi possível encontrar o número de registos é inferior a 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
O conjunto de dados contém dados dos clientes e indicações sobre a respetiva resposta para uma campanha de endereçamento direta. Cada linha representa um cliente. O conjunto de dados contém nove funcionalidades sobre dados demográficos de utilizador e comportamento pretérito e três colunas de etiqueta (visite, conversão e gastar).  Visite é uma coluna binary que indica que um cliente visitados após a campanha de marketing. Conversão indica adquiridos de um cliente algo. Gastos é a quantidade que foi gasto.  O conjunto de dados foi disponibilizado por Kevin Hillstrom para MineThatData email Analytics e Data Mining desafio.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funcionalidades de exemplos de teste no conjunto de dados de notícias da Reuters RCV1 V2. O conjunto de dados tem 781K artigos de notícias, juntamente com suas IDs (primeira coluna do conjunto de dados). Cada artigo é com o token, stopworded e surgiu com. O conjunto de dados foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funcionalidades de exemplos de treinamento no conjunto de dados de notícias da Reuters RCV1 V2. O conjunto de dados tem o K de 23 de artigos de notícias, juntamente com suas IDs (primeira coluna do conjunto de dados). Cada artigo é com o token, stopworded e surgiu com. O conjunto de dados foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Competição de ferramentas de conjunto de dados da deteção de dados de conhecimento do concurso KDD Cup 1999 e extração de dados (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
O conjunto de dados foi transferido e armazenado no armazenamento de Blobs do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) e inclui treinamento e teste de conjuntos de dados. O conjunto de dados de treinamento tem aproximadamente mil 126 linhas e colunas de 43, incluindo as etiquetas. Três colunas são parte das informações de etiqueta e 40 colunas, que consiste de funcionalidades numéricas e de cadeia de caracteres/categóricos, estão disponíveis para o modelo de formação. Os dados de teste tem aproximadamente 22,5 K exemplos com as mesmas 43 colunas como em dados de treinamento de teste.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Atribuições de tópico de artigos de notícias no conjunto de dados de notícias da Reuters RCV1 V2. Um artigo de notícias pode ser atribuído a vários tópicos. O formato de cada linha é "&lt;nome do tópico&gt; &lt;id do documento&gt; 1". O conjunto de dados contém as atribuições de tópico de 2,6 milhões. O conjunto de dados foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
O desafio de avaliação do desempenho de estudante do concurso KDD Cup 2010 vêm os dados (<a href="http://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">avaliação do desempenho dos alunos</a>). Os dados usados são o conjunto de treinamento de Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, r., Ritter, s., Gordon, G.J. e Koedinger, K.R. (2010). Algebra I 2008-2009. Conjunto de dados do desafio da KDD Cup 2010 educacionais Data Mining desafio. Encontrá-lo em <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
O conjunto de dados foi transferido e armazenado no armazenamento de Blobs do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) e contém ficheiros de registo de um estudante de ensino do sistema. Os recursos fornecidos incluem ID do problema e sua descrição breve, ID de estudante, timestamp e o número de tentativas o aluno feito antes de resolver o problema da forma correta. O conjunto de dados original tiver registos 8.9M; Este conjunto de dados tem sido a amostragem-a para baixo para as primeiras 100 mil linhas. O conjunto de dados tem 23 colunas separados por tabulações de vários tipos: numéricos, categóricos e timestamp.
  </td>
</tr>

</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
