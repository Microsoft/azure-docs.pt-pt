---
title: Mova dados da Web Table utilizando a Fábrica de Dados Azure
description: Saiba como mover dados de uma tabela numa página Web utilizando a Azure Data Factory.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387363"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Mova dados de uma fonte de tabela Web usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-web-table-connector.md)
> * [Versão 2 (versão atual)](../connector-web-table.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector](../connector-web-table.md)da tabela Web em V2 .

Este artigo descreve como usar a Atividade de Cópia na Fábrica de Dados Azure para mover dados de uma tabela numa página Web para uma loja de dados de sink suportado. Este artigo baseia-se no artigo sobre [atividades](data-factory-data-movement-activities.md) de movimento de dados que apresenta uma visão geral do movimento de dados com atividade de cópia e a lista de lojas de dados suportadas como fontes/pias.

Atualmente, a fábrica de dados suporta apenas a transferência de dados de uma tabela Web para outras lojas de dados, mas não transfere dados de outras lojas de dados para um destino de tabela Web.

> [!IMPORTANT]
> Este conector web suporta atualmente apenas a extração do conteúdo da tabela a partir de uma página HTML. Para recuperar dados de um ponto final HTTP/s, utilize o [conector HTTP.](data-factory-http-connector.md)

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de tabela Web, precisa de configurar um Runtime de Integração Auto-hospedado (também conhecido como Data Management Gateway) e configurar a propriedade `gatewayName` no serviço ligado ao lavatório. Por exemplo, para copiar da tabela Web para o armazenamento do Blob Azure, configure o serviço ligado ao Armazenamento Azure como o seguinte:

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
Pode criar um pipeline com uma atividade de cópia que transfere dados de uma loja de dados Cassandra no local utilizando diferentes ferramentas/APIs. 

- A maneira mais fácil de criar um pipeline é utilizar o **Assistente de Cópia**. Ver [Tutorial: Crie um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um pipeline utilizando o assistente de dados Copy. 
- Também pode utilizar as seguintes ferramentas para criar um pipeline: **Estúdio Visual,** **Azure PowerShell,** **Modelo de Gestor de Recursos Azure,** **.NET API**e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um pipeline com uma atividade de cópia. 

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de sumidouro:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos** de dados para representar dados de entrada e saída para a operação de cópia. 
3. Crie um **pipeline** com uma atividade de cópia que tome um conjunto de dados como entrada e um conjunto de dados como uma saída. 

Quando utiliza o assistente, as definições jSON para estas entidades da Fábrica de Dados (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Fábrica de Dados utilizando o formato JSON.  Para obter uma amostra com definições JSON para entidades da Fábrica de Dados que são usadas para copiar dados de uma tabela web, consulte o [exemplo jSON: Copiar dados da tabela Web para](#json-example-copy-data-from-web-table-to-azure-blob) a secção Azure Blob deste artigo. 

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades data Factory específicas de uma tabela Web:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado à Web.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **Web** |Sim |
| Url |URL para a fonte web |Sim |
| authenticationType |Anónimo. |Sim |

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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo Criação de conjuntos de [dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjuntos de dados (Azure SQL, Azure blob, tabela Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção typeProperties para conjunto de dados do tipo **WebTable** tem as seguintes propriedades

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo |tipo de conjunto de dados. deve ser definido para **WebTable** |Sim |
| caminho |Um URL relativo ao recurso que contém a tabela. |Não. Quando o caminho não é especificado, apenas é utilizado o URL especificado na definição de serviço ligado. |
| index |O índice da tabela no recurso. Consulte [o índice de uma tabela numa](#get-index-of-a-table-in-an-html-page) página HTML para obter passos para obter o índice de uma tabela numa página HTML. |Sim |

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
Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Creating Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e a política estão disponíveis para todos os tipos de atividades.

Considerando que as propriedades disponíveis na secção typeProperties da atividade variam com cada tipo de atividade. Para a atividade de Cópia, variam dependendo dos tipos de fontes e pias.

Atualmente, quando a fonte na atividade de cópia é do tipo **WebSource,** não são suportadas propriedades adicionais.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Exemplo jSON: Copiar dados da tabela Web para Azure Blob
A amostra que se segue mostra:

1. Um serviço ligado do tipo [Web.](#linked-service-properties)
2. Um serviço ligado do tipo [AzureStorage.](data-factory-azure-blob-connector.md#linked-service-properties)
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [WebTable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de dados de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [WebSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de uma tabela Web para uma bolha Azure a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

A amostra que se segue mostra como copiar dados de uma tabela Web para uma bolha Azure. No entanto, os dados podem ser copiados diretamente para qualquer um dos lavatórios indicados no artigo [Atividades](data-factory-data-movement-activities.md) de Movimento de Dados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

**Serviço ligado à web** Este exemplo utiliza o serviço ligado à Web com autenticação anónima. Consulte a secção de [serviço ligada à Web](#linked-service-properties) para obter diferentes tipos de autenticação que possa utilizar.

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

Conjunto de dados de **entrada WebTable** A definição **externa** para **verdadeira** informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

> [!NOTE]
> Consulte [o índice de uma tabela numa](#get-index-of-a-table-in-an-html-page) página HTML para obter passos para obter o índice de uma tabela numa página HTML.  
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



**Pipeline com atividade de Cópia**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar os conjuntos de dados de entrada e saída e está programado para funcionar a cada hora. Na definição JSON do gasoduto, o tipo de **origem** é definido para **WebSource** e o tipo **de pia** é definido para **BlobSink**.

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
2. Clique em **Nova Consulta** na barra de ferramentas, aponte para From **Other Sources** e clique na **Web**.

    ![Menu de consulta de poder](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. Na caixa de diálogo **From Web,** introduza **URL** que utilizaria no serviço ligado JSON (por exemplo: https://en.wikipedia.org/wiki/) juntamente com o caminho que especificaria para o conjunto de dados (por exemplo: AFI%27s_100_Years... 100_Movies), e **clique**OK .

    ![Do diálogo da Web](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    URL utilizado neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se vir a caixa de diálogo de **conteúdo web access,** selecione o **URL**certo, **a autenticação,** e clique em **Ligar**.

   ![Aceder à caixa de diálogo de conteúdo web](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Clique num item de **tabela** na vista da árvore para ver o conteúdo da tabela e, em seguida, clique no botão **Editar** na parte inferior.  

   ![Diálogo do navegador](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Na janela **Do Editor de Consulta,** clique no botão **Advanced Editor** na barra de ferramentas.

    ![Botão Editor Avançado](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo do Editor Avançado, o número ao lado de "Source" é o índice.

    ![Editor Avançado - Índice](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Se estiver a utilizar o Excel 2013, utilize o [Microsoft Power Query para](https://www.microsoft.com/download/details.aspx?id=39379) o Excel obter o índice. Consulte [o Connect para um](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artigo de página web para obter detalhes. Os passos são semelhantes se estiver a utilizar o [Microsoft Power BI para desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Para mapear colunas desde o conjunto de dados de origem até colunas a partir de conjunto de dados de sumidouro, consulte [colunas de conjunto de dados de mapeamento na Fábrica](data-factory-map-columns.md)de Dados Azure .

## <a name="performance-and-tuning"></a>Desempenho e Afinação
Consulte o [Copy Activity Performance & Tuning Guide](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Fábrica de Dados Do Azure e várias formas de o otimizar.
