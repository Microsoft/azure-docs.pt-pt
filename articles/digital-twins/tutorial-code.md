---
title: 'Tutorial: Código de uma aplicação de cliente'
titleSuffix: Azure Digital Twins
description: Tutorial para escrever o código mínimo para uma aplicação do cliente, utilizando o .NET (C#) SDK.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 8e7ad721eba103679f55886053e8ba9e888573c0
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057489"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Tutorial: Codificação com as APIs de Gémeos Digitais Azure

É comum que os desenvolvedores que trabalham com a Azure Digital Twins escrevam uma aplicação de cliente para interagir com a sua instância do serviço Azure Digital Twins. Este tutorial focado no programador proporciona uma introdução à programação contra o serviço Azure Digital Twins, utilizando a [biblioteca de clientes Azure IoT Digital Twin para .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Acompanha-o através da escrita de uma aplicação de cliente de consola C# passo a passo, começando do zero.

> [!div class="checklist"]
> * Criar projeto
> * Começar com o código do projeto   
> * Amostra de código completa
> * Limpar recursos
> * Passos seguintes

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial utiliza a linha de comando para o trabalho de instalação e projeto. Portanto, pode usar qualquer editor de código para percorrer os exercícios.

O que precisa para começar:
* Qualquer editor de código
* **.NET Core 3.1** na sua máquina de desenvolvimento. Pode descarregar esta versão do .NET Core SDK para várias plataformas a partir do [Download .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>Criar projeto

Assim que estiver pronto para ir com a sua instância Azure Digital Twins, comece a configurar o projeto da aplicação do cliente. 

Abra uma solicitação de comando ou outra janela de consola na sua máquina e crie um diretório de projeto vazio onde gostaria de armazenar o seu trabalho durante este tutorial. Nomeie o diretório o que quiser (por exemplo, *DigitalTwinsCodeTutorial).*

Navegue para o novo diretório.

Uma vez no diretório do projeto, crie um projeto de aplicação de consola .NET vazio. Na janela de comando, executar o seguinte comando para criar um projeto C# mínimo para a consola:

```cmd/sh
dotnet new console
```

Isto irá criar vários ficheiros dentro do seu diretório, incluindo um chamado *Program.cs* onde irá escrever a maior parte do seu código.

Em seguida, adicione duas dependências necessárias para trabalhar com a Azure Digital Twins:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity
```

A primeira dependência é a [biblioteca de clientes Azure IoT Digital Twin para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). A segunda dependência fornece ferramentas para ajudar na autenticação contra o Azure.

Mantenha a janela de comando aberta, pois continuará a usá-la durante todo o tutorial.

## <a name="get-started-with-project-code"></a>Começar com o código do projeto

Nesta secção, começará a escrever o código para o seu novo projeto de aplicação para trabalhar com a Azure Digital Twins. As ações abrangidas incluem:
* Autenticação contra o serviço
* Carregar um modelo
* Erros de captura
* Criação de gémeos digitais
* Criação de relacionamentos
* Consulta de gémeos digitais

Há também uma secção que mostra o código completo no final do tutorial. Pode usar isto como referência para verificar o seu programa à medida que avança.

Para começar, abra o ficheiro *Program.cs* em qualquer editor de código. Você verá um modelo de código mínimo que se parece com este:

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Primeiro, adicione `using` algumas linhas no topo do código para puxar as dependências necessárias.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

Em seguida, irá adicionar código a este ficheiro para preencher alguma funcionalidade. 

### <a name="authenticate-against-the-service"></a>Autenticar contra o serviço

A primeira coisa que a sua aplicação terá de fazer é autenticar contra o serviço Azure Digital Twins. Em seguida, pode criar uma classe de cliente de serviço para aceder às funções SDK.

Para autenticar, necessita de três peças de informação:
* O *ID do Diretório (inquilino)* para a sua subscrição
* O *ID da Aplicação (cliente)* criado quando configura a instância Azure Digital Twins mais cedo
* O *nome anfitrião* da sua instância Azure Digital Twins

>[!TIP]
> Se não conhece o seu *ID do Diretório (inquilino),* pode obtê-lo executando este comando em [Azure Cloud Shell:](https://shell.azure.com)
> 
> ```azurecli
> az account show --query tenantId
> ```

Em *Program.cs,* cole o seguinte código abaixo do "Olá, Mundo!" linha de impressão no `Main` método. Desa cota o valor do `adtInstanceUrl` seu *anfitrião de*instância Azure Digital Twins, `clientId` para o seu *ID de aplicação,* e `tenantId` para o seu *ID do Diretório*.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

Guarde o ficheiro. 

Note que este exemplo usa uma credencial de navegador interativo:
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

Este tipo de credencial fará com que uma janela do navegador abra, pedindo-lhe que forneça as suas credenciais Azure. 

>[!NOTE]
> Para obter informações sobre outros tipos de credenciais, consulte a documentação das [bibliotecas de autenticação da plataforma de identidade da Microsoft.](../active-directory/develop/reference-v2-libraries.md)

Na sua janela de comando, execute o código com este comando: 

```cmd/sh
dotnet run
```

Isto irá restaurar as dependências na primeira execução, e depois executar o programa. 
* Se não ocorrer qualquer erro, o programa imprimirá *o cliente de Serviço criado - pronto para ir*.
* Uma vez que ainda não existe qualquer erro de manipulação neste projeto, se alguma coisa correr mal, verás uma exceção lançada pelo código.

### <a name="upload-a-model"></a>Faça upload de um modelo

A Azure Digital Twins não tem vocabulário de domínio intrínseco. Os tipos de elementos no seu ambiente que pode representar em Azure Digital Twins são definidos por si, utilizando **modelos**. [Os modelos](concepts-models.md) são semelhantes às aulas em linguagens de programação orientadas a objetos; fornecem modelos definidos pelo utilizador para [que gémeos digitais](concepts-twins-graph.md) sigam e instantaneamente mais tarde. Estão escritas numa linguagem semelhante a JSON chamada Linguagem de Definição de **Gémeos Digitais (DTDL).**

O primeiro passo para criar uma solução Azure Digital Twins é definir pelo menos um modelo num ficheiro DTDL.

No diretório onde criou o seu projeto, crie um novo ficheiro *.json* chamado *SampleModel.js.* Pasta no seguinte corpo de ficheiro: 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Se estiver a utilizar o Visual Studio para este tutorial, poderá querer selecionar o ficheiro JSON recém-criado e definir a propriedade *Copy to Output Directory* no inspetor de propriedade para *copiar se Newer* ou *Copy Always*. Isto permitirá ao Visual Studio encontrar o ficheiro JSON com o caminho predefinido quando executar o programa com **F5** durante o resto do tutorial.

> [!TIP] 
> Existe uma [amostra de DTDL Validador](/samples/azure-samples/dtdl-validator/dtdl-validator) agnóstico que pode usar para verificar documentos de modelo para se certificar de que o DTDL é válido. É construído sobre a biblioteca de parser DTDL, sobre a qual pode ler mais em [*Como-a: Parse e validar modelos.*](how-to-parse-models.md)

Em seguida, adicione mais um código para *Program.cs* para carregar o modelo que acabou de criar na sua instância Azure Digital Twins.

Primeiro, adicione algumas `using` declarações ao topo do ficheiro:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

Em seguida, prepare-se para utilizar os métodos assíncronos no serviço C# SDK, alterando a assinatura do `Main` método para permitir a execução de async. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> A utilização `async` não é estritamente necessária, uma vez que o SDK também fornece versões sincronizadas de todas as chamadas. Esta prática tutorial é `async` usando.

Em seguida, vem o primeiro pedaço de código que interage com o serviço Azure Digital Twins. Este código carrega o ficheiro DTDL que criou a partir do seu disco e, em seguida, envia-o para a sua instância de serviço Azure Digital Twins. 

Cole no seguinte código de acordo com o código de autorização que adicionou anteriormente.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

Na sua janela de comando, execute o programa com este comando: 

```cmd/sh
dotnet run
```
"Upload a model" será impresso na saída, mas ainda não há saída para indicar se os modelos foram ou não carregados com sucesso.

Para adicionar uma declaração de impressão indicando se os modelos são realmente carregados com sucesso, adicione o seguinte código logo após a secção anterior:

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```
Antes de executar o programa novamente para testar este novo código, lembre-se que da última vez que executou o programa, já fez o upload do seu modelo. A Azure Digital Twins não permitirá carregar o mesmo modelo duas vezes, por isso, se tentar carregar novamente o mesmo modelo, o programa deverá lançar uma exceção.

Agora, executar o programa novamente com este comando na sua janela de comando:

```cmd/sh
dotnet run
```

O programa deve abrir uma exceção. Quando tenta carregar um modelo que já foi carregado, o serviço retorna um erro de "mau pedido" através da API REST. Como resultado, o cliente da Azure Digital Twins SDK, por sua vez, lançará uma exceção para cada código de devolução de serviços que não seja o sucesso. 

A próxima secção fala sobre exceções como esta e como lidar com elas no seu código.

### <a name="catch-errors"></a>Erros de captura

Para evitar que o programa se despenhe, pode adicionar código de exceção em torno do código de upload do modelo. Embrulhe a chamada do cliente existente `client.CreateModelsAsync` num manipulador de tentativa/captura, como este:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```
Se executar o programa com `dotnet run` a sua janela de comando agora, verá que receberá um código de erro de volta. A saída é mais ou menos assim:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Model with same ID already exists dtmi:com:contoso:SampleModel;1. Use Model_List API to view models that already exist. See the Swagger example. (http://aka.ms/ModelListSwSmpl):}}

Headers:
api-supported-versions: REDACTED
Date: Thu, 10 Sep 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

A partir de agora, o tutorial irá embrulhar todas as chamadas para métodos de serviço em manipuladores de tentativa/captura.

### <a name="create-digital-twins"></a>Criar gémeos digitais

Agora que fez o upload de um modelo para Azure Digital Twins, pode usar esta definição de modelo para criar **gémeos digitais.** [Gémeos digitais](concepts-twins-graph.md) são exemplos de um modelo, e representam as entidades dentro do seu ambiente de negócios — coisas como sensores numa quinta, quartos num edifício ou luzes num carro. Esta secção cria alguns gémeos digitais com base no modelo que carregou anteriormente.

Adicione uma nova `using` declaração no topo, pois necessitará do serializer incorporado .NET Json `System.Text.Json` em:

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

Em seguida, adicione o seguinte código ao fim do `Main` método para criar e inicializar três gémeos digitais com base neste modelo.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

Na janela de comando, execute o programa com `dotnet run` . Em seguida, repita para executar o programa novamente. 

Note que não se comete nenhum erro quando os gémeos são criados pela segunda vez, mesmo que os gémeos já existam após a primeira corrida. Ao contrário da criação de modelos, a criação de gémeos é, no nível REST, uma chamada *PUT* com semântica *upsert.* Isto significa que, se um gémeo já existir, tentar criá-lo novamente irá apenas substituí-lo. Não é necessário nenhum erro.

### <a name="create-relationships"></a>Criar relacionamentos

Em seguida, pode criar **relações** entre os gémeos que criou, para ligá-los a um **gráfico gémeo.** [Os gráficos gémeos](concepts-twins-graph.md) são usados para representar todo o seu ambiente.

Para ser capaz de criar relacionamentos, você precisará do espaço de `Azure.DigitalTwins.Core.Serialization` nome. Acrescentou isto ao projeto mais cedo com esta `using` declaração:

```csharp
using Azure.DigitalTwins.Core.Serialization;
```

Adicione um novo método estático à `Program` classe, por baixo do `Main` método:
```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Em seguida, adicione o seguinte código ao fim do `Main` método para ligar para o `CreateRelationship` código:
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

Na janela de comando, execute o programa com `dotnet run` .

Note que a Azure Digital Twins não permitirá criar uma relação se já existir uma com o mesmo ID — por isso, se executar o programa várias vezes, verá exceções na criação de relacionamentos. Este código apanha as exceções e ignora-as. 

### <a name="list-relationships"></a>Relações de lista

O próximo código que vai adicionar permite-lhe ver a lista de relacionamentos que criou.

Adicione o novo método seguinte à classe `Program`:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Em seguida, adicione o seguinte código ao fim do `Main` método para ligar para o `ListRelationships` código:

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

Na janela de comando, execute o programa com `dotnet run` . Devias ver uma lista de todas as relações que criaste.

Aqui está uma saída de exemplo:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Type name: System.Collections.Generic.Dictionary'2[System.String,System.String]: dtmi:contosocom:DigitalTwins:SampleModel;1
Create twin: sampleTwin-0
Create twin: sampleTwin-1
Create twin: sampleTwin-2
Created relationship successfully
Created relationship successfully
Twin sampleTwin-0 is connected to:
-contains->sampleTwin-1
-contains->sampleTwin-2

```

### <a name="query-digital-twins"></a>Consultas gémeas digitais

Uma das principais características da Azure Digital Twins é a capacidade [de consultar](concepts-query-language.md) o seu gráfico gémeo de forma fácil e eficiente para responder a questões sobre o seu ambiente.

A última secção de código a adicionar neste tutorial executa uma consulta contra a instância Azure Digital Twins. A consulta usada neste exemplo devolve todos os gémeos digitais no caso.

Adicione o seguinte código ao fim do `Main` método:

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

Na janela de comando, execute o programa com `dotnet run` . Devias ver todos os gémeos digitais neste caso na produção.

## <a name="complete-code-example"></a>Exemplo completo de código

Nesta altura do tutorial, tem uma app completa para clientes, capaz de realizar ações básicas contra a Azure Digital Twins. Para referência, o código completo do programa em *Program.cs* é listado abaixo:

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Limpar recursos
 
O caso utilizado neste tutorial pode ser reutilizado no próximo tutorial, [*Tutorial: Explore o básico com uma aplicação de cliente de amostra.*](tutorial-command-line-app.md) Se planeia continuar para o próximo tutorial, pode manter a instância Azure Digital Twins que instalou aqui.
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Por fim, elimine a pasta de projeto que criou na sua máquina local.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma aplicação de cliente de consola .NET de raiz. Escreveu código para esta aplicação de clientes para realizar as ações básicas numa instância Azure Digital Twins.

Continue até ao próximo tutorial para explorar as coisas que pode fazer com uma aplicação de cliente de amostra: 

> [!div class="nextstepaction"]
> [*Tutorial: Explore o básico com uma aplicação de cliente de amostra*](tutorial-command-line-app.md)