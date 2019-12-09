---
title: Transformar dados usando o script U-SQL-Azure
description: Saiba como processar ou transformar dados executando scripts U-SQL no serviço de computação Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: c6d3510dfdd02bf2eb07d656c706c44d895c582d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927905"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformar dados executando scripts U-SQL no Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](data-factory-usql-activity.md)
> * [Versão 2 (versão atual)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço de Data Factory, consulte [atividade de U-SQL na v2](../transform-data-using-data-lake-analytics.md).

Um pipeline em um data factory do Azure processa dados em serviços de armazenamento vinculados usando serviços de computação vinculados. Ele contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve a **Data Lake Analytics atividade u-SQL** que executa um script **u-SQL** em um serviço vinculado de computação **Azure data Lake Analytics** . 

Crie uma conta de Azure Data Lake Analytics antes de criar um pipeline com uma atividade de U-SQL Data Lake Analytics. Para saber mais sobre Azure Data Lake Analytics, consulte Introdução [ao Azure data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Examine o [tutorial criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md) para obter etapas detalhadas para criar um data Factory, serviços vinculados, conjuntos de informações e um pipeline. Use trechos JSON com o editor de Data Factory ou o Visual Studio ou Azure PowerShell para criar Data Factory entidades.

## <a name="supported-authentication-types"></a>Tipos de autenticação com suporte
A atividade U-SQL dá suporte a tipos de autenticação abaixo em Data Lake Analytics:
* Autenticação do principal de serviço
* Autenticação de credencial de usuário (OAuth) 

Recomendamos que você use a autenticação de entidade de serviço, especialmente para uma execução agendada de U-SQL. O comportamento de expiração do token pode ocorrer com a autenticação de credenciais do usuário. Para obter detalhes de configuração, consulte a seção [Propriedades do serviço vinculado](#azure-data-lake-analytics-linked-service) .

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics serviço vinculado
Você cria um serviço vinculado **Azure data Lake Analytics** para vincular um serviço de computação Azure data Lake Analytics a uma data Factory do Azure. O Data Lake Analytics atividade U-SQL no pipeline refere-se a esse serviço vinculado. 

A tabela a seguir fornece descrições para as propriedades genéricas usadas na definição de JSON. Você pode escolher entre a entidade de serviço e a autenticação de credenciais de usuário.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **tipo** |A propriedade Type deve ser definida como: **AzureDataLakeAnalytics**. |Sim |
| **accountName** |Nome da conta de Azure Data Lake Analytics. |Sim |
| **dataLakeAnalyticsUri** |URI de Azure Data Lake Analytics. |Não |
| **subscriptionId** |ID da assinatura do Azure |Não (se não for especificado, a assinatura do data factory será usada). |
| **resourceGroupName** |Nome do grupo de recursos do Azure |Não (se não for especificado, o grupo de recursos do data factory será usado). |

### <a name="service-principal-authentication-recommended"></a>Autenticação de entidade de serviço (recomendada)
Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory (AD do Azure) e conceda a ela acesso ao Data Lake Store. Para obter passos detalhados, consulte [autenticação serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:
* ID da aplicação
* Chave da aplicação 
* ID do inquilino

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique o ID de cliente. da aplicação | Sim |
| **servicePrincipalKey** | Especifique a chave da aplicação. | Sim |
| **tenant** | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |

**Exemplo: autenticação de entidade de serviço**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autenticação de credenciais de usuário
Como alternativa, você pode usar a autenticação de credenciais de usuário para Data Lake Analytics especificando as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **nesse** | Clique no botão **autorizar** no Editor de data Factory e insira sua credencial que atribui a URL de autorização gerada automaticamente a essa propriedade. | Sim |
| **sessionId** | ID da sessão OAuth da sessão de autorização do OAuth. Cada ID de sessão é exclusiva e pode ser usada apenas uma vez. Essa configuração é gerada automaticamente quando você usa o editor de Data Factory. | Sim |

**Exemplo: autenticação de credenciais de usuário**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiração do token
O código de autorização gerado usando o botão **autorizar** expira depois de algum tempo. Consulte a tabela a seguir para obter os tempos de expiração para diferentes tipos de contas de usuário. Você poderá ver a seguinte mensagem de erro quando o **token de autenticação expirar**: erro de operação de credencial: INVALID_GRANT-AADSTS70002: erro ao validar as credenciais. AADSTS70008: a concessão de acesso fornecida expirou ou foi revogada. ID de rastreamento: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID de correlação: fac30a0c-6be6-4e02-8D69-a776d2ffefd7 timestamp: 2015-12-15 21:09:31Z

| Tipo de utilizador | Expira após |
|:--- |:--- |
| Contas de usuário não gerenciadas pelo Azure Active Directory (@hotmail.com, @live.com, etc.) |12 horas |
| Contas de usuários gerenciadas pelo Azure Active Directory (AAD) |14 dias após a última execução da fatia. <br/><br/>90 dias, se uma fatia com base no serviço vinculado baseado em OAuth for executada pelo menos uma vez a cada 14 dias. |

Para evitar/resolver esse erro, recrie-o usando o botão **autorizar** quando o **token expirar** e reimplantar o serviço vinculado. Você também pode gerar valores para as propriedades **SessionID** e **Authorization** programaticamente usando o código da seguinte maneira:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Consulte os tópicos classe [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)e [classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes de data Factory usadas no código. Adicione uma referência a: Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms. dll para a classe WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
O trecho JSON a seguir define um pipeline com uma atividade Data Lake Analytics U-SQL. A definição da atividade tem uma referência para o serviço vinculado Azure Data Lake Analytics que você criou anteriormente.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

A tabela a seguir descreve os nomes e as descrições das propriedades que são específicas para essa atividade. 

| Propriedade            | Descrição                              | Obrigatório                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| tipo                | A propriedade Type deve ser definida como **DataLakeAnalyticsU-SQL**. | Sim                                      |
| linkedServiceName   | Referência ao Azure Data Lake Analytics registrado como um serviço vinculado no Data Factory | Sim                                      |
| scriptPath          | Caminho para a pasta que contém o script U-SQL. O nome do arquivo diferencia maiúsculas de minúsculas. | Não (se você usar o script)                   |
| scriptLinkedService | Serviço vinculado que vincula o armazenamento que contém o script ao data factory | Não (se você usar o script)                   |
| .              | Especifique o script embutido em vez de especificar scriptPath e scriptLinkedService. Por exemplo: `"script": "CREATE DATABASE test"`. | Não (se você usar scriptPath e scriptLinkedService) |
| degreeOfParallelism | O número máximo de nós usados simultaneamente para executar o trabalho. | Não                                       |
| prioridade            | Determina quais trabalhos de todos os que estão na fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. | Não                                       |
| parâmetros          | Parâmetros para o script U-SQL          | Não                                       |
| runtimeVersion      | Versão de tempo de execução do mecanismo U-SQL a ser usado | Não                                       |
| compilationMode     | <p>Modo de compilação do U-SQL. Deve ser um destes valores:</p> <ul><li>**Semântica:** Execute verificações semânticas e verificações de integridade necessárias.</li><li>**Completo:** Execute a compilação completa, incluindo verificação de sintaxe, otimização, geração de código, etc.</li><li>**SingleBox:** Execute a compilação completa, com a configuração TargetType para SingleBox.</li></ul><p>Se você não especificar um valor para essa propriedade, o servidor determinará o modo de compilação ideal. </p> | Não                                       |

Consulte [definição de script SearchLogProcessing. txt](#sample-u-sql-script) para a definição de script. 

## <a name="sample-input-and-output-datasets"></a>Exemplos de conjuntos de dados de entrada e saída
### <a name="input-dataset"></a>Conjunto de dados de entrada
Neste exemplo, os dados de entrada residem em um Azure Data Lake Store (arquivo SearchLog. tsv na pasta datalake/entrada). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Conjunto de saída
Neste exemplo, os dados de saída produzidos pelo script U-SQL são armazenados em um Azure Data Lake Store (pasta datalake/de saída). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Serviço vinculado de Data Lake Store de exemplo
Aqui está a definição da amostra Azure Data Lake Store serviço vinculado usado pelos conjuntos de dados de entrada/saída. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Confira o artigo [mover dados para e de Azure data Lake Store](data-factory-azure-datalake-connector.md) para obter descrições das propriedades JSON. 

## <a name="sample-u-sql-script"></a>Script U-SQL de exemplo

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Os valores para **\@** os parâmetros in e **\@out** no script U-SQL são passados dinamicamente pelo ADF usando a seção ' Parameters '. Consulte a seção "parâmetros" na definição do pipeline.

Você pode especificar outras propriedades, como degreeOfParallelism e Priority, também em sua definição de pipeline para os trabalhos que são executados no serviço de Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parâmetros dinâmicos
Na definição de pipeline de exemplo, os parâmetros in e out são atribuídos com valores embutidos em código. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Em vez disso, é possível usar parâmetros dinâmicos. Por exemplo: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Nesse caso, os arquivos de entrada ainda são coletados na pasta/datalake/Input e os arquivos de saída são gerados na pasta/datalake/output Os nomes de arquivo são dinâmicos com base na hora de início da fatia.  


