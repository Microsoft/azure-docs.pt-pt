---
title: Leia a entrada em qualquer formato utilizando deserializadores personalizados .NET em Azure Stream Analytics
description: Este artigo explica o formato de serialização e as interfaces que definem os deserializadores personalizados .NET para trabalhos de nuvem e borda do Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 140a836882ad3abe048047120e4fe1ebc0a3067c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018161"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Leia a entrada em qualquer formato utilizando deserializadores personalizados .NET

.NET os deserializadores personalizados permitem que o seu trabalho Azure Stream Analytics leia dados de formatos fora dos três [formatos de dados incorporados](stream-analytics-parsing-json.md). Este artigo explica o formato de serialização e as interfaces que definem os deserializadores personalizados .NET para trabalhos de nuvem e borda do Azure Stream Analytics. Há também exemplos deserializadores para o tampão de protocolo e formato CSV.

## <a name="net-custom-deserializer"></a>.NET deserializador personalizado

Seguem-se as amostras de código das interfaces que definem o deserializador personalizado e `StreamDeserializer<T>` implementam.

`UserDefinedOperator` é a classe base para todos os operadores de streaming personalizados. Inicializa- `StreamingContext` que fornece um contexto que inclui mecanismo de publicação de diagnósticos para os quais terá de desorbus quaisquer problemas com o seu deserializador.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

O seguinte corte de código é a deserialização para os dados de streaming. 

Devem ser emitidos erros de salto através `IStreamingDiagnostics` `UserDefinedOperator` do método Initialize do ôm's. Todas as exceções serão tratadas como erros e o desercializador será recriado. Depois de um certo número de erros, o trabalho irá para um estado falhado.

`StreamDeserializer<T>` deserializa um fluxo em objeto do tipo `T` . Devem ser satisfeitas as seguintes condições:

1. T é uma classe ou uma estrutura.
1. Todos os campos públicos em T são ou
    1. Um dos [sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] ou seus equivalentes nulos.
    1. Outra estrutura ou classe seguindo as mesmas regras.
    1. Conjunto de tipo `T2` que segue as mesmas regras.
    1. IList `T2` onde T2 segue as mesmas regras.
    1. Não tem nenhum tipo recursivo.

O parâmetro `stream` é o fluxo que contém o objeto serializado. `Deserialize` devolve uma coleção de `T` instâncias.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` fornece um contexto que inclui mecanismo de publicação de diagnósticos para o operador do utilizador.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` são os diagnósticos para operadores definidos pelo utilizador, incluindo funções serializadoras, desserializantes e definidas pelo utilizador.

`WriteError` escreve uma mensagem de erro para registos de recursos e envia o erro para diagnósticos.

`briefMessage` é uma breve mensagem de erro. Esta mensagem aparece nos diagnósticos e é utilizada pela equipa de produtos para fins de depuragem. Não inclua informações sensíveis e mantenha a mensagem com menos de 200 caracteres

`detailedMessage` é uma mensagem de erro detalhada que só é adicionada aos seus registos de recursos no seu armazenamento. Esta mensagem deve ter menos de 2000 caracteres.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Exemplos de deserializador

Esta secção mostra-lhe como escrever deserializadores personalizados para Protobuf e CSV. Para exemplos adicionais, como o formato AVRO para event hub capture, visite [Azure Stream Analytics no GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Formato tampão de protocolo (Protobuf)

Este é um exemplo usando o formato de tampão de protocolo.

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

Executar `protoc.exe` a partir do **Google.Protobuf.Tools** NuGet gera um ficheiro .cs com a definição. O ficheiro gerado não é mostrado aqui. Deve certificar-se de que a versão do Protobuf Nuget que utiliza no seu projeto Stream Analytics corresponde à versão Protobuf que foi usada para gerar a entrada. 

O seguinte código snippet é a implementação de deserializador assumindo que o ficheiro gerado está incluído no projeto. Esta implementação é apenas um invólucro fino sobre o ficheiro gerado.

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

O seguinte corte de código é um simples deserializador CSV que também demonstra erros de propagação.

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

Cada entrada Stream Analytics tem um **formato de serialização.** Para obter mais informações sobre as opções de entrada, consulte a documentação [da API input REST.](/rest/api/streamanalytics/2016-03-01/inputs)

O seguinte código Javascript é um exemplo do formato de serialização deserializador .NET ao utilizar a API REST:

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

`serializationClassName` deve ser uma classe que implementa `StreamDeserializer<T>` . Isto é descrito na secção seguinte.

## <a name="region-support"></a>Suporte de região

Esta funcionalidade está disponível nas seguintes regiões:

* E.U.A. Centro-Oeste
* Europa do Norte
* E.U.A. Leste
* E.U.A. Oeste
* E.U.A. Leste 2
* Europa Ocidental

Pode [solicitar apoio](https://aka.ms/ccodereqregion) para regiões adicionais.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Quando é que esta funcionalidade estará disponível em todas as regiões do Azure?

Esta funcionalidade está disponível em [6 regiões.](#region-support) Se estiver interessado em utilizar esta funcionalidade noutra região, pode [submeter um pedido.](https://aka.ms/ccodereqregion) O apoio a todas as regiões de Azure está no roteiro.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Posso aceder ao MetadasPropertyValue a partir das minhas entradas semelhantes à função GetMetadataPropertyValue?

Esta funcionalidade não é suportada. Se precisar desta capacidade, pode votar a favor deste pedido no [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Posso partilhar a minha implementação de deserializador com a comunidade para que outros possam beneficiar?

Uma vez implementado o seu deserializador, pode ajudar os outros partilhando-o com a comunidade. Envie o seu código para o [Azure Stream Analytics GitHub repo](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="what-are-the-other-limitations-of-using-custom-deserializers-in-stream-analytics"></a>Quais são as outras limitações de utilização de deserializantes personalizados no Stream Analytics?

Se a sua entrada for de formato Protobuf com um tipo de esquema `MapField` contendo, não será capaz de implementar um deserializador personalizado. Além disso, os deserializadores personalizados não suportam dados de amostra ou dados de pré-visualização. 

## <a name="next-steps"></a>Passos Seguintes

* [.NET deserializadores personalizados para trabalhos em nuvem Azure Stream Analytics](custom-deserializer.md)
