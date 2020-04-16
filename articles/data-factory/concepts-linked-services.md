---
title: Serviços ligados no Azure Data Factory
description: Conheça os serviços ligados na Data Factory. Serviços ligados ligam computações/lojas de dados à fábrica de dados.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: a6002ed173ca5358df4257f4c8b41c88bcf60ad8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418385"
---
# <a name="linked-services-in-azure-data-factory"></a>Serviços ligados no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve quais são os serviços ligados, como são definidos em formato JSON, e como são usados em oleodutos Azure Data Factory.

Se é novo na Data Factory, consulte [introdução à Azure Data Factory](introduction.md) para uma visão geral.

## <a name="overview"></a>Descrição geral

Uma fábrica de dados pode ter um ou mais pipelines. Um **oleoduto** é um agrupamento lógico de **atividades** que, em conjunto, desempenham uma tarefa. As atividades num pipeline definem as ações a efetuar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados de um SQL Server no local para o armazenamento de Blob Azure. Em seguida, você pode usar uma atividade da Colmeia que executa um script hive em um cluster Azure HDInsight para processar dados do armazenamento Blob para produzir dados de saída. Finalmente, poderá utilizar uma segunda atividade de cópia para copiar os dados de saída para o Azure SQL Data Warehouse, para além dos quais são construídas soluções de reporte de inteligência empresarial (BI). Para mais informações sobre oleodutos e atividades, consulte [Pipelines e atividades](concepts-pipelines-activities.md) na Azure Data Factory.

Agora, um conjunto de **dados** é uma visão nomeada de dados que simplesmente aponta ou refere os dados que pretende utilizar nas suas **atividades** como inputs e saídas.

Antes de criar um conjunto de dados, deve criar um **serviço ligado** para ligar a sua loja de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Pense desta forma; o conjunto de dados representa a estrutura dos dados dentro das lojas de dados ligadas, e o serviço ligado define a ligação à fonte de dados. Por exemplo, um serviço ligado ao Armazenamento Azure liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados Azure Blob representa o recipiente blob e a pasta dentro dessa conta de Armazenamento Azure que contém as bolhas de entrada a serem processadas.

Aqui está um cenário de amostra. Para copiar dados do armazenamento Blob para uma Base de Dados SQL, cria dois serviços ligados: Armazenamento Azure e Base de Dados Azure SQL. Em seguida, crie dois conjuntos de dados: conjunto de dados Azure Blob (que se refere ao serviço ligado ao Armazenamento Azure) e conjunto de dados da tabela Azure SQL (que se refere ao serviço ligado à Base de Dados Azure SQL). Os serviços ligados ao Armazenamento Azure e à Base de Dados Azure SQL contêm cordas de ligação que a Data Factory utiliza no prazo de execução para se ligarem ao seu Armazenamento Azure e à base de dados Azure SQL, respectivamente. O conjunto de dados Azure Blob especifica o recipiente blob e a pasta blob que contém as bolhas de entrada no seu armazenamento Blob. O conjunto de dados da tabela Azure SQL especifica a tabela SQL na sua Base de Dados SQL a que os dados serão copiados.

O diagrama seguinte mostra as relações entre o oleoduto, a atividade, o conjunto de dados e o serviço ligado na Fábrica de Dados:

![Relação entre o gasoduto, a atividade, o conjunto de dados, os serviços ligados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Serviço ligado JSON

Um serviço ligado na Data Factory é definido no formato JSON da seguinte forma:

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

A tabela seguinte descreve propriedades no JSON acima:

Propriedade | Descrição | Necessário |
-------- | ----------- | -------- |
nome | Nome do serviço ligado. Ver [Azure Data Factory - Regras de nomeação.](naming-rules.md) |  Sim |
tipo | Tipo de serviço ligado. Por exemplo: Armazenamento Azure (loja de dados) ou AzureBatch (computação). Consulte a descrição do typeProperties. | Sim |
typeProperties | As propriedades do tipo são diferentes para cada loja de dados ou cálculo. <br/><br/> Para os tipos de loja de dados suportados e suas propriedades de tipo, consulte a tabela do tipo de conjunto de [dados](concepts-datasets-linked-services.md#dataset-type) neste artigo. Navegue para o artigo de conector da loja de dados para saber sobre propriedades do tipo específicas de uma loja de dados. <br/><br/> Para os tipos de cálculo suportados e suas propriedades de tipo, consulte [serviços ligados à Compute.](compute-linked-services.md) | Sim |
connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. Pode utilizar o Tempo de Execução de Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não especificado, utiliza o tempo de funcionar de integração azure padrão. | Não

## <a name="linked-service-example"></a>Exemplo de serviço ligado

O seguinte serviço ligado é um serviço ligado ao Armazenamento Azure. Note que o tipo está definido para o Armazenamento Azure. As propriedades do tipo para o serviço ligado ao Armazenamento Azure incluem uma cadeia de ligação. O serviço Data Factory utiliza esta cadeia de ligação para se ligar à loja de dados em tempo de execução.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Criar serviços ligados

Pode criar serviços ligados utilizando uma destas ferramentas ou SDKs: [.NET API,](quickstart-create-data-factory-dot-net.md) [PowerShell,](quickstart-create-data-factory-powershell.md) [REST API,](quickstart-create-data-factory-rest-api.md)Modelo de Gestor de Recursos Azure e portal Azure

## <a name="data-store-linked-services"></a>Serviços ligados à loja de dados

Pode encontrar a lista de lojas de dados suportadas pela Data Factory a partir de artigo de visão geral do [conector.](copy-activity-overview.md#supported-data-stores-and-formats) Clique numa loja de dados para saber as propriedades de ligação suportadas.

## <a name="compute-linked-services"></a>Serviços ligados de computação

Ambientes [computacionais de](compute-linked-services.md) referência suportados para detalhes sobre diferentes ambientes de computação a que pode ligar a partir da sua fábrica de dados, bem como as diferentes configurações.

## <a name="next-steps"></a>Passos seguintes

Consulte o seguinte tutorial para obter instruções passo a passo para a criação de oleodutos e conjuntos de dados utilizando uma destas ferramentas ou SDKs.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Quickstart: criar uma fábrica de dados usando o PowerShell](quickstart-create-data-factory-powershell.md)
- [Quickstart: criar uma fábrica de dados utilizando a Rest API](quickstart-create-data-factory-rest-api.md)
- [Quickstart: criar uma fábrica de dados utilizando o portal Azure](quickstart-create-data-factory-portal.md)
