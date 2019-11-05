---
title: Configurar ambientes de ciência de dados no Azure – processo de ciência de dados de equipe
description: Configure ambientes de ciência de dados no Azure para uso no processo de ciência de dados de equipe.
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
ms.openlocfilehash: 63a77a4f715402951ba8af70755196d52e3b742d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492386"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Configurar ambientes de ciência de dados para utilizar no Team Data Science Process
O processo de ciência de dados de equipe usa vários ambientes de ciência de dados para o armazenamento, o processamento e a análise de dados. Eles incluem o armazenamento de BLOBs do Azure, vários tipos de máquinas virtuais do Azure, clusters HDInsight (Hadoop) e espaços de trabalho do Azure Machine Learning. A decisão sobre qual ambiente usar depende do tipo e da quantidade de dados a serem modelados e do destino de destino para esses dados na nuvem. 

* Para obter orientação sobre as perguntas a serem consideradas ao tomar essa decisão, consulte [planejar seu Azure Machine Learning ambiente de ciência de dados](plan-your-environment.md). 
* Para um catálogo de alguns dos cenários que você pode encontrar ao fazer análises avançadas, consulte [cenários para o processo de ciência de dados de equipe](plan-sample-scenarios.md)

Os artigos a seguir descrevem como configurar os vários ambientes de ciência de dados usados pelo processo de ciência de dados de equipe.

* [Conta de armazenamento do Azure](../../storage/common/storage-quickstart-create-account.md)
* [Cluster HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Espaço de trabalho Azure Machine Learning Studio (clássico)](../studio/create-workspace.md)

O **Microsoft máquina virtual de ciência de dados (DSVM)** também está disponível como uma imagem de VM (máquina virtual) do Azure. Essa VM é pré-instalada e configurada com várias ferramentas populares que são comumente usadas para análise de dados e aprendizado de máquina. O DSVM está disponível no Windows e no Linux. Para obter mais informações, consulte [introdução ao máquina virtual de ciência de dados baseado em nuvem para Linux e Windows](../data-science-virtual-machine/overview.md).

Saiba como criar:

- [DSVM do Windows](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
