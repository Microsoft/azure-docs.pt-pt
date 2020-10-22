---
title: Transforme dados usando o script U-SQL
description: Saiba como processar ou transformar dados executando scripts U-SQL no serviço de computação Azure Data Lake Analytics.
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
ms.openlocfilehash: ab5a76a9734ca879e468a1921554f91680be8339
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370666"
---
# <a name="process-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Processar dados executando scripts U-SQL no Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que está a utilizar:"]
> * [Versão 1](v1/data-factory-usql-activity.md)
> * [Versão atual](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Um oleoduto numa fábrica de dados da Azure processa dados em serviços de armazenamento ligados utilizando serviços de computação ligados. Contém uma sequência de atividades em que cada atividade realiza uma operação de processamento específica. Este artigo descreve a **Atividade U-SQL do Data Lake Analytics** que executa um script **U-SQL** num serviço de computação **Azure Data Lake Analytics.** 

Crie uma conta Azure Data Lake Analytics antes de criar um oleoduto com uma Atividade U-SQL do Data Lake Analytics. Para saber mais sobre a Azure Data Lake Analytics, consulte [Começar com a Azure Data Lake Analytics.](../data-lake-analytics/data-lake-analytics-get-started-portal.md)


## <a name="azure-data-lake-analytics-linked-service"></a>Serviço ligado a Azure Data Lake Analytics
Você cria um serviço **Azure Data Lake Analytics** ligado para ligar um serviço de computação Azure Data Lake Analytics a uma fábrica de dados Azure. A atividade U-SQL do Data Lake Analytics no oleoduto refere-se a este serviço ligado. 

A tabela a seguir fornece descrições para as propriedades genéricas utilizadas na definição JSON. 

| Propriedade                 | Descrição                              | Obrigatório                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **tipo**                 | A propriedade tipo deve ser configurada para: **AzureDataLakeAnalytics**. | Yes                                      |
| **accountName**          | Nome da conta Azure Data Lake Analytics.  | Yes                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics URI.           | No                                       |
| **subscriçãoId**       | ID de assinatura Azure                    | No                                       |
| **nome do Grupo de Recursos**    | Nome do grupo de recursos do Azure                | No                                       |

### <a name="service-principal-authentication"></a>Autenticação do principal de serviço
O serviço Azure Data Lake Analytics está ligado a uma autenticação principal do serviço para se ligar ao serviço Azure Data Lake Analytics. Para utilizar a autenticação principal do serviço, registe uma entidade de aplicação no Azure Ative Directory (Azure AD) e conceda-lhe o acesso tanto ao Data Lake Analytics como à Data Lake Store que utiliza. Para etapas detalhadas, consulte [a autenticação do Serviço ao Serviço.](../data-lake-store/data-lake-store-authenticate-using-active-directory.md) Tome nota dos seguintes valores, que utiliza para definir o serviço ligado:

* ID da Aplicação
* Chave de aplicação 
* ID do inquilino

Conceder permissão principal do serviço aos seus Azure Data Lake Anatlyics utilizando o [Add User Wizard](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Utilize a autenticação principal do serviço especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Obrigatório |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique a identificação do cliente da aplicação.     | Yes      |
| **servicePrincipalKey** | Especifique a chave da aplicação.           | Yes      |
| **tenant**              | Especifique a informação do inquilino (nome de domínio ou ID do inquilino) sob a qual a sua aplicação reside. Pode recuperá-lo pairando sobre o rato no canto superior direito do portal Azure. | Yes      |

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

Para saber mais sobre o serviço ligado, consulte [os serviços ligados ao Compute.](compute-linked-services.md)

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade de U-SQL do Data Lake Analytics
O seguinte snippet JSON define um oleoduto com uma Atividade U-SQL do Data Lake Analytics. A definição de atividade tem uma referência ao serviço ligado Azure Data Lake Analytics que criou anteriormente. Para executar um script U-SQL do Data Lake Analytics, a Data Factory submete o script especificado para o Data Lake Analytics, e as entradas e saídas necessárias são definidas no script para data lake analytics para obter e obter. 

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

A tabela seguinte descreve nomes e descrições de propriedades específicas a esta atividade. 

| Propriedade            | Descrição                              | Obrigatório |
| :------------------ | :--------------------------------------- | :------- |
| name                | Nome da atividade no oleoduto     | Yes      |
| descrição         | Texto descrevendo o que a atividade faz.  | No       |
| tipo                | Para a atividade U-SQL do Data Lake Analytics, o tipo de atividade é  **DataLakeAnalyticsU-SQL**. | Yes      |
| linkedServiceName   | Serviço ligado ao Azure Data Lake Analytics. Para saber mais sobre este serviço ligado, consulte o artigo [de serviços ligados a Compute.](compute-linked-services.md)  |Yes       |
| scriptPath          | Caminho para a pasta que contém o script U-SQL. O nome do ficheiro é sensível a casos. | Yes      |
| scriptLinkedService | Serviço ligado que liga a **Azure Data Lake Store** ou **Azure Storage** que contém o script para a fábrica de dados | Yes      |
| graus DeParallelismo | O número máximo de nós usados simultaneamente para gerir o trabalho. | No       |
| prioridade            | Determina quais os trabalhos de todos os que estão na fila que devem ser selecionados para serem executados primeiro. Quanto menor for o número, maior é a prioridade. | No       |
| parâmetros          | Parâmetros para passar para o script U-SQL.    | No       |
| execuçãoVersão      | Versão de tempo de execução do motor U-SQL para utilizar. | No       |
| compilaçãoMode     | <p>Modo de compilação de U-SQL. Deve ser um destes valores: **Semântico:** Apenas efetue verificações semânticas e verificações de sanidade necessárias, **Full:** Realize a compilação completa, incluindo verificação de sintaxe, otimização, geração de código, etc., **SingleBox:** Execute a compilação completa, com a definição targetType para SingleBox. Se não especificar um valor para esta propriedade, o servidor determina o modo de compilação ideal. | No |

Consulte [SearchLogProcessing.txt](#sample-u-sql-script) para a definição do script. 

## <a name="sample-u-sql-script"></a>Guião U-SQL de amostra

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

No exemplo do script acima, a entrada e saída do script é definida ** \@ dentro** e ** \@ fora** dos parâmetros. Os valores para parâmetros ** \@ de entrada** e ** \@ saída** no script U-SQL são aprovados dinamicamente pela Data Factory utilizando a secção "parâmetros". 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

Neste caso, os ficheiros de entrada ainda são recolhidos a partir da pasta /datalake/entrada e os ficheiros de saída são gerados na pasta /datalake/output. Os nomes dos ficheiros são dinâmicos com base na hora de início da janela ser passada quando o gasoduto é acionado.  

## <a name="next-steps"></a>Passos seguintes
Veja os seguintes artigos que explicam como transformar dados de outras formas: 

* [Atividade da colmeia](transform-data-using-hadoop-hive.md)
* [Atividade do porco](transform-data-using-hadoop-pig.md)
* [Atividade mapReduce](transform-data-using-hadoop-map-reduce.md)
* [Atividade de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Atividade de faísca](transform-data-using-spark.md)
* [Atividade personalizada do .NET](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (clássico) Atividade de execução de lote](transform-data-using-machine-learning.md)
* [Atividade de procedimento armazenado](transform-data-using-stored-procedure.md)
