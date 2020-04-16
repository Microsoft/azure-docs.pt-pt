---
title: Transforme dados usando o script U-SQL
description: Aprenda a processar ou transformar dados executando scripts U-SQL no serviço de computação Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 427b7fff7b8f76412d7bd9d63aeb64583637779c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418971"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformar dados ao executar scripts U-SQL no Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-usql-activity.md)
> * [Versão atual](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Um oleoduto numa fábrica de dados azure processa dados em serviços de armazenamento ligados utilizando serviços de cálculo ligados. Contém uma sequência de atividades em que cada atividade realiza uma operação de processamento específica. Este artigo descreve a **Atividade U-SQL** do Data Lake Analytics que executa um script **U-SQL** num serviço ligado à computação **Azure Data Lake Analytics.** 

Crie uma conta Azure Data Lake Analytics antes de criar um pipeline com uma Atividade U-SQL do Data Lake Analytics. Para saber mais sobre o Azure Data Lake Analytics, consulte [Start start with Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Serviço ligado ao Lago de Dados Azure Analytics
Cria um serviço ligado ao **Azure Data Lake Analytics** para ligar um serviço de computação Azure Data Lake Analytics a uma fábrica de dados Azure. A atividade de Data Lake Analytics U-SQL no pipeline refere-se a este serviço ligado. 

O quadro seguinte apresenta descrições para as propriedades genéricas utilizadas na definição JSON. 

| Propriedade                 | Descrição                              | Necessário                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **tipo**                 | A propriedade do tipo deve ser definida para: **AzureDataLakeAnalytics**. | Sim                                      |
| **nome da conta**          | Nome da conta azure Data Lake Analytics.  | Sim                                      |
| **dadosLakeAnalyticsUri** | Azure Data Lake Analytics URI.           | Não                                       |
| **subscriptionId**       | ID de subscrição azure                    | Não                                       |
| **resourceGroupName**    | Nome do grupo de recursos do Azure                | Não                                       |

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço
O serviço ligado ao Azure Data Lake Analytics requer uma autenticação principal de serviço para se ligar ao serviço Azure Data Lake Analytics. Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD) e conceda-lhe o acesso tanto ao Data Lake Analytics como à Data Lake Store que utiliza. Para obter passos detalhados, consulte a [autenticação serviço-a-serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Tome nota dos seguintes valores, que utiliza para definir o serviço vinculado:

* ID da aplicação
* Chave de aplicação 
* ID do inquilino

Conceda a permissão principal do serviço ao seu Azure Data Lake Anatlyics utilizando o Assistente de [Utilizador Add](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Utilize a autenticação principal do serviço especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| **serviçoPrincipalId**  | Especifique a identificação do cliente do pedido.     | Sim      |
| **serviçoPrincipalKey** | Especifique a chave da aplicação.           | Sim      |
| **inquilino**              | Especifique as informações do arrendatário (nome de domínio ou ID do inquilino) sob a qual reside a sua candidatura. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Sim      |

**Exemplo: Autenticação principal do serviço**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Para saber mais sobre o serviço ligado, consulte [os serviços ligados à Compute.](compute-linked-services.md)

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
O seguinte snippet JSON define um oleoduto com uma Atividade U-SQL do Data Lake Analytics. A definição de atividade tem uma referência ao serviço ligado ao Azure Data Lake Analytics que criou anteriormente. Para executar um script De Data Lake Analytics U-SQL, data Factory submete o script especificado para o Data Lake Analytics, e as inputs e saídas necessárias são definidas no script para Data Lake Analytics para obter e saída. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

A tabela seguinte descreve nomes e descrições de propriedades específicas desta atividade. 

| Propriedade            | Descrição                              | Necessário |
| :------------------ | :--------------------------------------- | :------- |
| nome                | Nome da atividade no oleoduto     | Sim      |
| descrição         | Texto descrevendo o que a atividade faz.  | Não       |
| tipo                | Para a atividade U-SQL do Data Lake Analytics, o tipo de atividade é **DataLakeAnalyticsU-SQL**. | Sim      |
| linkedServiceName   | Serviço Ligado ao Azure Data Lake Analytics. Para conhecer este serviço ligado, consulte o artigo de [serviços ligados à Compute.](compute-linked-services.md)  |Sim       |
| scriptPath          | Caminho para pasta que contém o script U-SQL. O nome do ficheiro é sensível a casos. | Sim      |
| scriptLinkedService | Serviço ligado que liga a **Azure Data Lake Store** ou **O Armazenamento Azure** que contém o script para a fábrica de dados | Sim      |
| degreeOfParallelismo | O número máximo de nós usado simultaneamente para gerir o trabalho. | Não       |
| prioridade            | Determina quais os postos de trabalho que estão em fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. | Não       |
| parâmetros          | Parâmetros para passar para o script U-SQL.    | Não       |
| versão tempo de execução      | Versão de tempo de execução do motor U-SQL para usar. | Não       |
| compilaçãoMode     | <p>Modo de compilação de U-SQL. Deve ser um desses valores: **Semântico:** Apenas efetuar verificações semânticas e verificações de sanidade necessárias, **Full:** Execute a compilação completa, incluindo verificação de sintaxe, otimização, geração de códigos, etc., **SingleBox:** Execute a compilação completa, com definição targetType para SingleBox. Se não especificar um valor para esta propriedade, o servidor determina o modo de compilação ideal. | Não |

Consulte [SearchLogProcessing.txt](#sample-u-sql-script) para obter a definição de script. 

## <a name="sample-u-sql-script"></a>Exemplo de script U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
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

No exemplo do script acima, a entrada e saída para o script é definida ** \@dentro** e ** \@fora** dos parâmetros. Os valores para ** \@** os parâmetros dentro e ** \@fora** do script U-SQL são passados dinamicamente pela Data Factory utilizando a secção de 'parâmetros'. 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

Neste caso, os ficheiros de entrada são ainda recolhidos a partir da pasta /datalake/input e os ficheiros de saída são gerados na pasta /datalake/output. Os nomes dos ficheiros são dinâmicos com base na hora de início da janela que está a ser passada quando o gasoduto é acionado.  

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade de porco](transform-data-using-hadoop-pig.md)
* [MapReduzir a atividade](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de lote de aprendizagem automática](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
