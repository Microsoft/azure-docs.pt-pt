---
title: Mover dados de tabela da Web com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre como mover dados de uma tabela numa página da Web com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5b84e49bac35ef8fc9a6c8c7ca90bfd6048dc1c4
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839601"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Mover dados de uma origem de tabela da Web com o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory, que está a utilizar:"]
> * [Versão 1](data-factory-web-table-connector.md)
> * [Versão 2 (versão atual)](../connector-web-table.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [conector de tabela da Web no V2](../connector-web-table.md).

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para mover dados de uma tabela numa página da Web para um arquivo de dados sink suportado. Este artigo baseia-se a [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo apresenta uma visão geral de movimento de dados com a atividade de cópia e a lista dos arquivos de dados suportados como origens/sinks.

Atualmente, o Data factory suporta apenas mover dados de uma tabela de Web para outros arquivos de dados, mas não mover dados de outros dados de arquivos para um destino de tabela da Web.

> [!IMPORTANT]
> Este conector Web atualmente suporta apenas extrair conteúdo de tabela de uma página HTML. Para obter dados de um ponto de final HTTP/s, utilize [conector HTTP](data-factory-http-connector.md) em vez disso.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de tabela da Web, tem de configurar um Runtime de integração autoalojado (também conhecido como o Data Management Gateway) e configurar o `gatewayName` serviço ligado de propriedade no coletor. Por exemplo, para copiar a partir da tabela de Web para o armazenamento de Blobs do Azure, configure o serviço ligado do armazenamento do Azure como o seguinte:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move os dados de um arquivo de dados do Cassandra no local através de APIs/ferramentas diferentes. 

- A maneira mais fácil para criar um pipeline é utilizar o **Assistente para copiar**. Consulte [Tutorial: Criar um pipeline com o Assistente para copiar](data-factory-copy-data-wizard-tutorial.md) para um rápido passo a passo sobre como criar um pipeline com o Assistente para copiar dados. 
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo Azure Resource Manager**, **.NET API**, e **REST API**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se usar as ferramentas ou APIs, que execute os seguintes passos para criar um pipeline que move os dados de um arquivo de dados de origem para um arquivo de dados de sink:

1. Crie **serviços ligados** para ligar a dados de entrada e saídos armazena à fábrica de dados.
2. Crie **conjuntos de dados** para representar os dados de entrada e saídos da operação de cópia. 
3. Criar uma **pipeline** com uma atividade de cópia que usa um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, definições de JSON para estas entidades do Data Factory (serviços ligados, conjuntos de dados e pipeline) são criadas automaticamente para. Ao utilizar ferramentas/APIs (exceto a .NET API), define essas entidades do Data Factory com o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são utilizadas para copiar dados de uma tabela de web, consulte [exemplo de JSON: Copiar dados de tabela da Web para BLOBs do Azure](#json-example-copy-data-from-web-table-to-azure-blob) seção deste artigo. 

As secções seguintes fornecem detalhes sobre as propriedades JSON, que são utilizadas para definir entidades do Data Factory específicas a uma tabela de Web:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece uma descrição para elementos JSON específicos ao serviço ligado da Web.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| type |A propriedade de tipo tem de ser definida como: **Web** |Sim |
| url |URL para a origem da Web |Sim |
| authenticationType |Anónimo. |Sim |

### <a name="using-anonymous-authentication"></a>Utilizar a autenticação anónima

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [criar conjuntos de dados](data-factory-create-datasets.md) artigo. Seções, como a estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

O **typeProperties** secção é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Os typeProperties secção para o conjunto de dados do tipo **WebTable** tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type |Tipo de conjunto de dados. tem de ser definido como **WebTable** |Sim |
| path |Um URL relativo ao recurso que contém a tabela. |Não. Quando o caminho não for especificado, é utilizado apenas o URL especificado na definição do serviço ligado. |
| index |O índice da tabela no recurso. Ver [Get índice de uma tabela numa página HTML](#get-index-of-a-table-in-an-html-page) secção para obter passos para obter o índice de uma tabela numa página HTML. |Sim |

**Example:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [criar Pipelines](data-factory-create-pipelines.md) artigo. Propriedades, tais como o nome, descrição, entrada e saída de tabelas e a política estão disponíveis para todos os tipos de atividades.

Ao passo que, propriedades disponíveis na secção typeProperties da atividade variar de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam consoante os tipos de origens e sinks.

Atualmente, quando a origem na atividade de cópia é do tipo **WebSource**, não existem propriedades adicionais são suportadas.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Exemplo JSON: Copiar dados de tabela da Web para BLOBs do Azure
O exemplo a seguir mostra:

1. Um serviço ligado do tipo [Web](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Entrada [conjunto de dados](data-factory-create-datasets.md) do tipo [WebTable](#dataset-properties).
4. Uma saída [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. R [pipeline](data-factory-create-pipelines.md) com a atividade de cópia que utiliza [WebSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma tabela de Web para um blob do Azure a cada hora. As propriedades JSON utilizadas nestes exemplos são descritas nas seções a seguir os exemplos.

O exemplo a seguir mostra como copiar dados de uma tabela de Web para um blob do Azure. No entanto, os dados podem ser copiados diretamente para qualquer um dos sinks indicados na [atividades de movimento de dados](data-factory-data-movement-activities.md) artigo através da atividade de cópia no Azure Data Factory.

**Serviço ligado do Web** este exemplo utiliza o serviço Web vinculado com a autenticação anónima. Ver [serviço ligado do Web](#linked-service-properties) secção para diferentes tipos de autenticação, pode utilizar.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Serviço ligado do Armazenamento do Azure**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**Conjunto de dados de entrada WebTable** definição **externos** para **verdadeiro** informa o serviço Data Factory, que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

> [!NOTE]
> Ver [Get índice de uma tabela numa página HTML](#get-index-of-a-table-in-an-html-page) secção para obter passos para obter o índice de uma tabela numa página HTML.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Conjunto de dados dos Blobs do Azure**

Os dados são escritos para um blob novo a cada hora (frequência: hora, intervalo: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Pipeline com atividade de cópia**

O pipeline contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e é agendada para ser executada a cada hora. No pipeline de definição de JSON, o **origem** tipo está definido como **WebSource** e **sink** tipo está definido como **BlobSink**.

Ver as propriedades de tipo de WebSource para obter a lista das propriedades compatíveis com o WebSource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Obter o índice de uma tabela numa página HTML
1. Inicie **Excel 2016** e mude para o **dados** separador.  
2. Clique em **nova consulta** na barra de ferramentas, aponte para **de outras origens** e clique em **da Web**.

    ![Menu de consulta de energia](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. Na **da Web** caixa de diálogo, introduza **URL** que usaria em ligado JSON do serviço (por exemplo: https://en.wikipedia.org/wiki/) , juntamente com o caminho tem de especificar para o conjunto de dados (por exemplo: AFI % 27s_100_Years... 100_Movies) e clique em **OK**.

    ![Na caixa de diálogo da Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL utilizado neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se vir **conteúdo da Web de acesso** caixa de diálogo caixa, selecione o direito **URL**, **autenticação**e clique em **Connect**.

   ![Caixa de diálogo do conteúdo do acesso Web](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Clique num **tabela** item na vista de árvore para ver o conteúdo da tabela e, em seguida, clique em **editar** na parte inferior.  

   ![Caixa de diálogo navegador](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Na **Editor de consultas** janela, clique em **Editor avançado** botão na barra de ferramentas.

    ![Botão de Editor avançado](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo Editor avançado, o número junto a "Origem" é o índice.

    ![Editor - avançado de índice](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Se estiver a utilizar o Excel 2013, utilize [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) para obter o índice. Ver [ligar a uma página da web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artigo para obter detalhes. Os passos são semelhantes se estiver a utilizar [Microsoft Power BI para Desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Para mapear colunas do conjunto de dados de origem para colunas do conjunto de dados de sink, consulte [mapeamento de colunas do conjunto de dados no Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e Otimização
Ver [desempenho de atividade de cópia e guia de ajuste](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chave a que um impacto no desempenho de movimento de dados (atividade de cópia) no Azure Data Factory e diversas maneiras para otimizá-lo.
