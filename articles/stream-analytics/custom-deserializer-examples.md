---
title: Leia a entrada em qualquer formato utilizando desserializers personalizados .NET no Azure Stream Analytics
description: Este artigo explica o formato de serialização e as interfaces que definem os desserializers personalizados .NET para a nuvem e os trabalhos de borda do Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 5cde80bf3205557884dfe8f2b8f5e79031bbca69
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612066"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Leia a entrada em qualquer formato utilizando desserializers personalizados .NET

Os desserializers personalizados .NET permitem que o seu trabalho de Análise de Fluxo de Azure leia dados de formatos fora dos três [formatos de dados incorporados](stream-analytics-parsing-json.md). Este artigo explica o formato de serialização e as interfaces que definem os desserializers personalizados .NET para a nuvem e os trabalhos de borda do Azure Stream Analytics. Há também desserializers exemplo para o formato Protocol Buffer e CSV.

## <a name="net-custom-deserializer"></a>.NET desserializer personalizado

A seguir são as amostras de código que `StreamDeserializer<T>`definem o desserializador personalizado e implementam .

`UserDefinedOperator`é a classe base para todos os operadores de streaming personalizados. Ele inicializa `StreamingContext`, que fornece um contexto que inclui mecanismo sinuoso para a publicação de diagnósticos para os quais você precisará dedepusar quaisquer problemas com o seu desserializer.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

O seguinte código é a desserialização para os dados de streaming. 

Erros skippable devem ser `IStreamingDiagnostics` emitidos utilizando o `UserDefinedOperator`método inicializar. Todas as exceções serão tratadas como erros e o desserializer será recriado. Depois de um certo número de erros, o trabalho irá para um estado falhado.

`StreamDeserializer<T>`desserializa um fluxo em `T`objeto de tipo . Devem ser satisfeitas as seguintes condições:

1. T é uma classe ou uma estrutura.
1. Todos os campos públicos em T são ou
    1. Um dos [sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] ou seus equivalentes nulos.
    1. Outra estrutura ou classe seguindo as mesmas regras.
    1. Um conjunto `T2` de tipos que seguem as mesmas regras.
    1. IList`T2` onde T2 segue as mesmas regras.
    1. Não tem nenhum tipo recursivo.

O parâmetro `stream` é o fluxo que contém o objeto serializado. `Deserialize`devolve uma `T` coleção de instâncias.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`fornece um contexto que inclui mecanismos de publicação de diagnósticos para o operador do utilizador.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`são os diagnósticos para os operadores definidos pelo utilizador, incluindo funções definidas pelo serializador, desserializer e utilizador.

`WriteError`escreve uma mensagem de erro para registos de recursos e envia o erro para diagnósticos.

`briefMessage`é uma breve mensagem de erro. Esta mensagem aparece em diagnósticos e é usada pela equipa de produtos para fins de depuração. Não inclua informações sensíveis e mantenha a mensagem menos de 200 caracteres

`detailedMessage`é uma mensagem de erro detalhada que só é adicionada aos seus registos de recursos no seu armazenamento. Esta mensagem deve ser inferior a 2000 caracteres.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Exemplos de desserializadores

Esta secção mostra-lhe como escrever desserializers personalizados para Protobuf e CSV. Para exemplos adicionais, como o formato AVRO para Captura do Hub de Eventos, visite [o Azure Stream Analytics no GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Formato tampão de protocolo (Protobuf)

Este é um exemplo usando o formato tampão de protocolo.

Assuma a seguinte definição de tampão de protocolo.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

A `protoc.exe` partir do **Google.Protobuf.Tools** NuGet gera um ficheiro .cs com a definição. O ficheiro gerado não é mostrado aqui.

O seguinte código é a implementação de desserializer assumindo que o ficheiro gerado está incluído no projeto. Esta implementação é apenas um invólucro fino sobre o ficheiro gerado.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

O seguinte código é um simples desserializador CSV que também demonstra erros de propagação.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Formato de serialização para APIs REST

Cada entrada stream Analytics tem um formato de **serialização.** Para obter mais informações sobre as opções de entrada, consulte a documentação [input REST API.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)

O seguinte código Javascript é um exemplo do formato de serialização de deserializer .NET ao utilizar a API REST:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName`deve ser uma classe `StreamDeserializer<T>`que implementa. Isto é descrito na secção seguinte.

## <a name="region-support"></a>Suporte de região

Esta funcionalidade está disponível nas seguintes regiões:

* E.U.A. Centro-Oeste
* Europa do Norte
* E.U.A. Leste
* E.U.A. Oeste
* E.U.A. Leste 2
* Europa ocidental

Pode [solicitar apoio](https://aka.ms/ccodereqregion) para regiões adicionais.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Quando é que esta funcionalidade estará disponível em todas as regiões do Azure?

Esta funcionalidade está disponível em [6 regiões.](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support) Se estiver interessado em utilizar esta funcionalidade noutra região, pode [submeter um pedido.](https://aka.ms/ccodereqregion) O apoio a todas as regiões de Azure está no roteiro.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Posso aceder metadadosPropertyValue a partir das minhas inputs semelhantes à função GetMetadataPropertyValue?

Esta funcionalidade não é suportada. Se precisar desta capacidade, pode votar a favor deste pedido no [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Posso partilhar a minha implementação de desserializador com a comunidade para que outros possam beneficiar?

Uma vez implementado o seu desserializer, pode ajudar os outros partilhando-o com a comunidade. Envie o seu código para o [repo Do Azure Stream Analytics GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="what-are-the-other-limitation-of-using-custom-deserializers-in-stream-analytics"></a>Qual é a outra limitação de usar desserializers personalizados no Stream Analytics?

Se a sua entrada for de formato Protobuf com esquema que contenha o tipo MapField, não será capaz de implementar um desserializador personalizado. Estamos a trabalhar no apoio a este tipo de avanço.

## <a name="next-steps"></a>Passos Seguintes

* [.NET desserializers personalizados para trabalhos em nuvem Azure Stream Analytics](custom-deserializer.md)
