---
title: Analytics on Hadoop Azure HDInsight usando Hive - Team Data Science Process
description: Exemplos do Processo de Ciência de Dados da Equipa que passam pelo uso da Hive no Hadoop Azure HDInsight para fazer análises preditivas.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864167"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>HDInsight Hadoop data science walkthroughs usando Hive on Azure 

Estes passadiços usam a Hive com um aglomerado de Hadoop HDInsight para fazer análises preditivas. Seguem os passos delineados no Processo de Ciência de Dados da Equipa. Para uma visão geral do Processo de Ciência de Dados da Equipa, consulte o [Processo de Ciência](overview.md)de Dados . Para uma introdução ao Azure HDInsight, consulte [Introdução ao Azure HDInsight, à pilha de tecnologia Hadoop e aos clusters Hadoop.](../../hdinsight/hadoop/apache-hadoop-introduction.md)

Os walkthroughs adicionais de ciência de dados que executam o Processo de Ciência de Dados da Equipa são agrupados pela **plataforma** que utilizam. Consulte [walkthroughs executando o Processo](walkthroughs.md) de Ciência de Dados da Equipa para uma itemização destes exemplos.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Prever dicas de táxi usando hive com Hadoop HDInsight

Os [clusters de Hadoop Use HDInsight](hive-walkthrough.md) usam dados de táxis de Nova Iorque para prever: 

- Se uma gorjeta é paga 
- A distribuição dos valores das gorjetas

O cenário é implementado usando a Hive com um [cluster hadoop Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Você aprende a armazenar, explorar e recursos dados de engenheiro sacar dados de uma viagem de táxi e datação de tarifas de NYC publicamente disponíveis. Também utiliza o Azure Machine Learning para construir e implementar os modelos.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Prever cliques de publicidade usando hive com Hadoop HDInsight

Os [Clusters hadoop Do Uso Azure HDInsight num](hive-criteo-walkthrough.md) conjunto de dados de 1 TB utilizam um conjunto de dados de clique supérbio disponível publicamente para prever se uma gorjeta é paga e os valores esperados. [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) O cenário é implementado usando a Hive com um [cluster Hadoop Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, engenheiro de recursos e dados de amostras. Utiliza o Azure Machine Learning para construir, treinar e marcar um modelo de classificação binário que prevê se um utilizador clica num anúncio. O walkthrough conclui mostrando como publicar um destes modelos como um serviço Web.


## <a name="next-steps"></a>Passos seguintes

Para uma discussão sobre os componentes-chave que compõem o Processo de Ciência de Dados da Equipa, consulte a visão geral do Processo de Ciência de Dados da [Equipa.](overview.md)

Para uma discussão sobre o ciclo de vida do Processo de Ciência de Dados da Equipa que pode usar para estruturar os seus projetos de ciência de dados, consulte o ciclo de vida do Processo de Ciência de [Dados da Equipa.](lifecycle.md) O ciclo de vida traça os passos, do início ao fim, que os projetos geralmente seguem quando são executados. 

