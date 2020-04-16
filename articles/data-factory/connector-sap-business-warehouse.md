---
title: Copiar dados do SAP BW
description: Saiba como copiar dados do SAP Business Warehouse para armazenar dados de sumidouro suportado utilizando uma atividade de cópia num pipeline azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/04/2019
ms.openlocfilehash: 2f8406038be10ba3bdc207bf447fecb86a376fe8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418070"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Versão atual](connector-sap-business-warehouse.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve como utilizar a Atividade de Cópia na Fábrica de Dados Azure para copiar dados de um Armazém De Negócios SAP (BW). Baseia-se no artigo de visão geral da [atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

>[!TIP]
>Para conhecer o suporte global da ADF no cenário de integração de dados DoSAP, consulte a integração de [dados do SAP utilizando o livro branco da Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) com introdução detalhada, comparação e orientação.

## <a name="supported-capabilities"></a>Capacidades suportadas

Este conector SAP Business Warehouse é suportado para as seguintes atividades:

- [Copiar atividade](copy-activity-overview.md) com matriz de [origem/pia suportada](copy-activity-overview.md)
- [Atividade de procura](control-flow-lookup-activity.md)

Pode copiar dados do SAP Business Warehouse para qualquer loja de dados de sink suportado. Para obter uma lista de lojas de dados que são suportadas como fontes/pias pela atividade de cópia, consulte a tabela de lojas de [dados suportadas.](copy-activity-overview.md#supported-data-stores-and-formats)

Especificamente, este conector SAP Business Warehouse suporta:

- SAP Business Warehouse **versão 7.x**.
- Copiar dados de **InfoCubes e QueryCubes** (incluindo consultas BEx) utilizando consultas MDX.
- Copiar dados utilizando a autenticação básica.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar este conector SAP Business Warehouse, é necessário:

- Criar um Tempo de Integração Auto-hospedado. Consulte o artigo [De Integração Autónoma](create-self-hosted-integration-runtime.md) para obter mais detalhes.
- Instale a **biblioteca SAP NetWeaver** na máquina de tempo de integração. Pode obter a biblioteca SAP Netweaver do seu administrador SAP, ou diretamente do Centro de [Descarregamento](https://support.sap.com/swdc)de Software SAP . Procure o **SAP Note #1025361** para obter o local de descarregamento para a versão mais recente. Certifique-se de que escolhe a biblioteca SAP NetWeaver **de 64 bits** que corresponde à instalação de Tempo de Execução de Integração. Em seguida, instale todos os ficheiros incluídos no SAP NetWeaver RFC SDK de acordo com a Nota SAP. A biblioteca SAP NetWeaver também está incluída na instalação sap Client Tools.

>[!TIP]
>Para resolver problemas de conectividade com o SAP BW, certifique-se de:
>- Todas as bibliotecas de dependência extraídas do NetWeaver RFC SDK estão em vigor na pasta %windir%\system32. Normalmente tem icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- As portas necessárias para ligar ao SAP Server estão ativadas na máquina DE IR auto-hospedada, que normalmente são a porta 3300 e 3201.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seguintes secções fornecem detalhes sobre propriedades que são usadas para definir entidades data Factory específicas para o conector SAP Business Warehouse.

## <a name="linked-service-properties"></a>Propriedades de serviço seletos

Os seguintes imóveis são suportados para o serviço ligado ao SAP Business Warehouse (BW):

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade tipo deve ser definida para: **SapBw** | Sim |
| servidor | Nome do servidor em que reside a instância SAP BW. | Sim |
| sistemaNúmero | Número do sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma corda. | Sim |
| clientId | Identificação do cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma corda. | Sim |
| userName | Nome do utilizador que tem acesso ao servidor SAP. | Sim |
| palavra-passe | A palavra-passe do utilizador. Marque este campo como um SecureString para o armazenar de forma segura na Data Factory, ou [refira um segredo armazenado no Cofre de Chaves Azure](store-credentials-in-key-vault.md). | Sim |
| connectVia | O Tempo de [Integração](concepts-integration-runtime.md) a utilizar para se ligar à loja de dados. É necessário um tempo de execução de integração auto-hospedado, tal como mencionado nos [pré-requisitos.](#prerequisites) |Sim |

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)

Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo conjuntos de [dados.](concepts-datasets-linked-services.md) Esta secção fornece uma lista de propriedades suportadas pelo conjunto de dados SAP BW.

Para copiar dados do SAP BW, detete a propriedade do tipo do conjunto de dados para **SapBwCube**. Embora não existam propriedades específicas do tipo suportadas para o conjunto de dados SAP BW do tipo RelationalTable.

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

Se estiver `RelationalTable` a utilizar o conjunto de dados dactilografado, ainda é suportado como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy

Para obter uma lista completa de secções e imóveis disponíveis para definir atividades, consulte o artigo [Pipelines.](concepts-pipelines-activities.md) Esta secção fornece uma lista de propriedades suportadas por fonte sap BW.

### <a name="sap-bw-as-source"></a>SAP BW como fonte

Para copiar dados do SAP BW, as seguintes propriedades são suportadas na secção de **origem** da atividade de cópia:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade do tipo da fonte de atividade de cópia deve ser definida para: **SapBwSource** | Sim |
| consulta | Especifica a consulta MDX para ler os dados da instância SAP BW. | Sim |

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

Se estiver `RelationalSource` a usar a fonte dactilografada, continua a ser suportada como está, enquanto é sugerido que utilize o novo para a frente.

## <a name="data-type-mapping-for-sap-bw"></a>Mapeamento de tipo de dados para SAP BW

Ao copiar dados do SAP BW, os seguintes mapeamentos são utilizados desde tipos de dados SAP BW para tipos de dados provisórios da Azure Data Factory. Consulte [schema e mapeamentos](copy-activity-schema-and-type-mapping.md) de tipo de dados para saber como a atividade de cópia mapeia o esquema de origem e o tipo de dados para a pia.

| Tipo de dados SAP BW | Tipo de dados provisórios da fábrica de dados |
|:--- |:--- |
| ACCP | int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEZ | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| RIO LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| CRU | Byte[] |
| CADEIA BRUTA | Byte[] |
| CORDA | String |
| UNIDADE | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="lookup-activity-properties"></a>Propriedades de atividade de procura

Para saber mais detalhes sobre as propriedades, consulte a [atividade de Lookup.](control-flow-lookup-activity.md)


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista de lojas de dados suportadas como fontes e pias pela atividade de cópia na Azure Data Factory, consulte as lojas de [dados suportadas](copy-activity-overview.md#supported-data-stores-and-formats).
