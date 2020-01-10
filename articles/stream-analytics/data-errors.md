---
title: Azure Stream Analytics erros de dados de log de diagnóstico
description: Este artigo explica os diferentes erros de dados de entrada e saída que podem ocorrer ao usar o Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 0546464b4d1bcc9eaa4fbffe265486985d9c58f3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465023"
---
# <a name="azure-stream-analytics-data-errors"></a>Erros de dados de Azure Stream Analytics

Erros de dados são erros que ocorrem durante o processamento dos dados.  Esses erros ocorrem com mais frequência durante as operações de serialização de dados, serialização e gravação.  Quando ocorrem erros de dados, Stream Analytics grava informações detalhadas e eventos de exemplo nos logs de diagnóstico.  Em alguns casos, o resumo dessas informações também é fornecido por meio de notificações do Portal.

Este artigo descreve os diferentes tipos de erro, as causas e os detalhes do log de diagnóstico para erros de dados de entrada e saída.

## <a name="diagnostic-log-schema"></a>Esquema de log de diagnóstico

Consulte [solucionar problemas Azure Stream Analytics usando logs de diagnóstico](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) para ver o esquema para logs de diagnóstico. O JSON a seguir é um valor de exemplo para o campo de **Propriedades** de um log de diagnóstico para um erro de dados.

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

* Causa: o tipo de compactação de entrada selecionado não corresponde aos dados.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: mensagens com qualquer erro de desserialização, incluindo tipo de compactação inválido, são removidas da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. Para o Hub de eventos, o identificador é a PartitionID, o deslocamento e o número de sequência.

**Mensagem de erro**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Causa: o cabeçalho dos dados de entrada é inválido. Por exemplo, um CSV tem colunas com nomes duplicados.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: as mensagens com qualquer erro de desserialização, incluindo cabeçalho inválido, são descartadas da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Carga real de até poucos kilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Causa: as colunas de entrada definidas com CREATE TABLE ou por meio de carimbo de data/hora não existem.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: os eventos com colunas ausentes são removidos da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Nomes das colunas que estão ausentes. 
   * Carga real até alguns quilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Causa: não é possível converter a entrada para o tipo especificado na instrução CREATE TABLE.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: eventos com erro de conversão de tipo são descartados da entrada.
* Detalhes do log
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

* Causa: os dados de entrada não estão no formato correto. Por exemplo, a entrada não é um JSON válido.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: todos os eventos na mensagem depois que um erro de dados inválido foi encontrado são removidos da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Carga real de até poucos kilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Causa: o valor do carimbo de data/hora por expressão não pode ser convertido em DateTime.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: eventos com carimbo de data/hora de entrada inválido são removidos da entrada.
* Detalhes do log
   * Identificador de mensagem de entrada. 
   * Mensagem de erro. 
   * Carga real de até poucos kilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Causa: o valor de TIMESTAMP por sobre timestampColumn é nulo.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: eventos com chave de carimbo de data/hora de entrada inválida são removidos da entrada.
* Detalhes do log
   * A carga real de até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Causa: a diferença entre a hora do aplicativo e a hora de chegada é maior do que a janela de tolerância de chegada tardia.
* Notificação do portal fornecida: não
* Nível de log de diagnóstico: informações
* Impacto: os eventos de entrada tardias são tratados de acordo com a configuração "manipular outros eventos" na seção ordenação de eventos da configuração do trabalho. Para obter mais informações, consulte [políticas de tratamento de tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do log
   * Hora e hora de chegada do aplicativo. 
   * Carga real de até poucos kilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Causa: a diferença entre a hora do aplicativo e a hora de chegada é maior que 5 minutos.
* Notificação do portal fornecida: não
* Nível de log de diagnóstico: informações
* Impacto: os eventos de entrada antecipados são tratados de acordo com a configuração "manipular outros eventos" na seção ordenação de eventos da configuração do trabalho. Para obter mais informações, consulte [políticas de tratamento de tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do log
   * Hora e hora de chegada do aplicativo. 
   * Carga real de até poucos kilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Causa: o evento é considerado fora de ordem de acordo com a janela de tolerância para fora de ordem definida.
* Notificação do portal fornecida: não
* Nível de log de diagnóstico: informações
* Impacto: eventos fora de ordem são tratados de acordo com a configuração "manipular outros eventos" na seção ordenação de eventos da configuração do trabalho. Para obter mais informações, consulte [políticas de tratamento de tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes do log
   * Carga real de até poucos kilobytes.

**Mensagem de erro**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Erros de dados de saída

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Causa: a coluna necessária para a saída não existe. Por exemplo, uma coluna definida como tabela do Azure PartitionKey does't existe.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: todos os erros de conversão de dados de saída, incluindo a coluna necessária ausente, são tratados de acordo com a configuração de [política de dados de saída](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Detalhes do log
   * Nome da coluna e o identificador de registro ou parte do registro.

**Mensagem de erro**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Causa: o valor da coluna não está de acordo com a saída. Por exemplo, o nome da coluna não é uma coluna de tabela do Azure válida.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: todos os erros de conversão de dados de saída, incluindo nome de coluna inválido, são tratados de acordo com a configuração de [política de dados de saída](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Detalhes do log
   * Nome da coluna e o identificador de registro ou parte do registro.

**Mensagem de erro**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Causa: uma coluna não pode ser convertida em um tipo válido na saída. Por exemplo, o valor da coluna é incompatível com restrições ou tipo definido na tabela SQL.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: todos os erros de conversão de dados de saída, incluindo erro de conversão de tipo, são tratados de acordo com a configuração de [política de dados de saída](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes do log
   * Nome da coluna.
   * O identificador de registro ou parte do registro.

**Mensagem de erro**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Causa: o valor da mensagem é maior que o tamanho de saída com suporte. Por exemplo, um registro é maior que 1 MB para uma saída do hub de eventos.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: todos os erros de conversão de dados de saída, incluindo o limite de tamanho excedido do registro, são manipulados de acordo com a configuração da [política de dados](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes do log
   * O identificador de registro ou parte do registro.

**Mensagem de erro**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Causa: um registro já contém uma coluna com o mesmo nome de uma coluna do sistema. Por exemplo, CosmosDB saída com uma coluna chamada ID quando a coluna ID é para uma coluna diferente.
* Notificação do portal fornecida: Sim
* Nível de log de diagnóstico: aviso
* Impacto: todos os erros de conversão de dados de saída, incluindo chave duplicada, são tratados de acordo com a configuração de [política de dados de saída](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy) .
* Detalhes do log
   * Nome da coluna.
   * O identificador de registro ou parte do registro.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Passos seguintes

* [Solucionar problemas Azure Stream Analytics usando logs de diagnóstico](stream-analytics-job-diagnostic-logs.md)

* [Entender Stream Analytics monitoramento de trabalho e como monitorar consultas](stream-analytics-monitoring.md)
