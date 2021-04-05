---
title: Copiar dados da Web Table utilizando a Azure Data Factory
description: Saiba mais sobre o Conector de Tabela Web da Azure Data Factory que permite copiar dados de uma tabela web para lojas de dados suportadas pela Data Factory como pias.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 1ab45868afd8d1b7ba1f61f5eaacca283817e6d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367015"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Copie os dados da tabela Web utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-web-table-connector.md)
> * [Versão atual](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de uma base de dados de tabelas Web. Baseia-se no artigo [de visão geral](copy-activity-overview.md) da atividade de cópia que apresenta uma visão geral da atividade da cópia.

A diferença entre este conector de mesa Web, o [conector REST](connector-rest.md) e o [conector HTTP](connector-http.md) são:

- **O conector da tabela web** extrai o conteúdo da tabela a partir de uma página web HTML.
- **O conector REST** suporta especificamente a cópia de dados de APIs RESTful.
- **O conector HTTP** é genérico para obter dados de qualquer ponto final HTTP, por exemplo, para descarregar ficheiros. 

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector de mesa Web é suportado para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de fonte/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados da base de dados da tabela Web para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como fontes/pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector de mesa Web suporta **a extração de conteúdo de tabela de uma página HTML**.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector de mesa Web, é necessário configurar um tempo de execução de integração auto-hospedado. Consulte o artigo [de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As secções seguintes fornecem detalhes sobre propriedades que são usadas para definir entidades da Data Factory específicas do conector de tabela Web.

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas

As seguintes propriedades são suportadas para o serviço ligado à tabela Web:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **Web** |Yes |
| url | URL para a fonte web |Yes |
| authenticationType | Valor permitido é: **Anónimo**. |Yes |
| connectVia | O [tempo de execução de integração](concepts-integration-runtime.md) a ser utilizado para ligar à loja de dados. É necessário um tempo de integração auto-organizado, tal como mencionado nos [Pré-Requisitos](#prerequisites). |Yes |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [conjuntos de dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados da tabela Web.

Para copiar dados da tabela Web, defina a propriedade tipo do conjunto de dados para **webTable**. As seguintes propriedades são suportadas:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| tipo | A propriedade tipo do conjunto de dados deve ser definida para: **WebTable** | Yes |
| caminho |Um URL relativo ao recurso que contém a tabela. |N.º Quando o caminho não é especificado, apenas é utilizado o URL especificado na definição de serviço ligada. |
| índice |O índice da tabela no recurso. Consulte [o índice de uma tabela numa](#get-index-of-a-table-in-an-html-page) secção de página HTML para obter o índice de uma tabela numa página HTML. |Yes |

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

Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte de tabela Web.

### <a name="web-table-as-source"></a>Tabela web como fonte

Para copiar dados da tabela Web, desagrafe o tipo de origem na atividade de cópia para **WebSource,** não são suportadas propriedades adicionais.

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

Para obter o índice de uma tabela que precisa de configurar em [propriedades de conjunto de dados,](#dataset-properties)pode utilizar, por exemplo, o Excel 2016 como a ferramenta da seguinte forma:

1. Lançar **Excel 2016** e mudar para o separador **Dados.**
2. Clique em **Nova Consulta** na barra de ferramentas, aponte para **Outras Fontes** e clique na **Web**.

    ![Menu de consulta de energia](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. Na caixa de diálogo **da Web,** introduza **o URL** que utilizaria no serviço ligado JSON (por exemplo: https://en.wikipedia.org/wiki/) juntamente com o caminho que especificaria para o conjunto de dados (por exemplo: AFI%27s_100_Years... 100_Movies), e clique **OK**.

    ![Do diálogo web](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    URL utilizado neste exemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Se vir a caixa de diálogo **de conteúdo do Access Web,** selecione o **URL** certo, **a autenticação** e clique em **Connect**.

   ![Aceder caixa de diálogo de conteúdo web](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. Clique num item de **tabela** na vista da árvore para ver o conteúdo da tabela e, em seguida, clique em **Editar** o botão Na parte inferior.  

   ![caixa de diálogo Navegador](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. Na janela **'Editor de Consulta',** clique no botão **Editor Avançado** na barra de ferramentas.

    ![Botão de editor avançado](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. Na caixa de diálogo do Editor Avançado, o número ao lado de "Source" é o índice.

    ![Editor Avançado - Índice](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Se estiver a utilizar o Excel 2013, utilize [a Microsoft Power Query para obter](https://www.microsoft.com/download/details.aspx?id=39379) o índice. Consulte [o 'Connect' para obter mais](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) detalhes sobre a página web. Os passos são semelhantes se estiver a utilizar [o Microsoft Power BI para desktop](https://powerbi.microsoft.com/desktop/).


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para obter detalhes sobre as propriedades, consulte [a atividade de Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e sumidouros pela atividade de cópia na Azure Data Factory, consulte lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)
