---
title: Transformar dados com o script de U-SQL - Azure | Documentos da Microsoft
description: Saiba como processar ou transformar dados ao executar scripts U-SQL no serviço de computação do Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 5835c37363c7e9d2dd3253c08ab97f17852725f5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777299"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformar dados ao executar scripts U-SQL no Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-usql-activity.md)
> * [Versão 2 (versão atual)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, veja [atividade de U-SQL no V2](../transform-data-using-data-lake-analytics.md).

Um pipeline de uma fábrica de dados do Azure processa os dados nos serviços de armazenamento ligada com os serviços ligados de computação. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específico. Este artigo descreve a **atividade de U-SQL do Data Lake Analytics** que executa um **U-SQL** script num **Azure Data Lake Analytics** serviço ligado de computação. 

Crie uma conta do Azure Data Lake Analytics antes de criar um pipeline com uma atividade de U-SQL do Data Lake Analytics. Para saber mais sobre o Azure Data Lake Analytics, consulte [introdução ao Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Reveja os [criar seu primeiro tutorial de pipeline](data-factory-build-your-first-pipeline.md) para obter passos detalhados criar uma fábrica de dados ligada a serviços, conjuntos de dados e um pipeline. Use trechos de código JSON com o Editor do Data Factory ou o Visual Studio ou o Azure PowerShell para criar entidades do Data Factory.

## <a name="supported-authentication-types"></a>Tipos de autenticação suportados
Oferece suporte a atividade de U-SQL abaixo tipos de autenticação contra o Data Lake Analytics:
* Autenticação do principal de serviço
* Autenticação de credencial (OAuth) do utilizador 

Recomendamos que utilize autenticação do principal de serviço, especialmente para uma execução agendada do U-SQL. Comportamento de expiração do token pode ocorrer a autenticação de credenciais de utilizador. Para obter detalhes de configuração, consulte a [propriedades do serviço ligado](#azure-data-lake-analytics-linked-service) secção.

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço ligado do Azure Data Lake Analytics
Criar uma **do Azure Data Lake Analytics** serviço para uma fábrica de dados do Azure de computação do serviço ligado para ligar um Azure Data Lake Analytics. Este serviço ligado refere-se a atividade de U-SQL do Data Lake Analytics no pipeline. 

A tabela seguinte fornece descrições para as propriedades genéricas usadas na definição de JSON. Ainda mais pode escolher entre o principal de serviço e a autenticação de credenciais de utilizador.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tipo** |A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. |Sim |
| **accountName** |Nome da conta do Azure Data Lake Analytics. |Sim |
| **dataLakeAnalyticsUri** |URI do Azure Data Lake Analytics. |Não |
| **subscriptionId** |Id de subscrição do Azure |Não (se não for especificado, a subscrição do data factory é utilizada). |
| **resourceGroupName** |Nome do grupo de recursos do Azure |Não (se não for especificado, grupo de recursos do data factory é utilizado). |

### <a name="service-principal-authentication-recommended"></a>Autenticação do principal de serviço (recomendada)
Para utilizar autenticação do principal de serviço, registe-se uma entidade de aplicação no Azure Active Directory (Azure AD) e conceder o acesso ao Data Lake Store. Para obter passos detalhados, consulte [autenticação serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, o que utilizar para definir o serviço ligado:
* ID da aplicação
* Chave da aplicação 
* ID do Inquilino

Utilize autenticação do principal de serviço ao especificar as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique o ID de cliente. da aplicação | Sim |
| **servicePrincipalKey** | Especifique a chave da aplicação. | Sim |
| **tenant** | Especifique as informações de inquilino (inquilino ou nome do ID de domínio) em que reside a aplicação. Pode recuperá-la ao pairar o cursor do rato no canto superior direito do portal do Azure. | Sim |

**Exemplo: Autenticação do principal de serviço**
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

### <a name="user-credential-authentication"></a>Autenticação de credenciais de utilizador
Em alternativa, pode utilizar a autenticação de credenciais de utilizador para o Data Lake Analytics ao especificar as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **authorization** | Clique nas **autorizar** no Editor do Data Factory e digitar suas credenciais que atribui o URL de autorização gerado automaticamente a esta propriedade. | Sim |
| **sessionId** | ID de sessão OAuth a partir da sessão de autorização de OAuth. Cada ID de sessão é exclusivo e pode ser usada apenas uma vez. Esta definição é gerada automaticamente ao utilizar o Editor do Data Factory. | Sim |

**Exemplo: Autenticação de credenciais de utilizador**
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
O código de autorização que gerou utilizando o **autorizar** botão expira após algum tempo. Veja a seguinte tabela para as horas de expiração para diferentes tipos de contas de utilizador. Pode ver o erro seguinte mensagem quando a autenticação **token expira**: Erro de operação de credenciais: invalid_grant - Error":"invalid_grant","error_description":"aadsts70002: Credenciais de validação de erro. AADSTS70008: A concessão de acesso fornecido está expirada ou revogada. ID de rastreio: ID de correlação d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z

| Tipo de utilizador | Expira após |
|:--- |:--- |
| Contas de utilizador não são geridas pelo Azure Active Directory (@hotmail.com, @live.com, etc.) |12 horas |
| Contas de utilizadores geridas pelo Azure Active Directory (AAD) |Execute 14 dias após o último setor. <br/><br/>90 dias, se um setor com base no serviço ligado com base em OAuth for executado, pelo menos, uma vez a cada 14 dias. |

Para evitar/resolva este erro, autorizar com o **autorizar** botão quando a **token expira** e voltar a implementar o serviço ligado. Também pode gerar valores para **sessionId** e **autorização** propriedades programaticamente usando o código da seguinte forma:

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

Ver [classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx), e [AuthorizationSessionGetResponse classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) tópicos para obter detalhes sobre as classes de fábrica de dados utilizadas no código. Adicione uma referência ao: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
O seguinte fragmento JSON define um pipeline com uma atividade de U-SQL do Data Lake Analytics. Definição da atividade possui uma referência ao serviço ligado do Azure Data Lake Analytics que criou anteriormente.   

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

A tabela seguinte descreve os nomes e descrições das propriedades que são específicas para esta atividade. 

| Propriedade            | Descrição                              | Necessário                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| tipo                | A propriedade de tipo deve ser definida como **DataLakeAnalyticsU SQL**. | Sim                                      |
| linkedServiceName   | Referência para o Azure Data Lake Analytics registado como um serviço ligado no Data Factory | Sim                                      |
| scriptPath          | Caminho para a pasta que contém o script de U-SQL. Nome do ficheiro diferencia maiúsculas de minúsculas. | Não (se usar o script)                   |
| scriptLinkedService | Serviço ligado que liga o armazenamento que contém o script para a fábrica de dados | Não (se usar o script)                   |
| Script              | Especifique o script inline em vez de especificar scriptPath e scriptLinkedService. Por exemplo: `"script": "CREATE DATABASE test"`. | Não (se usar scriptPath e scriptLinkedService) |
| degreeOfParallelism | O número máximo de nós em simultâneo utilizada para executar a tarefa. | Não                                       |
| prioridade            | Determina quais os trabalhos em fila de espera devem ser selecionados para executar primeiro. Menor o número, maior será a prioridade. | Não                                       |
| parâmetros          | Parâmetros para o script de U-SQL          | Não                                       |
| runtimeVersion      | Versão de Runtime do motor de U-SQL para utilizar | Não                                       |
| compilationMode     | <p>Modo de compilação do U-SQL. Tem de ser um dos seguintes valores:</p> <ul><li>**Semântica:** Execute apenas verificações semânticas e verificações de sanidade necessário.</li><li>**Total:** Execute a compilação completa, incluindo a verificação de sintaxe, otimização, geração de código, etc.</li><li>**SingleBox:** Execute a compilação completa, com a definição de TargetType para SingleBox.</li></ul><p>Se não especificar um valor para esta propriedade, o servidor determina o modo de compilação ideal. </p> | Não                                       |

Ver [definição de Script SearchLogProcessing.txt](#sample-u-sql-script) para a definição de script. 

## <a name="sample-input-and-output-datasets"></a>Conjuntos de dados de saída e entrada de exemplo
### <a name="input-dataset"></a>Conjunto de dados de entrada
Neste exemplo, os dados de entrada residem num Store de Lake de dados do Azure (Searchlog ficheiro na pasta datalake/entrada). 

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

### <a name="output-dataset"></a>Conjunto de dados de saída
Neste exemplo, os dados de saída produzidos pelo script do U-SQL são armazenados num Store de Lake de dados do Azure (pasta datalake/saída). 

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

### <a name="sample-data-lake-store-linked-service"></a>Serviço ligado do exemplo de Data Lake Store
Esta é a definição do exemplo de que serviço utilizado pelos conjuntos de dados de entrada/saída de ligado do Azure Data Lake Store. 

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

Ver [mover dados para e do Azure Data Lake Store](data-factory-azure-datalake-connector.md) artigo para obter descrições das propriedades JSON. 

## <a name="sample-u-sql-script"></a>Script de U-SQL de exemplo

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

Os valores para  **\@na** e  **\@horizontalmente** parâmetros do script de U-SQL são transmitidos dinamicamente pelo ADF utilizando a secção "parameters". Consulte a secção "parameters" na definição do pipeline.

Também pode especificar outras propriedades como degreeOfParallelism e prioridade na sua definição de pipeline para as tarefas que são executados no serviço Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parâmetros dinâmicos
A definição de pipeline de exemplo, entrada e saída parâmetros são atribuídos com valores embutidos. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

É possível usar parâmetros dinâmicos em vez disso. Por exemplo: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Neste caso, os ficheiros de entrada ainda são captados a partir da pasta /datalake/input e ficheiros de saída são gerados na pasta /datalake/output. Os nomes de ficheiro são dinâmicos com base na hora de início do setor.  


