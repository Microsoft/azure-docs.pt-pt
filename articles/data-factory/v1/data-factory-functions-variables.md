---
title: Funções de fábrica de dados e variáveis do sistema
description: Fornece uma lista de funções da Azure Data Factory e variáveis do sistema
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9acc369e24d1bac92dea3fb6ae391a410e5f6c3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73667658"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Fábrica de Dados Azure - Funções e Variáveis do Sistema
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver a utilizar a versão atual do serviço Data Factory, consulte [as variáveis do Sistema na Fábrica](../control-flow-system-variables.md)de Dados .

Este artigo fornece informações sobre funções e variáveis suportadas pela Azure Data Factory.

## <a name="data-factory-system-variables"></a>Variáveis do sistema data factory

| Nome da Variável | Descrição | Âmbito do objeto | Casos de âmbito e utilização da JSON |
| --- | --- | --- | --- |
| Início da janela |Intervalo de início de tempo para a janela de execução da atividade atual |atividade |<ol><li>Especifique consultas de seleção de dados. Consulte artigos de conector referenciados no artigo Atividades de Movimento de [Dados.](data-factory-data-movement-activities.md)</li> |
| WindowEnd |Intervalo de tempo para a janela de execução da atividade atual |atividade |o mesmo que o WindowStart. |
| SliceStart |Início do intervalo de tempo para a produção de fatia de dados |atividade<br/>conjunto de dados |<ol><li>Especifique caminhos dinâmicos de pastas e nomes de ficheiros enquanto trabalha com [conjuntos](data-factory-onprem-file-system-connector.md)de dados do Sistema De Ficheiros [Azure Blob](data-factory-azure-blob-connector.md) e File .</li><li>Especifique as dependências de entrada com funções de fábrica de dados na recolha de inputs de atividade.</li></ol> |
| SliceEnd |Intervalo de fim de tempo para a fatia de dados atual. |atividade<br/>conjunto de dados |o mesmo que o SliceStart. |

> [!NOTE]
> Atualmente, a fábrica de dados requer que o calendário especificado na atividade corresponda exatamente ao calendário especificado na disponibilidade do conjunto de dados de saída. Portanto, WindowStart, WindowEnd e SliceStart e SliceEnd mapeiam sempre para o mesmo período de tempo e uma única fatia de saída.
> 

### <a name="example-for-using-a-system-variable"></a>Exemplo para usar uma variável do sistema
No exemplo seguinte, ano, mês, dia e hora do **SliceStart** são extraídos em variáveis separadas que são usadas pelas **propriedades da pastaPath** e **fileName.**

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Funções da Fábrica de Dados
Pode utilizar funções na fábrica de dados juntamente com variáveis do sistema para os seguintes fins:

1. Especificar consultas de seleção de dados (ver artigos de conector referenciados pelo artigo Atividades de Movimento de [Dados.](data-factory-data-movement-activities.md)
   
   A sintaxe para invocar uma função de fábrica de dados é: ** $$ \<função>** para consultas de seleção de dados e outras propriedades na atividade e conjuntos de dados.  
2. Especificando dependências de entrada com funções de fábrica de dados na recolha de inputs de atividade.
   
    $$ não é necessário para especificar expressões de dependência de entrada.     

Na amostra seguinte, a propriedade **sqlReaderQuery** num ficheiro JSON é `Text.Format` atribuída a um valor devolvido pela função. Esta amostra também utiliza uma variável de sistema chamada **WindowStart,** que representa o tempo de início da janela de execução da atividade.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Consulte o tópico de strings de formato de data e tempo personalizado que descreve [diferentes](https://msdn.microsoft.com/library/8kb3ddd4.aspx) opções de formatação que pode usar (por exemplo: ay vs. yyyy). 

### <a name="functions"></a>Funções
As tabelas a seguir listam todas as funções na Azure Data Factory:

| Categoria | Função | Parâmetros | Descrição |
| --- | --- | --- | --- |
| Hora |Addhours (X,Y) |X: DataTime <br/><br/>Y: int |Adiciona horas Y ao tempo x. <br/><br/>Exemplo: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Hora |AddMinutes (X,Y) |X: DataTime <br/><br/>Y: int |Adiciona minutos Y a X.<br/><br/>Exemplo: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Hora |Início de Hora(X) |X: Data |Obtém o tempo de partida para a hora representada pelo componente de hora de X. <br/><br/>Exemplo: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |Adddays (X,Y) |X: DataTime<br/><br/>Y: int |Adiciona y dias a X. <br/><br/>Exemplo: 9/15/2013 12:00:00 PM + 2 dias = 9/17/2013 12:00:00 PM.<br/><br/>Também pode subtrair dias especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |Addmonths (X,Y) |X: DataTime<br/><br/>Y: int |Adiciona y meses a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Também pode subtrair meses especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |Adums (X,Y) |X: DataTime <br/><br/>Y: int |Adiciona Y * 3 meses a X.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |Addweeks (X,Y) |X: DataTime<br/><br/>Y: int |Adiciona Y * 7 dias a X<br/><br/>Exemplo: 9/15/2013 12:00:00PM + 1 semana = 9/22/2013 12:00:00<br/><br/>Também pode subtrair semanas especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears (X,Y) |X: DataTime<br/><br/>Y: int |Adiciona y anos a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Também pode subtrair anos especificando Y como um número negativo.<br/><br/>Exemplo: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Dia (X) |X: DataTime |Recebe o componente diurno de X.<br/><br/>Exemplo: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayofWeek(X) |X: DataTime |Recebe o componente do dia da semana de X.<br/><br/>Exemplo: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear(X) |X: DataTime |Recebe o dia do ano representado pela componente do ano de X.<br/><br/>Exemplos:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth (X) |X: DataTime |Recebe os dias do mês representados pelo componente mensal do parâmetro X.<br/><br/>Exemplo: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |Fim do dia(X) |X: DataTime |Obtém a data-data que representa o fim do dia (componente do dia) de X.<br/><br/>Exemplo: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |Fim do mês(X) |X: DataTime |Obtém o final do mês representado por componente mensal do parâmetro X. <br/><br/>Exemplo: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (data que representa o final do mês de setembro) |
| Date |Início do Dia(X) |X: DataTime |Recebe o início do dia representado pelo componente diurno do parâmetro X.<br/><br/>Exemplo: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |De(X) |X: Corda |Parse string X a uma hora de encontro. |
| DateTime |Carrapatos (X) |X: DataTime |Obtém a propriedade do parâmetro X. Um tique é igual a 100 nanossegundos. O valor deste imóvel representa o número de carraças que decorreram desde as 12:00:00, 1 de janeiro de 0001. |
| Texto |Formato (X) |X: Variável de corda |Formatos o `\\'` texto (utilizar a combinação para escapar `'` ao carácter).|

> [!IMPORTANT]
> Quando utilizar uma função dentro de outra **$$** função, não precisa de utilizar o prefixo para a função interna. Por exemplo: $$Text.Format('PartitionKey \\eq\\'my_pkey_filter_value' \\e RowKey ge '{0: yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). Neste exemplo, note que **$$** o prefixo não é utilizado para a função **Time.AddHours.** 

#### <a name="example"></a>Exemplo
No exemplo seguinte, os parâmetros de entrada e saída para `Text.Format` a atividade da Colmeia são determinados utilizando a função e a variável do sistema SliceStart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Exemplo 2

No exemplo seguinte, o parâmetro DateTime para a atividade do procedimento armazenado é determinado utilizando o Texto. Função de formato e variável SliceStart. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Exemplo 3
Para ler os dados do dia anterior em vez do dia representado pelo SliceStart, utilize a função AddDays como mostrado no seguinte exemplo: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Consulte o tópico de strings de formato de data e tempo personalizado que descreve [diferentes](https://msdn.microsoft.com/library/8kb3ddd4.aspx) opções de formatação que pode usar (por exemplo: yy vs. yyyy). 

