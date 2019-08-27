---
title: Serviços vinculados no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre os serviços vinculados no Data Factory. Vínculos de dados de computação/armazenamento de serviços vinculados para data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: 904e063ae64a971de7f34fbfac63b7679f3bc363
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019944"
---
# <a name="linked-services-in-azure-data-factory"></a>Serviços vinculados no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-datasets-linked-services.md)

Este artigo descreve quais serviços vinculados são, como eles são definidos no formato JSON e como eles são usados em pipelines de Azure Data Factory.

Se você for novo no Data Factory, consulte [introdução ao Azure data Factory](introduction.md) para obter uma visão geral.

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. Um **pipeline** é um agrupamento lógico de **atividades** que juntos executam uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um SQL Server local para o armazenamento de BLOBs do Azure. Em seguida, você pode usar uma atividade do hive que executa um script do hive em um cluster do Azure HDInsight para processar dados do armazenamento de BLOBs para produzir dados de saída. Por fim, você pode usar uma segunda atividade de cópia para copiar os dados de saída para o Azure SQL Data Warehouse, sobre quais soluções de relatório de business intelligence (BI) são criadas. Para obter mais informações sobre pipelines e atividades, consulte [pipelines e atividades](concepts-pipelines-activities.md) em Azure data Factory.

Agora, um **DataSet** é uma exibição nomeada de dados que simplesmente aponta ou faz referência aos dados que você deseja usar em suas **atividades** como entradas e saídas.

Antes de criar um conjunto de dados, você deve criar um **serviço vinculado** para vincular seu armazenamento de data ao data Factory. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Imagine dessa forma; o DataSet representa a estrutura dos dados dentro dos armazenamentos de dados vinculados e o serviço vinculado define a conexão com a fonte de dados. Por exemplo, um serviço vinculado do armazenamento do Azure vincula uma conta de armazenamento ao data factory. Um conjunto de dados de blob do Azure representa o contêiner de BLOB e a pasta dentro dessa conta de armazenamento do Azure que contém os blobs de entrada a serem processados.

Aqui está um cenário de exemplo. Para copiar dados de um armazenamento de BLOBs para um SQL Database, você cria dois serviços vinculados: Armazenamento do Azure e banco de dados SQL do Azure. Em seguida, crie dois conjuntos de valores: Conjunto de dados de BLOBs do Azure (que se refere ao serviço vinculado do armazenamento do Azure) e ao DataSet da tabela SQL do Azure (que se refere ao serviço vinculado do banco de dados SQL do Azure). Os serviços vinculados de armazenamento do Azure e banco de dados SQL do Azure contêm cadeias de conexão que Data Factory usa em tempo de execução para se conectar ao armazenamento do Azure e ao banco de dados SQL do Azure, respectivamente O conjunto de dados de blob do Azure especifica o contêiner de BLOB e a pasta de BLOB que contém os blobs de entrada no armazenamento de BLOBs. O conjunto de dados da tabela SQL do Azure especifica a tabela SQL em seu banco de dado SQL para a qual os dados serão copiados.

O diagrama a seguir mostra as relações entre pipeline, atividade, conjunto de serviços e serviço vinculado no Data Factory:

![Relação entre pipeline, Activity, DataSet, serviços vinculados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Serviço vinculado JSON
Um serviço vinculado no Data Factory é definido no formato JSON da seguinte maneira:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

A tabela a seguir descreve as propriedades no JSON acima:

Propriedade | Descrição | Requerido |
-------- | ----------- | -------- |
name | Nome do serviço vinculado. Consulte [regras de nomenclatura de Azure data Factory](naming-rules.md). |  Sim |
type | Tipo do serviço vinculado. Por exemplo: AzureStorage (armazenamento de dados) ou AzureBatch (computação). Consulte a descrição para typeproperties. | Sim |
typeProperties | As propriedades de tipo são diferentes para cada armazenamento de dados ou computação. <br/><br/> Para os tipos de armazenamento de dados com suporte e suas propriedades de tipo, consulte a tabela de [tipos de conjunto](concepts-datasets-linked-services.md#dataset-type) de dado neste artigo. Navegue até o artigo conector do repositório de dados para saber mais sobre as propriedades de tipo específicas para um armazenamento de dados. <br/><br/> Para os tipos de computação com suporte e suas propriedades de tipo, consulte [Serviços vinculados de computação](compute-linked-services.md). | Sim |
connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Você pode usar os Integration Runtime Azure Integration Runtime ou auto-hospedado (se o armazenamento de dados estiver localizado em uma rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não

## <a name="linked-service-example"></a>Exemplo de serviço vinculado
O serviço vinculado a seguir é um serviço vinculado do armazenamento do Azure. Observe que o tipo é definido como AzureStorage. As propriedades de tipo para o serviço vinculado do armazenamento do Azure incluem uma cadeia de conexão. O serviço de Data Factory usa essa cadeia de conexão para se conectar ao armazenamento de dados em tempo de execução.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Criar serviços ligados
Você pode criar serviços vinculados usando uma destas ferramentas ou SDKs: [API .net](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [API REST](quickstart-create-data-factory-rest-api.md), Azure Resource Manager modelo e portal do Azure

## <a name="data-store-linked-services"></a>Serviços vinculados do repositório de dados
A conexão a repositórios de dados pode ser encontrada em nossos armazenamentos de [dados e formatos com suporte](copy-activity-overview.md#supported-data-stores-and-formats). Referencie a lista para propriedades de conexão específicas necessárias para diferentes repositórios.

## <a name="data-store-supported-activities"></a>Atividades com suporte do repositório de dados

[!INCLUDE [Connector-activity support matrix](../../includes/connector-activity-support-matrix.md)]

## <a name="compute-linked-services"></a>Serviços ligados de computação
Faça referência a [ambientes de computação com suporte](compute-linked-services.md) para obter detalhes sobre diferentes ambientes de computação aos quais você pode se conectar do data Factory, bem como as diferentes configurações.

## <a name="next-steps"></a>Passos Seguintes
Consulte o tutorial a seguir para obter instruções passo a passo para criar pipelines e conjuntos de valores usando uma dessas ferramentas ou SDKs.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Início rápido: criar um data factory usando o PowerShell](quickstart-create-data-factory-powershell.md)
- [Início rápido: criar um data factory usando a API REST](quickstart-create-data-factory-rest-api.md)
- [Início rápido: criar um data factory usando portal do Azure](quickstart-create-data-factory-portal.md)
