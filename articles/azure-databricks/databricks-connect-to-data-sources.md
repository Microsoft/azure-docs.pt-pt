---
title: 'Ligar a diferentes fontes de dados de Tecidos Azure '
description: Saiba como ligar-se à Base de Dados Azure SQL, Azure Data Lake Store, armazenamento de blob, Cosmos DB, Event Hubs e Azure SQL Data Warehouse da Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 79a821a4c8fe4cb2d048f0dcb0a6e091462a1779
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548797"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Ligar a fontes de dados de Tecidos Azure

Este artigo fornece ligações a todas as diferentes fontes de dados do Azure que podem ser ligadas aos Bricks Azure. Siga os exemplos nestes links para extrair dados das fontes de dados do Azure (por exemplo, Armazenamento De Blob Azure, Hubs de Eventos Azure, etc.) num cluster de Databricks Azure, e executar trabalhos analíticos neles. 

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter um espaço de trabalho Azure Databricks e um cluster Spark. Siga as instruções em [Get started com Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Fontes de dados de Tijolos de Dados Azure

A lista seguinte fornece as fontes de dados em Azure que pode utilizar com os Bricks Azure. Para obter uma lista completa de fontes de dados que podem ser utilizadas com os Bricks Azure, consulte as fontes de [dados dos Tijolos de Dados Do Azure](/azure/databricks/data/data-sources/index).

- [Base de dados Azure SQL](/azure/databricks/data/data-sources/sql-databases)

    Este link fornece a API DataFrame para a ligação às bases de dados SQL utilizando jDBC e como controlar o paralelismo das leituras através da interface JDBC. Este tópico fornece exemplos detalhados usando a API Scala, com exemplos abreviados python e Spark SQL no final.
- [Armazenamento do Azure Data Lake](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Este link fornece exemplos sobre como usar o principal de serviço de Diretório Ativo Azure para autenticar com o Armazenamento do Lago De Dados Azure. Também fornece instruções sobre como aceder aos dados no Armazenamento do Lago De Dados Azure a partir de Tijolos de Dados Azure.

- [Armazenamento Azure Blob](/azure/databricks/data/data-sources/azure/azure-storage)

    Este link fornece exemplos sobre como aceder diretamente ao Armazenamento De Azure Blob a partir de Tijolos de Dados Azure utilizando a chave de acesso ou o SAS para um determinado recipiente. O link também fornece informações sobre como aceder ao Armazenamento De Azure Blob a partir de Tijolos de Dados Azure usando a API RDD.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Este link fornece instruções sobre como usar o [conector Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) da Azure Databricks para aceder a dados em Azure Cosmos DB.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Este link fornece instruções sobre como utilizar o [conector Azure Event Hubs Spark](https://github.com/Azure/azure-event-hubs-spark) a partir de Databricks Azure para aceder a dados em Hubs de Eventos Azure.

- [Armazém de dados Azure SQL](/azure/synapse-analytics/sql-data-warehouse/)

    Este link fornece instruções sobre como utilizar o conector Azure SQL Data Warehouse para ligar a partir de Tijolos de Dados Azure.
    

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre fontes de onde pode importar dados para Os Databricks Do Azure, consulte fontes de [dados para os Tijolos de Dados Do Azure](/azure/databricks/data/data-sources/index).


