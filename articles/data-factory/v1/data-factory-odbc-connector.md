---
title: Mover dados das lojas de dados ODBC
description: Saiba como mover dados das lojas de dados ODBC usando a Azure Data Factory.
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
ms.openlocfilehash: 3c68b1f4d76a1899ce473c57f3a6d5de1eab71c6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636872"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Mover dados das lojas de dados da ODBC usando a Azure Data Factory
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-odbc-connector.md)
> * [Versão 2 (versão atual)](../connector-odbc.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte o [conector ODBC em V2](../connector-odbc.md).


Este artigo explica como utilizar a Copy Activity in Azure Data Factory para mover dados de uma loja de dados ODBC no local. Baseia-se no artigo de Atividades de Movimento de [Dados,](data-factory-data-movement-activities.md) que apresenta uma visão geral do movimento de dados com a atividade da cópia.

Pode copiar dados de uma loja de dados ODBC para qualquer loja de dados de lavatórios suportados. Para obter uma lista de lojas de dados suportadas como pias pela atividade de cópia, consulte a tabela [de lojas de dados suportadas.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Atualmente, a fábrica de dados suporta apenas a transferência de dados de uma loja de dados ODBC para outras lojas de dados, mas não para transferir dados de outras lojas de dados para uma loja de dados ODBC.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Habilitação da conectividade
O serviço Data Factory suporta a ligação a fontes ODBC no local utilizando o Gateway de Gestão de Dados. Consulte [dados em movimento entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem para saber sobre o Gateway de Gestão de Dados e instruções passo a passo sobre a configuração do gateway. Utilize o portal para ligar a uma loja de dados ODBC mesmo que esteja hospedado num Azure IaaS VM.

Pode instalar o gateway na mesma máquina no local ou no VM Azure que a loja de dados ODBC. No entanto, recomendamos que instale o gateway numa máquina separada/Azure IaaS VM para evitar a contenção de recursos e para um melhor desempenho. Quando instalar o gateway numa máquina separada, a máquina deverá ser capaz de aceder à máquina com a loja de dados ODBC.

Além do Gateway de Gestão de Dados, também é necessário instalar o controlador ODBC para a loja de dados na máquina gateway.

> [!NOTE]
> Consulte [os problemas de gateway de resolução de problemas](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) para obter dicas sobre questões relacionadas com a ligação de resolução de problemas/gateways.

## <a name="getting-started"></a>Introdução
Pode criar um pipeline com uma atividade de cópia que move dados de uma loja de dados ODBC utilizando diferentes ferramentas/APIs.

A forma mais fácil de criar um oleoduto é utilizar o **Copy Wizard** . Ver [Tutorial: Criar um pipeline utilizando o Copy Wizard](data-factory-copy-data-wizard-tutorial.md) para uma rápida passagem na criação de um oleoduto utilizando o assistente de dados Copy.

Também pode utilizar as seguintes ferramentas para criar um pipeline: **Visual Studio** , **Azure PowerShell,** **Azure Resource Manager,** **.NET API** e **REST API** . Consulte o tutorial de [atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar um oleoduto com uma atividade de cópia.

Quer utilize as ferramentas ou APIs, executa os seguintes passos para criar um pipeline que transfere dados de uma loja de dados de origem para uma loja de dados de lavatórios:

1. Crie **serviços ligados** para ligar as lojas de dados de entrada e saída à sua fábrica de dados.
2. Crie **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia.
3. Crie um **pipeline** com uma atividade de cópia que leva um conjunto de dados como entrada e um conjunto de dados como uma saída.

Quando utiliza o assistente, as definições de JSON para estas entidades da Data Factory (serviços ligados, conjuntos de dados e o pipeline) são automaticamente criadas para si. Quando utiliza ferramentas/APIs (exceto .NET API), define estas entidades da Data Factory utilizando o formato JSON. Para obter uma amostra com definições JSON para entidades da Data Factory que são usadas para copiar dados de uma loja de dados ODBC, consulte [o exemplo JSON: Copiar dados da loja de dados ODBC para a secção Azure Blob](#json-example-copy-data-from-odbc-data-store-to-azure-blob) deste artigo.

As seguintes secções fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades da Data Factory específicas para a loja de dados ODBC:

## <a name="linked-service-properties"></a>Propriedades de serviço ligadas
A tabela seguinte fornece descrição para elementos JSON específicos do serviço ligado ao ODBC.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tipo |A propriedade tipo deve ser definida para: **OnPremisesOdbc** |Sim |
| conexãoStragem |A parte credencial de não acesso da cadeia de ligação e uma credencial encriptada opcional. Consulte os exemplos nas seguintes secções. <br/><br/>Pode especificar a cadeia de ligação com padrão como `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` , ou utilizar o sistema DSN (Data Source Name) que configura na máquina de gateway com `"DSN=<name of the DSN>;"` (ainda precisa especificar a parte credencial no serviço ligado em conformidade). |Sim |
| credencial |A parte credencial de acesso da cadeia de ligação especificada no formato de valor da propriedade específica do condutor. Exemplo: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Não |
| authenticationType |Tipo de autenticação utilizada para ligar à loja de dados ODBC. Os valores possíveis são: Anónimo e Básico. |Sim |
| userName |Especifique o nome de utilizador se estiver a utilizar a autenticação Básica. |Não |
| palavra-passe |Especifique a palavra-passe para a conta de utilizador que especificou para o nome de utilizador. |Não |
| gatewayName |Nome do gateway que o serviço Data Factory deve utilizar para ligar à loja de dados ODBC. |Sim |

### <a name="using-basic-authentication"></a>Utilização da autenticação básica

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
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Utilização da autenticação básica com credenciais encriptadas
Pode encriptar as credenciais utilizando o cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) (versão 1.0 do Azure PowerShell) ou [o New-AzureDataFactoryEncryptValue](/previous-versions/azure/dn834940(v=azure.100)) (0.9 ou versão anterior do Azure PowerShell).

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

### <a name="using-anonymous-authentication"></a>Utilização da autenticação anónima

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

## <a name="dataset-properties"></a>Dataset properties (Propriedades do conjunto de dados)
Para obter uma lista completa de secções & propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criar conjuntos de dados.](data-factory-create-datasets.md) Secções como estrutura, disponibilidade e política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, Azure blob, Azure table, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados na loja de dados. A secção de tipos depropriedades para conjunto de dados do tipo **RelationalTable** (que inclui conjunto de dados ODBC) tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na loja de dados ODBC. |Sim |

## <a name="copy-activity-properties"></a>Propriedades da atividade Copy
Para obter uma lista completa das secções & propriedades disponíveis para definir atividades, consulte o artigo [Criar Pipelines.](data-factory-create-pipelines.md) Propriedades como nome, descrição, tabelas de entrada e saída, e políticas estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na secção **de tipoProperties** da atividade, por outro lado, variam com cada tipo de atividade. Para a atividade copy, variam dependendo dos tipos de fontes e pias.

Na atividade de cópia, quando a fonte é do tipo **RelationalSource** (que inclui o ODBC), as seguintes propriedades estão disponíveis na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| consulta |Utilize a consulta personalizada para ler dados. |Cadeia de consulta SQL. Por exemplo: selecione * do MyTable. |Sim |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>Exemplo JSON: Copiar dados da loja de dados da ODBC para a Azure Blob
Este exemplo fornece definições JSON que pode usar para criar um oleoduto utilizando [o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra como copiar dados de uma fonte ODBC para um Azure Blob Storage. No entanto, os dados podem ser copiados para qualquer um dos lavatórios [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) indicados utilizando a Atividade de Cópia na Fábrica de Dados Azure.

A amostra tem as seguintes entidades de fábrica de dados:

1. Um serviço ligado do tipo [OnPremisesOdbc](#linked-service-properties).
2. Um serviço ligado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Um conjunto de [dados](data-factory-create-datasets.md) de entrada do tipo [RelacionalTable](#dataset-properties).
4. Um conjunto de [dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Copy Activity que utiliza [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

A amostra copia dados de uma consulta resulta numa loja de dados ODBC para uma bolha a cada hora. As propriedades JSON utilizadas nestas amostras são descritas em secções que seguem as amostras.

Como primeiro passo, crie o portal de gestão de dados. As instruções estão nos [dados móveis entre locais no local e](data-factory-move-data-between-onprem-and-cloud.md) artigo em nuvem.

**Serviço ligado ao ODBC** Este exemplo utiliza a autenticação Básica. Consulte a secção [de serviços da ODBC para](#linked-service-properties) diferentes tipos de autenticação que pode utilizar.

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

**Conjunto de dados de entrada ODBC**

A amostra pressupõe que criou uma tabela "MyTable" numa base de dados ODBC e contém uma coluna chamada "timetampcolumn" para dados da série de tempo.

Definição "externa": "verdadeiro" informa o serviço Data Factory de que o conjunto de dados é externo à fábrica de dados e não é produzido por uma atividade na fábrica de dados.

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

Os dados são escritos para uma nova bolha a cada hora (frequência: hora, intervalo: 1). O caminho da pasta para a bolha é avaliado dinamicamente com base na hora de início da fatia que está a ser processada. O caminho da pasta utiliza partes do ano, mês, dia e horas da hora de início.

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

**Copiar a atividade num oleoduto com fonte ODBC (RelationalSource) e pia Blob (BlobSink)**

O pipeline contém uma Atividade de Cópia que está configurada para utilizar estes conjuntos de dados de entrada e saída e está programado para ser executado a cada hora. Na definição JSON do gasoduto, o tipo **de fonte** é definido para **RelationalSource** e o tipo **de pia** é definido para **BlobSink** . A consulta SQL especificada para a propriedade **de consulta** seleciona os dados na hora passada para copiar.

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
Conforme mencionado no artigo [de atividades](data-factory-data-movement-activities.md) de movimento de dados, a atividade copy realiza conversões automáticas de tipo de origem para tipos de pia com a seguinte abordagem em duas etapas:

1. Converter de tipos de origem nativa para .NET tipo
2. Converter de tipo .NET para tipo de pia nativa

Ao mover dados de lojas de dados ODBC, os tipos de dados ODBC são mapeados para os tipos de .NET, conforme mencionado no tópico [de Mapeamentos do Tipo de Dados ODBC.](/dotnet/framework/data/adonet/odbc-data-type-mappings)

## <a name="map-source-to-sink-columns"></a>Fonte do mapa para afundar colunas
Para obter informações sobre as colunas de mapeamento em conjunto de dados de origem para colunas no conjunto de dados da pia, consulte [as colunas de conjunto de dados de mapeamento na Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetível de fontes relacionais
Ao copiar dados de lojas de dados relacionais, tenha em mente a repetibilidade para evitar resultados não intencionais. Na Azure Data Factory, pode repetir manualmente uma fatia. Também pode configurar a política de reagem para um conjunto de dados para que uma fatia seja re-executada quando ocorre uma falha. Quando uma fatia é reexame de qualquer forma, você precisa ter certeza de que os mesmos dados são lidos, não importa quantas vezes uma fatia é executada. Ver [leitura repetível de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Resolver problemas de conectividade
Para resolver problemas de conexão, utilize o separador diagnóstico do Gestor de Configuração gateway de **gestão** de **dados** .

1. Gestor **de configuração gateway de gestão de dados de lançamento** . Pode executar "C:\Program Files\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe" diretamente (ou) procurar **gateway** para encontrar um link para a aplicação **Do Gateway de Gestão de Dados** do Microsoft, como mostrado na imagem seguinte.

    ![Gateway de pesquisa](./media/data-factory-odbc-connector/search-gateway.png)
2. Mude para o **separador Diagnósticos.**

    ![Diagnóstico do gateway](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Selecione o **tipo** de loja de dados (serviço ligado).
4. Especifique **a autenticação** e introduza **credenciais** (ou) **insira o fio de ligação** que é utilizado para ligar à loja de dados.
5. Clique **na ligação de teste** para testar a ligação à loja de dados.

## <a name="performance-and-tuning"></a>Performance e Afinação
Consulte [copy Activity Performance & Guia de Afinação](data-factory-copy-activity-performance.md) para conhecer os fatores-chave que impactam o desempenho do movimento de dados (Copy Activity) na Azure Data Factory e várias formas de otimizá-lo.