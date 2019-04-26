---
title: Configurar dados de ambientes de ciência no Azure - Team Data Science Process
description: Configure dados de ambientes de ciência no Azure para utilização no processo de ciência de dados de equipa.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2c66da6717018237aebaf5fdaf803a8ac98f82be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60344687"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Configurar ambientes de ciência de dados para utilizar no Team Data Science Process
O processo de ciência de dados de equipa utiliza vários ambientes de ciência de dados para o armazenamento, processamento e análise de dados. Eles incluem o armazenamento de Blobs do Azure, vários tipos de máquinas virtuais do Azure, clusters de HDInsight (Hadoop) e áreas de trabalho do Azure Machine Learning. A decisão sobre o ambiente no qual pretende utilizar depende do tipo e a quantidade de dados a ser modelada e o destino de destino para que os dados na cloud. 

* Para obter orientações sobre questões a considerar ao tomar esta decisão, consulte [planear o Azure Machine Learning dados ciência ambiente](plan-your-environment.md). 
* Para um catálogo de alguns dos cenários que poderá encontrar durante a realização de análises avançadas, consulte [cenários para o processo de ciência de dados de equipa](plan-sample-scenarios.md)

Os seguintes artigos descrevem como configurar os vários ambientes de ciência de dados utilizados pelo processo de ciência de dados de equipa.

* [Conta de armazenamento do Azure](../../storage/common/storage-quickstart-create-account.md)
* [Cluster do HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Área de trabalho de Machine Learning Studio do Azure](../studio/create-workspace.md)

O **Máquina Virtual de ciência de dados (DSVM) do Microsoft** também está disponível como uma imagem de máquina virtual do Azure (VM). Esta VM está previamente instalado e configurado com várias ferramentas populares que são frequentemente utilizadas para análise de dados e machine learning. A DSVM está disponível no Windows e Linux. Para obter mais informações, consulte [introdução para o baseado na nuvem dados de Máquina Virtual de ciência para Linux e Windows](../data-science-virtual-machine/overview.md).

Saiba como criar:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [DSVM do Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
- [VM de aprendizagem profunda](../data-science-virtual-machine/provision-deep-learning-dsvm.md)
