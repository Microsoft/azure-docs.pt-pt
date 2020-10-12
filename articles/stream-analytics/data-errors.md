---
title: Erros de dados de registo de recursos do Azure Stream Analytics
description: Este artigo explica os diferentes erros de dados de entrada e saída que podem ocorrer quando se utiliza o Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 1c649499fd9eaedac0ca4ff9c182e13a9da223ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88053155"
---
# <a name="azure-stream-analytics-data-errors"></a>Erros de dados do Azure Stream Analytics

Erros de dados são erros que ocorrem durante o processamento dos dados.  Estes erros ocorrem mais frequentemente durante as operações de des-serialização, serialização e escrita de dados.  Quando ocorrem erros de dados, o Stream Analytics escreve informações detalhadas e exemplos de eventos para os registos de recursos. Ative os registos de diagnóstico no seu trabalho para obter estes detalhes adicionais. Em alguns casos, um resumo desta informação também é fornecido através de notificações do portal.

Este artigo descreve os diferentes tipos de erros, causas e detalhes de registo de recursos para erros de dados de entrada e saída.

## <a name="resource-logs-schema"></a>Esquema de registos de recursos

Consulte [o Troubleshoot Azure Stream Analytics utilizando registos de diagnóstico](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) para ver o esquema de registos de recursos. O JSON seguinte é um valor de exemplo para o campo **Propriedades** de um registo de recursos para um erro de dados.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Erros de dados de entrada

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Causa: O tipo de compressão de entrada selecionado não corresponde aos dados.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: As mensagens com eventuais erros de deserialização, incluindo o tipo de compressão inválida, são retiradas da entrada.
* Detalhes do registo
   * Identificador de mensagem de entrada. Para o Event Hub, o identificador é o Número partitionId, Offset e Sequência.

**Mensagem de erro**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Causa: O cabeçalho dos dados de entrada é inválido. Por exemplo, um CSV tem colunas com nomes duplicados.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: As mensagens com eventuais erros de deserialização, incluindo o cabeçalho inválido, são retiradas da entrada.
* Detalhes do registo
   * Identificador de mensagem de entrada. 
   * Carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Causa: As colunas de entrada definidas com TABELA CREATE ou através do TIMETAMP BY não existem.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Os eventos com colunas em falta são retirados da entrada.
* Detalhes do registo
   * Identificador de mensagem de entrada. 
   * Nomes das colunas que faltam. 
   * Carga real até alguns quilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Causa: Não é possível converter a entrada para o tipo especificado na declaração DE TABELA CREATE.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Os eventos com erro de conversão do tipo são retirados da entrada.
* Detalhes do registo
   * Identificador de mensagem de entrada. 
   * Nome da coluna e tipo esperado.

**Mensagens de erro**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Causa: Os dados de entrada não estão no formato certo. Por exemplo, a entrada não é válida JSON.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os eventos na mensagem após um erro de dados inválido são retirados da entrada.
* Detalhes do registo
   * Identificador de mensagem de entrada. 
   * Carga real até alguns quilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InálidoInputTimestamp

* Causa: O valor da expressão TIMETAMP POR expressão não pode ser convertido para a hora da data.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Os eventos com o tempo de entrada inválido são retirados da entrada.
* Detalhes do registo
   * Identificador de mensagem de entrada. 
   * Mensagem de erro. 
   * Carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimestampKey

* Causa: O valor do TIMETAMP BY OVER timetampColumn é NU.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Os eventos com chave de tempo de entrada inválida são retirados da entrada.
* Detalhes do registo
   * A carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>Evento De Última Hora

* Causa: A diferença entre a hora de aplicação e a hora de chegada é maior do que a janela de tolerância à chegada tardia.
* Notificação do portal fornecida: Não
* Nível de registo de recursos: Informação
* Impacto: Os eventos de entrada tardia são tratados de acordo com a definição "Lidar com outros eventos" na secção de Pedido de Eventos da configuração do trabalho. Para mais informações consulte as [Políticas de Manuseamento de Tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do registo
   * Hora da aplicação e hora de chegada. 
   * Carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>Início do Evento

* Causa: A diferença entre a hora de aplicação e a hora de chegada é superior a 5 minutos.
* Notificação do portal fornecida: Não
* Nível de registo de recursos: Informação
* Impacto: Os eventos de entrada precoce são tratados de acordo com a definição "Lidar com outros eventos" na secção de Pedido de Eventos da configuração do trabalho. Para mais informações consulte as [Políticas de Manuseamento de Tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do registo
   * Hora da aplicação e hora de chegada. 
   * Carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Causa: O evento é considerado fora de ordem de acordo com a janela de tolerância fora da ordem definida.
* Notificação do portal fornecida: Não
* Nível de registo de recursos: Informação
* Impacto: Os eventos fora de ordem são tratados de acordo com a definição "Lidar com outros eventos" na secção de Encomenda de Eventos da configuração do trabalho. Para mais informações consulte as [Políticas de Manuseamento de Tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do registo
   * Carga real até alguns quilobytes.

**Mensagem de erro**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Erros de dados de saída

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Causa: A coluna necessária para a saída não existe. Por exemplo, uma coluna definida como Azure Table PartitionKey não existe.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo a coluna requerida em falta, são tratados de acordo com a definição [da Política de Dados de Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes do registo
   * Nome da coluna e do identificador de registos ou parte do registo.

**Mensagem de erro**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Causa: O valor da coluna não está em conformidade com a saída. Por exemplo, o nome da coluna não é uma coluna de tabela Azure válida.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo o nome da coluna inválida, são tratados de acordo com a definição [de Política de Dados de Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes do registo
   * Nome da coluna e identificador de registos ou parte do registo.

**Mensagem de erro**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Causa: Uma coluna não pode ser convertida para um tipo válido na saída. Por exemplo, o valor da coluna é incompatível com restrições ou tipo definido na tabela SQL.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo erro de conversão do tipo, são tratados de acordo com a definição [da Política de Dados de Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes do registo
   * O nome da coluna.
   * Ou o identificador de registos ou parte do registo.

**Mensagem de erro**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Causa: O valor da mensagem é maior do que o tamanho de saída suportado. Por exemplo, um recorde é maior que 1 MB para uma saída do Event Hub.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo o limite de tamanho excededo de registo, são tratados de acordo com a definição [da Política de Dados de Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes do registo
   * Ou o identificador de registos ou parte do registo.

**Mensagem de erro**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Causa: Um registo já contém uma coluna com o mesmo nome de uma coluna do Sistema. Por exemplo, a saída cosmosdb com uma coluna chamada ID quando a coluna ID é para uma coluna diferente.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo a chave duplicada, são tratados de acordo com a definição [da Política de Dados de Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes do registo
   * O nome da coluna.
   * Ou o identificador de registos ou parte do registo.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas Azure Stream Analytics usando registos de diagnóstico](stream-analytics-job-diagnostic-logs.md)

* [Compreender a monitorização do trabalho do Stream Analytics e como monitorizar consultas](stream-analytics-monitoring.md)
