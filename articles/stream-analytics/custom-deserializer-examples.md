---
title: Ler entrada em qualquer formato usando desserializadores personalizados do .NET no Azure Stream Analytics
description: Este artigo explica o formato de serialização e as interfaces que definem desserializadores .NET personalizados para Azure Stream Analytics trabalhos de nuvem e borda.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: a64912921e5ce8c0dc76fbf2ae0be8fb34bacf1a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553023"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Ler entrada em qualquer formato usando desserializadores personalizados do .NET

Os desserializadores personalizados do .NET permitem que seu trabalho de Azure Stream Analytics leia dados de formatos fora dos três [formatos de dados internos](stream-analytics-parsing-json.md). Este artigo explica o formato de serialização e as interfaces que definem desserializadores personalizados do .NET para Azure Stream Analytics trabalhos de nuvem e borda. Também há exemplos de desserializadores para buffer de protocolo e formato CSV.

## <a name="net-custom-deserializer"></a>Desserializador personalizado do .NET

Os exemplos de código a seguir são as interfaces que definem o desserializador personalizado e implementam `StreamDeserializer<T>`.

`UserDefinedOperator` é a classe base para todos os operadores de streaming personalizados. Ele inicializa `StreamingContext`, que fornece contexto que inclui o mecanismo para a publicação de diagnósticos para os quais você precisará depurar quaisquer problemas com o seu desserializador.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

O trecho de código a seguir é a desserialização para streaming de dados. 

Os erros ignoráveis devem ser emitidos usando `IStreamingDiagnostics` passadas pelo método Initialize de `UserDefinedOperator`. Todas as exceções serão tratadas como erros e o desserializador será recriado. Após um determinado número de erros, o trabalho vai para um status de falha.

`StreamDeserializer<T>` desserializa um fluxo em um objeto do tipo `T`. As seguintes condições devem ser atendidas:

1. T é uma classe ou struct.
1. Todos os campos públicos em T são
    1. Um de [SByte, byte, short, ushort, int, uint, Long, DateTime, String, float, Double] ou seus equivalentes anuláveis.
    1. Outra struct ou classe seguindo as mesmas regras.
    1. Matriz do tipo `T2` que segue as mesmas regras.
    1. IList`T2` em que T2 segue as mesmas regras.
    1. Não tem nenhum tipo recursivo.

O parâmetro `stream` é o fluxo que contém o objeto serializado. `Deserialize` retorna uma coleção de instâncias de `T`.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` fornece contexto que inclui o mecanismo de publicação de diagnóstico para o operador de usuário.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` é o diagnóstico para operadores definidos pelo usuário, incluindo serializador, desserializador e funções definidas pelo usuário.

`WriteError` grava uma mensagem de erro nos logs de diagnóstico e envia o erro para diagnóstico.

`briefMessage` é uma breve mensagem de erro. Essa mensagem aparece no diagnóstico e é usada pela equipe do produto para fins de depuração. Não inclua informações confidenciais e mantenha a mensagem com menos de 200 caracteres

`detailedMessage` é uma mensagem de erro detalhada que só é adicionada aos seus logs de diagnóstico em seu armazenamento. Esta mensagem deve ter menos de 2000 caracteres.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Exemplos de desserializador

Esta seção mostra como escrever desserializadores personalizados para Protobuf e CSV. Para obter exemplos adicionais, visite [Azure Stream Analytics no GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Formato de buffer de protocolo (Protobuf)

Este é um exemplo que usa o formato de buffer de protocolo.

Suponha a seguinte definição de buffer de protocolo.

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

A execução de `protoc.exe` do NuGet do **Google. Protobuf. Tools** gera um arquivo. cs com a definição. O arquivo gerado não é mostrado aqui.

O trecho de código a seguir é a implementação de desserializador, supondo que o arquivo gerado esteja incluído no projeto. Essa implementação é apenas um wrapper fino sobre o arquivo gerado.

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

O trecho de código a seguir é um desserializador CSV simples que também demonstra a propagação de erros.

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

Cada entrada de Stream Analytics tem um **formato de serialização**. Para obter mais informações sobre as opções de entrada, consulte a documentação da [API REST de entrada](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) .

O código JavaScript a seguir é um exemplo do formato de serialização do desserializador do .NET ao usar a API REST:

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

`serializationClassName` deve ser uma classe que implementa `StreamDeserializer<T>`. Isso é descrito na seção a seguir.

## <a name="region-support"></a>Suporte de região

Esse recurso está disponível nas seguintes regiões:

* E.U.A. Centro-Oeste
* Europa do Norte
* Este dos E.U.A.
* Oeste dos E.U.A.
* Este dos E.U.A. 2
* Europa Ocidental

Você pode [solicitar suporte](https://aka.ms/ccodereqregion) para regiões adicionais.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Quando esse recurso estará disponível em todas as regiões do Azure?

Esse recurso está disponível em [6 regiões](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support). Se você estiver interessado em usar essa funcionalidade em outra região, poderá [Enviar uma solicitação](https://aka.ms/ccodereqregion). O suporte para todas as regiões do Azure está no roteiro.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Posso acessar o MetadataPropertyValue de minhas entradas semelhantes à função GetMetadataPropertyValue?

Não há suporte para essa funcionalidade. Se você precisar desse recurso, poderá votar nessa solicitação no [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Posso compartilhar minha implementação de desserializador com a Comunidade para que outras pessoas possam se beneficiar?

Depois de implementar seu desserializador, você pode ajudar outras pessoas compartilhando-o com a Comunidade. Envie seu código para o [repositório GitHub Azure Stream Analytics](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

## <a name="next-steps"></a>Próximos Passos

* [Desserializadores personalizados do .NET para trabalhos de Azure Stream Analytics nuvem](custom-deserializer.md)
