---
title: Erros de dados de registo de recursos do Azure Stream Analytics
description: Este artigo explica os diferentes erros de dados de entrada e saída que podem ocorrer ao utilizar o Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 5c5da26935e489a1b9489f63b83af176921c3a5a
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133809"
---
# <a name="azure-stream-analytics-data-errors"></a>Erros de dados do Azure Stream Analytics

Erros de dados são erros que ocorrem durante o processamento dos dados.  Estes erros ocorrem mais frequentemente durante operações de desserialização de dados, serialização e escrita.  Quando ocorrem erros de dados, o Stream Analytics escreve informações detalhadas e eventos de exemplo para os registos de recursos.  Em alguns casos, o resumo desta informação também é fornecido através de notificações do portal.

Este artigo descreve os diferentes tipos de erros, causas e detalhes do registo de recursos para erros de dados de entrada e saída.

## <a name="resource-logs-schema"></a>Esquema de Registos de Recursos

Consulte [o Troubleshoot Azure Stream Analytics utilizando registos](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) de diagnóstico para ver o esquema para obter registos de recursos. O JSON seguinte é um valor exemplo para o campo **Propriedades** de um registo de recursos para um erro de dados.

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
* Impacto: As mensagens com quaisquer erros de desserialização, incluindo o tipo de compressão inválido, são retiradas da entrada.
* Detalhes de registo
   * Identificador de mensagem de entrada. Para o Event Hub, o identificador é o Número de Partição, Offset e Sequência.

**Mensagem de erro**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Causa: O cabeçalho dos dados de entrada é inválido. Por exemplo, um CSV tem colunas com nomes duplicados.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: As mensagens com quaisquer erros de desserialização, incluindo cabeçalho inválido, são retiradas da entrada.
* Detalhes de registo
   * Identificador de mensagem de entrada. 
   * Carga útil real até poucos quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Causa: As colunas de entrada definidas com TABELA CREATE ou através do TIMESTAMP BY não existem.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Os eventos com colunas em falta são retirados da entrada.
* Detalhes de registo
   * Identificador de mensagem de entrada. 
   * Nomes das colunas que faltam. 
   * Carga útil real até alguns quilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Causa: Incapaz de converter a entrada para o tipo especificado na declaração CREATE TABLE.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Os eventos com erro de conversão de tipo são retirados da entrada.
* Detalhes de registo
   * Identificador de mensagem de entrada. 
   * Nome da coluna e do tipo esperado.

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
* Impacto: Todos os eventos na mensagem após ter sido encontrado um erro de dados inválido são retirados da entrada.
* Detalhes de registo
   * Identificador de mensagem de entrada. 
   * Carga útil real até poucos quilobytes.

**Mensagens de erro**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Causa: O valor da expressão TIMESTAMP POR expressão não pode ser convertido até à data.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Os eventos com carimbo de tempo de entrada inválido são retirados da entrada.
* Detalhes de registo
   * Identificador de mensagem de entrada. 
   * Mensagem de erro. 
   * Carga útil real até poucos quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Causa: O valor do TIMESTAMP BY OVER TIMEStampColumn é NULO.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Os eventos com a tecla de marca de entrada inválida são retirados da entrada.
* Detalhes de registo
   * A carga útil real até poucos quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>Evento LateInput

* Causa: A diferença entre o tempo de aplicação e o tempo de chegada é maior do que a janela de tolerância à chegada tardia.
* Notificação do portal fornecida: Não
* Nível de registo de recursos: Informação
* Impacto: Os eventos de entrada tardia são tratados de acordo com a definição "Lidar com outros eventos" na secção de Ordenação de Eventos da configuração do trabalho. Para mais informações consulte [As Políticas de Tratamento de Tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes de registo
   * Hora da inscrição e hora de chegada. 
   * Carga útil real até poucos quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>Evento EarlyInput

* Causa: A diferença entre o tempo de aplicação e o tempo de chegada é superior a 5 minutos.
* Notificação do portal fornecida: Não
* Nível de registo de recursos: Informação
* Impacto: Os primeiros eventos de entrada são tratados de acordo com a definição "Lidar com outros eventos" na secção de Ordenação de Eventos da configuração do trabalho. Para mais informações consulte [As Políticas de Tratamento de Tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes de registo
   * Hora da inscrição e hora de chegada. 
   * Carga útil real até poucos quilobytes.

**Mensagem de erro**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>Evento Fora de Ordem

* Causa: O evento é considerado fora de ordem de acordo com a janela de tolerância fora de ordem definida.
* Notificação do portal fornecida: Não
* Nível de registo de recursos: Informação
* Impacto: Os eventos fora de ordem são tratados de acordo com a definição "Lidar com outros eventos" na secção de Ordenação de Eventos da configuração do trabalho. Para mais informações consulte [As Políticas de Tratamento de Tempo](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Detalhes de registo
   * Carga útil real até poucos quilobytes.

**Mensagem de erro**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Erros de dados de saída

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>Error de conversão de outputData.RequiredColumnMissing

* Causa: A coluna necessária para a saída não existe. Por exemplo, uma coluna definida como Chave de Partição da Tabela Azure não existe.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo a coluna requerida em falta, são tratados de acordo com a definição da Política de Dados de [Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes de registo
   * Nome da coluna e do identificador de registo ou parte do registo.

**Mensagem de erro**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>Error Deconversão de Dados de Saída.ColumnNameInvalid

* Causa: O valor da coluna não está em conformidade com a saída. Por exemplo, o nome da coluna não é uma coluna de mesa azure válida.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo o nome da coluna inválido, são tratados de acordo com a definição da Política de Dados de [Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes de registo
   * Nome da coluna e identificador de registo ou parte do registo.

**Mensagem de erro**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>Error de conversão de dados de saída.erro de conversão de tipo

* Causa: Uma coluna não pode ser convertida para um tipo válido na saída. Por exemplo, o valor da coluna é incompatível com constrangimentos ou tipo definidos na tabela SQL.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo erro de conversão de tipo, são tratados de acordo com a definição da Política de Dados de [Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes de registo
   * Nome da coluna.
   * Ou identificador de registo ou parte do registo.

**Mensagem de erro**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Causa: O valor da mensagem é maior do que o tamanho da saída suportada. Por exemplo, um recorde é superior a 1 MB para uma saída do Event Hub.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo o limite de tamanho superior a recorde, são tratados de acordo com a definição da Política de Dados de [Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes de registo
   * Ou identificador de registo ou parte do registo.

**Mensagem de erro**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Causa: Um registo já contém uma coluna com o mesmo nome que uma coluna do Sistema. Por exemplo, a saída cosmosDB com uma coluna chamada ID quando a coluna id é para uma coluna diferente.
* Notificação do portal fornecida: Sim
* Nível de registo de recursos: Aviso
* Impacto: Todos os erros de conversão de dados de saída, incluindo a chave duplicada, são tratados de acordo com a definição da Política de Dados de [Saída.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-output-error-policy)
* Detalhes de registo
   * Nome da coluna.
   * Ou identificador de registo ou parte do registo.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Passos seguintes

* [Troubleshoot Azure Stream Analytics usando registos de diagnóstico](stream-analytics-job-diagnostic-logs.md)

* [Compreender a monitorização do trabalho do Stream Analytics e como monitorizar as consultas](stream-analytics-monitoring.md)
