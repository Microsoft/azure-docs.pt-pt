---
title: Copiar dados da Web Table utilizando a Azure Data Factory
description: Saiba mais sobre o Conector de Mesa Web da Azure Data Factory que permite copiar dados de uma tabela web para lojas de dados suportadas pela Data Factory como pias.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 0eb4d37342685c13027a69bb6cb85f618fa63f20
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410205"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Copiar dados da tabela Web utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-web-table-connector.md)
> * [Versão atual](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados de tabelaweb. Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

A diferença entre este conector de tabela Web, o [conector REST](connector-rest.md) e o [conector HTTP](connector-http.md) são:

- **O conector** de mesa web extrai o conteúdo da tabela a partir de uma página web HTML.
- **O conector REST** suporta especificamente a cópia de dados de APIs RESTful.
- **O conector HTTP** é genérico para recuperar dados de qualquer ponto final http, por exemplo, para descarregar ficheiros. 

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de tabela Web é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da base de dados da web table para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector de tabela Web suporta **a extração do conteúdo da tabela a partir de uma página HTML**.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de mesa Web, precisa de configurar um Tempo de Integração Auto-hospedado. Consulte o artigo [De Integração Autónoma](create-self-hosted-integration-runtime.md) para obter mais detalhes.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas ao conector de tabela Web.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

As seguintes propriedades são suportadas para o serviço ligado à tabela Web:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Web** |Sim |
| url | URL para a fonte web |Sim |
| authenticationType | O valor permitido é: **Anónimo.** |Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. É necessário um tempo de execução de integração auto-hospedado, tal como mencionado nos [pré-requisitos.](#prerequisites) |Sim |

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados da tabela Web.

Para copiar dados da tabela Web, detete a propriedade do tipo do conjunto de dados para **WebTable**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **WebTable** | Sim |
| path |Um URL relativo ao recurso que contém a tabela. |Não. Quando o caminho não é especificado, apenas é utilizado o URL especificado na definição de serviço ligado. |
| índice |O índice da tabela no recurso. Consulte [o índice de uma tabela numa](#get-index-of-a-table-in-an-html-page) página HTML para obter passos para obter o índice de uma tabela numa página HTML. |Sim |

**Exemplo:**

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

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte de tabela Web.

### <a name="web-table-as-source"></a>Tabela web como fonte

Para copiar dados da tabela Web, delineie o tipo de origem na atividade de cópia para **webSource**, não são suportadas propriedades adicionais.

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

## <a name="get-index-of-a-table-in-an-html-page"></a>Obtenha índice de uma tabela numa página HTML

Para obter o índice de uma tabela que precisa configurar em propriedades de conjunto de [dados,](#dataset-properties)pode utilizar, por exemplo, o Excel 2016 como a ferramenta seguinte:

1. Lançar **Excel 2016** e mudar para o separador **Dados.**
2. Clique em **Nova Consulta** na barra de ferramentas, aponte para From **Other Sources** e clique na **Web**.

    ![Menu de consulta de poder](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. Na caixa de diálogo **From Web,** introduza **URL** que utilizaria https://en.wikipedia.org/wiki/) no serviço ligado JSON (por exemplo: juntamente com o caminho que especificaria para o conjunto de dados (por exemplo: AFI%27s_100_Years... 100_Movies), e **clique**OK .

    ![Do diálogo da Web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL utilizado neste exemplo:https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se vir a caixa de diálogo de **conteúdo web access,** selecione o **URL**certo, **a autenticação,** e clique em **Ligar**.

   ![Aceder à caixa de diálogo de conteúdo web](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Clique num item de **tabela** na vista da árvore para ver o conteúdo da tabela e, em seguida, clique no botão **Editar** na parte inferior.  

   ![caixa de diálogo Navegador](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Na janela **Do Editor de Consulta,** clique no botão **Advanced Editor** na barra de ferramentas.

    ![Botão Editor Avançado](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo do Editor Avançado, o número ao lado de "Source" é o índice.

    ![Editor Avançado - Índice](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Se estiver a utilizar o Excel 2013, utilize o [Microsoft Power Query para](https://www.microsoft.com/download/details.aspx?id=39379) o Excel obter o índice. Consulte [o Connect para um](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) artigo de página web para obter detalhes. Os passos são semelhantes se estiver a utilizar o [Microsoft Power BI para desktop](https://powerbi.microsoft.com/desktop/).


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
