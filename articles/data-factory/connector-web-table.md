---
title: Copiar dados da tabela da Web usando Azure Data Factory | Microsoft Docs
description: Saiba mais sobre o conector de tabela da Web de Azure Data Factory que permite copiar dados de uma tabela da Web para armazenamentos de dados com suporte do Data Factory como coletores.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: eceb61234e3ae10934e6bb9057f8ddc800c19c94
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009729"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Copiar dados da tabela da Web usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-web-table-connector.md)
> * [Versão atual](connector-web-table.md)

Este artigo descreve como usar a atividade de cópia em Azure Data Factory para copiar dados de um banco de dado de tabela da Web. Ele se baseia no [copiar descrição geral da atividade](copy-activity-overview.md) artigo apresenta uma visão geral da atividade de cópia.

A diferença entre esse conector de tabela da Web, o [conector REST](connector-rest.md) e o [conector http](connector-http.md) são:

- O **conector de tabela da Web** extrai o conteúdo da tabela de uma página HTML.
- O **conector REST** especificamente dá suporte à cópia de dados de APIs RESTful.
- O **conector http** é genérico para recuperar dados de qualquer ponto de extremidade http, por exemplo, para baixar o arquivo. 

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de tabela da Web tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados de um banco de dado de tabela da Web para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista dos arquivos de dados que são suportados como origens/sinks a atividade de cópia, consulte a [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, esse conector de tabela da Web dá suporte à **extração de conteúdo de tabela de uma página HTML**.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse conector de tabela da Web, você precisa configurar um Integration Runtime auto-hospedado. Ver [Integration Runtime autoalojado](create-self-hosted-integration-runtime.md) artigo para obter detalhes.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector de tabela da Web.

## <a name="linked-service-properties"></a>Propriedades do serviço ligado

As propriedades a seguir têm suporte para o serviço vinculado de tabela da Web:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type deve ser definida como: **Web** |Sim |
| url | URL para a origem da Web |Sim |
| authenticationType | O valor permitido é: **Anonymous**. |Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser utilizado para ligar ao arquivo de dados. Um Integration Runtime auto-hospedado é necessário conforme mencionado em [pré-requisitos](#prerequisites). |Sim |

**Example:**

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

Para obter uma lista completa das secções e propriedades disponíveis para definir conjuntos de dados, consulte a [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de tabelas da Web.

Para copiar dados de uma tabela da Web, defina a propriedade Type do DataSet para **webtable**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| type | A propriedade Type do conjunto de conjuntos deve ser definida como: **WebTable** | Sim |
| path |Uma URL relativa para o recurso que contém a tabela. |Não. Quando o caminho não for especificado, somente a URL especificada na definição de serviço vinculado será usada. |
| index |O índice da tabela no recurso. Consulte a seção [obter índice de uma tabela em uma página HTML](#get-index-of-a-table-in-an-html-page) para obter as etapas para obter o índice de uma tabela em uma página HTML. |Sim |

**Example:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa das secções e propriedades disponíveis para a definição de atividades, consulte a [Pipelines](concepts-pipelines-activities.md) artigo. Esta seção fornece uma lista das propriedades com suporte pela origem da tabela da Web.

### <a name="web-table-as-source"></a>Tabela da Web como fonte

Para copiar dados da tabela da Web, defina o tipo de fonte na atividade de cópia como **websource**, não há suporte para nenhuma propriedade adicional.

**Example:**

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Obter o índice de uma tabela em uma página HTML

Para obter o índice de uma tabela que você precisa configurar nas [Propriedades do conjunto](#dataset-properties)de ferramentas, você pode usar, por exemplo, o Excel 2016 como a ferramenta da seguinte maneira:

1. Inicie o **Excel 2016** e alterne para a guia **dados** .
2. Clique em **nova consulta** na barra de ferramentas, aponte para **de outras fontes** e clique em **da Web**.

    ![Menu de Power Query](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. Na caixa **de** diálogo da Web, insira a **URL** que você usaria no serviço vinculado JSON (por exemplo: https://en.wikipedia.org/wiki/) com o caminho que você especificaria para o conjunto de um (por exemplo: AFI% 27s_100_Years... 100_Movies) e clique em **OK**.

    ![Da caixa de diálogo da Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL usada neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se você vir a caixa de diálogo **acessar conteúdo da Web** , selecione a **URL**correta, a **autenticação**e clique em **conectar**.

   ![Caixa de diálogo acessar conteúdo da Web](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Clique em um item de **tabela** no modo de exibição de árvore para ver o conteúdo da tabela e, em seguida, clique no botão **Editar** na parte inferior.  

   ![Caixa de diálogo navegador](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Na janela **Editor de consultas** , clique no botão **Editor avançado** na barra de ferramentas.

    ![Botão de Editor Avançado](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo Editor Avançado, o número ao lado de "origem" é o índice.

    ![Editor Avançado-índice](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Se você estiver usando o Excel 2013, use [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) para obter o índice. Consulte [o artigo conectar-se a uma página da Web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) para obter detalhes. As etapas são semelhantes se você estiver usando [o Microsoft Power bi para desktop](https://powerbi.microsoft.com/desktop/).


## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos Seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks, a atividade de cópia no Azure Data Factory, veja [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
