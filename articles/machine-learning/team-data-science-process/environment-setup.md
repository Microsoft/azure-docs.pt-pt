---
title: Configurar ambientes de ciência de dados em Azure - Processo de Ciência de Dados de Equipa
description: Crie ambientes de ciência de dados no Azure para uso no Processo de Ciência de Dados de Equipa.
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
ms.openlocfilehash: 557550627fe2c39571a848723e5d716324fee240
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321177"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Configurar ambientes de ciência de dados para utilizar no Team Data Science Process
O Processo de Ciência de Dados da Equipa utiliza vários ambientes de ciência de dados para o armazenamento, processamento e análise de dados. Eles incluem Azure Blob Storage, vários tipos de máquinas virtuais Azure, clusters HDInsight (Hadoop) e espaços de trabalho de aprendizagem de máquinas Azure. A decisão sobre qual o ambiente a utilizar depende do tipo e quantidade de dados a modelar e do destino-alvo para esses dados na nuvem. 

* Para obter orientações sobre questões a ter em conta ao tomar esta decisão, consulte [Plan Your Azure Machine Learning Data Science Environment](plan-your-environment.md). 
* Para obter um catálogo de alguns dos cenários que poderá encontrar ao fazer [análises avançadas, consulte Cenários para o Processo de Ciência de Dados da Equipa](plan-sample-scenarios.md)

Os seguintes artigos descrevem como configurar os vários ambientes de ciência de dados utilizados pelo Processo de Ciência de Dados de Equipa.

* [Conta de armazenamento Azure](../../storage/common/storage-account-create.md)
* [Cluster HDInsight (Hadoop)](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
* [Azure Machine Learning Studio (espaço de trabalho clássico)](../classic/create-workspace.md)

A **Máquina Virtual da Microsoft Data Science (DSVM)** também está disponível como uma imagem de máquina virtual Azure (VM). Este VM é pré-instalado e configurado com várias ferramentas populares que são comumente usadas para análise de dados e machine learning. O DSVM está disponível tanto no Windows como no Linux. Para obter mais informações, consulte Introdução à máquina virtual de [ciência de dados baseada na nuvem para Linux e Windows](../data-science-virtual-machine/overview.md).

Saiba como criar:

- [DSVM do Windows](../data-science-virtual-machine/provision-vm.md)
- [DSVM do Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [DSVM do CentOS](../data-science-virtual-machine/release-notes.md)