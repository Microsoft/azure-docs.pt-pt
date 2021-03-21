---
title: Analytics on Azure HDInsight Hadoop usando Hive - Processo de Ciência de Dados de Equipa
description: Exemplos do Processo de Ciência de Dados de Equipa que percorrem o uso da Colmeia no Azure HDInsight Hadoop para fazer análises preditivas.
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
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75864167"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>HDInsight Hadoop data science walkthroughs usando Hive on Azure 

Estes walkthroughs usam a Colmeia com um cluster HDInsight Hadoop para fazer análises preditivas. Seguem os passos delineados no Processo de Ciência de Dados da Equipa. Para uma visão geral do processo de ciência de dados da equipa, consulte o [Processo de Ciência de Dados.](overview.md) Para uma introdução ao Azure HDInsight, consulte [Introdução ao Azure HDInsight, a pilha de tecnologia Hadoop e clusters Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

As análises adicionais da ciência dos dados que executam o Processo de Ciência de Dados de Equipa são agrupadas pela **plataforma** que utilizam. Consulte [Walkthroughs executando o Processo de Ciência de Dados de Equipa](walkthroughs.md) para uma itemização destes exemplos.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Prever dicas de táxi usando Hive com HDInsight Hadoop

Os [clusters Use HDInsight Hadoop](hive-walkthrough.md) utilizam dados de táxis de Nova Iorque para prever: 

- Se uma gorjeta é paga 
- A distribuição de montantes de gorjeta

O cenário é implementado usando a Colmeia com um [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Você aprende a armazenar, explorar e apresentar dados de engenheiros de uma viagem de táxi e dataset de tarifas de NYC publicamente disponíveis. Também utiliza a Azure Machine Learning para construir e implementar os modelos.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Prever cliques de anúncio usando Hive com HDInsight Hadoop

O [Use Azure HDInsight Hadoop Clusters em um walkthrough de conjunto de dados de 1-TB](hive-criteo-walkthrough.md) utiliza um conjunto de dados de clique [criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) disponível publicamente para prever se uma gorjeta é paga e os valores esperados. O cenário é implementado usando a Hive com um [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, criar engenheiro e diminuir os dados da amostra. Utiliza a Azure Machine Learning para construir, treinar e marcar um modelo de classificação binária prevendo se um utilizador clica num anúncio. O walkthrough conclui mostrando como publicar um destes modelos como um serviço Web.


## <a name="next-steps"></a>Passos seguintes

Para uma discussão dos componentes-chave que compõem o Processo de Ciência de Dados de Equipa, consulte [a visão geral do Processo de Ciência de Dados da Equipa.](overview.md)

Para uma discussão sobre o ciclo de vida do Processo de Ciência de Dados de Equipa que pode utilizar para estruturar os seus projetos de ciência de dados, consulte [o ciclo de vida do Processo de Ciência de Dados de Equipa.](lifecycle.md) O ciclo de vida descreve os passos, do início ao fim, que os projetos geralmente seguem quando são executados. 

