---
title: Mover dados da tabela da Web usando Azure Data Factory
description: Saiba mais sobre como mover dados de uma tabela em uma página da Web usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d2ea038c7d7212529185d77a6ba9e64deacb1c9e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927944"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Mover dados de uma fonte de tabela da Web usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-web-table-connector.md)
> * [Versão 2 (versão atual)](../connector-web-table.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector de tabela da Web na v2](../connector-web-table.md).

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para mover dados de uma tabela em uma página da Web para um armazenamento de dados de coletor com suporte. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) que apresenta uma visão geral da movimentação de dados com a atividade de cópia e a lista de armazenamentos de dados com suporte como fontes/coletores.

Atualmente, o data Factory dá suporte apenas à movimentação de dados de uma tabela da Web para outros armazenamentos de dados, mas não à movimentação de dados de outros armazenamentos de dados para um destino de tabela da Web.

> [!IMPORTANT]
> Este conector da Web atualmente dá suporte apenas à extração de conteúdo de tabela de uma página HTML. Para recuperar dados de um ponto de extremidade HTTP/s, use o [conector http](data-factory-http-connector.md) em vez disso.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector de tabela da Web, você precisa configurar um Integration Runtime auto-hospedado (também conhecido como gateway Gerenciamento de Dados) e configurar a propriedade `gatewayName` no serviço vinculado do coletor. Por exemplo, para copiar da tabela da Web para o armazenamento de BLOBs do Azure, configure o serviço vinculado do armazenamento do Azure como o seguinte:

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
Você pode criar um pipeline com uma atividade de cópia que move dados de um armazenamento de dados Cassandra local usando diferentes ferramentas/APIs. 

- A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados. 
- Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia. 

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída. 

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de uma tabela da Web, consulte a seção [exemplo de JSON: copiar dados da tabela da Web para o blob do Azure](#json-example-copy-data-from-web-table-to-azure-blob) deste artigo. 

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para uma tabela da Web:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado da Web.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **Web** |Sim |
| Url |URL para a origem da Web |Sim |
| authenticationType |Modo. |Sim |

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima

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
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeproperties para o conjunto de um do tipo **webtable** tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |Tipo do conjunto de um. deve ser definido como **Webtable** |Sim |
| Caminho |Uma URL relativa para o recurso que contém a tabela. |Não. Quando o caminho não for especificado, somente a URL especificada na definição de serviço vinculado será usada. |
| index |O índice da tabela no recurso. Consulte a seção [obter índice de uma tabela em uma página HTML](#get-index-of-a-table-in-an-html-page) para obter as etapas para obter o índice de uma tabela em uma página HTML. |Sim |

**Exemplo:**

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
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e política estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na seção typeproperties da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Atualmente, quando a origem na atividade de cópia é do tipo **websource**, não há suporte para nenhuma propriedade adicional.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Exemplo de JSON: copiar dados da tabela da Web para o blob do Azure
O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [Web](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [webtable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [websource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de uma tabela da Web para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

O exemplo a seguir mostra como copiar dados de uma tabela da Web para um blob do Azure. No entanto, os dados podem ser copiados diretamente para qualquer um dos coletores declarados no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) usando a atividade de cópia no Azure data Factory.

**Serviço vinculado da Web** Este exemplo usa o serviço vinculado da Web com autenticação anônima. Consulte a seção [serviço vinculado da Web](#linked-service-properties) para diferentes tipos de autenticação que você pode usar.

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

**Conjunto de dados de entrada da tabela** A configuração de **external** como **true** informa ao serviço de data Factory que o dataset é externo ao data Factory e não é produzido por uma atividade no data Factory.

> [!NOTE]
> Consulte a seção [obter índice de uma tabela em uma página HTML](#get-index-of-a-table-in-an-html-page) para obter as etapas para obter o índice de uma tabela em uma página HTML.  
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

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1).

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

O pipeline contém uma atividade de cópia configurada para usar os conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** está definido como **websource** e o tipo de **coletor** está definido como **BlobSink**.

Consulte Propriedades de tipo de fonte da websource para obter a lista de propriedades com suporte pela fonte de origem.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Obter o índice de uma tabela em uma página HTML
1. Inicie o **Excel 2016** e alterne para a guia **dados** .  
2. Clique em **nova consulta** na barra de ferramentas, aponte para **de outras fontes** e clique em **da Web**.

    ![Menu de Power Query](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. Na caixa **de** diálogo da Web, insira a **URL** que você USARIA no serviço vinculado JSON (por exemplo: https://en.wikipedia.org/wiki/) juntamente com o caminho que você especificaria para o conjunto de os (por exemplo: AFI% 27s_100_Years... 100_Movies) e clique em **OK**.

    ![Da caixa de diálogo da Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL usada neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se você vir a caixa de diálogo **acessar conteúdo da Web** , selecione a **URL**correta, a **autenticação**e clique em **conectar**.

   ![Caixa de diálogo acessar conteúdo da Web](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Clique em um item de **tabela** no modo de exibição de árvore para ver o conteúdo da tabela e, em seguida, clique no botão **Editar** na parte inferior.  

   ![caixa de diálogo Navegador](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Na janela **Editor de consultas** , clique no botão **Editor avançado** na barra de ferramentas.

    ![Botão de Editor Avançado](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo Editor Avançado, o número ao lado de "origem" é o índice.

    ![Editor Avançado-índice](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Se você estiver usando o Excel 2013, use [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) para obter o índice. Consulte [o artigo conectar-se a uma página da Web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) para obter detalhes. As etapas são semelhantes se você estiver usando [o Microsoft Power bi para desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Para mapear colunas do conjunto de fonte de origem para colunas do conjunto de coleta, consulte [mapeando colunas do conjunto de linhas no Azure data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
