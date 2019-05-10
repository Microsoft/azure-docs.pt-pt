---
title: Copiar dados de tabela da Web com o Azure Data Factory | Documentos da Microsoft
description: Saiba mais sobre Web tabela conector do Azure Data Factory que lhe permite copiar dados de uma tabela de web para os arquivos de dados suportados pelo Data Factory como sinks.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: e578b3a6b3905569567b568b0130c1ed1b90d915
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557771"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Copiar dados da tabela da Web com o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-web-table-connector.md)
> * [Versão atual](connector-web-table.md)

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de uma base de dados de tabela do Web. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

A diferença entre este conector de tabela da Web, o [conector REST](connector-rest.md) e o [conector HTTP](connector-http.md) são:

- **Conector de tabela de Web** extrações de tabela conteúdo a partir de uma página da Web HTML.
- **Conector REST** especificamente suporte copiam dados a partir de RESTful APIs.
- **Conector HTTP** é genérico para recuperar dados a partir de qualquer ponto final de HTTP, por exemplo, para transferir o ficheiro. 

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados da base de dados de tabela de Web para qualquer arquivo de dados de sink suportados. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector de tabela de Web suporta **extrair o conteúdo da tabela de uma página HTML**.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de tabela da Web, terá de configurar um Runtime de integração autoalojado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre as propriedades que são utilizadas para definir entidades do Data Factory específicas para o conector de tabela da Web.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As seguintes propriedades são suportadas para o serviço ligado de tabela da Web:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo tem de ser definida como: **Web** |Sim |
| url | URL para a origem da Web |Sim |
| authenticationType | O valor permitido é: **Anónimo**. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Runtime de integração autoalojado é necessário, conforme mencionado na [pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo de conjuntos de dados. Esta seção fornece uma lista de propriedades suportadas pelo conjunto de dados de tabela de Web.

Para copiar dados de tabela da Web, defina a propriedade de tipo de conjunto de dados para **WebTable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade de tipo do conjunto de dados deve ser definida como: **WebTable** | Sim |
| path |Um URL relativo ao recurso que contém a tabela. |Não. Quando o caminho não for especificado, é utilizado apenas o URL especificado na definição do serviço ligado. |
| index |O índice da tabela no recurso. Ver [Get índice de uma tabela numa página HTML](#get-index-of-a-table-in-an-html-page) secção para obter passos para obter o índice de uma tabela numa página HTML. |Sim |

**Exemplo:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista de propriedades suportadas pela origem de tabela da Web.

### <a name="web-table-as-source"></a>Tabela Web como origem

Para copiar dados de tabela da Web, defina o tipo de origem na atividade de cópia para **WebSource**, não existem propriedades adicionais são suportadas.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Obter o índice de uma tabela numa página HTML

Para obter o índice de uma tabela que tem de configurar no [propriedades do conjunto de dados](#dataset-properties), pode usar por exemplo, o Excel 2016 como a ferramenta da seguinte forma:

1. Inicie **Excel 2016** e mude para o **dados** separador.
2. Clique em **nova consulta** na barra de ferramentas, aponte para **de outras origens** e clique em **da Web**.

    ![Menu de consulta de energia](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. Na **da Web** caixa de diálogo, introduza **URL** que usaria em ligado JSON do serviço (por exemplo: https://en.wikipedia.org/wiki/) , juntamente com o caminho tem de especificar para o conjunto de dados (por exemplo: AFI % 27s_100_Years... 100_Movies) e clique em **OK**.

    ![Na caixa de diálogo da Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL utilizado neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se vir **conteúdo da Web de acesso** caixa de diálogo caixa, selecione o direito **URL**, **autenticação**e clique em **Connect**.

   ![Caixa de diálogo do conteúdo do acesso Web](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Clique num **tabela** item na vista de árvore para ver o conteúdo da tabela e, em seguida, clique em **editar** na parte inferior.  

   ![Caixa de diálogo navegador](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Na **Editor de consultas** janela, clique em **Editor avançado** botão na barra de ferramentas.

    ![Botão de Editor avançado](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo Editor avançado, o número junto a "Origem" é o índice.

    ![Editor - avançado de índice](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Se estiver a utilizar o Excel 2013, utilize [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) para obter o índice. Ver [ligar a uma página da web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artigo para obter detalhes. Os passos são semelhantes se estiver a utilizar [Microsoft Power BI para Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
