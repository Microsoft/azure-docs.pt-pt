---
title: Crie SDKs personalizados para Azure Digital Twins com AutoRest
titleSuffix: Azure Digital Twins
description: Veja como gerar SDKs personalizados, para usar Azure Digital Twins com idiomas que não C#.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: c1dbdc4761c107a8e5028a43ead9710d45526016
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461184"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Crie SDKs personalizados para gémeos digitais Azure usando o AutoRest

Neste momento, os únicos SDKs de plano de dados publicados para interagir com as APIs de Gémeos Digitais Azure são para .NET (C#), JavaScript e Java. Você pode ler sobre estes SDKs, e as APIs em geral, em [*Como-a-: Use as APIs e SDKs de Gémeos Digitais Azure*](how-to-use-apis-sdks.md). Se estiver a trabalhar noutro idioma, este artigo irá mostrar-lhe como gerar o seu próprio plano de dados SDK na linguagem à sua escolha, utilizando o AutoRest.

>[!NOTE]
> Também pode utilizar o AutoRes para gerar um plano de controlo SDK, se quiser. Para tal, complete os passos deste artigo utilizando o mais recente ficheiro **do plano** de controlo Swagger (OpenAPI) da pasta Do plano de controlo [Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) em vez do plano de dados um.

## <a name="set-up-your-machine"></a>Configurar a sua máquina

Para gerar um SDK, você precisará:
* [AutoRest](https://github.com/Azure/autorest), versão 2.0.4413 (a versão 3 não está suportada atualmente)
* [Node.js](https://nodejs.org) como pré-requisito para o AutoRest
* O mais recente ficheiro do plano de dados da Azure Digital **Twins, Swagger** (OpenAPI) da pasta Do plano de [dados Swagger,](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)e a sua pasta de exemplos que acompanha.  Descarregue o ficheiro Swagger *digitaltwins.js* e a sua pasta de exemplos para a sua máquina local.

Uma vez que a sua máquina esteja equipada com tudo da lista acima, está pronto a usar o AutoRest para criar o SDK.

## <a name="create-the-sdk-with-autorest"></a>Criar o SDK com AutoRest 

Se tiver Node.js instalado, pode executar este comando para se certificar de que tem a versão certa do AutoRes instalado:
```cmd/sh
npm install -g autorest@2.0.4413
```

Para executar o AutoRest contra o ficheiro Azure Digital Twins Swagger, siga estes passos:
1. Copie o ficheiro Azure Digital Twins Swagger e a sua pasta de exemplos que o acompanha num diretório de trabalho.
2. Use uma janela de ordem de comando para mudar para o diretório de trabalho.
3. Corra autorest com o seguinte comando. Substitua o `<language>` espaço reservado pelo seu idioma de eleição: , , e assim por `python` `java` `go` diante. (Pode encontrar a lista completa de opções no [AutoRest README](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

Como resultado, verá uma nova pasta chamada *ADTApi* no seu diretório de trabalho. Os ficheiros SDK gerados terão o espaço de nome *ADTApi*. Você continuará a usar esse espaço de nome através do resto dos exemplos de uso neste artigo.

O AutoRest suporta uma vasta gama de geradores de códigos linguísticos.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Adicione o SDK a um projeto do Estúdio Visual

Pode incluir os ficheiros gerados pelo AutoRest diretamente numa solução .NET. No entanto, é provável que queira incluir o Azure Digital Twins SDK em vários projetos separados (aplicações para clientes, aplicações Azure Functions, e assim por diante). Por esta razão, pode ser útil construir um projeto separado (uma biblioteca de classe .NET) a partir dos ficheiros gerados. Em seguida, você pode incluir este projeto de biblioteca de classes em várias soluções como referência de projeto.

Esta secção dá instruções sobre como construir o SDK como uma biblioteca de classes, que é o seu próprio projeto e pode ser incluído em outros projetos. Estes passos dependem do **Visual Studio** (pode instalar a versão mais recente a partir [daqui).](https://visualstudio.microsoft.com/downloads/)

Eis os passos:

1. Crie uma nova solução visual Studio para uma biblioteca de classes
2. Use *a ADTApi* como nome do projeto
3. No Solutions Explorer, selecione à direita o projeto *ADTApi* da solução gerada e escolha *Adicionar > Item Existente...*
4. Encontre a pasta onde gerou o SDK e selecione os ficheiros ao nível da raiz
5. Prima "Ok"
6. Adicione uma pasta ao projeto (selecione à direita o projeto no Solution Explorer e escolha *Adicionar > Nova Pasta)*
7. Nomeie os *modelos* de pasta
8. Selecione à direita a pasta *Modelos* no Explorador de Soluções e *selecione Adicionar > Item existente...*
9. Selecione os ficheiros na pasta *Modelos* do SDK gerado e prima "Ok"

Para construir o SDK com sucesso, o seu projeto necessitará destas referências:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Para adicionar estes, abra *ferramentas > Gestor de Pacotes NuGet > gerir pacotes NuGet para solução...*.

1. No painel, certifique-se de que o *separador Browse* está selecionado
2. Procurar *microsoft.rest*
3. Selecione os `ClientRuntime` `ClientRuntime.Azure` pacotes e adicione-os à sua solução

Agora pode construir o projeto e incluí-lo como referência de projeto em qualquer aplicação Azure Digital Twins que escreva.

## <a name="general-guidelines-for-generated-sdks"></a>Orientações gerais para SDKs gerados

Esta secção contém informações gerais e diretrizes para a utilização do SDK gerado.

### <a name="synchronous-and-asynchronous-calls"></a>Chamadas sincronizadas e assíncronos

Todas as funções SDK vêm em versões sincronizadas e assíncronos.

### <a name="typed-and-untyped-data"></a>Dados dactilografados e não tipigidos

REST As chamadas API geralmente devolvem objetos fortemente dactilografados. No entanto, como a Azure Digital Twins permite que os utilizadores definam os seus próprios tipos personalizados para gémeos, não há forma de pré-definir dados estáticos de retorno para muitas chamadas Azure Digital Twins. Em vez disso, as APIs devolvem tipos de invólucros fortemente digitado quando aplicável, e os dados gémeos de tipo personalizado estão em objetos Json.NET (utilizados onde quer que o tipo de dados "objeto" apareça nas assinaturas API). Pode lançar estes objetos adequadamente no seu código.

### <a name="error-handling"></a>Processamento de erros

Sempre que ocorrer um erro no SDK (incluindo erros HTTP como o 404), o SDK lançará uma exceção. Por esta razão, é importante encapsular todas as chamadas da API dentro de blocos de tentativa/captura.

Aqui está um código que tenta adicionar um gémeo e apanha quaisquer erros neste processo:

```csharp
try
{
    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>Paginação

O AutoResco gera dois tipos de padrões de paging para o SDK:
* Um para todos os APIs, exceto a API de Consulta
* Um para a Consulta API

No padrão de paging não-consulta, aqui está um corte de código mostrando como recuperar uma lista de relações de saída de Azure Digital Twins:

```csharp
 try 
 {
     // List the relationships.
    AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
    Console.WriteLine($"Twin {srcId} is connected to:");
    // Iterate through the relationships found.
    int numberOfRelationships = 0;
    await foreach (string rel in results)
    {
         ++numberOfRelationships;
         // Do something with each relationship found
         Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
    }
    Console.WriteLine($"Found {numberOfRelationships} relationships on {srcId}");
} catch (RequestFailedException rex) {
    Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
}
```

O segundo padrão é gerado apenas para a API de consulta. Usa `continuationToken` explicitamente.

Aqui está um exemplo com este padrão:

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>Passos seguintes

Caminhe pelos degraus para criar uma aplicação para clientes onde pode utilizar o seu SDK:
* [*Tutorial: Código de uma aplicação de cliente*](tutorial-code.md)
