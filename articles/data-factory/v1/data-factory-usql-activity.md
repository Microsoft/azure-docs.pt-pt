---
title: Transformar dados usando o script U-SQL - Azure
description: Saiba como processar ou transformar dados executando scripts U-SQL no serviço de computação Azure Data Lake Analytics - versão 1.
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 5931cb28721e8658a771ceea1cd94624a0c09f7c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100392923"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformar dados ao executar scripts U-SQL no Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-usql-activity.md)
> * [Versão 2 (versão atual)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte a [U-SQL Activity em V2](../transform-data-using-data-lake-analytics.md).

Um oleoduto numa fábrica de dados da Azure processa dados em serviços de armazenamento ligados utilizando serviços de computação ligados. Contém uma sequência de atividades em que cada atividade realiza uma operação de processamento específica. Este artigo descreve a **Atividade U-SQL do Data Lake Analytics** que executa um script **U-SQL** num serviço de computação **Azure Data Lake Analytics.** 

Crie uma conta Azure Data Lake Analytics antes de criar um oleoduto com uma Atividade U-SQL do Data Lake Analytics. Para saber mais sobre a Azure Data Lake Analytics, consulte [Começar com a Azure Data Lake Analytics.](../../data-lake-analytics/data-lake-analytics-get-started-portal.md)

Reveja o tutorial do [seu primeiro pipeline](data-factory-build-your-first-pipeline.md) para obter etapas detalhadas para criar uma fábrica de dados, serviços ligados, conjuntos de dados e um oleoduto. Utilize snippets JSON com Data Factory Editor ou Visual Studio ou Azure PowerShell para criar entidades da Data Factory.

## <a name="supported-authentication-types"></a>Tipos de autenticação suportados
A atividade U-SQL suporta abaixo os tipos de autenticação contra data lake analytics:
* Autenticação do principal de serviço
* Autenticação credencial do utilizador (OAuth) 

Recomendamos que utilize a autenticação principal do serviço, especialmente para uma execução U-SQL programada. O comportamento de expiração do token pode ocorrer com a autenticação credencial do utilizador. Para mais detalhes de configuração, consulte a secção [de propriedades de serviço Linked.](#azure-data-lake-analytics-linked-service)

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço Ligado Azure Data Lake Analytics
Você cria um serviço **Azure Data Lake Analytics** ligado para ligar um serviço de computação Azure Data Lake Analytics a uma fábrica de dados Azure. A atividade U-SQL do Data Lake Analytics no oleoduto refere-se a este serviço ligado. 

A tabela a seguir fornece descrições para as propriedades genéricas utilizadas na definição JSON. Pode ainda escolher entre o principal de serviço e a autenticação credencial do utilizador.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **tipo** |A propriedade tipo deve ser configurada para: **AzureDataLakeAnalytics**. |Yes |
| **contaName** |Nome da conta Azure Data Lake Analytics. |Yes |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics URI. |No |
| **subscriçãoId** |Id de assinatura Azure |Não (Se não for especificada, utiliza-se a subscrição da fábrica de dados). |
| **nome do Grupo de Recursos** |Nome do grupo de recursos do Azure |Não (Se não for especificado, é utilizado o grupo de recursos da fábrica de dados). |

### <a name="service-principal-authentication-recommended"></a>Autenticação principal do serviço (recomendado)
Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD) e conceda-lhe o acesso à Data Lake Store. Para etapas detalhadas, consulte [a autenticação do Serviço ao Serviço.](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md) Tome nota dos seguintes valores, que utiliza para definir o serviço ligado:
* ID da Aplicação
* Chave de aplicação 
* ID do inquilino

Utilize a autenticação principal do serviço especificando as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique a identificação do cliente da aplicação. | Yes |
| **servicePrincipalKey** | Especifique a chave da aplicação. | Yes |
| **tenant** | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Yes |

**Exemplo: Autenticação principal do serviço**
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

### <a name="user-credential-authentication"></a>Autenticação credencial do utilizador
Em alternativa, pode utilizar a autenticação credencial do utilizador para data lake analytics especificando as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **autorização** | Clique no botão **Authorize** no Editor de Fábrica de Dados e introduza a sua credencial que atribui o URL de autorização autogerado a esta propriedade. | Yes |
| **sessionId** | Identificação da Sessão OAuth da Sessão de Autorização da OAuth. Cada ID de sessão é único e só pode ser usado uma vez. Esta definição é gerada automaticamente quando utiliza o Editor de Fábrica de Dados. | Yes |

**Exemplo: Autenticação credencial do utilizador**
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

#### <a name="token-expiration"></a>Expiração simbólica
O código de autorização gerado através do botão **Authorize** expira após algum tempo. Consulte a tabela seguinte para os tempos de validade para diferentes tipos de contas de utilizador. Poderá ver a seguinte mensagem de erro quando o **token** de autenticação expirar : Erro de funcionamento credencial: invalid_grant - AADSTS70002: Credenciais de validação de erros. AADSTS70008: A subvenção de acesso prestada é caducada ou revogada. ID de vestígio: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31

| Tipo de utilizador | Expira depois |
|:--- |:--- |
| Contas de utilizador NÃO geridas pelo Azure Ative Directory @hotmail.com (, @live.com etc.) |12 horas |
| Contas de utilizadores geridas por Azure Ative Directory (AAD) |14 dias após a última corrida. <br/><br/>90 dias, se uma fatia baseada no serviço ligado à OAuth for executado pelo menos uma vez a cada 14 dias. |

Para evitar/resolver este erro, reautorize a utilização do botão **Authorize** quando o **token expirar** e recolocar o serviço ligado. Também pode gerar valores para as propriedades **de sessãoId** e **de autorização** programáticamente usando o código da seguinte forma:

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

Consulte [a classe AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), [AzureDataLakeAnalyticsLinkedService Class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice), e [AutorizaçõesSessionGetResponse Class](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) para mais detalhes sobre as classes data factory utilizadas no código. Adicione uma referência a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
O seguinte snippet JSON define um oleoduto com uma Atividade U-SQL do Data Lake Analytics. A definição de atividade tem uma referência ao serviço ligado Azure Data Lake Analytics que criou anteriormente.   

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

A tabela seguinte descreve nomes e descrições de propriedades específicas a esta atividade. 

| Propriedade            | Descrição                              | Obrigatório                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| tipo                | A propriedade tipo deve ser definida para **DataLakeAnalyticsU-SQL**. | Yes                                      |
| linkedServiceName   | Referência ao Azure Data Lake Analytics registado como um serviço ligado na Data Factory | Yes                                      |
| scriptPath          | Caminho para a pasta que contém o script U-SQL. O nome do ficheiro é sensível a casos. | Não (se utilizar o script)                   |
| scriptLinkedService | Serviço ligado que liga o armazenamento que contém o script à fábrica de dados | Não (se utilizar o script)                   |
| script              | Especifique o script inline em vez de especificar scriptPath e scriptLinkedService. Por exemplo: `"script": "CREATE DATABASE test"`. | Não (se utilizar scriptPath e scriptLinkedService) |
| graus DeParallelismo | O número máximo de nós usados simultaneamente para gerir o trabalho. | No                                       |
| prioridade            | Determina quais os trabalhos de todos os que estão na fila que devem ser selecionados para serem executados primeiro. Quanto menor for o número, maior é a prioridade. | No                                       |
| parâmetros          | Parâmetros para o script U-SQL          | No                                       |
| execuçãoVersão      | Versão de tempo de execução do motor U-SQL para usar | No                                       |
| compilaçãoMode     | <p>Modo de compilação de U-SQL. Deve ser um destes valores:</p> <ul><li>**Semântica:** Apenas efetuar verificações semânticas e verificações de sanidade necessárias.</li><li>**Cheio:** Execute a compilação completa, incluindo verificação de sintaxe, otimização, geração de código, etc.</li><li>**SingleBox:** Execute a compilação completa, com a definição targetType para SingleBox.</li></ul><p>Se não especificar um valor para esta propriedade, o servidor determina o modo de compilação ideal. </p> | No                                       |

Consulte [SearchLogProcessing.txt Definição de Script](#sample-u-sql-script) para a definição do script. 

## <a name="sample-input-and-output-datasets"></a>Conjuntos de dados de entrada e saída de amostras
### <a name="input-dataset"></a>Conjunto de dados de entrada
Neste exemplo, os dados de entrada residem numa Loja de Data Lake Azure (ficheiro SearchLog.tsv na pasta datalake/input). 

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
Neste exemplo, os dados de saída produzidos pelo script U-SQL são armazenados numa Azure Data Lake Store (pasta datalake/output). 

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

### <a name="sample-data-lake-store-linked-service"></a>Serviço ligado à loja de dados de amostras
Aqui está a definição da amostra Azure Data Lake Store serviço ligado usado pelos conjuntos de dados de entrada/saída. 

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

Consulte [os dados do Movimento de e para a Azure Data Lake Store](data-factory-azure-datalake-connector.md) para descrições das propriedades da JSON. 

## <a name="sample-u-sql-script"></a>Guião U-SQL da amostra

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

Os valores para parâmetros **\@ de entrada** e **\@ saída** no script U-SQL são aprovados dinamicamente pela ADF utilizando a secção "parâmetros". Consulte a secção "parâmetros" na definição do gasoduto.

Você pode especificar outras propriedades, como o grauOfParallelismo e prioridade também na sua definição de pipeline para os trabalhos que funcionam no serviço Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parâmetros dinâmicos
Na definição do gasoduto de amostra, os parâmetros de entrada e saída são atribuídos com valores codificados. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Em vez disso, é possível utilizar parâmetros dinâmicos. Por exemplo: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Neste caso, os ficheiros de entrada ainda são recolhidos a partir da pasta /datalake/entrada e os ficheiros de saída são gerados na pasta /datalake/output. Os nomes dos ficheiros são dinâmicos com base na hora de início da fatia.