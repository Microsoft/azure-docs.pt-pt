---
title: Análises no Azure HDInsight Hadoop com o Hive - Team Data Science Process
description: Exemplos do processo de ciência de dados de equipa que o orientam por meio do uso do Hive no Azure HDInsight Hadoop para realizar a Análise Preditiva.
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
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864167"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Instruções de ciência de dados de Hadoop do HDInsight com o Hive no Azure 

Esta instruções passo a passo utiliza o Hive com um cluster de Hadoop do HDInsight para realizar a Análise Preditiva. Eles seguem as etapas descritas no processo de ciência de dados de equipa. Para uma descrição geral do processo de ciência de dados de equipa, consulte [processo de ciência de dados](overview.md). Para uma introdução ao Azure HDInsight, consulte [introdução ao Azure HDInsight, a pilha de tecnologia do Hadoop e os clusters do Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Instruções de ciência de dados adicionais que executar o processo de ciência de dados de equipa estão agrupadas pela **plataforma** que utilizam. Ver [instruções passo a passo, executar o processo de ciência de dados de equipa](walkthroughs.md) para também uma itemização destes exemplos.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Prever gorjetas para utilizar o Hive com o HDInsight Hadoop

O [clusters do Hadoop de HDInsight utilização](hive-walkthrough.md) estas instruções utilizam dados a partir de Nova Iorque capotados para prever: 

- Se uma dica é pago 
- A distribuição de quantidades de sugestão

O cenário é implementado utilizando o Hive com uma [cluster do Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Irá aprender a armazenar, explorar e dados de engenheiro de funcionalidade de um NYC disponível publicamente táxis conjunto de dados de viagens e Europeia. Também utilizar o Azure Machine Learning para criar e implementar os modelos.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Prever cliques de anúncio, utilizando o Hive com o HDInsight Hadoop

O [uso de clusters Azure HDInsight Hadoop em um conjunto de um DataSet de 1 TB](hive-criteo-walkthrough.md) usa um [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) publicamente disponível, clique em conjunto de um para prever se uma gorjeta é paga e os valores esperados. O cenário é implementado utilizando o Hive com uma [cluster do Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar, engenheiro de recursos e reduzir os dados de exemplo. Ele usa o Azure Machine Learning para criar, preparar e classificar um modelo de classificação binária prever se um usuário clica num anúncio. Conclusão do passo a passo que mostra como publicar um desses modelos como um serviço Web.


## <a name="next-steps"></a>Passos seguintes

Para uma discussão sobre os principais componentes que compõem o processo de ciência de dados de equipa, consulte [descrição geral do processo de ciência de dados de equipa](overview.md).

Para uma discussão sobre o ciclo de vida do processo de ciência de dados de equipa que pode utilizar para estruturar os seus projetos de ciência de dados, consulte [ciclo de vida do processo de ciência de dados de equipa](lifecycle.md). O ciclo de vida descreve os passos, do início ao fim, que projetos seguem, normalmente, quando forem executados. 

