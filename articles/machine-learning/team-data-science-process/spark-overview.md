---
title: Ciência dos dados usando Spark em Azure HDInsight - Team Data Science Process
description: O kit de ferramentas Spark MLlib traz consideráveis capacidades de modelação de machine learning para o ambiente HDInsight distribuído.
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
ms.openlocfilehash: 64caa1228cd073358bef496721c22b17554031d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82189288"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Visão geral da ciência dos dados usando Spark no Azure HDInsight

Este conjunto de tópicos mostra como usar o HDInsight Spark para completar tarefas comuns de ciência de dados, tais como ingestão de dados, engenharia de funcionalidades, modelação e avaliação de modelos. Os dados utilizados são uma amostra da viagem de táxi de NYC de 2013 e dataset de tarifas. Os modelos construídos incluem regressão logística e linear, florestas aleatórias e árvores impulsionadas por gradientes. Os tópicos também mostram como armazenar estes modelos no armazenamento de blob Azure (WASB) e como pontuar e avaliar o seu desempenho preditivo. Tópicos mais avançados cobrem como os modelos podem ser treinados usando validação cruzada e varredura de hiperparâmetros. Este tópico de visão geral também refere os tópicos que descrevem como configurar o cluster Spark que você precisa para completar os passos nas caminhadas fornecidas.

## <a name="spark-and-mllib"></a>Faísca e MLlib
[Spark](https://spark.apache.org/) é um quadro de processamento paralelo de código aberto que suporta o processamento na memória para aumentar o desempenho de aplicações analíticas de big data. O motor de processamento Spark é construído para velocidade, facilidade de utilização e análise sofisticada. As capacidades de computação distribuídas pela Spark fazem dele uma boa escolha para os algoritmos iterativos usados na aprendizagem automática e nas computações de gráficos. [MLlib](https://spark.apache.org/mllib/) é a biblioteca de machine learning escalável da Spark que traz as capacidades de modelação algorítmica para este ambiente distribuído.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) é o Azure hospedado oferta de spark de código aberto. Também inclui suporte para **os cadernos Jupyter PySpark** no cluster Spark que podem executar consultas interativas Spark SQL para transformar, filtrar e visualizar dados armazenados em Blobs Azure (WASB). PySpark é a API Python para a Spark. Os fragmentos de código que fornecem as soluções e mostram os enredos relevantes para visualizar os dados aqui executados em cadernos Jupyter instalados nos clusters Spark. Os passos de modelação nestes tópicos contêm código que mostra como treinar, avaliar, poupar e consumir cada tipo de modelo.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Configuração: Aglomerados de faíscas e cadernos jupyter
Os passos e códigode configuração são fornecidos neste walkthrough para a utilização de uma Faísca HDInsight 1.6. Mas os cadernos Jupyter são fornecidos para os clusters HDInsight Spark 1.6 e Spark 2.0. Uma descrição dos cadernos e links para eles são fornecidas no [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório GitHub que os contém. Além disso, o código aqui e nos cadernos ligados é genérico e deve funcionar em qualquer cluster Spark. Se não estiver a utilizar o HDInsight Spark, os passos de configuração e gestão do cluster podem ser ligeiramente diferentes do que é mostrado aqui. Por conveniência, aqui estão os links para os cadernos Jupyter para Spark 1.6 (a ser executado no núcleo pySpark do servidor Jupyter Notebook) e Spark 2.0 (a ser executado no núcleo pySpark3 do servidor Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Cadernos Spark 1.6
Estes cadernos devem ser executados no núcleo pySpark do servidor de cadernos Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Fornece informações sobre como realizar a exploração de dados, modelação e pontuação com vários algoritmos diferentes.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Inclui tópicos em #1 de cadernos, e desenvolvimento de modelos utilizando a finação e validação cruzada do hiperparâmetro.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Mostra como operacionalizar um modelo salvo usando Python em clusters HDInsight.

### <a name="spark-20-notebooks"></a>Cadernos Spark 2.0
Estes cadernos devem ser executados no núcleo pySpark3 do servidor de cadernos Jupyter.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Este ficheiro fornece informações sobre como realizar a exploração de dados, modelação e pontuação em clusters Spark 2.0 utilizando a viagem de táxi de NYC e o conjunto de dados de tarifas descrito [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Este caderno pode ser um bom ponto de partida para explorar rapidamente o código que fornecemos para a Spark 2.0. Para um caderno mais detalhado analisa os dados do Táxi de NYC, consulte o próximo caderno nesta lista. Consulte as notas que seguem esta lista que compara estes cadernos.
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Este ficheiro mostra como realizar a discussão de dados (Operações Spark SQL e dataframe), exploração, modelação e pontuação utilizando a viagem de táxi de NYC e o conjunto de dados de tarifas descrito [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Este ficheiro mostra como realizar a discussão de dados (Operações Spark SQL e dataframe), exploração, modelação e pontuação utilizando o conhecido conjunto de dados de partida da Companhia Aérea On-time de 2011 e 2012. Integrámos o conjunto de dados da companhia aérea com os dados meteorológicos do aeroporto (por exemplo, velocidade do vento, temperatura, altitude, etc.) antes da modelação, para que estas características meteorológicas possam ser incluídas no modelo.

<!-- -->

> [!NOTE]
> O conjunto de dados da companhia aérea foi adicionado aos cadernos Spark 2.0 para ilustrar melhor o uso de algoritmos de classificação. Consulte os seguintes links para obter informações sobre o conjunto de dados de partida a tempo da companhia aérea e o conjunto de dados meteorológicos:
> 
> - Dados de partida a tempo das companhias aéreas:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Dados meteorológicos do aeroporto:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Os cadernos Spark 2.0 nos conjuntos de dados de atraso de táxi e de voo da companhia aérea de NYC podem demorar 10 minutos ou mais a funcionar (dependendo do tamanho do seu cluster HDI). O primeiro caderno da lista acima mostra muitos aspetos da exploração de dados, visualização e formação de modelos ML num caderno que leva menos tempo a ser executado com conjunto de dados de NYC amostrado, no qual os ficheiros de táxi e tarifas foram pré-associados: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Este caderno demora muito menos tempo a terminar (2-3 minutos) e pode ser um bom ponto de partida para explorar rapidamente o código que fornecemos para o Spark 2.0.

<!-- -->

Para obter orientações sobre a operacionalização de um modelo Spark 2.0 e o consumo de modelo para pontuação, consulte o [documento Spark 1.6 sobre o consumo,](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) por exemplo, delineando os passos necessários. Para utilizar este exemplo no Spark 2.0, substitua o ficheiro de código Python por [este ficheiro](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Pré-requisitos

Os seguintes procedimentos estão relacionados com a Faísca 1.6. Para a versão Spark 2.0, utilize os cadernos descritos e ligados anteriormente.

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

## <a name="the-nyc-2013-taxi-data"></a>Os dados do táxi de NYC 2013
Os dados da NyC Taxi Trip são cerca de 20 GB de valores comprimidos separados de vírem (CSV) ficheiros (~48 GB não comprimidos), compreendendo mais de 173 milhões de viagens individuais e as tarifas pagas por cada viagem. Cada registo de viagem inclui o local e hora de recolha e entrega, número de licença de hack anoonymizado (motorista) e número de medalhão (id único do táxi). Os dados abrangem todas as viagens do ano de 2013 e são fornecidos nos dois conjuntos de dados seguintes para cada mês:

1. Os ficheiros CSV 'trip_data' contêm detalhes da viagem, tais como o número de passageiros, pontos de recolha e entrega, duração da viagem e duração da viagem. Aqui estão alguns registos de amostras:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Os ficheiros CSV 'trip_fare' contêm detalhes da tarifa paga por cada viagem, tais como tipo de pagamento, valor da tarifa, sobretaxa e impostos, gorjetas e portagens, e o valor total pago. Aqui estão alguns registos de amostras:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Retirámos uma amostra de 0,1% destes\_ficheiros\_e juntámos os dados da viagem e os ficheiros CVS de viagem num único conjunto de dados para utilizar como conjunto de dados de entrada para esta passagem. A chave única\_para juntar\_dados de viagem e tarifa\_de viagem é composta pelos campos: medalhão, licença de hack e data de recolha.\_ Cada registo do conjunto de dados contém os seguintes atributos que representam uma viagem de táxi de NYC:

| Campo | Breve Descrição |
| --- | --- |
| medalhão |Medalhão de táxi anoonizado (id único do táxi) |
| hack_license |Número de licença de transporte de Hackney anoymizada |
| vendor_id |Id do vendedor de táxis |
| rate_code |Taxa de táxi de NYC de tarifa |
| store_and_fwd_flag |Armazenar e bandeira para a frente |
| pickup_datetime |Adata-se & hora |
| dropoff_datetime |Data de entrega & hora |
| pickup_hour |Hora de recolha |
| pickup_week |Retomar a semana do ano |
| dia da semana |Dia da semana (intervalo 1-7) |
| passenger_count |Número de passageiros em uma viagem de táxi |
| trip_time_in_secs |Tempo de viagem em segundos |
| trip_distance |Distância de viagem percorrida em milhas |
| pickup_longitude |Apanhe longitude |
| pickup_latitude |Pegar a latitude |
| dropoff_longitude |Longitude dropoff |
| dropoff_latitude |Latitude dropoff |
| direct_distance |Distância direta entre locais de recolha e entrega |
| payment_type |Tipo de pagamento (dinheiro, cartão de crédito, etc.) |
| fare_amount |Tarifa em |
| sobretaxa |Sobretaxa |
| mta_tax |Imposto de Transporte do Metro MTA |
| tip_amount |Valor da gorjeta |
| tolls_amount |Valor das portagens |
| total_amount |Montante total |
| gorjeta |Gorjeta (0/1 para não ou sim) |
| tip_class |Classe dica (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Execute código de um caderno Jupyter no cluster Spark
Pode lançar o Caderno Jupyter do portal Azure. Encontre o seu cluster Spark no seu painel de instrumentos e clique nele para introduzir a página de gestão para o seu cluster. Para abrir o caderno associado ao cluster Spark, clique em **Cluster Dashboards** -> **Jupyter Notebook**.

![Dashboards de cluster](./media/spark-overview/spark-jupyter-on-portal.png)

Também pode navegar ***`https://CLUSTERNAME.azurehdinsight.net/jupyter`*** para aceder aos Cadernos Jupyter. Substitua a parte CLUSTERNAME deste URL com o nome do seu próprio cluster. Precisa da senha da sua conta de administração para aceder aos cadernos.

![Pesquise os cadernos jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Selecione PySpark para ver um diretório que contenha alguns exemplos de cadernos pré-embalados que usam a API PySpark. Os cadernos que contêm as amostras de código para este conjunto de tópicos Spark estão disponíveis no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Pode fazer o upload dos cadernos diretamente do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) para o servidor de cadernos Jupyter no seu cluster Spark. Na página inicial do seu Jupyter, clique no botão **Upload** na parte direita do ecrã. Abre um explorador de ficheiros. Aqui pode colar o URL GitHub (conteúdo bruto) do Caderno e clicar **em Open**.

Vê o nome do ficheiro na sua lista de ficheiros jupyter com um botão **upload** novamente. Clique neste botão **de upload.** Agora importaste o caderno. Repita estes passos para fazer upload dos outros cadernos desta passagem.

> [!TIP]
> Pode clicar corretamente nos links do seu navegador e selecionar **Copy Link** para obter o URL de conteúdo bruto GitHub. Pode colar este URL na caixa de diálogo do explorador de ficheiros Jupyter Upload.
> 
> 

Agora pode:

* Consulte o código clicando no caderno.
* Execute cada célula pressionando **SHIFT-ENTER**.
* Executar todo o caderno clicando em **Cell** -> **Run**.
* Utilize a visualização automática de consultas.

> [!TIP]
> O kernel PySpark visualiza automaticamente a saída de consultas SQL (HiveQL). É-lhe dada a opção de selecionar entre vários tipos diferentes de visualizações (Tabela, Tarte, Linha, Área ou Bar) utilizando os botões do menu **Tipo** no caderno:
>
>

![Curva ROC de regressão logística para abordagem genérica](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Passos seguintes?
Agora que está configurado com um cluster HDInsight Spark e fez o upload dos cadernos Jupyter, está pronto para trabalhar através dos tópicos que correspondem aos três cadernos PySpark. Mostram como explorar os seus dados e, em seguida, como criar e consumir modelos. O caderno avançado de exploração de dados e modelação mostra como incluir validação cruzada, varredura de hiperparâmetros e avaliação de modelos.

**Exploração de Dados e modelação com Faísca:** Explore o conjunto de dados e crie, marque e avalie os modelos de machine learning trabalhando através dos modelos de [classificação binária e regressão para dados com o tópico de toolkit Spark MLlib.](spark-data-exploration-modeling.md)

**Consumo de modelo:** Para aprender a pontuar os modelos de classificação e regressão criados neste tópico, consulte [o Score e avalie os modelos de aprendizagem automática construídos pela Spark.](spark-model-consumption.md)

**Validação cruzada e varredura de hiperparâmetros**: Ver [advanced a exploração e modelação de dados com a Spark](spark-advanced-data-exploration-modeling.md) sobre como os modelos podem ser treinados usando validação cruzada e varredura de hiperparâmetros

