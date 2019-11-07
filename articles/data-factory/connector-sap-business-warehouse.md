---
title: Copiar dados de SAP BW usando Azure Data Factory
description: Saiba como copiar dados do SAP Business Warehouse para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: c2dbacc2fd7906aaf22447dfb39c543206f05392
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680290"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse usando o Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Versão atual](connector-sap-business-warehouse.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um SAP Business Warehouse (BW). Ele se baseia no artigo [visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para aprender o suporte geral do ADF no cenário de integração de dados do SAP, consulte [integração de dados SAP usando Azure data Factory White Paper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução, análise e diretrizes detalhadas.

## <a name="supported-capabilities"></a>Recursos com suporte

Este conector do SAP Business Warehouse tem suporte para as seguintes atividades:

- [Atividade de cópia](copy-activity-overview.md) com [matriz de coletor/origem com suporte](copy-activity-overview.md)
- [Atividade de Pesquisa](control-flow-lookup-activity.md)

Você pode copiar dados do SAP Business Warehouse para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como fontes/coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats) .

Especificamente, esse conector do SAP Business Warehouse dá suporte a:

- SAP Business Warehouse **versão 7. x**.
- Copiar dados de **InfoCubes e QueryCubes** (incluindo consultas Bex) usando consultas MDX.
- Copiar dados usando a autenticação básica.

## <a name="prerequisites"></a>Pré-requisitos

Para usar este conector do SAP Business Warehouse, você precisa:

- Configure um Integration Runtime auto-hospedado. Confira o artigo de [Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md) para obter detalhes.
- Instale a **biblioteca do SAP NetWeaver** no computador Integration Runtime. Você pode obter a biblioteca do SAP NetWeaver do administrador do SAP ou diretamente do [centro de download de software SAP](https://support.sap.com/swdc). Pesquise o **#1025361 de observação SAP** para obter o local de download para a versão mais recente. Verifique se você selecionou a biblioteca do SAP NetWeaver de **64 bits** que corresponde à sua instalação do Integration Runtime. Em seguida, instale todos os arquivos incluídos no SDK RFC do SAP NetWeaver de acordo com a nota SAP. A biblioteca do SAP NetWeaver também está incluída na instalação das ferramentas de cliente SAP.

>[!TIP]
>Para solucionar o problema de conectividade para SAP BW, verifique se:
>- Todas as bibliotecas de dependências extraídas do SDK da RFC NetWeaver estão em vigor na pasta%windir%\System32 Normalmente, ele tem icudt34. dll, icuin34. dll, icuuc34. dll, libicudecnumber. dll, librfc32. dll, libsapucum. dll, sapcrypto. dll, sapcryto_old. dll, sapnwrfc. dll.
>- As portas necessárias usadas para se conectar ao servidor SAP estão habilitadas no computador IR auto-hospedado, que geralmente são as portas 3300 e 3201.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades que são usadas para definir Data Factory entidades específicas ao conector do SAP Business Warehouse.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SAP Business Warehouse (BW):

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type deve ser definida como: **SapBw** | Sim |
| servidor | Nome do servidor no qual reside a instância de SAP BW. | Sim |
| Número | Número de sistema do sistema de SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma cadeia de caracteres. | Sim |
| clientId | ID do cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma cadeia de caracteres. | Sim |
| Usu | Nome do usuário que tem acesso ao servidor SAP. | Sim |
| palavra-passe | A palavra-passe do utilizador. Marque este campo como uma SecureString para armazená-lo com segurança no Data Factory ou [faça referência a um segredo armazenado em Azure Key Vault](store-credentials-in-key-vault.md). | Sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Um Integration Runtime auto-hospedado é necessário conforme mencionado em [pré-requisitos](#prerequisites). |Sim |

**Exemplo:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades de DataSet

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de os, consulte o artigo [conjuntos de valores](concepts-datasets-linked-services.md) . Esta seção fornece uma lista das propriedades com suporte pelo conjunto de SAP BW DataSet.

Para copiar dados de SAP BW, defina a propriedade Type do conjunto de dado como **SapBwCube**. Embora não haja propriedades específicas do tipo com suporte para o conjunto de SAP BW do tipo RelationalTable.

**Exemplo:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se você estivesse usando `RelationalTable` dataset tipado, ele ainda tem suporte como está, enquanto você é sugerido para usar o novo no futuro.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de seções e propriedades disponíveis para definir atividades, consulte o artigo [pipelines](concepts-pipelines-activities.md) . Esta seção fornece uma lista das propriedades com suporte pela origem do SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW como fonte

Para copiar dados de SAP BW, as propriedades a seguir têm suporte na seção **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade Type da fonte da atividade de cópia deve ser definida como: **SapBwSource** | Sim |
| consulta | Especifica a consulta MDX para ler dados da instância de SAP BW. | Sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se você estivesse usando `RelationalSource` fonte tipada, ainda há suporte no estado em que se encontra, enquanto você é sugerido para usar o novo no futuro.

## <a name="data-type-mapping-for-sap-bw"></a>Mapeamento de tipo de dados para SAP BW

Ao copiar dados do SAP BW, os seguintes mapeamentos são usados de tipos de dados SAP BW para Azure Data Factory tipos de dados provisórios. Consulte [mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e o esquema de origem para o coletor.

| Tipo de dados SAP BW | Tipo de dados provisório do data Factory |
|:--- |:--- |
| ACCP | inteiro |
| º | String |
| CLNT | String |
| CURR | Vírgula |
| CUKY | String |
| DEZ | Vírgula |
| FLTP | Clique |
| INT1 | Minuciosa |
| INT2 | Int16 |
| INT4 | inteiro |
| Lima | String |
| LCHR | String |
| LRAW | Byte [] |
| PREC | Int16 |
| QUAN | Vírgula |
| RECEBEM | Byte [] |
| RAWSTRING | Byte [] |
| Strings | String |
| UNIDADE | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="lookup-activity-properties"></a>Propriedades da atividade de pesquisa

Para obter detalhes sobre as propriedades, verifique a [atividade de pesquisa](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de armazenamentos de dados com suporte como fontes e coletores pela atividade de cópia no Azure Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
