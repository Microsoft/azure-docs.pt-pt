---
title: Transforme dados usando script U-SQL - Azure
description: Aprenda a processar ou transformar dados executando scripts U-SQL no serviço de computação Azure Data Lake Analytics.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74927905"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformar dados ao executar scripts U-SQL no Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](data-factory-usql-activity.md)
> * [Versão 2 (versão atual)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte a [Atividade U-SQL em V2](../transform-data-using-data-lake-analytics.md).

Um oleoduto numa fábrica de dados azure processa dados em serviços de armazenamento ligados utilizando serviços de cálculo ligados. Contém uma sequência de atividades em que cada atividade realiza uma operação de processamento específica. Este artigo descreve a **Atividade U-SQL** do Data Lake Analytics que executa um script **U-SQL** num serviço ligado à computação **Azure Data Lake Analytics.** 

Crie uma conta Azure Data Lake Analytics antes de criar um pipeline com uma Atividade U-SQL do Data Lake Analytics. Para saber mais sobre o Azure Data Lake Analytics, consulte [Start start with Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Reveja o [seu primeiro tutorial](data-factory-build-your-first-pipeline.md) de pipeline para obter passos detalhados para criar uma fábrica de dados, serviços ligados, conjuntos de dados e um pipeline. Utilize snippets JSON com Data Factory Editor ou Visual Studio ou Azure PowerShell para criar entidades data Factory.

## <a name="supported-authentication-types"></a>Tipos de autenticação suportado
A atividade U-SQL suporta abaixo os tipos de autenticação contra data lake Analytics:
* Autenticação do principal de serviço
* Autenticação credencial do utilizador (OAuth) 

Recomendamos que utilize a autenticação principal do serviço, especialmente para uma execução programada da U-SQL. O comportamento de expiração do token pode ocorrer com a autenticação credencial do utilizador. Para mais detalhes de configuração, consulte a secção de propriedades de [serviço Linked.](#azure-data-lake-analytics-linked-service)

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço ligado ao Lago de Dados Azure Analytics
Cria um serviço ligado ao **Azure Data Lake Analytics** para ligar um serviço de computação Azure Data Lake Analytics a uma fábrica de dados Azure. A atividade de Data Lake Analytics U-SQL no pipeline refere-se a este serviço ligado. 

O quadro seguinte apresenta descrições para as propriedades genéricas utilizadas na definição JSON. Pode ainda escolher entre o diretor de serviço e a autenticação credencial do utilizador.

| Propriedade | Descrição | Necessário |
| --- | --- | --- |
| **tipo** |A propriedade do tipo deve ser definida para: **AzureDataLakeAnalytics**. |Sim |
| **nome da conta** |Nome da conta azure Data Lake Analytics. |Sim |
| **dadosLakeAnalyticsUri** |Azure Data Lake Analytics URI. |Não |
| **subscriptionId** |Id de subscrição azure |Não (se não especificado, utiliza-se a subscrição da fábrica de dados). |
| **resourceGroupName** |Nome do grupo de recursos do Azure |Não (se não especificado, é utilizado um grupo de recursos da fábrica de dados). |

### <a name="service-principal-authentication-recommended"></a>Autenticação do principal de serviço (recomendado)
Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD) e conceda-lhe o acesso à Data Lake Store. Para obter passos detalhados, consulte a [autenticação serviço-a-serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:
* ID da aplicação
* Chave de aplicação 
* ID do inquilino

Utilize a autenticação principal do serviço especificando as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **serviçoPrincipalId** | Especifique a identificação do cliente do pedido. | Sim |
| **serviçoPrincipalKey** | Especifique a chave da aplicação. | Sim |
| **inquilino** | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Sim |

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
Em alternativa, pode utilizar a autenticação credencial do utilizador para data lake Analytics, especificando as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| **autorização** | Clique no botão **Autorizar** no Editor da Fábrica de Dados e introduza a sua credencial que atribui o URL de autorização autogerado a esta propriedade. | Sim |
| **sessionId** | OAuth session ID da sessão de autorização da OAuth. Cada id da sessão é único e só pode ser usado uma vez. Esta definição é gerada automaticamente quando utiliza o Editor da Fábrica de Dados. | Sim |

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

#### <a name="token-expiration"></a>Expiração do token
O código de autorização gerado através do botão **Autorizar** expira passado algum tempo. Consulte a tabela seguinte para os prazos de validade para diferentes tipos de contas de utilizador. Pode ver a seguinte mensagem de erro quando o **token**de autenticação expirar : Erro de operação credencial: invalid_grant - AADSTS70002: Erro validando credenciais. AADSTS70008: A subvenção de acesso fornecida é expirada ou revogada. Identificação do traço: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z

| Tipo de utilizador | Expira depois |
|:--- |:--- |
| Contas de utilizador NÃO geridas pelo@hotmail.comAzure Ative Directory (, @live.cometc.) |12 horas |
| Contas de utilizadores geridas pelo Azure Ative Directory (AAD) |14 dias após a última corrida. <br/><br/>90 dias, se uma fatia baseada no serviço ligado baseado em OAuth funciona pelo menos uma vez a cada 14 dias. |

Para evitar/resolver este erro, reautorize a utilização do botão **Autorizar** quando o **token expirar** e recolocar o serviço ligado. Também pode gerar valores para **propriedades sessõesId** e **autorização** programáticamente usando código da seguinte forma:

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

Consulte os tópicos da [Classe AzureDataLakeStoreLinkedService,](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx) [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)e [AuthorizationSessionGetResponse Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes Data Factory utilizadas no código. Adicione uma referência a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
O seguinte snippet JSON define um oleoduto com uma Atividade U-SQL do Data Lake Analytics. A definição de atividade tem uma referência ao serviço ligado ao Azure Data Lake Analytics que criou anteriormente.   

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

A tabela seguinte descreve nomes e descrições de propriedades específicas desta atividade. 

| Propriedade            | Descrição                              | Necessário                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| tipo                | A propriedade do tipo deve ser definida para **DataLakeAnalyticsU-SQL**. | Sim                                      |
| linkedServiceName   | Referência ao Azure Data Lake Analytics registado como um serviço ligado na Data Factory | Sim                                      |
| scriptPath          | Caminho para pasta que contém o script U-SQL. O nome do ficheiro é sensível a casos. | Não (se usar o script)                   |
| scriptLinkedService | Serviço ligado que liga o armazenamento que contém o script à fábrica de dados | Não (se usar o script)                   |
| .              | Especifique o script inline em vez de especificar scriptPath e scriptLinkedService. Por exemplo: `"script": "CREATE DATABASE test"`. | Não (se utilizar scriptPath e scriptLinkedService) |
| degreeOfParallelismo | O número máximo de nós usado simultaneamente para gerir o trabalho. | Não                                       |
| prioridade            | Determina quais os postos de trabalho que estão em fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. | Não                                       |
| parâmetros          | Parâmetros para o script U-SQL          | Não                                       |
| versão tempo de execução      | Versão de tempo de execução do motor U-SQL para usar | Não                                       |
| compilaçãoMode     | <p>Modo de compilação de U-SQL. Deve ser um destes valores:</p> <ul><li>**Semântico:** Apenas efetue verificações semânticas e verificações de sanidade necessárias.</li><li>**Cheio:** Realizar a compilação completa, incluindo verificação de sintaxe, otimização, geração de códigos, etc.</li><li>**Caixa Única:** Execute a compilação completa, com a definição targetType para SingleBox.</li></ul><p>Se não especificar um valor para esta propriedade, o servidor determina o modo de compilação ideal. </p> | Não                                       |

Consulte a definição de [script SearchLogProcessing.txt](#sample-u-sql-script) para a definição do script. 

## <a name="sample-input-and-output-datasets"></a>Conjuntos de dados de entrada e saída de amostras
### <a name="input-dataset"></a>Conjunto de dados de entrada
Neste exemplo, os dados de entrada residem numa Loja de Lagos De Dados Azure (ficheiro SearchLog.tsv na pasta datalake/input). 

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
Neste exemplo, os dados de saída produzidos pelo script U-SQL são armazenados numa Loja de Lagos De Dados Azure (pasta datalake/saída). 

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

### <a name="sample-data-lake-store-linked-service"></a>Serviço ligado à loja de dados de dados de dados
Aqui está a definição da amostra De serviço ligado à Loja do Lago de Dados, utilizada pelos conjuntos de dados de entrada/saída. 

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

Consulte [os dados de e para a Azure Data Lake Store](data-factory-azure-datalake-connector.md) para descrições das propriedades da JSON. 

## <a name="sample-u-sql-script"></a>Exemplo U-SQL Script

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

Os valores para ** \@** os parâmetros dentro e ** \@fora** do script U-SQL são passados dinamicamente pela ADF utilizando a secção de "parâmetros". Consulte a secção de "parâmetros" na definição do gasoduto.

Pode especificar outras propriedades, como o degreeOfParallelismo e prioridade também na definição do seu pipeline para os trabalhos que funcionam no serviço Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parâmetros dinâmicos
Na definição do gasoduto da amostra, os parâmetros dentro e fora são atribuídos com valores codificados. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

É possível utilizar parâmetros dinâmicos. Por exemplo: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Neste caso, os ficheiros de entrada são ainda recolhidos a partir da pasta /datalake/input e os ficheiros de saída são gerados na pasta /datalake/output. Os nomes dos ficheiros são dinâmicos com base na hora de início da fatia.  


