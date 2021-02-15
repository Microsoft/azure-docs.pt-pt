---
title: 'Estúdio ML (clássico): Use os conjuntos de dados da amostra - Azure'
description: Descrições dos conjuntos de dados utilizados em modelos de amostra incluídos no Machine Learning Studio (clássico). Pode utilizar estes conjuntos de dados de amostra para as suas experiências.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 8a848f6589902c2eabf5b4e14a4d000dc4ed081e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518441"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>Utilize os conjuntos de dados da amostra no Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


[top]: #machine-learning-sample-datasets

Quando cria um novo espaço de trabalho no Azure Machine Learning Studio (clássico), uma série de conjuntos de dados e experiências de amostra são incluídos por padrão. Muitos destes conjuntos de dados de amostra são utilizados pelos modelos de amostragem na [Galeria Azure AI](https://gallery.azure.ai/). Outros são incluídos como exemplos de vários tipos de dados normalmente usados na aprendizagem automática.

Alguns destes conjuntos de dados estão disponíveis no armazenamento da Azure Blob. Para estes conjuntos de dados, a tabela seguinte fornece uma ligação direta. Pode utilizar estes conjuntos de dados nas suas experiências utilizando o módulo [De Dados de Importação.][import-data]

Os restantes conjuntos de dados da amostra estão disponíveis no seu espaço de trabalho em **Conjuntos de Dados Guardados**. Você pode encontrar isso na paleta de módulos à esquerda da tela de experimentação no Machine Learning Studio (clássico).
Pode utilizar qualquer um destes conjuntos de dados na sua própria experiência arrastando-o para a sua tela de experimentação.

## <a name="datasets"></a>Conjuntos de dados

<table>

<tr>
  <th>Nome do conjunto de dados</th>
  <th>Descrição do conjunto de dados</th>
</tr>

<tr>
  <td>Conjunto de dados de classificação binária de rendimento do rendimento do rendimento de adultos</td>
  <td>
Um subconjunto da base de dados do Censos de 1994, utilizando adultos que trabalham com mais de 16 anos com um índice de rendimento ajustado de > 100.
<p></p>
<b>Utilização:</b> Classificar as pessoas que usam a demografia para prever se uma pessoa ganha mais de 50 mil por ano.
<p></p>
<b>Investigação Relacionada:</b> Kohavi, R., Becker, B., (1996). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação </td>
</tr>

<tr>
  <td>Conjunto de dados de códigos de aeroporto</td>
  <td>
Códigos do aeroporto dos E.U.A.
<p></p>
Este conjunto de dados contém uma linha para cada aeroporto dos EUA, fornecendo o número de identificação do aeroporto e o nome, juntamente com a cidade e o estado da localização.
  </td>
</tr>

<tr>
  <td>Dados sobre os preços do automóvel (Raw)</td>
  <td>
Informação sobre automóveis por marca e modelo, incluindo o preço, características como o número de cilindros e MPG, bem como uma pontuação de risco de seguro.
<p></p>
A pontuação de risco está inicialmente associada ao preço automóvel. É então ajustado para o risco real num processo conhecido pelos atuadores como símbolo. Um valor de +3 indica que o automóvel é arriscado, e um valor de -3 que provavelmente é seguro.
<p></p>
<b>Utilização:</b> Prever a pontuação de risco por características, utilizando a regressão ou classificação multivariada. 
<p></p>
<b>Investigação Relacionada:</b> Schlimmer, J.C. (1987). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação </td>
</tr>

<tr>
  <td>Conjunto de dados UCI de Aluguer de Bicicletas</td>
  <td>
Conjunto de dados de aluguer de bicicletas UCI que é baseado em dados reais da empresa Capital Bikeshare que mantém uma rede de aluguer de bicicletas em Washington DC.
<p></p>
O conjunto de dados tem uma linha para cada hora de cada dia em 2011 e 2012, num total de 17.379 linhas. A gama de alugueres de bicicletas de hora em hora é de 1 a 977.

  </td>
</tr>

<tr>
  <td>Imagem de Bill Gates RGB</td>
  <td>
Arquivo de imagem disponível ao público convertido em dados CSV.
<p></p>
O código para converter a imagem é fornecido na quantização de cor utilizando a página de detalhe do modelo <strong>de clustering K-Means.</strong>
  </td>
</tr>

<tr>
  <td>Dados de dádiva de sangue</td>
  <td>
Um subconjunto de dados da base de dados de dadores de sangue do Centro de Serviços de Transfusão de Sangue de Hsin-Chu City, Taiwan.
<p></p>
Os dados dos dadores incluem os meses desde a última doação), e a frequência, ou o número total de doações, tempo desde a última doação, e quantidade de sangue doado.
<p></p>
<b>Utilização:</b> O objetivo é prever, através da classificação, se o dador doou sangue em março de 2007, onde 1 indica um dador durante o período-alvo, e 0 um não dador. 
<p></p>
<b>Investigação Relacionada:</b> Yeh, I.C., (2008). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação
<p></p>
Yeh, I-Cheng, Yang, King-Jang, e Ting, Tao-Ming, "Descoberta de conhecimento no modelo RFM usando a sequência de Bernoulli, "Expert Systems with Applications, 2008, <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Dados do cancro da mama</td>
  <td>
Um dos três conjuntos de dados relacionados com o cancro fornecidos pelo Instituto de Oncologia que aparece frequentemente na literatura de aprendizagem automática. Combina informação de diagnóstico com características da análise laboratorial de cerca de 300 amostras de tecido.
<p></p>
<b>Utilização:</b> Classificar o tipo de cancro, com base em 9 atributos, alguns dos quais lineares e alguns são categóricos. 
<p></p>
<b>Investigação Relacionada:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação </td>
</tr>

<tr>
  <td>Características do cancro da mama <td>
O conjunto de dados contém informações para regiões suspeitas de 102K (candidatos) de imagens de raio-X, cada uma descrita por 117 funcionalidades. As funcionalidades são proprietárias e o seu significado não é revelado pelos criadores do conjunto de dados (Siemens Healthcare). 
  </td>
</tr>

<tr>
  <td>Informação sobre o Cancro da Mama</td>
  <td>
O conjunto de dados contém informações adicionais para cada região suspeita de imagem de raio-X. Cada exemplo fornece informações (por exemplo, etiqueta, ID do paciente, coordenadas de patch em relação a toda a imagem) sobre o número de linha correspondente no conjunto de dados do Cancro da Mama. Cada paciente tem vários exemplos. Para os doentes que têm cancro, alguns exemplos são positivos e alguns negativos. Para os doentes que não têm cancro, todos os exemplos são negativos. O conjunto de dados tem exemplos de 102K. O conjunto de dados é tendencioso, 0,6% dos pontos são positivos, os restantes são negativos. O conjunto de dados foi disponibilizado pela Siemens Healthcare.
  </td>
</tr>

<tr>
  <td>Rótulos de Appetency CRM compartilhados</td>
  <td>
Etiquetas do desafio de previsão da relação com o cliente KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">(orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>Etiquetas CRM Churn Compartilhadas</td>
  <td>
Etiquetas do desafio de previsão da relação com o cliente KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">(orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>Conjunto de dados crm compartilhado</td>
  <td>
Estes dados provêm do desafio de previsão da relação com o cliente da KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip"> (orange_small_train.data.zip). </a>
<p></p>
O conjunto de dados contém 50 mil clientes da empresa francesa de telecomunicações Orange. Cada cliente tem 230 funcionalidades anonimizadas, 190 das quais numéricas e 40 são categóricas. As características são muito escassas.
  </td>
</tr>

<tr>
  <td>Etiquetas de upselling CRM partilhadas</td>
  <td>
Etiquetas do desafio de previsão da relação com o cliente KDD Cup 2009<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">(orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Energy-Efficiency Dados de Regressão</td>
  <td>
Uma coleção de perfis de energia simulados, baseados em 12 formas diferentes de construção. Os edifícios são diferenciados por oito características. Isto inclui área de envidraçamento, distribuição de área de vidro, e orientação.
<p></p>
<b>Utilização:</b> Utilize a regressão ou a classificação para prever a classificação de eficiência energética baseada como uma de duas respostas reais valorizadas. Para a classificação multi-classes, é em torno da variável de resposta para o número mais próximo. 
<p></p>
<b>Investigação Relacionada:</b> Xifara, A. & Tsanas, A. (2012). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação </td>
</tr>

<tr>
  <td>Dados de atrasos de voo</td>
  <td>
Dados de desempenho de voo de passageiros retirados da recolha de dados tranStats do Departamento de Transportes dos EUA<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">(On-Time).</a>
<p></p>
O conjunto de dados cobre o período de tempo April-October 2013. Antes de ser enviado para o Azure Machine Learning Studio (clássico), o conjunto de dados foi processado da seguinte forma:
<ul>
  <li>O conjunto de dados foi filtrado para cobrir apenas os 70 aeroportos mais movimentados dos EUA</li>
  <li>Voos cancelados foram rotulados como atrasados por mais de 15 minutos</li>
  <li>Voos desviados foram filtrados</li>
  <li>Foram selecionadas as seguintes colunas: Ano, Mês, DiaofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Cancelado</li>
</ul>
</td>
</tr>

<tr>
  <td>Desempenho do voo a tempo (Raw)</td>
  <td>
Registos de chegadas e partidas de aviões nos Estados Unidos a partir de outubro de 2011.
<p></p>
<b>Utilização:</b> Prever atrasos de voo. 
<p></p>
<b>Investigação Relacionada:</b> Do Departamento de Transportes dos <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a> EUA.
  </td>
</tr>

<tr>
  <td>Dados dos incêndios florestais</td>
  <td>
Contém dados meteorológicos, como índices de temperatura e humidade e velocidade do vento. Os dados são recolhidos de uma zona do nordeste de Portugal, aliada a registos de incêndios florestais.
<p></p>
<b>Utilização:</b> Trata-se de uma tarefa de regressão difícil, onde o objetivo é prever a área ardida dos incêndios florestais. 
<p></p>
<b>Investigação Relacionada:</b> Cortez, P., & Morais, A. (2008). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação
<p></p>
[Cortez e Morais, 2007] P. Cortez e A. Morais. Uma abordagem de mineração de dados para prever incêndios florestais usando dados meteorológicos. Em J. Neves, M.F. Santos e J. Machado Eds., Novas Tendências em Inteligência Artificial, Procedimentos da 13ª EPIA 2007 - Conferência Portuguesa de Inteligência Artificial, dezembro, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponível em: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a> .
  </td>
</tr>

<tr>
  <td>Conjunto de dados do Cartão de Crédito Alemão UCI</td>
  <td>
O conjunto de dados da UCI Statlog (Cartão de Crédito Alemão)<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">(Statlog+German+Credit+Data),</a>utilizando o ficheiro german.data.
<p></p>
O conjunto de dados classifica as pessoas, descritas por um conjunto de atributos, como riscos de crédito baixos ou elevados. Cada exemplo representa uma pessoa. Existem 20 características, tanto numéricas como categóricas, e uma etiqueta binária (o valor do risco de crédito). As entradas de alto risco de crédito têm etiqueta = 2, entradas de baixo risco de crédito têm etiqueta = 1. O custo de classificar mal um exemplo de baixo risco como elevado é 1, enquanto o custo de classificação errada de um exemplo de alto risco como baixo é 5.
  </td>
</tr>

<tr>
  <td>Títulos de filme do IMDB</td>
  <td>
O conjunto de dados contém informações sobre filmes que foram classificados em tweets do Twitter: ID filme IMDB, nome do filme, género e ano de produção. Há filmes de 17K no conjunto de dados. O conjunto de dados foi introduzido no artigo "S. Dooms, T. De Pessemier e L. Martens. MovieTweetings: um conjunto de dados de classificação de filme recolhidos do Twitter. Workshop de Crowdsourcing e Computação Humana para Sistemas Recomendadores, CrowdRec na RecSys 2013."
  </td>
</tr>

<tr>
  <td>Iris dois dados de classe</td>
  <td>
Esta é talvez a base de dados mais conhecida da literatura de reconhecimento de padrões. O conjunto de dados é relativamente pequeno, contendo 50 exemplos cada uma das medições de pétalas de três variedades de íris.
<p></p>
<b>Utilização:</b> Prever o tipo de íris a partir das medições.  
<p></p>
<b>Investigação Relacionada:</b> Fisher, R.A. (1988). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação </td>
</tr>

<tr>
  <td>Tweets de cinema</td>
  <td>
O conjunto de dados é uma versão estendida do conjunto de dados do Movie Tweetings. O conjunto de dados tem classificações de 170K para filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa um tweet e é um tuple: ID do utilizador, ID de filme IMDB, classificação, timetamp, número de favoritos para este tweet, e número de retweets deste tweet. O conjunto de dados foi disponibilizado por A. Said, S. Dooms, B. Loni e D. Tikk para o Recommender Systems Challenge 2014.
  </td>
</tr>

<tr>
  <td>Dados do MPG para vários automóveis</td>
  <td>
Este conjunto de dados é uma versão ligeiramente modificada do conjunto de dados fornecido pela biblioteca StatLib da Universidade Carnegie Mellon. O conjunto de dados foi usado na Exposição da Associação Estatística Americana de 1983.
<p></p>
Os dados listam o consumo de combustível para vários automóveis em milhas por galão. Inclui também informações como o número de cilindros, deslocação do motor, potência, peso total e aceleração.
<p></p>
<b>Utilização:</b> Prever a economia de combustível com base em três atributos discretos multivalorizados e cinco atributos contínuos. 
<p></p>
<b>Investigação Relacionada:</b> StatLib, Universidade Carnegie Mellon, (1993). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação </td>
</tr>

<tr>
  <td>Conjunto de dados de classificação binária de diabetes pima indianos</td>
  <td>
Um subconjunto de dados do Instituto Nacional de Diabetes e Doenças Digestivas e Renais. O conjunto de dados foi filtrado para se concentrar em pacientes femininos da herança indiana de Pima. Os dados incluem dados médicos, como os níveis de glicose e insulina, bem como fatores de estilo de vida.
<p></p>
<b>Utilização:</b> Prever se o sujeito tem diabetes (classificação binária). 
<p></p>
<b>Investigação Relacionada:</b> Sigillito, V. (1990). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml "</a>. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação </td>
</tr>

<tr>
  <td>Dados do cliente do restaurante</td>
  <td>
Um conjunto de metadados sobre clientes, incluindo demografia e preferências.
<p></p>
<b>Utilização:</b> Utilize este conjunto de dados, em combinação com os outros dois conjuntos de dados do restaurante, para treinar e testar um sistema de recomendadores. 
<p></p>
<b>Investigação Relacionada:</b> Bache, K. e Lichman, M. (2013). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação.
  </td>
</tr>

<tr>
  <td>Dados de recursos de restaurante</td>
  <td>
Um conjunto de metadados sobre restaurantes e suas características, como tipo de comida, estilo de jantar e localização.
<p></p>
<b>Utilização:</b> Utilize este conjunto de dados, em combinação com os outros dois conjuntos de dados do restaurante, para treinar e testar um sistema de recomendadores. 
<p></p>
<b>Investigação Relacionada:</b> Bache, K. e Lichman, M. (2013). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação.
  </td>
</tr>

<tr>
  <td>Classificações de restaurantes</td>
  <td>
Contém classificações dadas pelos utilizadores a restaurantes numa escala de 0 a 2.
<p></p>
<b>Utilização:</b> Utilize este conjunto de dados, em combinação com os outros dois conjuntos de dados do restaurante, para treinar e testar um sistema de recomendadores. 
<p></p>
<b>Investigação Relacionada:</b> Bache, K. e Lichman, M. (2013). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação.
  </td>
</tr>

<tr>
  <td>Conjunto de dados multi-classes de 10 000 de aço</td>
  <td>
Este conjunto de dados contém uma série de registos de ensaios de reciclagem de aço. Contém os atributos físicos (largura, espessura, tipo (bobina, folha, etc.) dos tipos de aço resultantes.
<p></p>
<b>Utilização:</b> Prever qualquer um dos dois atributos da classe numérica; dureza ou força. Também pode analisar correlações entre atributos.
<p></p>
As notas de aço seguem um padrão definido, definido pela SAE e outras organizações. Procura uma 'nota' específica (a variável de classe) e quer entender os valores necessários. 
<p></p>
<b>Investigação Relacionada:</b> Sterling, D. & Buntine, W. (NA). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação e Ciências da Computação
<p></p>
Um guia útil para as qualidades de aço pode ser encontrado aqui: <a href="https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf">https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Dados do telescópio</td>
  <td>
Registo de explosões de partículas gama de alta energia juntamente com ruído de fundo, ambos simulados usando um processo de Monte Carlo.
<p></p>
A intenção da simulação era melhorar a precisão dos telescópios gama cherenkov atmosféricos terrestres. Isto é feito usando métodos estatísticos para diferenciar entre o sinal desejado (duches de radiação Cherenkov) e o ruído de fundo (chuveiros hadrónicos iniciados por raios cósmicos na atmosfera superior).
<p></p>
Os dados foram pré-processados para criar um cluster alongado com o longo eixo é orientado para o centro da câmara. As características desta elipse (muitas vezes chamadas de parâmetros Hillas) estão entre os parâmetros de imagem que podem ser usados para a discriminação.
<p></p>
<b>Utilização:</b> Preveja se a imagem de um chuveiro representa sinal ou ruído de fundo.
<p></p>
<b>Notas:</b> A precisão simples da classificação não faz sentido para estes dados, uma vez que classificar um evento de fundo como sinal é pior do que classificar um evento de sinal como pano de fundo. Para comparar diferentes classificadores, o gráfico ROC deve ser utilizado. A probabilidade de aceitar um evento de fundo como sinal deve ser inferior a um dos seguintes limiares: 0.01, 0.02, 0.05, 0.1 ou 0.2.
<p></p>
Além disso, note que o número de eventos de fundo (h, para chuveiros hadrónicos) é subestimado. Em medições reais, a classe h ou ruído representa a maioria dos eventos. 
<p></p>
<b>Investigação Relacionada:</b> Bock, R.K. (1995). Repositório de Aprendizagem automática da <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> UCI. Irvine, CA: Universidade da Califórnia, Escola de Informação </td>
</tr>

<tr>
  <td>Conjunto de dados meteorológicos</td>
  <td>
Observações meteorológicas por hora da NOAA<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">(dados fundidos de 201304 a 201310).</a>
<p></p>
Os dados meteorológicos cobrem observações feitas a partir de estações meteorológicas do aeroporto, abrangendo o período April-October 2013. Antes de ser enviado para o Azure Machine Learning Studio (clássico), o conjunto de dados foi processado da seguinte forma:
<ul>
  <li>Os IDs da estação meteorológica foram mapeados para os iDs do aeroporto correspondentes</li>
  <li>Estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram filtradas</li>
  <li>A coluna Date foi dividida em colunas separadas de ano, mês e dia</li>
  <li>Foram selecionadas as seguintes colunas: AirportID, Ano, Mês, Dia, Tempo, TimeZone, SkyCondition, Visibilidade, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, Hourciply, Alprettime, Alpretertime, Alpretertime,</li>
</ul>
  </td>
</tr>

<tr>
  <td>Conjunto de dados Wikipedia SP 500</td>
  <td>
Os dados são derivados da Wikipédia ( <a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a> ) com base em artigos de cada empresa S&P 500, armazenados como dados XML.
<p></p>
Antes de ser enviado para o Azure Machine Learning Studio (clássico), o conjunto de dados foi processado da seguinte forma:
<ul>
  <li>Extrair conteúdo de texto para cada empresa específica</li>
  <li>Remover formatação wiki</li>
  <li>Remover caracteres não alfanuméricos</li>
  <li>Converter todo o texto em minúsculas</li>
  <li>Categorias conhecidas da empresa foram adicionadas</li>
</ul>
<p></p>
Note-se que para algumas empresas não foi possível encontrar um artigo, pelo que o número de registos é inferior a 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
O conjunto de dados contém dados do cliente e indicações sobre a sua resposta a uma campanha de correio eletrónico direto. Cada linha representa um cliente. O conjunto de dados contém nove funcionalidades sobre a demografia do utilizador e o comportamento passado, e três colunas de etiquetas (visita, conversão e gasto).  A visita é uma coluna binária que indica que um cliente visitou após a campanha de marketing. A conversão indica que um cliente comprou algo. Gastar é o montante que foi gasto.  O conjunto de dados foi disponibilizado por Kevin Hillstrom para MineThatData E-Mail Analytics e Data Mining Challenge.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Características dos exemplos de teste no conjunto de dados de notícias RCV1-V2 Reuters. O conjunto de dados tem artigos de notícias 781K juntamente com os seus IDs (primeira coluna do conjunto de dados). Cada artigo é tokenizado, stopworded, e stemmed. O conjunto de dados foi disponibilizado por David. D. O Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Características dos exemplos de treino no conjunto de dados de notícias RCV1-V2 Reuters. O conjunto de dados tem artigos de notícias 23K juntamente com os seus IDs (primeira coluna do conjunto de dados). Cada artigo é tokenizado, stopworded, e stemmed. O conjunto de dados foi disponibilizado por David. D. O Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Dataset da KDD Cup 1999 Knowledge Discovery and Data Mining Tools Competition<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html"> (kddcup99.html</a>).
<p></p>
O conjunto de dados foi descarregado e armazenado no armazenamento de Azure Blob<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv"> (network_intrusion_detection.csv</a>) e inclui conjuntos de dados de treino e teste. O conjunto de dados de formação tem aproximadamente 126K linhas e 43 colunas, incluindo as etiquetas. Três colunas fazem parte da informação do rótulo, e 40 colunas, compostas por características numéricas e de cordas/categóricas, estão disponíveis para treinar o modelo. Os dados do teste têm aproximadamente 22,5K exemplos de teste com as mesmas 43 colunas que nos dados de treino.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Atribuições de tópicos para artigos noticiosos no conjunto de dados de notícias RCV1-V2 Reuters. Um artigo de notícias pode ser atribuído a vários tópicos. O formato de cada linha é " &lt; documento de nome tópico &gt; &lt; &gt; id 1". O conjunto de dados contém atribuições de tópicos de 2,6M. O conjunto de dados foi disponibilizado por David. D. O Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Estes dados provêm do desafio de avaliação do desempenho dos alunos da KDD Cup 2010<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">(avaliação do desempenho dos alunos).</a> Os dados utilizados são o conjunto de treino Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Álgebra I 2008-2009. Challenge dataset da KDD Cup 2010 Educational Data Mining Challenge. Encontre-o em <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp.</a>
<p></p>
O conjunto de dados foi descarregado e armazenado no armazenamento de Azure Blob<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt"> (student_performance.txt</a>) e contém ficheiros de registo de um sistema de tutoria de estudantes. As funcionalidades fornecidas incluem identificação de problemas e sua breve descrição, identificação do aluno, timetamp, e quantas tentativas o aluno fez antes de resolver o problema da maneira correta. O conjunto de dados original tem registos de 8,9M; este conjunto de dados foi reduzido para as primeiras linhas de 100K. O conjunto de dados tem 23 colunas separadas por separados de vários tipos: numérica, categórica e horatamp.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Inicie as suas experiências com exemplos](sample-experiments.md)

<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data