---
title: Resiliência & alta disponibilidade
titleSuffix: Azure Machine Learning
description: Aprenda a tornar os seus recursos de Aprendizagem de Máquinas Azure mais resistentes a interrupções utilizando uma configuração de alta disponibilidade.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 09/16/2020
ms.openlocfilehash: 7a1a63893e6e2988fc5f21e84f21c74315d856b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93325479"
---
# <a name="increase-azure-machine-learning-resiliency"></a>Aumentar a resiliência da aprendizagem automática do Azure



Neste artigo, você aprenderá a tornar os seus recursos de Aprendizagem automática microsoft Azure mais resistentes usando configurações de alta disponibilidade. Pode configurar os serviços Azure de que a Azure Machine Learning depende para uma elevada disponibilidade. Este artigo identifica os serviços que pode configurar para uma elevada disponibilidade, e liga-se a informações adicionais sobre a configuração destes recursos.

> [!NOTE]
> A Azure Machine Learning em si não oferece uma opção de recuperação de desastres.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Compreenda os serviços da Azure para a Azure Machine Learning

A Azure Machine Learning depende de vários serviços Azure e tem várias camadas. Alguns destes serviços são prestados na sua subscrição (cliente). Você é responsável pela configuração de alta disponibilidade destes serviços. Outros serviços são criados numa subscrição da Microsoft e geridos pela Microsoft. 

Os serviços Azure incluem:

* **Azure Machine Learning infrastructure**: Um ambiente gerido pela Microsoft para o espaço de trabalho Azure Machine Learning.

* **Recursos associados**: Recursos a provisionados na sua subscrição durante a criação do espaço de trabalho Azure Machine Learning. Estes recursos incluem armazenamento Azure, Cofre de Chaves Azure, Registo de Contentores Azure e Insights de Aplicação. Você é responsável por configurar configurações de alta disponibilidade para estes recursos.
  * O armazenamento predefinido tem dados como modelo, dados de registo de treino e conjunto de dados.
  * O Key Vault tem credenciais para armazenamento de Azure, registo de contentores e lojas de dados.
  * O Registo de Contentores tem uma imagem estival para ambientes de treino e inferenculação.
  * Application Insights destina-se a monitorizar a aprendizagem automática Azure.

* **Recursos de cálculo**: Recursos que cria após a implantação do espaço de trabalho. Por exemplo, pode criar um conjunto de computação ou de cálculo para treinar um modelo de Machine Learning.
  * Caso de cálculo e cluster de computação: ambientes de desenvolvimento de modelos geridos pela Microsoft.
  * Outros recursos: Recursos de computação da Microsoft que pode anexar à Azure Machine Learning, tais como Azure Kubernetes Service (AKS), Azure Databricks, Azure Container Instances e Azure HDInsight. Você é responsável por configurar configurações de alta disponibilidade para estes recursos.

* **Lojas de dados adicionais**: Azure Machine Learning pode montar lojas de dados adicionais, tais como Azure Storage, Azure Data Lake Storage e Azure SQL Database para dados de formação.  Estas lojas de dados são advisionadas dentro da sua subscrição. É responsável por configurar as suas definições de alta disponibilidade.

A tabela que se segue mostra quais os serviços Azure geridos pela Microsoft, que são geridos por si, e que estão altamente disponíveis por padrão.

| Serviço | Gerido por | Alta disponibilidade por padrão |
| ----- | ----- | ----- |
| **Infraestrutura de aprendizagem automática Azure** | Microsoft | |
| **Recursos associados** |
| Armazenamento do Azure | O utilizador | |
| Key Vault | O utilizador | ✓ |
| Container Registry | O utilizador | |
| Application Insights | O utilizador | ND |
| **Recursos de cálculo** |
| Instância de computação | Microsoft |  |
| Cluster de cálculo | Microsoft |  |
| Outros recursos computacional, tais como AKS, <br>Azure Databricks, Container Instances, HDInsight | O utilizador |  |
| **Lojas de dados adicionais** como Azure Storage, SQL Database,<br> Base de Dados Azure para PostgreSQL, Base de Dados Azure para MySQL, <br>Sistema de ficheiros Azure Databricks | O utilizador | |

O resto deste artigo descreve as ações que precisa de tomar para tornar cada um destes serviços altamente disponível.

## <a name="associated-resources"></a>Recursos associados

> [!IMPORTANT]
> A Azure Machine Learning não suporta o failover da conta de armazenamento padrão utilizando armazenamento geo-redundante (GRS), armazenamento redundante de geo-zona (GZRS), armazenamento geo-redundante de acesso à leitura (RA-GRS) ou armazenamento de geo-zona-redundante de acesso à leitura (RA-GZRS).

Certifique-se de configurar as definições de alta disponibilidade de cada recurso, referindo-se à seguinte documentação:

* **Armazenamento azul**: Para configurar configurações de alta disponibilidade, consulte [a redundância do Azure Storage](../storage/common/storage-redundancy.md).
* **Porta-chaves**: O Cofre de Chaves proporciona uma elevada disponibilidade por defeito e não requer nenhuma ação do utilizador.  Consulte [a disponibilidade e redundância do Cofre da Chave Azure.](../key-vault/general/disaster-recovery-guidance.md)
* **Registo do contentor**: Escolha a opção de registo Premium para a geo-replicação. Ver [Geo-replicação no Registo de Contentores Azure](../container-registry/container-registry-geo-replication.md).
* **Insights de Aplicação**: Os Insights de Aplicação não fornecem configurações de alta disponibilidade. Para ajustar o período de retenção de dados e detalhes, consulte [a recolha, retenção e armazenamento de dados em Insights de Aplicação](../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Recursos de cálculo

Certifique-se de configurar as definições de alta disponibilidade de cada recurso, referindo-se à seguinte documentação:

* **Serviço Azure Kubernetes**: Consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres no Serviço Azure Kubernetes (AKS)](../aks/operator-best-practices-multi-region.md) e [Crie um cluster Azure Kubernetes Service (AKS) que utilize zonas de disponibilidade.](../aks/availability-zones.md) Se o cluster AKS foi criado utilizando o Azure Machine Learning Studio, SDK ou CLI, a alta disponibilidade inter-região não é suportada.
* **Azure Databricks**: Consulte [a recuperação regional de desastres para os clusters Azure Databricks](/azure/databricks/scenarios/howto-regional-disaster-recovery).
* **Instâncias do Contentor**: Um orquestrador é responsável pelo fracasso. Ver [Instâncias do Contentor Azure e orquestradores de contentores](../container-instances/container-instances-orchestrator-relationship.md).
* **HDInsight**: Consulte [serviços de alta disponibilidade suportados pela Azure HDInsight](../hdinsight/hdinsight-high-availability-components.md).

## <a name="additional-data-stores"></a>Lojas de dados adicionais

Certifique-se de configurar as definições de alta disponibilidade de cada recurso, referindo-se à seguinte documentação:

* **Recipiente Azure Blob / Ficheiros Azure / Data Lake Storage Gen2**: O mesmo que o armazenamento predefinido.
* **Data Lake Storage Gen1**: Consulte [a alta disponibilidade e orientação de recuperação de desastres para data lake storage gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md).
* **Base de Dados SQL**: Consulte [alta disponibilidade para Azure SQL Database e SQL Managed Instance](../azure-sql/database/high-availability-sla.md).
* **Base de Dados Azure para PostgreSQL**: Ver [conceitos de alta disponibilidade na Base de Dados Azure para PostgreSQL - Servidor Único](../postgresql/concepts-high-availability.md).
* **Azure Database for MySQL**: Consulte [a continuidade do negócio na Base de Dados Azure para o MySQL](../mysql/concepts-business-continuity.md).
* **Sistema de ficheiros Azure Databricks**: Consulte [a recuperação regional de desastres para os clusters Azure Databricks](/azure/databricks/scenarios/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Se fornecer a sua própria chave gerida pelo cliente para implementar um espaço de trabalho Azure Machine Learning, a Azure Cosmos DB também está alojada dentro da sua subscrição. Nesse caso, é responsável por configurar as suas definições de alta disponibilidade. Ver [Alta disponibilidade com Azure Cosmos DB](../cosmos-db/high-availability.md).

## <a name="next-steps"></a>Passos seguintes

Para implementar a Azure Machine Learning com recursos associados às suas definições de alta disponibilidade, utilize um [modelo de Gestor de Recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).