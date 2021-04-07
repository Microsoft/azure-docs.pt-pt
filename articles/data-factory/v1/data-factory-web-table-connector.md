---
title: Mover dados da Web Table usando a Azure Data Factory
description: Saiba como mover dados de uma tabela numa página Web usando a Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7663848bd06244de7efb169a576e11a5c78204c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392906"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Mover dados de uma fonte de tabela Web usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-web-table-connector.md)
> * [Versão 2 (versão atual)](../connector-web-table.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector da tabela Web em V2](../connector-web-table.md).

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de uma tabela numa página Web para uma loja de dados de lavatórios suportados. Este artigo baseia-se no artigo de atividades de movimento de [dados](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade de cópia e a lista de lojas de dados suportadas como fontes/pias.

Atualmente, a fábrica de dados suporta apenas a transferência de dados de uma tabela Web para outras lojas de dados, mas não a transferência de dados de outras lojas de dados para um destino de tabela Web.

> [!IMPORTANT]
> Este conector Web suporta atualmente apenas a extração de conteúdo de tabela de uma página HTML. Para obter dados de um ponto final HTTP/s, utilize o [conector HTTP.](data-factory-http-connector.md)

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de mesa Web, é necessário configurar um Tempo de Execução de Integração Auto-hospedado (também conhecido como Data Management Gateway) e configurar a `gatewayName` propriedade no serviço ligado à pia. Por exemplo, para copiar da tabela Web para o armazenamento Azure Blob, configuure o serviço ligado ao Azure Storage como o seguinte:

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
Pode criar um pipeline com uma atividade de cópia que move dados de uma loja de dados cassandra no local, utilizando diferentes ferramentas/APIs. 

- A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy. 
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia. 

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma tabela web, consulte [o exemplo JSON: Copiar dados da tabela Web para a secção Azure Blob](#json-example-copy-data-from-web-table-to-azure-blob) deste artigo. 

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas de uma tabela Web:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado à Web.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **Web** |Yes |
| Url |URL para a fonte web |Yes |
| authenticationType |Anónimo. |Yes |

### <a name="using-anonymous-authentication"></a>Utilização da autenticação anónima

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção de tipos de propriedades para conjunto de dados do tipo **WebTable** tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo |tipo do conjunto de dados. deve ser definido para **WebTable** |Yes |
| caminho |Um URL relativo ao recurso que contém a tabela. |N.º Quando o caminho não é especificado, apenas é utilizado o URL especificado na definição de serviço ligada. |
| índice |O índice da tabela no recurso. Consulte [o índice de uma tabela numa](#get-index-of-a-table-in-an-html-page) secção de página HTML para obter o índice de uma tabela numa página HTML. |Yes |

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
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e política estão disponíveis para todos os tipos de atividades.

Enquanto que as propriedades disponíveis na secção de tipos de atividade variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

Atualmente, quando a origem na atividade de cópia é do tipo **WebSource,** não são suportadas propriedades adicionais.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Exemplo JSON: Copiar dados da tabela Web para Azure Blob
A seguinte amostra mostra:

1. Um serviço ligado do tipo [Web.](#linked-service-properties)
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [WebTable](#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [WebSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de uma tabela Web para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

A amostra que se segue mostra como copiar dados de uma tabela Web para uma bolha Azure. No entanto, os dados podem ser copiados diretamente para qualquer um dos lavatórios indicados no artigo de Atividades de [Movimento de Dados,](data-factory-data-movement-activities.md) utilizando a Atividade de Cópia na Fábrica de Dados Azure.

**Serviço ligado à web** Este exemplo utiliza o serviço ligado à Web com autenticação anónima. Consulte a secção [de serviços ligado à Web](#linked-service-properties) para diferentes tipos de autenticação que pode utilizar.

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

**Serviço ligado do Storage do Azure**

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

**Conjunto de dados de entrada webTable** A definição **externa** à **verdadeira** informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

> [!NOTE]
> Consulte [o índice de uma tabela numa](#get-index-of-a-table-in-an-html-page) secção de página HTML para obter o índice de uma tabela numa página HTML.  
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

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1).

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



**Pipeline com atividade de Copy**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **WebSource** e o tipo **de pia** é definido para **BlobSink**.

Consulte as propriedades do tipo WebSource para a lista de propriedades suportadas pelo WebSource.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Obtenha índice de uma tabela numa página HTML
1. Lançar **Excel 2016** e mudar para o separador **Dados.**  
2. Clique em **Nova Consulta** na barra de ferramentas, aponte para **Outras Fontes** e clique na **Web**.

    ![Menu de consulta de energia](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. Na caixa de diálogo **da Web,** introduza **o URL** que utilizaria no serviço ligado JSON (por exemplo: https://en.wikipedia.org/wiki/) juntamente com o caminho que especificaria para o conjunto de dados (por exemplo: AFI%27s_100_Years... 100_Movies), e clique **OK**.

    ![Do diálogo web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL utilizado neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se vir a caixa de diálogo **de conteúdo do Access Web,** selecione o **URL** certo, **a autenticação** e clique em **Connect**.

   ![Aceder caixa de diálogo de conteúdo web](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Clique num item de **tabela** na vista da árvore para ver o conteúdo da tabela e, em seguida, clique em **Editar** o botão Na parte inferior.  

   ![caixa de diálogo Navegador](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Na janela **'Editor de Consulta',** clique no botão **Editor Avançado** na barra de ferramentas.

    ![Botão de editor avançado](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo do Editor Avançado, o número ao lado de "Source" é o índice.

    ![Editor Avançado - Índice](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Se estiver a utilizar o Excel 2013, utilize [a Microsoft Power Query para obter](https://www.microsoft.com/download/details.aspx?id=39379) o índice. Consulte [o 'Connect' para obter mais](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) detalhes sobre a página web. Os passos são semelhantes se estiver a utilizar [o Microsoft Power BI para desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas a partir do conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.
