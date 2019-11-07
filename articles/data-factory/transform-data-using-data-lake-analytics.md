---
title: Transformar dados usando o script U-SQL-Azure
description: Saiba como processar ou transformar dados executando scripts U-SQL no serviço de computação Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: abnarain
ms.openlocfilehash: 53fb6773becff9f76c9658171965fbd148e94bc8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683868"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformar dados executando scripts U-SQL no Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selecione a versão do serviço de Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-usql-activity.md)
> * [Versão atual](transform-data-using-data-lake-analytics.md)

Um pipeline em um data factory do Azure processa dados em serviços de armazenamento vinculados usando serviços de computação vinculados. Ele contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve a **Data Lake Analytics atividade u-SQL** que executa um script **u-SQL** em um serviço vinculado de computação **Azure data Lake Analytics** . 

Crie uma conta de Azure Data Lake Analytics antes de criar um pipeline com uma atividade de U-SQL Data Lake Analytics. Para saber mais sobre Azure Data Lake Analytics, consulte Introdução [ao Azure data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics serviço vinculado
Você cria um serviço vinculado **Azure data Lake Analytics** para vincular um serviço de computação Azure data Lake Analytics a uma data Factory do Azure. O Data Lake Analytics atividade U-SQL no pipeline refere-se a esse serviço vinculado. 

A tabela a seguir fornece descrições para as propriedades genéricas usadas na definição de JSON. 

| Propriedade                 | Descrição                              | Necessário                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **tipo**                 | A propriedade Type deve ser definida como: **AzureDataLakeAnalytics**. | Sim                                      |
| **accountName**          | Nome da conta de Azure Data Lake Analytics.  | Sim                                      |
| **dataLakeAnalyticsUri** | URI de Azure Data Lake Analytics.           | Não                                       |
| **subscriptionId**       | ID da assinatura do Azure                    | Não                                       |
| **resourceGroupName**    | Nome do grupo de recursos do Azure                | Não                                       |

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço
O serviço vinculado Azure Data Lake Analytics requer uma autenticação de entidade de serviço para se conectar ao serviço Azure Data Lake Analytics. Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory (AD do Azure) e conceda a ela o acesso ao Data Lake Analytics e ao Data Lake Store que ele usa. Para obter etapas detalhadas, consulte [autenticação de serviço a serviço](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que você usa para definir o serviço vinculado:

* ID da aplicação
* Chave do aplicativo 
* ID do inquilino

Conceda permissão de entidade de serviço ao seu Azure Data Lake Anatlyics usando o [Assistente para Adicionar usuário](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Necessário |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique a ID do cliente do aplicativo.     | Sim      |
| **servicePrincipalKey** | Especifique a chave do aplicativo.           | Sim      |
| **vários**              | Especifique as informações do locatário (nome de domínio ou ID do locatário) em que seu aplicativo reside. Você pode recuperá-lo passando o mouse no canto superior direito do portal do Azure. | Sim      |

**Exemplo: autenticação de entidade de serviço**
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

Para saber mais sobre o serviço vinculado, consulte [Serviços vinculados de computação](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
O trecho JSON a seguir define um pipeline com uma atividade Data Lake Analytics U-SQL. A definição da atividade tem uma referência para o serviço vinculado Azure Data Lake Analytics que você criou anteriormente. Para executar um script Data Lake Analytics U-SQL, Data Factory envia o script que você especificou para o Data Lake Analytics, e as entradas e saídas necessárias são definidas no script para Data Lake Analytics para busca e saída. 

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

A tabela a seguir descreve os nomes e as descrições das propriedades que são específicas para essa atividade. 

| Propriedade            | Descrição                              | Necessário |
| :------------------ | :--------------------------------------- | :------- |
| nome                | Nome da atividade no pipeline     | Sim      |
| descrição         | Texto que descreve o que a atividade faz.  | Não       |
| tipo                | Para Data Lake Analytics atividade U-SQL, o tipo de atividade é **DataLakeAnalyticsU-SQL**. | Sim      |
| linkedServiceName   | Serviço vinculado para Azure Data Lake Analytics. Para saber mais sobre esse serviço vinculado, consulte o artigo [Serviços vinculados de computação](compute-linked-services.md) .  |Sim       |
| scriptPath          | Caminho para a pasta que contém o script U-SQL. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim      |
| scriptLinkedService | Serviço vinculado que vincula o **Azure data Lake Store** ou o **armazenamento do Azure** que contém o script para o data Factory | Sim      |
| degreeOfParallelism | O número máximo de nós usados simultaneamente para executar o trabalho. | Não       |
| prioridade            | Determina quais trabalhos de todos os que estão na fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. | Não       |
| parâmetros          | Parâmetros para passar para o script U-SQL.    | Não       |
| runtimeVersion      | Versão de tempo de execução do mecanismo U-SQL a ser usada. | Não       |
| CompilationMode     | <p>Modo de compilação do U-SQL. Deve ser um destes valores: **Semantic:** somente executar verificações semânticas e verificações de integridade necessárias, **Full:** executar a compilação completa, incluindo verificação de sintaxe, otimização, geração de código, etc., **SingleBox:** executar todo o compilação, com a configuração TargetType para SingleBox. Se você não especificar um valor para essa propriedade, o servidor determinará o modo de compilação ideal. | Não |

Consulte [SearchLogProcessing. txt](#sample-u-sql-script) para a definição do script. 

## <a name="sample-u-sql-script"></a>Script U-SQL de exemplo

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

No exemplo de script acima, a entrada e a saída para o script são definidas em\@e\@parâmetros **de** **saída** . Os valores para **\@** os parâmetros in e **\@out** no script U-SQL são passados dinamicamente pelo data Factory usando a seção ' Parameters '. 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

Nesse caso, os arquivos de entrada ainda são coletados na pasta/datalake/Input e os arquivos de saída são gerados na pasta/datalake/output Os nomes de arquivo são dinâmicos com base na hora de início da janela sendo passada quando o pipeline é disparado.  

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos que explicam como transformar dados de outras maneiras: 

* [Atividade do hive](transform-data-using-hadoop-hive.md)
* [Atividade Pig](transform-data-using-hadoop-pig.md)
* [Atividade MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming do Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade do Spark](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Atividade de execução de Machine Learning lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
