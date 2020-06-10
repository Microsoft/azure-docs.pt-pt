---
title: Parse e validar modelos
titleSuffix: Azure Digital Twins
description: Aprenda a usar a biblioteca de parser para analisar os modelos DTDL.
author: cschormann
ms.author: cschorm
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6346c1d2adca697649ebe879b97766672a23ae29
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613069"
---
# <a name="dtdl-client-side-parser-library"></a>Biblioteca de parser do lado do cliente DTDL

[Os modelos](concepts-models.md) em Azure Digital Twins são definidos usando a linguagem de definição de gémeos digitais baseada em JSON (DTDL). Para os casos em que é útil analisar os seus modelos, uma biblioteca de análise DTDL é fornecida em NuGet.org como uma biblioteca do lado do cliente: [Microsoft.Azure.DigitalTwins.Parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/).

Esta biblioteca proporciona acesso de modelo às definições de DTDL, atuando essencialmente como o equivalente à reflexão C# para dTDL. Esta biblioteca pode ser utilizada independentemente de qualquer [SDK Azure Digital Twins](how-to-use-apis-sdks.md), especialmente para validação DTDL num editor visual ou de texto. É útil para garantir que os ficheiros de definição do seu modelo são válidos antes de tentar enviá-los para o serviço.

Para utilizar a biblioteca de parser, fornece-lhe um conjunto de documentos DTDL. Normalmente, você recuperaria estes documentos de modelo do serviço, mas também poderia tê-los disponíveis localmente, se o seu cliente fosse responsável pelo upload do serviço em primeiro lugar. 

Aqui está o fluxo de trabalho geral para a utilização do parser:
1. Recupere alguns ou todos os documentos DTDL do serviço.
2. Passe os documentos DTDL devolvidos e na memória para o analisador.
3. O parser validará o conjunto de documentos que lhe foram transmitidos e devolverá informações detalhadas sobre erros. Esta capacidade é útil em cenários de editores.
4. Utilize as APIs do parser para continuar a analisar os modelos incluídos no conjunto de documentos. 

As capacidades do parser incluem:
* Obtenha todas as interfaces de modelo implementadas (o conteúdo da secção da `extends` interface).
* Obtenha todas as propriedades, telemetria, comandos, componentes e relacionamentos declarados no modelo. Este comando também recebe todos os metadados incluídos nestas definições, e tem em conta a herança `extends` (secções).
* Obtenha todas as definições de modelos complexas.
* Determine se um modelo é atribuível a partir de outro modelo.

> [!NOTE]
> Os dispositivos [IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) utilizam uma pequena variante de sintaxe para descrever a sua funcionalidade. Esta variante de sintaxe é um subconjunto semânticamente compatível do DTDL que é usado em Azure Digital Twins. Ao utilizar a biblioteca parser, não precisa de saber qual variante de sintaxe foi usada para criar o DTDL para o seu gémeo digital. O parser irá sempre, por defeito, devolver o mesmo modelo para a sintaxe PnP e Azure Digital Twins.

## <a name="use-the-dtdl-validator-sample"></a>Utilize a amostra de validador DTDL

Existe um código de amostra disponível que pode validar documentos de modelo para garantir que o DTDL é válido. É construído na biblioteca de parser DTDL e é agnóstico linguístico. Encontre-o aqui: [Amostra de Validador DTDL](https://github.com/Azure-Samples/DTDL-Validator).

A amostra validador pode ser usada como um utilitário de linha de comando para validar uma árvore de diretório de ficheiros DTDL. Também fornece um modo interativo. O código de origem mostra exemplos de como utilizar a biblioteca de parser.

Na pasta para a amostra DTDL Validador, consulte o ficheiro *readme.md* para obter instruções sobre como embalar a amostra numa execução autossuficiente.

Depois de ter construído um pacote autónomo e adicionado o executável ao seu caminho, pode executar o validador com este comando numa consola na sua máquina:

```cmd/sh
DTDLValidator
```

Com as opções predefinidos, a amostra procurará `*.json` ficheiros no diretório atual e em todas as subdireições. Também pode adicionar a seguinte opção para ter a pesquisa da amostra no diretório indicado e todas as subdiretórios para ficheiros com a extensão *.dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Pode adicionar a `-i` opção para a amostra entrar no modo interativo:

```cmd/sh
DTDLValidator -i
```

Para obter mais informações sobre esta amostra, consulte o código de origem ou corra `DTDLValidator --help` .

## <a name="use-the-parser-library-in-code"></a>Use a biblioteca parser em código

Também pode utilizar a biblioteca parser diretamente para validar os modelos.

Para suportar o exemplo de código parser abaixo, considere vários modelos definidos numa instância Azure Digital Twins:

> [!TIP] 
> O `dtmi:com:contoso:coffeeMaker` modelo encontra-se a utilizar a sintaxe do *modelo de capacidade,* o que implica que foi instalada no serviço através da ligação de um dispositivo PnP expondo esse modelo.

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

O código que se segue mostra um exemplo de como usar a biblioteca parser para refletir sobre estas definições em C#:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>Próximos passos

Assim que terminar de escrever os seus modelos, veja como os fazer upload (e fazer outras operações de gestão) com as APIs digitalTwinsModels:
* [Como fazer: Gerir um modelo gémeo](how-to-manage-model.md)