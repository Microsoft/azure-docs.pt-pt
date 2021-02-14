---
title: Mover dados da Salesforce utilizando a Data Factory
description: Saiba como mover dados da Salesforce utilizando a Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ccc20f415d13356de755af5d1d3afc5b29de72f2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387058"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Mover dados da Salesforce utilizando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-salesforce-connector.md)
> * [Versão 2 (versão atual)](../connector-salesforce.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector Salesforce em V2](../connector-salesforce.md).

Este artigo descreve como pode utilizar a Copy Activity numa fábrica de dados Azure para copiar dados da Salesforce para qualquer loja de dados que esteja listada sob a coluna Sink na tabela [de fontes e pias suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Este artigo baseia-se no artigo de [atividades](data-factory-data-movement-activities.md) de movimento de dados, que apresenta uma visão geral do movimento de dados com a Copy Activity e combinações de lojas de dados suportadas.

A Azure Data Factory suporta atualmente apenas a transferência de dados da Salesforce para lojas de [dados de sumidouros suportados,](data-factory-data-movement-activities.md#supported-data-stores-and-formats)mas não suporta a transferência de dados de outras lojas de dados para a Salesforce.

## <a name="supported-versions"></a>Versões suportadas
Este conector suporta as seguintes edições de Salesforce: Developer Edition, Professional Edition, Enterprise Edition ou Edição Ilimitada. E suporta a cópia da produção da Salesforce, caixa de areia e domínio personalizado.

## <a name="prerequisites"></a>Pré-requisitos
* A permissão da API deve ser ativada. Ver [como posso permitir o acesso da API em Salesforce por autorização definida?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Para copiar dados da Salesforce para lojas de dados no local, deve ter pelo menos o Gateway 2.0 de Gestão de Dados instalado no seu ambiente no local.

## <a name="salesforce-request-limits"></a>Limites de pedido da Salesforce
A Salesforce tem limites tanto para os pedidos totais de API como para os pedidos simultâneos de API. Tenha em atenção os seguintes pontos:

- Se o número de pedidos simultâneos exceder o limite, o estrangulamento ocorre e verá falhas aleatórias.
- Se o número total de pedidos exceder o limite, a conta Salesforce ficará bloqueada durante 24 horas.

Pode também receber o erro "REQUEST_LIMIT_EXCEEDED" em ambos os cenários. Consulte a secção "Limites de Pedido de API" no artigo ["Salesforce Developer Limits"](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) para obter mais informações.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados da Salesforce utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard**. Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API**. Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON. Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados da Salesforce, consulte [o exemplo JSON: Copiar dados da Salesforce para a secção Azure Blob](#json-example-copy-data-from-salesforce-to-azure-blob) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas da Salesforce:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela seguinte fornece descrições para elementos JSON específicos do serviço ligado à Salesforce.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **Salesforce**. |Yes |
| ambienteUrl | Especifique o URL da instância Salesforce. <br><br> - Predefinido é "https: \/ /login.salesforce.com". <br> - Para copiar dados da caixa de areia, especifique https://test.salesforce.com " " <br> - Para copiar dados do domínio personalizado, especifique, por exemplo, "https://[domínio].my.salesforce.com". |No |
| nome de utilizador |Especifique um nome de utilizador para a conta de utilizador. |Yes |
| palavra-passe |Especifique uma palavra-passe para a conta de utilizador. |Yes |
| securityToken |Especifique um sinal de segurança para a conta de utilizador. Consulte [obter um sinal de segurança](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) para obter instruções sobre como reiniciar/obter um sinal de segurança. Para conhecer os tokens de segurança em geral, consulte [a Segurança e a API.](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm) |Yes |

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, e assim por diante).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção de tipoproperties para um conjunto de dados do tipo **RelationalTable** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |O nome da mesa na Salesforce. |Não (se for especificada uma **consulta** de **RelationalSource)** |

> [!IMPORTANT]
> A parte "__c" do Nome API é necessária para qualquer objeto personalizado.

![A screenshot mostra o detalhe de definição de objeto personalizado onde você pode ver os nomes A P I dos objetos personalizados.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa de secções e propriedades disponíveis para definir atividades, consulte o artigo [Criar oleodutos.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e várias políticas estão disponíveis para todos os tipos de atividades.

As propriedades que estão disponíveis na secção de tipoProperties da atividade, por outro lado, variam com cada tipo de atividade. Para a Atividade de Cópia, variam consoante os tipos de fontes e pias.

Na atividade de cópia, quando a fonte é do tipo **RelationalSource** (que inclui a Salesforce), as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Necessário |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Uma consulta SQL-92 ou uma consulta [de idioma de consulta de objetos salesforce (SOQL).](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) Por exemplo: `select * from MyTable__c`. |Não (se o nome de **tabela** do conjunto de **dados** for especificado) |

> [!IMPORTANT]
> A parte "__c" do Nome API é necessária para qualquer objeto personalizado.

![A screenshot mostra os Campos Personalizados & Relacionamentos onde você pode ver os nomes A P I dos objetos personalizados.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Dicas de consulta
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Recuperação de dados utilizando a cláusula na coluna DateTime
Quando especificar a consulta SOQL ou SQL, preste atenção à diferença do formato DateTime. Por exemplo:

* **Amostra SOQL:**`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Amostra SQL**:
    * **Utilização do assistente de cópia para especificar a consulta:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Utilizando a edição JSON para especificar a consulta (carre de fuga corretamente):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Obtenção de dados do Relatório Salesforce
Pode obter dados dos relatórios da Salesforce especificando a consulta como `{call "<report name>"}` ,por exemplo, `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Recuperação de registos apagados do Caixote do Reciclagem da Salesforce
Para consultar os registos suaves eliminados do Salesforce Recycle Bin, pode especificar **"IsDeleted = 1"** na sua consulta. Por exemplo,

* Para consultar apenas os registos eliminados, especifique "selecione * de MyTable__c **onde é dedeleted= 1**"
* Para consultar todos os registos, incluindo os existentes e os eliminados, especifique "selecione * a partir de MyTable__c **onde é descaído = 0 ou IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Exemplo JSON: Copiar dados da Salesforce para Azure Blob
O exemplo a seguir fornece definições JSON de amostra que pode usar para criar um oleoduto utilizando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [a Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostram como copiar dados da Salesforce para o Azure Blob Storage. No entanto, os dados podem ser copiados para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

Aqui estão os artefactos da Data Factory que terá de criar para implementar o cenário. As secções que seguem a lista fornecem detalhes sobre estes passos.

* Um serviço ligado do tipo [Salesforce](#linked-service-properties)
* Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [RelacionalTable](#dataset-properties)
* Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Um [pipeline](data-factory-create-pipelines.md) com Atividade de Cópia que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Serviço ligado à Salesforce**

Este exemplo utiliza o serviço ligado à **Salesforce.** Consulte a secção [de serviços ligados à Salesforce](#linked-service-properties) para os imóveis que são suportados por este serviço ligado. Consulte [o sinal de segurança para](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) obter instruções sobre como reiniciar/obter o sinal de segurança.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```
**Conjunto de dados de entrada de salesforce**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

A definição **externa** à **verdadeira** informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

> [!IMPORTANT]
> A parte "__c" do Nome API é necessária para qualquer objeto personalizado.

![A screenshot mostra o detalhe de definição de objeto personalizado onde você pode ver singular Label, Plural Label, Object Name, e A P I Name.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Conjunto de dados de saída de blob Azure**

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
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline com Atividade de Cópia**

O pipeline contém Copy Activity, que está configurado para utilizar os conjuntos de dados de entrada e saída, e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **RelationalSource,** e o tipo **de pia** é definido para **BlobSink**.

Consulte [as propriedades do tipo RelationalSource](#copy-activity-properties) para a lista de propriedades suportadas pela RelationalSource.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
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
> [!IMPORTANT]
> A parte "__c" do Nome API é necessária para qualquer objeto personalizado.

![A screenshot mostra os Campos Personalizados & Relacionamentos com os nomes A P I chamados.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapeamento de tipo para Salesforce

| Tipo salesforce | . Tipo baseado em NET |
| --- | --- |
| Número automático |String |
| Caixa de verificação |Booleano |
| Moeda |Decimal |
| Data |DateTime |
| Data/Hora |DateTime |
| E-mail |String |
| Id |String |
| Relação de procura |String |
| Lista de escolhas multi-selecionadas |String |
| Número |Decimal |
| Percentagem |Decimal |
| Telefone |String |
| Picklist |String |
| Texto |Cadeia |
| Área de Texto |String |
| Área de texto (longo) |String |
| Área de texto (Rico) |String |
| Texto (Encriptado) |String |
| URL |String |

> [!NOTE]
> Para mapear colunas de conjunto de dados de origem para colunas a partir do conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Desempenho e otimização
Consulte o guia de [desempenho e afinação da Atividade](data-factory-copy-activity-performance.md) de Cópia para saber sobre os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.
