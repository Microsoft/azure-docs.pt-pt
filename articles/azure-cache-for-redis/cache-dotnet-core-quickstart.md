---
title: Início rápido para saber como utilizar a cache do Azure para Redis com aplicações de .NET Core | Documentos da Microsoft
description: Neste guia de introdução, saiba como aceder a Cache do Azure para Redis nas suas aplicações de .NET Core
services: cache,app-service
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: df615b940873cd59341424d0b40a9e399d710126
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896250"
---
# <a name="quickstart-use-azure-cache-for-redis-with-a-net-core-app"></a>Início rápido: Utilizar a Cache do Azure para Redis com um .NET Core de aplicação



Este guia de introdução mostra-lhe como começar a utilizar o Microsoft Azure Cache de Redis com .NET Core. Cache de Redis do Microsoft Azure baseia-se na popular Cache de Azure de código-fonte aberto para Redis. Ele fornece acesso a uma Cache de Azure segura, dedicada para Redis, gerida pela Microsoft. Uma cache criada através do Azure Cache de Redis é acessível a partir de qualquer aplicação dentro do Microsoft Azure.

Neste início rápido, vai utilizar o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) com código C\# numa aplicação de consola .NET Core. Vai criar uma cache, configurar a aplicação cliente .NET Core. Em seguida, vai adicionar e atualizar os objetos na cache. 

Pode utilizar qualquer editor de código para concluir os passos deste início rápido. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma excelente opção, disponível nas plataformas Windows, macOS e Linux.

![Aplicação de consola concluída](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [.NET SDK 2.0](https://www.microsoft.com/net/learn/get-started/windows) ou posterior.
* O cliente StackExchange.Redis exige o [.NET Framework 4 ou superior](https://www.microsoft.com/net/download/dotnet-framework-runtime).

## <a name="create-a-cache"></a>Criar uma cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

Anote o **NOME DO ANFITRIÃO** e a chave de acesso **Primária**. Vai utilizar estes valores mais tarde para construir o segredo *CacheConnection*.



## <a name="create-a-console-app"></a>Criar uma aplicação de consola

Abra uma nova janela de comandos e execute o seguinte comando para criar uma nova aplicação de consola .NET Core:

```
dotnet new console -o Redistest
```

Na janela de comandos, mude para o novo diretório de projeto *Redistest*.



## <a name="add-secret-manager-to-the-project"></a>Adicionar o Secret Manager ao projeto

Nesta secção, vai adicionar a [ferramenta Secret Manager](https://docs.microsoft.com/aspnet/core/security/app-secrets) ao seu projeto. Esta ferramenta armazena os dados confidenciais dos projetos de programação fora da árvore dos projetos. Esta abordagem ajuda a evitar a partilha acidental de segredos de aplicações no código fonte.

Abra o ficheiro *Redistest.csproj*. Adicione um elemento `DotNetCliToolReference` para incluir *Microsoft.Extensions.SecretManager.Tools*. Adicione também um elemento `UserSecretsId`, conforme mostrado abaixo, e guarde o ficheiro.

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp2.0</TargetFramework>
    <UserSecretsId>Redistest</UserSecretsId>
  </PropertyGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
  </ItemGroup>
</Project>
```

Execute o seguinte comando para adicionar o pacote *Microsoft.Extensions.Configuration.UserSecrets* ao projeto:

```
dotnet add package Microsoft.Extensions.Configuration.UserSecrets
```

Execute o seguinte comando para restaurar os pacotes:

```
dotnet restore
```

Na janela de comandos, execute o seguinte comando para armazenar um segredo novo denominado *CacheConnection*, depois de substituir os marcadores de posição (incluindo parênteses retos) para o nome da cache e a chave de acesso primária:

```
dotnet user-secrets set CacheConnection "<cache name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<primary-access-key>"
```

Adicione a seguinte instrução `using` ao ficheiro *Program.cs*:

```csharp
using Microsoft.Extensions.Configuration;
```

Adicione os seguintes membros à classe `Program` no *Program.cs*. Esse código inicializa uma configuração de acessar o segredo de utilizador para a Cache do Azure para a cadeia de ligação de Redis.

```csharp
        private static IConfigurationRoot Configuration { get; set; }
        const string SecretName = "CacheConnection";

        private static void InitializeConfiguration()
        {
            var builder = new ConfigurationBuilder()
                .AddUserSecrets<Program>();

            Configuration = builder.Build();
        }
```

## <a name="configure-the-cache-client"></a>Configurar o cliente de cache

Nesta secção, vai configurar a aplicação de consola para utilizar o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) para o .NET.

Na janela de comandos, execute o seguinte comando no diretório do projeto *Redistest*:

```
dotnet add package StackExchange.Redis
```

Depois de concluída a instalação, o cliente de cache *StackExchange.Redis* está disponível para utilizar com o seu projeto.


## <a name="connect-to-the-cache"></a>Ligar à cache

Adicione a seguinte instrução `using` ao ficheiro *Program.cs*:

```csharp
using StackExchange.Redis;
```

A ligação à Cache do Azure para o Redis é gerenciada pelo `ConnectionMultiplexer` classe. Esta classe deve ser partilhada e reutilizada em toda a aplicação cliente. Não crie uma nova ligação para cada operação. 

Em *Program.cs*, adicione os seguintes membros à classe `Program` da aplicação de consola:

```csharp
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = Configuration[SecretName];
            return ConnectionMultiplexer.Connect(cacheConnection);
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
```

Esta abordagem para partilhar uma instância `ConnectionMultiplexer` na aplicação utiliza uma propriedade estática que devolve uma instância ligada. O código oferece uma forma segura para os threads de modo a inicializar apenas uma única instância `ConnectionMultiplexer` ligada. `abortConnect` está definido como FALSO, o que significa que a chamada terá êxito mesmo que não é possível estabelecer uma ligação à Cache de Redis do Azure. Uma funcionalidade-chave do `ConnectionMultiplexer` consiste no restauro automático da conectividade à cache assim que o problema de rede, ou outros problemas, tiverem sido resolvidos.

O valor do segredo *CacheConnection* é acedido com o fornecedor de configuração do Gestor de Segredo e utilizado como o parâmetro de palavra-passe.

## <a name="executing-cache-commands"></a>Executar comandos de cache

Em *Program.cs*, adicione o seguinte código ao procedimento `Main` da classe `Program` da aplicação de consola:

```csharp
        static void Main(string[] args)
        {
            InitializeConfiguration();

            // Connection refers to a property that returns a ConnectionMultiplexer
            // as shown in the previous example.
            IDatabase cache = lazyConnection.Value.GetDatabase();

            // Perform cache operations using the cache object...

            // Simple PING command
            string cacheCommand = "PING";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : " + cache.Execute(cacheCommand).ToString());

            // Simple get and put of integral data types into the cache
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            cacheCommand = "SET Message \"Hello! The cache is working from a .NET Core console app!\"";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
            Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET Core console app!").ToString());

            // Demonstrate "SET Message" executed as expected...
            cacheCommand = "GET Message";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
            Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

            // Get the client list, useful to see if connection list is growing...
            cacheCommand = "CLIENT LIST";
            Console.WriteLine("\nCache command  : " + cacheCommand);
            Console.WriteLine("Cache response : \n" + cache.Execute("CLIENT", "LIST").ToString().Replace("id=", "id="));

            lazyConnection.Value.Dispose();
        }
```

Guarde *Program.cs*.

A Cache de Redis do Azure tem um número configurável de bases de dados (predefinição de 16) que pode ser utilizado para separar logicamente os dados dentro de uma Cache do Azure para Redis. O código estabelece ligação à base de dados predefinida, DB 0. Para obter mais informações, veja [O que são as bases de dados Redis?](cache-faq.md#what-are-redis-databases) e [Configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration).

Os itens de cache podem ser obtidos com os métodos `StringSet` e `StringGet`.

O Redis armazena grande parte dos dados como cadeias de Redis. No entanto, estas cadeias podem conter vários tipos de dados, incluindo dados binários serializados, que podem ser utilizados ao armazenar objetos .NET na cache.

Execute o seguinte comando na janela de comandos para criar a aplicação:

```
dotnet build
```

Em seguida, execute a aplicação com o comando seguinte:

```
dotnet run
```

No exemplo abaixo, pode ver que a chave `Message` tinha anteriormente um valor em cache, o qual foi definido com a Consola Redis no portal do Azure. A aplicação atualizou esse valor em cache. A aplicação também executou os comandos `PING` e `CLIENT LIST`.

![Aplicação de consola parcial](./media/cache-dotnet-core-quickstart/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Trabalhar com objetos .NET na cache

A Cache de Redis do Azure pode colocar em cache objetos .NET e tipos de dados primitivos, mas para que um objeto .NET pode ser colocado em cache tem de ser serializado. Esta serialização do objeto .NET é da responsabilidade do programador da aplicação, podendo o programador escolher o serializador pretendido.

Uma forma simples de serializar objetos passa por utilizar os métodos de serialização `JsonConvert` no [Newtonsoft.Json.](https://www.nuget.org/packages/Newtonsoft.Json/) e serializar de e para o JSON. Nesta secção, vai adicionar um objeto .NET à cache.

Execute o seguinte comando para adicionar o pacote *newtonsoft* à aplicação:

```
dotnet add package Newtonsoft.json
```

Adicione a seguinte instrução `using` na parte superior do ficheiro *Program.cs*:

```csharp
using Newtonsoft.Json;
```

Adicione a seguinte definição de classe `Employee` ao *Program.cs*:

```csharp
        class Employee
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public int Age { get; set; }

            public Employee(string EmployeeId, string Name, int Age)
            {
                this.Id = EmployeeId;
                this.Name = Name;
                this.Age = Age;
            }
        }
```

Na parte inferior do procedimento `Main()` no *Program.cs* e antes da chamada para `Dispose()`, adicione as seguintes linhas de código à cache e obtenha um objeto .NET serializado:

```csharp
            // Store .NET object to cache
            Employee e007 = new Employee("007", "Davide Columbo", 100);
            Console.WriteLine("Cache response from storing Employee .NET object : " + 
                cache.StringSet("e007", JsonConvert.SerializeObject(e007)));

            // Retrieve .NET object from cache
            Employee e007FromCache = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e007"));
            Console.WriteLine("Deserialized Employee .NET object :\n");
            Console.WriteLine("\tEmployee.Name : " + e007FromCache.Name);
            Console.WriteLine("\tEmployee.Id   : " + e007FromCache.Id);
            Console.WriteLine("\tEmployee.Age  : " + e007FromCache.Age + "\n");
```

Guarde *Program.cs* e reconstrua a aplicação com o seguinte comando:

```
dotnet build
```

Execute a aplicação com o seguinte comando para testar a serialização de objetos .NET:

```
dotnet run
```

![Aplicação de consola concluída](./media/cache-dotnet-core-quickstart/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Limpar recursos

Se avançar para o próximo tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se tiver concluído a aplicação de exemplo do início rápido, poderá eliminar os recursos do Azure criados neste início rápido, para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos nele contidos serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
>

Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...**, escreva o nome do grupo de recursos. As instruções neste artigo utilizaram um grupo de recursos denominado *TestResources*. No grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

![Eliminar](./media/cache-dotnet-core-quickstart/cache-delete-resource-group.png)

É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos para confirmar e clique em **Eliminar**.

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.



<a name="next-steps"></a>

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, aprendeu a utilizar o Cache do Azure para Redis a partir de uma aplicação .NET Core. Continue para o próximo início rápido utilizar a Cache do Azure para Redis com uma aplicação web ASP.NET.

> [!div class="nextstepaction"]
> [Crie uma aplicação web ASP.NET que utiliza uma Cache do Azure para Redis.](./cache-web-app-howto.md)




