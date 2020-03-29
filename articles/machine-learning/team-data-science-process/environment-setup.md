---
title: Criar ambientes de ciência de dados em Azure - Team Data Science Process
description: Criar ambientes de ciência de dados em Azure para uso no Processo de Ciência de Dados da Equipa.
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
ms.openlocfilehash: 6d1185e13397b3e466bd1e8d609a46f2f68bf390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063943"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Configurar ambientes de ciência de dados para utilizar no Team Data Science Process
O Processo de Ciência de Dados da Equipa utiliza vários ambientes de ciência de dados para o armazenamento, processamento e análise de dados. Incluem o Armazenamento De Blob Azure, vários tipos de máquinas virtuais Azure, clusters HDInsight (Hadoop) e espaços de trabalho azure machine learning. A decisão sobre qual o ambiente a utilizar depende do tipo e quantidade de dados a modelar e do destino-alvo desses dados na nuvem. 

* Para obter orientações sobre questões a considerar ao tomar esta decisão, consulte [Plan Your Azure Machine Learning Data Science Environment](plan-your-environment.md). 
* Para um catálogo de alguns dos cenários que poderá encontrar ao fazer [análises avançadas,](plan-sample-scenarios.md) consulte Cenários para o Processo de Ciência de Dados da Equipa

Os seguintes artigos descrevem como configurar os vários ambientes de ciência de dados usados pelo Processo de Ciência de Dados da Equipa.

* [Conta de armazenamento azure](../../storage/common/storage-account-create.md)
* [Cluster HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (clássico) espaço de trabalho](../studio/create-workspace.md)

A **Microsoft Data Science Virtual Machine (DSVM)** também está disponível como uma imagem de máquina virtual Azure (VM). Este VM é pré-instalado e configurado com várias ferramentas populares que são comumente usadas para análise de dados e machine learning. O DSVM está disponível tanto no Windows como no Linux. Para mais informações, consulte Introdução à máquina virtual de data science baseada [na nuvem para Linux e Windows](../data-science-virtual-machine/overview.md).

Aprenda a criar:

- [DSVM do Windows](../data-science-virtual-machine/provision-vm.md)
- [DSVM do Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [DSVM do CentOS](../data-science-virtual-machine/linux-dsvm-intro.md)
