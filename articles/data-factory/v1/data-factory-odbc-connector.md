---
title: Mover dados de armazenamentos de dados ODBC
description: Saiba mais sobre como mover dados de armazenamentos de dados ODBC usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e1735c2d2ed107f7ec65d68a6826267ee83a93f8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918717"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Mover dados de armazenamentos de dados ODBC usando Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-odbc-connector.md)
> * [Versão 2 (versão atual)](../connector-odbc.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [conector ODBC na v2](../connector-odbc.md).


Este artigo explica como usar a atividade de cópia no Azure Data Factory para mover dados de um armazenamento de dados ODBC local. Ele se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md) , que apresenta uma visão geral da movimentação de dados com a atividade de cópia.

Você pode copiar dados de um armazenamento de dados ODBC para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como coletores pela atividade de cópia, consulte a tabela [armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Atualmente, o data Factory dá suporte apenas à movimentação de dados de um armazenamento de dados ODBC para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para um armazenamento de dados ODBC.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Habilitando a conectividade
Data Factory serviço dá suporte à conexão com fontes ODBC locais usando o gateway de Gerenciamento de Dados. Confira o artigo [movendo dados entre locais e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre gerenciamento de dados gateway e instruções passo a passo sobre como configurar o gateway. Use o gateway para se conectar a um armazenamento de dados ODBC, mesmo se ele estiver hospedado em uma VM IaaS do Azure.

Você pode instalar o gateway no mesmo computador local ou na VM do Azure como o armazenamento de dados ODBC. No entanto, recomendamos que você instale o gateway em um computador separado/VM IaaS do Azure para evitar a contenção de recursos e para melhorar o desempenho. Quando você instala o gateway em um computador separado, o computador deve ser capaz de acessar o computador com o armazenamento de dados ODBC.

Além do gateway de Gerenciamento de Dados, você também precisa instalar o driver ODBC para o armazenamento de dados no computador do gateway.

> [!NOTE]
> Consulte [solucionar problemas de gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre como solucionar problemas relacionados à conexão/gateway.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de um armazenamento de dados ODBC usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de cópia**. Consulte [tutorial: criar um pipeline usando o assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter uma explicação rápida sobre como criar um pipeline usando o assistente para copiar dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Visual Studio**, **Azure PowerShell**, **modelo de Azure Resource Manager**, **API .net**e **API REST**. Ver [tutorial da atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

Se você usar as ferramentas ou APIs, execute as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor:

1. Crie **Serviços vinculados** para vincular armazenamentos de dados de entrada e saída ao seu data Factory.
2. Crie **conjuntos** de dados para representar o dado de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto como uma saída.

Quando você usa o assistente, as definições de JSON para essas entidades de Data Factory (serviços vinculados, conjuntos de valores e o Pipeline) são criadas automaticamente para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades de Data Factory usando o formato JSON. Para obter um exemplo com definições de JSON para Data Factory entidades usadas para copiar dados de um armazenamento de dados ODBC, confira a seção [exemplo de JSON: copiar dados do repositório de dados ODBC para o blob do Azure](#json-example-copy-data-from-odbc-data-store-to-azure-blob) deste artigo.

As seções a seguir fornecem detalhes sobre as propriedades JSON que são usadas para definir Data Factory entidades específicas para o armazenamento de dados ODBC:

## <a name="linked-service-properties"></a>Propriedades do serviço ligado
A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado ODBC.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade Type deve ser definida como: **OnPremisesOdbc** |Sim |
| connectionString |A parte de credencial de não acesso da cadeia de conexão e uma credencial criptografada opcional. Consulte os exemplos nas seções a seguir. <br/><br/>Você pode especificar a cadeia de conexão com um padrão como `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`ou usar o DSN do sistema (nome da fonte de dados) configurado no computador do gateway com `"DSN=<name of the DSN>;"` (você ainda precisa especificar a parte da credencial no serviço vinculado adequadamente). |Sim |
| credencial |A parte de credencial de acesso da cadeia de conexão especificada no formato de valor de propriedade específico do driver. Exemplo: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Não |
| authenticationType |Tipo de autenticação usado para se conectar ao armazenamento de dados ODBC. Os valores possíveis são: Anonymous e Basic. |Sim |
| userName |Especifique o nome de usuário se você estiver usando a autenticação básica. |Não |
| palavra-passe |Especifique a senha para a conta de usuário que você especificou para o nome para usuário. |Não |
| gatewayName |Nome do gateway que o serviço de Data Factory deve usar para se conectar ao armazenamento de dados ODBC. |Sim |

### <a name="using-basic-authentication"></a>Usando a autenticação básica

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Usando a autenticação básica com credenciais criptografadas
Você pode criptografar as credenciais usando o cmdlet [New-AzDataFactoryEncryptValue](https://docs.microsoft.com/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) (versão 1,0 do Azure PowerShell) ou [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0,9 ou versão anterior do Azure PowerShell).

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Usando a autenticação anônima

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados
Para obter uma lista completa das seções & propriedades disponíveis para definir os conjuntos de valores, consulte o artigo [criando conjuntos](data-factory-create-datasets.md) de itens. As seções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (SQL do Azure, BLOB do Azure, tabela do Azure, etc.).

A seção **typeproperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no repositório de dados. A seção typeproperties para o conjunto de um do tipo **RelationalTable** (que inclui o conjunto de um ODBC) tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela no repositório de dados ODBC. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das seções & propriedades disponíveis para definir as atividades, consulte o artigo [criando pipelines](data-factory-create-pipelines.md) . Propriedades como nome, descrição, tabelas de entrada e saída e políticas estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção **typeproperties** da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fontes e coletores.

Na atividade de cópia, quando a fonte é do tipo **RelationalSource** (que inclui o ODBC), as seguintes propriedades estão disponíveis na seção typeproperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Use a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: selecione * em MyTable. |Sim |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>Exemplo de JSON: copiar dados do armazenamento de dados ODBC para o blob do Azure
Este exemplo fornece definições de JSON que você pode usar para criar um pipeline usando o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ele mostra como copiar dados de uma fonte ODBC para um armazenamento de BLOBs do Azure. No entanto, os dados podem ser copiados para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure data Factory.

O exemplo tem as seguintes entidades de data factory:

1. Um serviço vinculado do tipo [OnPremisesOdbc](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um [conjunto](data-factory-create-datasets.md) de dados de entrada do tipo [RelationalTable](#dataset-properties).
4. Um [conjunto](data-factory-create-datasets.md) de uma saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

O exemplo copia dados de um resultado de consulta em um armazenamento de dados ODBC para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas em seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados. As instruções estão no artigo [movendo dados entre os locais e a nuvem do local](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço vinculado de ODBC** Este exemplo usa a autenticação básica. Consulte a seção [serviço vinculado ODBC](#linked-service-properties) para diferentes tipos de autenticação que você pode usar.

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Conjunto de dados de entrada ODBC**

O exemplo supõe que você tenha criado uma tabela "MyTable" em um banco de dados ODBC e que ela contenha uma coluna chamada "timestampcolumn" para data de série temporal.

A configuração "external": "true" informa ao serviço de Data Factory que o conjunto de os é externo ao data factory e não é produzido por uma atividade no data factory.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
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

**Conjunto de dados dos Blobs do Azure**

Os dados são gravados em um novo BLOB a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Atividade de cópia em um pipeline com origem ODBC (RelationalSource) e coletor de BLOB (BlobSink)**

O pipeline contém uma atividade de cópia que é configurada para usar esses conjuntos de dados de entrada e saída e está agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo de **origem** é definido como **RelationalSource** e o tipo de **coletor** está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora a serem copiados.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Mapeamento de tipo para ODBC
Conforme mencionado no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões automáticas dos tipos de origem nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados de armazenamentos de dados ODBC, os tipos de dados ODBC são mapeados para tipos .NET, conforme mencionado no tópico [mapeamentos de tipo de dados ODBC](https://msdn.microsoft.com/library/cc668763.aspx) .

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas do coletor
Para saber mais sobre como mapear colunas no conjunto de informações de origem para colunas no conjunto de informações do coletor, confira [mapeando colunas Azure data Factory do conjunto de](data-factory-map-columns.md)informações

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, tenha em mente a capacidade de repetição para evitar resultados indesejados. No Azure Data Factory, você pode executar novamente uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de uma para que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente de qualquer forma, você precisa garantir que os mesmos dados sejam lidos, independentemente de quantas vezes uma fatia é executada. Consulte [leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Solucionar problemas de conectividade
Para solucionar problemas de conexão, use a guia **diagnóstico** do **Gerenciamento de dados gateway Configuration Manager**.

1. Iniciar **Configuration Manager do gateway de gerenciamento de dados**. Você pode executar "C:\Program Files\Microsoft Gerenciamento de Dados Gateway\1.0\Shared\ConfigManager.exe" diretamente (ou) Pesquisar por **Gateway** para encontrar um link para o aplicativo de **gateway do Microsoft gerenciamento de dados** , conforme mostrado na imagem a seguir.

    ![Gateway de pesquisa](./media/data-factory-odbc-connector/search-gateway.png)
2. Alterne para a guia **diagnóstico** .

    ![Diagnóstico do gateway](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Selecione o **tipo** de armazenamento de dados (serviço vinculado).
4. Especifique a **autenticação** e insira **as credenciais** (ou) Insira a **cadeia de conexão** que é usada para se conectar ao armazenamento de dados.
5. Clique em **testar conexão** para testar a conexão com o armazenamento de dados.

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Consulte [Guia de ajuste do desempenho de atividade de cópia &](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (atividade de cópia) no Azure data Factory e várias maneiras de otimizá-lo.
