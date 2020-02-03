---
title: Ciência de dados com o Spark no Azure HDInsight - Team Data Science Process
description: O Kit de ferramentas do Spark MLlib traz considerável de aprendizagem, capacidades de modelação ao ambiente distribuído do HDInsight.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 63148b99e65a5ccc49d54d4ae6c58adebc72c6d3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718519"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Descrição geral da ciência de dados com o Spark no Azure HDInsight

Este conjunto de tópicos mostra como utilizar o Spark do HDInsight para executar tarefas comuns de ciência de dados, tais como ingestão de dados, engenharia de funcionalidades, modelação e avaliação do modelo. Os dados utilizados são um exemplo do 2013 NYC táxis viagem e Europeia conjunto de dados. Modelos construídos incluem regressão logística e linear, florestas aleatórias e árvores de elevada gradação. Os tópicos também mostram como armazenar esses modelos no armazenamento de Blobs do Azure (WASB) e como proceder à sua classificação e avaliar seu desempenho de previsão. Tópicos mais avançados abordam como os modelos podem ser treinado varrimento de validação cruzada e de hyper-parâmetro a utilizar. Este tópico de descrição geral referencia também os tópicos que descrevem como configurar o cluster do Spark que precisa para concluir os passos nas instruções fornecidas.

## <a name="spark-and-mllib"></a>Spark e o MLlib
[Spark](https://spark.apache.org/) é um quadro de processamento paralelo de código aberto que suporta o processamento na memória para aumentar o desempenho de aplicações analíticas de big data. O motor de processamento do Spark foi concebido para velocidade, facilidade de utilização e análise sofisticadas. Recursos de computação distribuída de dentro da memória do Spark tornam uma boa opção para algoritmos iterativos, utilizada em cálculos de machine learning e do graph. [MLlib](https://spark.apache.org/mllib/) é a biblioteca de machine learning escalável da Spark que traz as capacidades de modelação algorítmica para este ambiente distribuído.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) é o Azure hospedado oferta de spark de código aberto. Também inclui suporte para **os cadernos Jupyter PySpark** no cluster Spark que podem executar consultas interativas Spark SQL para transformar, filtrar e visualizar dados armazenados em Blobs Azure (WASB). PySpark é a API do Python para o Spark. Os fragmentos de código que fornecem as soluções e mostram os gráficos relevantes para visualizar os dados aqui executados em blocos de notas do Jupyter instalados nos clusters do Spark. Os passos de modelação nos seguintes tópicos contêm código que mostra como treinar, avaliar, guardar e consumir cada tipo de modelo.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Configuração: Os clusters do Spark e o Jupyter notebooks
Passos de configuração e de código são fornecidos esta explicação passo a passo para utilizar um HDInsight Spark 1.6. Mas blocos de notas do Jupyter são fornecidos para os clusters do HDInsight Spark 1.6 e Spark 2.0. Uma descrição dos cadernos e links para eles são fornecidas no [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório GitHub que os contém. Além disso, o código aqui e em blocos de notas ligados é genérico e deve trabalhar em qualquer cluster do Spark. Se não estiver a utilizar o Spark do HDInsight, os passos de configuração e o gerenciamento de cluster podem ser ligeiramente diferentes do que é mostrado aqui. Por conveniência, aqui estão os links para os cadernos Jupyter para Spark 1.6 (a ser executado no núcleo pySpark do servidor Jupyter Notebook) e Spark 2.0 (a ser executado no núcleo pySpark3 do servidor Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Blocos de notas do Spark 1.6
Estes blocos de notas estão a ser executados no kernel do pySpark, do servidor de bloco de notas do Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Fornece informações sobre como realizar a exploração de dados, modelação e pontuação com vários algoritmos diferentes.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Inclui tópicos em #1 de cadernos, e desenvolvimento de modelos utilizando a finação e validação cruzada do hiperparâmetro.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Mostra como operacionalizar um modelo salvo usando Python em clusters HDInsight.

### <a name="spark-20-notebooks"></a>Blocos de notas do Spark 2.0
Estes blocos de notas estão a ser executados no kernel do pySpark3 do servidor de bloco de notas do Jupyter.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Este ficheiro fornece informações sobre como realizar a exploração de dados, modelação e pontuação em clusters Spark 2.0 utilizando a viagem de táxi de NYC e o conjunto de dados de tarifas descrito [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Este bloco de notas pode ser um ponto de partida para explorar rapidamente o código fornecidos para Spark 2.0. Para um bloco de notas mais detalhado analisa os dados de táxis de NYC, consulte o seguinte bloco de notas nesta lista. Consulte as notas que seguem esta lista que compara estes cadernos.
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Este ficheiro mostra como realizar a discussão de dados (Operações Spark SQL e dataframe), exploração, modelação e pontuação utilizando a viagem de táxi de NYC e o conjunto de dados de tarifas descrito [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Este ficheiro mostra como realizar a discussão de dados (Operações Spark SQL e dataframe), exploração, modelação e pontuação utilizando o conhecido conjunto de dados de partida da Companhia Aérea On-time de 2011 e 2012. Integrámos o conjunto de dados da companhia aérea com os dados meteorológicos do aeroporto (por exemplo, velocidade do vento, temperatura, altitude, etc.) antes da modelação, para que estas características meteorológicas possam ser incluídas no modelo.

<!-- -->

> [!NOTE]
> O conjunto de dados de companhia aérea foi adicionado para os blocos de notas do Spark 2.0 para melhor ilustrar a utilização de algoritmos de classificação. Consulte as seguintes ligações para informações sobre a companhia aérea no tempo de conjunto de dados de saída e o conjunto de dados de Meteorologia:
> 
> - Dados de partida a tempo das companhias aéreas: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Dados meteorológicos do aeroporto: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Os blocos de notas do Spark 2.0 nos NYC táxis e companhia aérea voo atraso-conjuntos de dados podem demorar 10 minutos ou mais para ser executada (dependendo do tamanho do cluster do HDI). O primeiro caderno da lista acima mostra muitos aspetos da exploração de dados, visualização e formação de modelos ML num caderno que leva menos tempo a ser executado com conjunto de dados de NYC amostrado, no qual os ficheiros de táxi e tarifas foram pré-associados: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Este caderno demora muito menos tempo a terminar (2-3 minutos) e pode ser um bom ponto de partida para explorar rapidamente o código que fornecemos para o Spark 2.0.

<!-- -->

Para obter orientações sobre a operacionalização de um modelo Spark 2.0 e o consumo de modelo para pontuação, consulte o [documento Spark 1.6 sobre o consumo,](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) por exemplo, delineando os passos necessários. Para utilizar este exemplo no Spark 2.0, substitua o ficheiro de código Python por [este ficheiro](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Pré-requisitos

Os procedimentos seguintes relacionados com Spark 1.6. Para a versão de Spark 2.0, utilize os blocos de notas descrito e associados a anteriormente.

1. Precisa de uma subscrição do Azure. Se ainda não tem um, consulte [o Get Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Precisa de um aglomerado spark 1.6 para completar esta passagem. Para criar um, consulte as instruções fornecidas em [Get started: create Apache Spark on Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). O tipo de cluster e a versão são especificados no menu **Select Cluster Type.**

![Configurar cluster](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Para um tópico que mostra como usar Scala em vez de Python para completar tarefas para um processo de ciência de dados de ponta a ponta, consulte a [Data Science usando Scala com Spark on Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Os dados de táxis de 2013 NYC
Os dados da viagem de táxis de NYC são cerca de 20 GB de arquivos de comprimido valores separados por vírgulas (CSV) (GB de ~ 48 descomprimido), que consiste em mais de 173 milhões de viagens individuais e os fares pago para cada viagem. Cada registo de viagem inclui a localização de recolha e de redução e tempo, hack anónimo (driver) número de licença e o número de medallion (id exclusivo de táxis). Os dados abrange todas as viagens no ano de 2013 e são fornecidos no seguintes dois conjuntos de dados para todos os meses:

1. Os ficheiros CSV 'trip_data' contém detalhes de viagem, como o número de passageiros, pegam e pontos de redução, colocar a duração e comprimento de viagem. Aqui estão alguns exemplos de registros:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Os ficheiros CSV 'trip_fare' contenham detalhes de Europeia pago para cada viagem, como o tipo de pagamento, a quantidade de Europeia, sobretaxa e impostos, dicas e pedágio e o valor total pago. Aqui estão alguns exemplos de registros:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Retirámos uma amostra de 0,1% destes ficheiros e juntamo-nos à viagem\_dados e viagem\_ficheiros CVS de tarifa num único conjunto de dados para usar como conjunto de dados de entrada para esta passagem. A chave única para se juntar aos dados da viagem\_e viagem\_tarifa é composta pelos campos: medalhão, licença de\_e recolha\_data. Cada registro do conjunto de dados contém os seguintes atributos representa uma viagem de táxis de NYC:

| Campo | Breve Descrição |
| --- | --- |
| Medallion |Medallion de táxis anónimos (id de táxis exclusivo) |
| hack_license |Número de licença de carro Hackney anónimo |
| vendor_id |Id do fornecedor de táxis |
| rate_code |Taxa de táxis de NYC de Europeia |
| store_and_fwd_flag |Store e o sinalizador de encaminhamento |
| pickup_datetime |Escolher Data e hora |
| dropoff_datetime |Redução de data e hora |
| pickup_hour |Selecionar hora |
| pickup_week |Pegue a semana do ano |
| Dia da semana |Dia da semana (intervalo de 1 a 7) |
| passenger_count |Número de passageiros numa viagem de táxis |
| trip_time_in_secs |Tempo de viagem, em segundos |
| trip_distance |Deslocado em milhas de distância de viagem |
| pickup_longitude |Escolher longitude |
| pickup_latitude |Escolher latitude |
| dropoff_longitude |Longitude de redução |
| dropoff_latitude |Latitude de redução |
| direct_distance |Distância direta entre locais de recolha e entrega |
| payment_type |Tipo de pagamento (dinheiro, cartão de crédito, etc.) |
| fare_amount |Quantidade de Europeia no |
| Sobretaxa |Sobretaxa |
| mta_tax |Imposto de Transporte do Metro MTA |
| tip_amount |Gorjeta |
| tolls_amount |Quantidade de portagens |
| total_amount |Quantidade total |
| colocado para |Colocado para (0/1 para não ou Sim) |
| tip_class |Sugestão de classe (0: US $0, 1: 0-5 $, 2: us $6 a 10, 3: us $11-20, 4: > us $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Executar o código a partir de um bloco de notas do Jupyter no cluster do Spark
Pode iniciar o bloco de notas do Jupyter no portal do Azure. Encontre o seu cluster do Spark no seu dashboard e clique nele para inserir a página de gestão para o seu cluster. Para abrir o caderno associado ao cluster Spark, clique em **Cluster Dashboards** -> **Jupyter Notebook**.

![Dashboards de clusters](./media/spark-overview/spark-jupyter-on-portal.png)

Também pode navegar para ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** aceder aos Cadernos Jupyter. Substitua a parte CLUSTERNAME deste URL com o nome do seu próprio cluster. Terá da palavra-passe para a sua conta de administrador acessar os blocos de notas.

![Procurar os blocos de notas do Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Selecione PySpark para ver um diretório que contenha alguns exemplos de cadernos pré-embalados que usam a API PySpark. Os cadernos que contêm as amostras de código para este conjunto de tópicos Spark estão disponíveis no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Pode fazer o upload dos cadernos diretamente do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) para o servidor de cadernos Jupyter no seu cluster Spark. Na página inicial do seu Jupyter, clique no botão **Upload** na parte direita do ecrã. É aberto um Explorador de ficheiros. Aqui pode colar o URL GitHub (conteúdo bruto) do Caderno e clicar **em Open**.

Vê o nome do ficheiro na sua lista de ficheiros jupyter com um botão **upload** novamente. Clique neste botão **de upload.** Agora que importou o bloco de notas. Repita estes passos para carregar os outros blocos de notas nestas instruções.

> [!TIP]
> Pode clicar corretamente nos links do seu navegador e selecionar **Copy Link** para obter o URL de conteúdo bruto GitHub. Pode colar este URL na caixa de diálogo a de Explorador de ficheiros Jupyter carregar.
> 
> 

Agora, pode:

* Consulte o código clicando o bloco de notas.
* Execute cada célula pressionando **SHIFT-ENTER**.
* Execute todo o caderno clicando na **Cell** -> **Run**.
* Utilize a visualização automática de consultas.

> [!TIP]
> O kernel do PySpark automaticamente visualiza a saída de consultas SQL (HiveQL). É-lhe dada a opção de selecionar entre vários tipos diferentes de visualizações (Tabela, Tarte, Linha, Área ou Bar) utilizando os botões do menu **Tipo** no caderno:
>
>

![Curva cor MULTICLASSE de regressão logística de abordagem genérica](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Passos seguintes?
Agora que estão configurados com um cluster do Spark do HDInsight e carregar os blocos de notas do Jupyter, está pronto para trabalhar com os tópicos que correspondem aos três blocos de anotações do PySpark. Eles mostram como explorar os seus dados e, em seguida, como criar e consumir modelos. O bloco de notas do exploração e modelação avançada de dados mostra como incluir a validação cruzada, hyper varrimento, parâmetro e modelar a avaliação.

**Exploração de Dados e modelação com Faísca:** Explore o conjunto de dados e crie, marque e avalie os modelos de machine learning trabalhando através dos modelos de [classificação binária e regressão para dados com o tópico de toolkit Spark MLlib.](spark-data-exploration-modeling.md)

**Consumo de modelo:** Para aprender a pontuar os modelos de classificação e regressão criados neste tópico, consulte [o Score e avalie os modelos de aprendizagem automática construídos pela Spark.](spark-model-consumption.md)

**Validação cruzada e varredura de hiperparâmetros**: Ver [advanced a exploração e modelação de dados com a Spark](spark-advanced-data-exploration-modeling.md) sobre como os modelos podem ser treinados usando validação cruzada e varredura de hiperparâmetros

