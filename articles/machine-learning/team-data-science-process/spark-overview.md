---
title: Ciência de dados usando Faísca no Azure HDInsight - Processo de Ciência de Dados de Equipa
description: O kit de ferramentas Spark MLlib traz capacidades consideráveis de modelagem de machine learning para o ambiente HDInsight distribuído.
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
ms.openlocfilehash: 1dd82fb00c55e3676929999f204eae8755671038
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314748"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Visão geral da ciência dos dados usando Spark on Azure HDInsight

Este conjunto de tópicos mostra como usar o HDInsight Spark para completar tarefas comuns de ciência de dados, tais como ingestão de dados, engenharia de recursos, modelagem e avaliação de modelos. Os dados utilizados são uma amostra da viagem de táxi de NYC de 2013 e do conjunto de dados de tarifas. Os modelos construídos incluem regressão logística e linear, florestas aleatórias e árvores impulsionadas pelo gradiente. Os tópicos também mostram como armazenar estes modelos no armazenamento de blob Azure (WASB) e como marcar e avaliar o seu desempenho preditivo. Tópicos mais avançados cobrem como os modelos podem ser treinados usando a validação cruzada e a varredura de hiper-parâmetros. Este tópico de visão geral também refere os tópicos que descrevem como configurar o cluster Spark que você precisa para completar os passos nas etapas fornecidas.

## <a name="spark-and-mllib"></a>Faísca e MLlib
[Spark](https://spark.apache.org/) é um quadro de processamento paralelo de código aberto que suporta o processamento na memória para aumentar o desempenho de aplicações analíticas de big data. O motor de processamento de faíscas é construído para velocidade, facilidade de utilização e análise sofisticada. As capacidades de computação distribuídas na memória da Spark fazem com que seja uma boa escolha para os algoritmos iterativos usados na aprendizagem automática e computações de gráficos. [MLlib](https://spark.apache.org/mllib/) é a biblioteca de machine learning escalável da Spark que traz as capacidades de modelação algorítmica para este ambiente distribuído.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) é a oferta azure hospedada de Spark open-source. Também inclui suporte para **cadernos Jupyter PySpark** no cluster Spark que pode executar consultas interativas Spark SQL para a transformação, filtragem e visualização de dados armazenados em Azure Blobs (WASB). PySpark é a API python para faíscas. Os códigos cortam as soluções e mostram os enredos relevantes para visualizar os dados aqui executados em cadernos Jupyter instalados nos clusters Spark. Os passos de modelação nestes tópicos contêm código que mostra como treinar, avaliar, salvar e consumir cada tipo de modelo.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Configuração: Agrupamentos de faíscas e cadernos Jupyter
Os passos de configuração e o código são fornecidos neste walkthrough para a utilização de um HDInsight Spark 1.6. Mas os cadernos Jupyter são fornecidos tanto para os clusters HDInsight Spark 1.6 como para o Spark 2.0. Uma descrição dos cadernos e ligações a eles são fornecidas no [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) para o repositório GitHub que os contém. Além disso, o código aqui e nos cadernos ligados é genérico e deve funcionar em qualquer cluster Spark. Se não estiver a utilizar o HDInsight Spark, os passos de configuração e gestão do cluster podem ser ligeiramente diferentes do mostrado aqui. Por conveniência, aqui estão os links para os cadernos Jupyter para Spark 1.6 (a ser executado no núcleo pySpark do servidor Jupyter Notebook) e Spark 2.0 (a ser executado no núcleo pySpark3 do servidor Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Spark 1.6 cadernos
Estes cadernos devem ser executados no núcleo pySpark do servidor de cadernos Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Fornece informações sobre como realizar a exploração de dados, modelação e pontuação com vários algoritmos diferentes.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Inclui tópicos em #1 de cadernos e desenvolvimento de modelos usando afinação hiperparaítrâmea e validação cruzada.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Mostra como operacionalizar um modelo salvo usando Python em clusters HDInsight.

### <a name="spark-20-notebooks"></a>Spark 2.0 cadernos
Estes cadernos devem ser executados no núcleo pySpark3 do servidor de cadernos Jupyter.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Este ficheiro fornece informações sobre como realizar a exploração, modelação e pontuação de dados em clusters Spark 2.0 utilizando a viagem de táxi de NYC e conjunto de dados de tarifas [descritos aqui.](#the-nyc-2013-taxi-data) Este caderno pode ser um bom ponto de partida para explorar rapidamente o código que fornecemos para o Spark 2.0. Para um caderno mais detalhado analisa os dados do Táxi de NYC, consulte o próximo caderno desta lista. Consulte as notas que seguem esta lista que compara estes cadernos.
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Este ficheiro mostra como realizar a luta de dados (Spark SQL e operações dataframe), exploração, modelação e pontuação utilizando a viagem de táxi de NYC e conjunto de dados de tarifas [descritos aqui.](#the-nyc-2013-taxi-data)
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Este ficheiro mostra como realizar a realização de problemas de trabalho (Spark SQL e operações de dataframe), exploração, modelação e pontuação utilizando o conhecido conjunto de dados de partida on-time da Companhia Aérea de 2011 e 2012. Integrámos o conjunto de dados da companhia aérea com os dados meteorológicos do aeroporto (por exemplo, velocidade do vento, temperatura, altitude, etc.) antes da modelação, para que estas características meteorológicas possam ser incluídas no modelo.

<!-- -->

> [!NOTE]
> O conjunto de dados da companhia aérea foi adicionado aos cadernos Spark 2.0 para ilustrar melhor o uso de algoritmos de classificação. Consulte as seguintes ligações para obter informações sobre o conjunto de dados de partida on-time da companhia aérea e o conjunto de dados meteorológicos:
> 
> - Dados de partida a horas da companhia aérea: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Dados meteorológicos do aeroporto: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Os cadernos Spark 2.0 nos conjuntos de dados de atraso de táxi e voo da companhia aérea de NYC podem demorar 10 minutos ou mais a funcionar (dependendo do tamanho do seu cluster HDI). O primeiro caderno da lista acima mostra muitos aspetos da exploração de dados, visualização e formação de modelos ML num caderno que leva menos tempo para correr com o conjunto de dados de NYC, no qual os ficheiros de táxi e tarifas foram pré-unidos: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Este caderno demora muito mais tempo a terminar (2-3 minutos) e pode ser um bom ponto de partida para explorar rapidamente o código que previmos para o Spark 2.0.

<!-- -->

Para obter orientações sobre a operacionalização de um modelo Spark 2.0 e o consumo de modelo para pontuação, consulte o [documento Spark 1.6 sobre o consumo](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) para um exemplo que delineie os passos necessários. Para utilizar este exemplo no Spark 2.0, substitua o ficheiro de código Python por [este ficheiro](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Pré-requisitos

Os seguintes procedimentos estão relacionados com a Faísca 1.6. Para a versão Spark 2.0, utilize os cadernos descritos e ligados anteriormente.

1. Precisa de uma subscrição do Azure. Se ainda não tiver um, consulte [o teste gratuito do Get Azure.](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

2. Você precisa de um cluster Spark 1.6 para completar esta passagem. Para criar uma, consulte as instruções fornecidas em [Get start: create Apache Spark on Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). O tipo e versão do cluster é especificado no menu **Select Cluster Type.**

![Aglomerado de configuração](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Para um tópico que mostre como usar Scala em vez de Python para completar tarefas para um processo de ciência de dados de ponta a ponta, consulte a [Data Science usando Scala com Faísca em Azure.](scala-walkthrough.md)
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Os dados do táxi de NYC 2013
Os dados da NyC Taxi Trip são cerca de 20 GB de ficheiros de valores comamaiscos separados (CSV) (~48 GB não comprimidos), compreendendo mais de 173 milhões de viagens individuais e as tarifas pagas por cada viagem. Cada registo de viagem inclui o local e a hora de recolha e entrega, número de licença de hack anonimizado (motorista) e número de medalhão (id único do táxi). Os dados cobrem todas as viagens do ano de 2013 e são fornecidos nos dois conjuntos de dados seguintes para cada mês:

1. Os ficheiros CSV 'trip_data' contêm detalhes da viagem, tais como o número de passageiros, pontos de recolha e entrega, duração da viagem e duração da viagem. Aqui estão alguns registos de amostras:

   `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Os ficheiros CSV 'trip_fare' contêm detalhes da tarifa paga por cada viagem, como tipo de pagamento, valor da tarifa, sobretaxa e impostos, gorjetas e portagens, bem como o valor total pago. Aqui estão alguns registos de amostras:

   `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Recolhemos uma amostra de 0,1% destes ficheiros e juntivemos os ficheiros CVS de viagem e tarifa de \_ viagem num único conjunto de dados para usar como conjunto de \_ dados de entrada para este walkthrough. A chave única para juntar dados de viagem \_ e tarifa de viagem é composta pelos \_ campos: medalhão, licença de hack \_ e data de \_ recolha. Cada registo do conjunto de dados contém os seguintes atributos que representam uma viagem de táxi de NYC:

| Campo | Breve Descrição |
| --- | --- |
| medalhão |Medalhão de táxi anonimizado (id de táxi único) |
| hack_license |Anonimizado número de licença de transporte de hackney |
| vendor_id |Id de vendedor de táxi |
| rate_code |Taxa de táxi de NYC de tarifa |
| store_and_fwd_flag |Loja e bandeira para a frente |
| pickup_datetime |Hora & de tempo de recolha |
| dropoff_datetime |Data de entrega & hora |
| pickup_hour |Hora de recolha |
| pickup_week |Retomar a semana do ano |
| dia da semana |Dia da semana (intervalo 1-7) |
| passenger_count |Número de passageiros em uma viagem de táxi |
| trip_time_in_secs |Tempo de viagem em segundos |
| trip_distance |Distância de viagem percorrida em milhas |
| pickup_longitude |Pegue a longitude |
| pickup_latitude |Pegue a latitude |
| dropoff_longitude |Longitude dropoff |
| dropoff_latitude |Latitude de dropoff |
| direct_distance |Distância direta entre locais de recolha e de entrega |
| payment_type |Tipo de pagamento (dinheiro, cartão de crédito, etc.) |
| fare_amount |Valor da tarifa em |
| sobretaxa |Sobretaxa |
| mta_tax |Imposto de Transporte de Metro do MTA |
| tip_amount |Valor da gorjeta |
| tolls_amount |Valor das portagens |
| total_amount |Montante total |
| gorjeta |Gorjeta (0/1 para não ou sim) |
| tip_class |Aula de gorjeta (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Execute o código a partir de um caderno Jupyter no cluster Spark
Pode lançar o Caderno Jupyter a partir do portal Azure. Encontre o seu cluster Spark no seu painel de instrumentos e clique nele para introduzir a página de gestão para o seu cluster. Para abrir o caderno associado ao cluster Spark, clique no **Bloco de Painéis de Cluster**  ->  **Jupyter** Notebook .

![Painéis de cluster](./media/spark-overview/spark-jupyter-on-portal.png)

Também pode navegar para * *_`https://CLUSTERNAME.azurehdinsight.net/jupyter`_* _ para aceder aos Cadernos Jupyter. Substitua a parte clustername deste URL pelo nome do seu próprio cluster. Precisa da senha da sua conta de administração para aceder aos cadernos.

![Navegue por cadernos jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Selecione PySpark para ver um diretório que contém alguns exemplos de cadernos pré-embalados que usam a API PySpark. Os cadernos que contêm as amostras de código para este conjunto de temas Spark estão disponíveis no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Pode fazer o upload dos cadernos diretamente do [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) para o servidor de cadernos Jupyter no seu cluster Spark. Na página inicial do seu Jupyter, clique no botão _ *Upload* * na parte direita do ecrã. Abre um explorador de ficheiros. Aqui pode colar o URL gitHub (conteúdo bruto) do Caderno e clicar em **Abrir**.

Vê o nome do ficheiro na sua lista de ficheiros Jupyter com um botão **upload** novamente. Clique neste botão **upload.** Agora importaste o caderno. Repita estes passos para carregar os outros cadernos desta passagem.

> [!TIP]
> Pode clicar com o direito nos links do seu navegador e selecionar **Copy Link** para obter o URL de conteúdo cru do GitHub. Pode colar este URL na caixa de diálogo do explorador de ficheiros Jupyter Upload.
> 
> 

Agora pode:

* Consulte o código clicando no caderno.
* Execute cada célula premindo **SHIFT-ENTER**.
* Execute todo o caderno clicando em **Cell**  ->  **Run**.
* Utilize a visualização automática de consultas.

> [!TIP]
> O kernel PySpark visualiza automaticamente a saída de consultas SQL (HiveQL). É-lhe dada a opção de selecionar entre vários tipos diferentes de visualizações (Tabela, Tarte, Linha, Área ou Bar) utilizando os botões de menu **Tipo** no portátil:
>
>

![Curva ROC de regressão logística para abordagem genérica](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>O que se segue?
Agora que foi configurado com um cluster HDInsight Spark e carregou os cadernos Jupyter, está pronto para trabalhar através dos tópicos que correspondem aos três cadernos PySpark. Eles mostram como explorar os seus dados e, em seguida, como criar e consumir modelos. O notebook avançado de exploração e modelação de dados mostra como incluir validação cruzada, varredura de hiper-parâmetros e avaliação de modelos.

**Exploração de dados e modelação com faísca:** Explore o conjunto de dados e crie, marque e avalie os modelos de machine learning trabalhando através dos [modelos de classificação e regressão de Criar binários para dados com o tópico do kit de ferramentas Spark MLlib.](spark-data-exploration-modeling.md)

**Consumo de modelos:** Para aprender a pontuar os modelos de classificação e regressão criados neste tópico, consulte [Score e avalie os modelos de aprendizagem automática construídos pela Spark.](spark-model-consumption.md)

**Varredura de validação cruzada e hiperparímetro** : Consulte [a exploração e modelação avançadas de dados com a Spark](spark-advanced-data-exploration-modeling.md) sobre como os modelos podem ser treinados usando a validação cruzada e a varredura de hiper-parâmetros