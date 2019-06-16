---
title: Serviços no Azure Data Factory ligados | Documentos da Microsoft
description: Saiba mais sobre os serviços ligados no Data Factory. Serviços ligados ligam os arquivos de dados/computação à fábrica de dados.
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
ms.openlocfilehash: ba2041495e1e3c63ee322a0b748753ad6cb68914
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870136"
---
# <a name="linked-services-in-azure-data-factory"></a>Serviços ligados no Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-datasets-linked-services.md)

Este artigo descreve quais os serviços ligados são, como são definidas no formato JSON, e como são utilizados no Azure Data Factory pipelines.

Se estiver familiarizado com o Data Factory, veja [introdução ao Azure Data Factory](introduction.md) para uma descrição geral.

## <a name="overview"></a>Descrição geral
Uma fábrica de dados pode ter um ou mais pipelines. R **pipeline** é um agrupamento lógico de **atividades** que em conjunto, realizam uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de um servidor de SQL no local para o armazenamento de Blobs do Azure. Em seguida, poderá utilizar uma atividade do Hive que executa um script do Hive num cluster do Azure HDInsight para processar dados de armazenamento de BLOBs para produzir os dados de saída. Por fim, pode usar uma segunda atividade de cópia para copiar os dados de saída para o Azure SQL Data Warehouse, sobre o relatórios de business intelligence (BI) soluções foram concebidas. Para obter mais informações sobre os pipelines e atividades, consulte [Pipelines e atividades](concepts-pipelines-activities.md) no Azure Data Factory.

Agora, um **conjunto de dados** é uma vista com o nome de dados que simplesmente pontos ou faz referência aos dados que pretende utilizar no seu **atividades** como entradas e saídas.

Antes de criar um conjunto de dados, tem de criar uma **serviço ligado** para ligar o seu armazenamento de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Pense nesse assunto desta forma; o conjunto de dados representa a estrutura dos dados dentro dos arquivos de dados ligados e o serviço ligado define a ligação à origem de dados. Por exemplo, um armazenamento do Azure ligado serviço liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados de Blobs do Azure representa o contentor de BLOBs e a pasta dentro dessa conta de armazenamento do Azure que contém os blobs de entrada para serem processados.

Eis um cenário de exemplo. Para copiar dados do armazenamento de BLOBs para base de dados SQL, criar dois serviços ligados: Armazenamento do Azure e Azure base de dados SQL. Em seguida, crie dois conjuntos de dados: Conjunto de dados Blob do Azure (que se refere ao serviço ligado do armazenamento do Azure) e o conjunto de dados de tabela SQL do Azure (o que se refere-se para o serviço de base de dados do SQL do Azure ligada). O armazenamento do Azure e serviços de base de dados do SQL Azure ligado contenham cadeias de ligação que o Data Factory utiliza no tempo de execução para ligar para o armazenamento do Azure e a base de dados SQL do Azure, respectivamente. O conjunto de dados de Blobs do Azure Especifica o contentor de BLOBs e a pasta de BLOBs que contém os blobs de entrada no armazenamento de Blobs. O conjunto de dados de tabela SQL do Azure Especifica a tabela SQL na base de dados SQL para o qual os dados estão a ser copiado.

O diagrama seguinte mostra as relações entre o pipeline, atividade, conjunto de dados e serviço ligado no Data Factory:

![Relação entre o pipeline, atividade, conjunto de dados, serviços ligados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Serviço ligado JSON
Um serviço ligado no Data Factory é definido no formato JSON, da seguinte forma:

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

A tabela seguinte descreve as propriedades no JSON acima:

Propriedade | Descrição | Necessário |
-------- | ----------- | -------- |
name | Nome do serviço ligado. Ver [do Azure Data Factory - regras de nomenclatura](naming-rules.md). |  Sim |
type | Tipo de serviço ligado. Por exemplo: AzureStorage (arquivo de dados) ou AzureBatch (computação). Consulte a descrição de typeProperties. | Sim |
typeProperties | As propriedades de tipo são diferentes para cada arquivo de dados ou de computação. <br/><br/> Os dados suportados armazenar tipos e suas propriedades de tipo, consulte a [tipo de conjunto de dados](concepts-datasets-linked-services.md#dataset-type) tabela neste artigo. Navegue para o artigo de conector do arquivo de dados para saber mais sobre as propriedades de tipo específicas para um arquivo de dados. <br/><br/> Para os tipos de computação suportados e as respetivas propriedades de tipo, consulte [serviços ligados de computação](compute-linked-services.md). | Sim |
connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Pode utilizar o Runtime de integração do Azure ou o Runtime de integração autoalojado (se o seu armazenamento de dados está localizado numa rede privada). Se não for especificado, ele usa o padrão do Runtime de integração do Azure. | Não

## <a name="linked-service-example"></a>Exemplo de serviço ligado
O serviço ligado seguinte é um serviço ligado do armazenamento do Azure. Tenha em atenção que o tipo está definido como AzureStorage. As propriedades de tipo para o serviço ligado do armazenamento do Azure incluem uma cadeia de ligação. O serviço Data Factory utiliza esta cadeia de ligação para ligar ao arquivo de dados em tempo de execução.

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
Pode criar serviços ligados ao utilizar uma destas ferramentas ou SDKs: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), modelo do Azure Resource Manager e o portal do Azure

## <a name="data-store-linked-services"></a>Serviços ligados de arquivo de dados
Conexão com fontes de dados pode ser encontrado na nossa [arquivos de dados e formatos suportados](copy-activity-overview.md#supported-data-stores-and-formats). Referência a lista de propriedades de ligação específica necessária para diferentes lojas.

## <a name="compute-linked-services"></a>Serviços ligados de computação
Referência [suportados de ambientes de computação](compute-linked-services.md) para obter detalhes sobre os diferentes ambientes de computação pode ligar a partir da sua fábrica de dados, bem como as configurações diferentes.

## <a name="next-steps"></a>Passos Seguintes
Veja o tutorial seguinte para obter instruções passo a passo para a criação de pipelines e conjuntos de dados ao utilizar uma destas ferramentas ou SDKs.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Início rápido: criar uma fábrica de dados com o PowerShell](quickstart-create-data-factory-powershell.md)
- [Início rápido: criar uma fábrica de dados com a REST API](quickstart-create-data-factory-rest-api.md)
- [Início rápido: criar uma fábrica de dados com o portal do Azure](quickstart-create-data-factory-portal.md)
