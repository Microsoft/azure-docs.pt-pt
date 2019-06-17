---
title: Erros de dados de registo de diagnóstico do Azure Stream Analytics
description: Este artigo explica a entrada de diferente e os erros de dados de saída que podem ocorrer ao utilizar o Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: b00eb12092838746f4bfe16f00eac55df9224b09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65607232"
---
# <a name="azure-stream-analytics-data-errors"></a>Erros de dados do Azure Stream Analytics

Quando existe uma discrepância dos dados que é processado por uma tarefa do Azure Stream Analytics, o Stream Analytics envia um evento de erro de dados para os registos de diagnóstico. Stream Analytics escreve informações detalhadas e os eventos de exemplo, os seus registos de diagnóstico quando ocorrem erros de dados. Também é fornecido um resumo dessas informações através de notificações do portal para alguns erros.

Este artigo descreve os tipos de erro diferente, causas e detalhes de registo de diagnóstico para erros de entrada e saída de dados.

## <a name="diagnostic-log-schema"></a>Esquema de registo de diagnóstico

Ver [resolver problemas relacionados com o Azure Stream Analytics, utilizando os registos de diagnóstico](stream-analytics-job-diagnostic-logs.md#diagnostics-logs-schema) para ver o esquema para os registos de diagnóstico. O JSON seguinte é um valor de exemplo para o **propriedades** campo de um registo de diagnóstico para um erro de dados.

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

* Causa: O tipo de compressão de entrada que selecionou não corresponderem aos dados.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * Identificador da mensagem de entrada. Para o Hub de eventos, o identificador é o PartitionId, o deslocamento e o número de sequência.

**Mensagem de erro**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Causa: O cabeçalho de dados de entrada é inválido. Por exemplo, um CSV tem colunas com nomes duplicados.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * Identificador da mensagem de entrada. 
   * Payload real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Causa: As colunas de entrada definidas com CREATE TABLE ou por meio de TIMESTAMP BY não existe.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * Identificador da mensagem de entrada. 
   * Nomes das colunas que estão em falta. 
   * Payload real até alguns quilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Causa: Não é possível converter a entrada para o tipo especificado na instrução CREATE TABLE.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * Identificador da mensagem de entrada. 
   * Nome da coluna e tipo esperado.

**Mensagens de erro**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Causa: Dados de entrada não estão no formato correto. Por exemplo, a entrada não é um JSON válido.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * Identificador da mensagem de entrada. 
   * Payload real até alguns quilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Causa: Não é possível converter o valor da expressão TIMESTAMP BY para datetime.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * Identificador da mensagem de entrada. 
   * Mensagem de erro. 
   * Payload real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Causa: O valor de TIMESTAMP BY OVER timestampColumn má hodnotu NULL.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * A carga real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Causa: A diferença entre a hora de aplicação e a hora da chegada é superior à janela de tolerância de chegada tardia.
* Notificação do portal fornecida: Não
* Nível de registo de diagnóstico: Informações
* Detalhes do registo
   * Tempo do aplicativo e a hora de chegada. 
   * Payload real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Causa: A diferença entre a hora de aplicação e o tempo de chegada é superior a 5 minutos.
* Notificação do portal fornecida: Não
* Nível de registo de diagnóstico: Informações
* Detalhes do registo
   * Tempo do aplicativo e a hora de chegada. 
   * Payload real até alguns quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Causa: Evento é considerado fora de ordem, de acordo com a janela de tolerância fora de ordem definida.
* Notificação do portal fornecida: Não
* Nível de registo de diagnóstico: Informações
* Detalhes do registo
   * Payload real até alguns quilobytes.

**Mensagem de erro**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Erros de saída de dados

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Causa: A coluna necessária para a saída não existe. Por exemplo, existe uma coluna definida como does't PartitionKey de tabela do Azure.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * Nome da coluna e o identificador de registo ou parte do registo.

**Mensagem de erro**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Causa: O valor da coluna não esteja de acordo com a saída. Por exemplo, o nome da coluna não é uma coluna de tabela do Azure válida.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * Nome da coluna e identificador de registo ou parte do registo.

**Mensagem de erro**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Causa: Uma coluna não pode ser convertida para um tipo válido na saída. Por exemplo, o valor da coluna é incompatível com restrições ou tipo definido na tabela SQL.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * Nome da coluna.
   * O identificador de registo ou parte do registo.

**Mensagem de erro**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Causa: O valor da mensagem é superior ao tamanho suportado de saída. Por exemplo, um registo é maior do que 1 MB para um Hub de eventos de saída.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * O identificador de registo ou parte do registo.

**Mensagem de erro**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Causa: Um registo já contém uma coluna com o mesmo nome como uma coluna de sistema. Por exemplo, a saída do CosmosDB com uma coluna chamada ID quando a coluna ID é uma coluna diferente.
* Notificação do portal fornecida: Sim
* Nível de registo de diagnóstico: Aviso
* Detalhes do registo
   * Nome da coluna.
   * O identificador de registo ou parte do registo.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Passos Seguintes

* [Resolver problemas relacionados com o Azure Stream Analytics, utilizando os registos de diagnóstico](stream-analytics-job-diagnostic-logs.md)

* [Compreender a monitorização de tarefa do Stream Analytics e como monitorizar consultas](stream-analytics-monitoring.md)
