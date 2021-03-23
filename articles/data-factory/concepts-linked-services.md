---
title: Serviços ligados no Azure Data Factory
description: Conheça os serviços ligados na Data Factory. Serviços ligados ligam computação/lojas de dados à fábrica de dados.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: aaa690a4205951bd251a5230721e34fcb960a3b1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782746"
---
# <a name="linked-services-in-azure-data-factory"></a>Serviços ligados no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-create-datasets.md)
> * [Versão atual](concepts-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve o que são os serviços ligados, como são definidos no formato JSON e como são usados nos oleodutos Azure Data Factory.

Se é novo na Data Factory, consulte [a Introdução à Fábrica de Dados Azure](introduction.md) para uma visão geral.

## <a name="overview"></a>Descrição geral

Uma fábrica de dados pode ter um ou mais pipelines. Um **oleoduto** é um agrupamento lógico de **atividades** que juntos desempenham uma tarefa. As atividades num pipeline definem as ações a executar nos seus dados. Por exemplo, pode utilizar uma atividade de cópia para copiar dados do SQL Server para o armazenamento de Azure Blob. Em seguida, você pode usar uma atividade de Hive que executa um script hive em um cluster Azure HDInsight para processar dados do armazenamento Blob para produzir dados de saída. Finalmente, poderá utilizar uma segunda atividade de cópia para copiar os dados de saída para a Azure Synapse Analytics, além das quais são construídas soluções de reporte de informação de business intelligence (BI). Para obter mais informações sobre os oleodutos e atividades, consulte [Pipelines e atividades](concepts-pipelines-activities.md) na Azure Data Factory.

Agora, um conjunto de **dados** é uma visão nomeada de dados que simplesmente apontam ou referenciam os dados que pretende utilizar nas suas **atividades** como entradas e saídas.

Antes de criar um conjunto de dados, tem de criar um **serviço ligado** para ligar a sua loja de dados à fábrica de dados. Os serviços ligados são muito semelhantes às cadeias de ligação, que definem as informações de ligação necessárias para que o Data Factory se possa ligar a recursos externos. Pense desta forma; o conjunto de dados representa a estrutura dos dados dentro das lojas de dados ligadas, e o serviço ligado define a ligação à fonte de dados. Por exemplo, um serviço ligado ao Azure Storage liga uma conta de armazenamento à fábrica de dados. Um conjunto de dados Azure Blob representa o recipiente blob e a pasta dentro dessa conta de Armazenamento Azure que contém as bolhas de entrada a serem processadas.

Aqui está um cenário de amostra. Para copiar dados do armazenamento blob para uma Base de Dados SQL, cria dois serviços ligados: Azure Storage e Azure SQL Database. Em seguida, crie dois conjuntos de dados: conjunto de dados Azure Blob (que se refere ao serviço ligado ao Armazenamento Azure) e conjunto de dados de tabela Azure SQL (que se refere ao serviço ligado à Base de Dados Azure SQL). Os serviços ligados ao Azure Storage e Azure SQL Database contêm cadeias de ligação que a Data Factory utiliza em tempo de execução para ligar à sua Base de Dados Azure Storage e Azure SQL, respectivamente. O conjunto de dados Azure Blob especifica o recipiente blob e a pasta blob que contém as bolhas de entrada no seu armazenamento Blob. O conjunto de dados da tabela Azure SQL especifica a tabela SQL na sua Base de Dados SQL para a qual os dados devem ser copiados.

O diagrama a seguir mostra as relações entre o pipeline, a atividade, o conjunto de dados e o serviço ligado na Data Factory:

![Relação entre pipeline, atividade, conjunto de dados, serviços ligados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

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

A tabela a seguir descreve propriedades no JSON acima:

Propriedade | Descrição | Necessário |
-------- | ----------- | -------- |
name | Nome do serviço ligado. Ver [Azure Data Factory - Regras de nomeação](naming-rules.md). |  Sim |
tipo | Tipo de serviço ligado. Por exemplo: AzureBlobStorage (data store) ou AzureBatch (computação). Consulte a descrição para tipos de propriedades. | Sim |
typeProperties | As propriedades do tipo são diferentes para cada loja de dados ou cálculo. <br/><br/> Para os tipos de loja de dados suportados e as suas propriedades de tipo, consulte o artigo de visão geral do [conector.](copy-activity-overview.md#supported-data-stores-and-formats) Navegue para o artigo de conector da loja de dados para saber sobre propriedades de tipo específicas para uma loja de dados. <br/><br/> Para os tipos de computação suportados e suas propriedades de tipo, consulte [os serviços ligados ao Compute](compute-linked-services.md). | Sim |
connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. Pode utilizar o Tempo de Execução da Integração Azure ou o Tempo de Execução de Integração Auto-hospedado (se a sua loja de dados estiver localizada numa rede privada). Se não for especificado, utiliza o tempo de execução de integração Azure predefinido. | No

## <a name="linked-service-example"></a>Exemplo de serviço ligado

O seguinte serviço ligado é um serviço ligado ao armazenamento Azure Blob. Note que o tipo está definido para o armazenamento Azure Blob. As propriedades do tipo para o serviço ligado ao armazenamento Azure Blob incluem uma cadeia de ligação. O serviço Data Factory utiliza esta cadeia de ligação para ligar à loja de dados em tempo de execução.

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
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

Os serviços ligados podem ser criados no Azure Data Factory UX através do [centro de gestão](author-management-hub.md) e quaisquer atividades, conjuntos de dados ou fluxos de dados que os refiram.

Pode criar serviços ligados utilizando uma destas ferramentas ou SDKs: [.NET API,](quickstart-create-data-factory-dot-net.md) [PowerShell,](quickstart-create-data-factory-powershell.md) [REST API,](quickstart-create-data-factory-rest-api.md)Azure Resource Manager e Portal Azure.


## <a name="data-store-linked-services"></a>Serviços ligados à loja de dados

Pode encontrar a lista de lojas de dados suportadas pela Data Factory a partir do artigo de visão geral do [conector.](copy-activity-overview.md#supported-data-stores-and-formats) Clique numa loja de dados para saber as propriedades de conexão suportadas.

## <a name="compute-linked-services"></a>Serviços ligados de computação

Ambientes [computativos](compute-linked-services.md) de referência suportados para detalhes sobre diferentes ambientes de computação a que pode ligar a partir da sua fábrica de dados, bem como as diferentes configurações.

## <a name="next-steps"></a>Passos seguintes

Consulte o seguinte tutorial para obter instruções passo a passo para a criação de oleodutos e conjuntos de dados utilizando uma destas ferramentas ou SDKs.

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Início rápido: criar uma fábrica de dados com .NET)
- [Quickstart: criar uma fábrica de dados utilizando o PowerShell](quickstart-create-data-factory-powershell.md)
- [Quickstart: criar uma fábrica de dados utilizando a REST API](quickstart-create-data-factory-rest-api.md)
- [Quickstart: criar uma fábrica de dados utilizando o portal Azure](quickstart-create-data-factory-portal.md)
