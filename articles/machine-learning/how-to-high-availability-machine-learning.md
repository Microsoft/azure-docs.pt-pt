---
title: Como aumentar a resiliência
titleSuffix: Azure Machine Learning
description: Aprenda a tornar os seus recursos relacionados com a Azure Machine Learning mais resistentes a interrupções utilizando uma configuração de alta disponibilidade.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 07/16/2020
ms.openlocfilehash: 4f2bf239e1157f5c927c4449857ad5f7793ccb49
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098905"
---
# <a name="increase-the-resiliency-of-azure-machine-learning"></a>Aumentar a resiliência da Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a tornar os seus recursos relacionados com a Azure Machine Learning mais resistentes utilizando configurações de alta disponibilidade. Os serviços Azure de que a Azure Machine Learning depende podem ser configurados para uma elevada disponibilidade. Este artigo fornece informações sobre quais os serviços que podem ser configurados para uma elevada disponibilidade, e ligações à informação sobre a configuração destes recursos.

> [!NOTE]
> A Azure Machine Learning em si não oferece uma opção de recuperação de desastres.

## <a name="understand-azure-services-for-azure-machine-learning"></a>Compreender os serviços da Azure para a aprendizagem automática Azure

A Azure Machine Learning depende de vários serviços Azure e tem várias camadas. Alguns deles estão a provisionados na sua subscrição (cliente). É responsável pela configuração de alta disponibilidade destes serviços. Alguns são criados numa subscrição da Microsoft, e são geridos pela Microsoft.

* **Azure Machine Learning Infrastructure**: Ambiente gerido pela Microsoft para o espaço de trabalho Azure Machine Learning.

* **Recursos Associados**: Recursos a provisionados na sua subscrição durante a criação do espaço de trabalho Azure Machine Learning. Eles incluem Azure Storage, Azure Key Vault, Azure Container Registry (ACR) e App Insights. Você é responsável por configurações de alta disponibilidade para estes recursos.
  * O armazenamento predefinido tem dados como modelo, dados de registo de treino e conjunto de dados.
  * Key Vault tem credenciais para armazenamento, ACR, lojas de dados.
  * A ACR tem imagem de estivador para o ambiente de treino e inferencing.
  * App Insights destina-se a monitorizar a aprendizagem automática Azure.

* **Recursos de Cálculo**: Recursos que cria após a implantação do espaço de trabalho. Por exemplo, você pode criar um conjunto de computação ou compute para treinar um modelo de aprendizagem automática.
  * Compute Instance e Compute Cluster: Ambiente de desenvolvimento de modelos gerido pela Microsoft.
  * Outros Recursos: São os recursos de computação que podem ser anexados à Azure Machine Learning, como o Azure Kubernetes Service (AKS), Azure Databricks, Azure Container Instances (ACI) e HDInsight. Você é responsável pela definição de alta disponibilidade.

* **Data Stores adicionais**: Azure Machine Learning pode montar lojas de dados adicionais, tais como Azure Storage, Azure Data Lake Storage e Azure SQL Database para dados de formação.  Eles estão dentro da sua subscrição e você é responsável pela configuração de alta disponibilidade.

A tabela a seguir mostra quais os serviços geridos pela Microsoft, que são geridos por si, e que estão altamente disponíveis por padrão:

| Serviço | Gerido por | HA por padrão |
| ----- | ----- | ----- |
| **Infraestrutura de aprendizagem automática Azure** | Microsoft | |
| **Recursos Associados** |
| Storage do Azure | O utilizador | |
| Azure Key Vault | O utilizador | ✓ |
| Registo de Contentores do Azure | O utilizador | |
| Application Insights | O utilizador | ND |
| **Recursos computacional** |
| Instância computacional | Microsoft |  |
| Compute Cluster | Microsoft |  |
| Outros recursos, como o Serviço Azure Kubernetes, <br>Azure Databricks, Azure Container Instance, Azure HDInsight | O utilizador |  |
| **Lojas de dados adicionais,** tais como Azure Storage, Azure SQL Database,<br> Base de Dados Azure para PostgreSQL, Base de Dados Azure para MySQL, <br>Sistema de ficheiros Azure Databricks | O utilizador | |

Utilize as informações no resto deste documento para conhecer as ações necessárias para tornar cada um destes serviços altamente disponível.

## <a name="associated-resources"></a>Recursos Associados

> [!IMPORTANT]
> A Azure Machine Learning não suporta a falha da conta de armazenamento predefinido utilizando o armazenamento geo-redundante (GRS) ou o armazenamento redundante em geo-zona (GZRS) ou o armazenamento geo-redundante de acesso à leitura (RA-GRS) ou o armazenamento de zona de acesso de leitura -zona-redundante (RA-GZRS).

Certifique-se de que a definição de alta disponibilidade de cada recurso com informações abaixo.

* **Armazenamento azul**: Para configurar uma definição de alta disponibilidade, consulte [a redundância do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy).
* **Azure Key Vault**: Fornece um serviço de alta disponibilidade predefinido e não é necessária nenhuma ação do utilizador.  Consulte [a disponibilidade e redundância do Cofre da Chave Azure.](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)
* **Registo do contentor Azure**: Escolha SKU Premium para geo-replicação. Ver [Geo-replicação no Registo de Contentores Azure](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
* **Insights de Aplicação**: Não fornece uma definição de alta disponibilidade. Pode ajustar o período de retenção de dados e detalhes na [recolha, retenção e armazenamento de dados em Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#how-long-is-the-data-kept).

## <a name="compute-resources"></a>Recursos computacional

Certifique-se de que a definição de alta disponibilidade de cada recurso com a documentação abaixo.

* **Serviço Azure Kubernetes**: Consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres no Serviço Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) e [Crie um cluster Azure Kubernetes Service (AKS) que utilize zonas de disponibilidade.](https://docs.microsoft.com/azure/aks/availability-zones) Se o cluster AKS foi criado pela Azure Machine Learning (utilizando estúdio, SDK ou ML CLI), a alta disponibilidade entre regiões não é suportada.
* **Azure Databricks**: Consulte [a recuperação regional de desastres para os clusters Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).
* **Instância do recipiente Azure**: O orquestrador ACI é responsável pelo fracasso. Ver [Instâncias do Contentor Azure e orquestradores de contentores](https://docs.microsoft.com/azure/container-instances/container-instances-orchestrator-relationship).
* **Azure HDInsight**: Consulte [serviços de alta disponibilidade suportados pela Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-components).

## <a name="additional-data-stores"></a>Lojas de Dados Adicionais

Certifique-se de que a definição de alta disponibilidade de cada recurso com a documentação abaixo.

* **Azure Blob Container / Azure File Share / Azure Data Lake Gen2**: O mesmo que o armazenamento padrão.
* **Azure Data Lake Gen1**: Consulte[a orientação de recuperação de desastres para obter dados na Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-disaster-recovery-guidance).
* **Base de Dados Azure SQL**: Ver [Base de Dados SQL de Alta Disponibilidade e Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability).
* **Base de Dados Azure para PostgreSQL**: Ver [conceitos de alta disponibilidade na Base de Dados Azure para PostgreSQL - Servidor Único](https://docs.microsoft.com/azure/postgresql/concepts-high-availability).
* **Azure Database for MySQL**: Consulte [a continuidade do negócio na Base de Dados Azure para o MySQL](https://docs.microsoft.com/azure/mysql/concepts-business-continuity).
* **Databricks File System**: Consulte [a recuperação regional de desastres para os clusters Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery).

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Se fornecer a sua própria chave (chave gerida pelo cliente) para implantar o espaço de trabalho Azure Machine Learning, a Cosmos DB também é abastecada dentro da sua subscrição. Nesse caso, é responsável pela sua elevada disponibilidade. Ver [Alta disponibilidade com Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability)

## <a name="next-steps"></a>Passos seguintes

Para implementar a Azure Machine Learning com recursos associados com as suas Definições de alta disponibilidade, utilize um [modelo de Gestor de Recursos Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced).