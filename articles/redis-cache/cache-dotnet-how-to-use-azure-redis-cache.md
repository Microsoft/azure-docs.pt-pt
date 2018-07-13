---
title: Início Rápido para aprender a utilizar a Cache de Redis do Azure com as aplicações .NET | Microsoft Docs
description: Neste início rápido, aprenda como aceder à Cache de Redis do Azure a partir das suas aplicações .NET
services: redis-cache,app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: c502f74c-44de-4087-8303-1b1f43da12d5
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/18/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: 31d93fc8b2034152e61d24a789bba62bfd3b7892
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635443"
---
# <a name="quickstart-use-azure-redis-cache-with-a-net-application"></a>Início Rápido: Utilizar a Cache de Redis do Azure com a aplicação .NET



Este início rápido mostra como começar a utilizar a Cache de Redis do Microsoft Azure com o .NET. A Cache de Redis do Microsoft Azure baseia-se na popular Cache de Redis open source. Permite-lhe aceder a uma cache de Redis segura, dedicada e gerida pela Microsoft. Pode aceder a uma cache criada com a Cache de Redis do Azure a partir de qualquer aplicação dentro do Microsoft Azure.

Neste início rápido, irá utilizar o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) com código C\# numa aplicação de consola. Irá criar uma cache e configurar a aplicação de cliente .NET. Em seguida, irá adicionar e atualizar objetos na cache. 

![Aplicação de consola concluída](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio](https://www.visualstudio.com/downloads/)
* O cliente StackExchange.Redis exige o [.NET Framework 4 ou superior](https://www.microsoft.com/net/download/dotnet-framework-runtime).

## <a name="create-a-cache"></a>Criar uma cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

No computador, crie um ficheiro denominado *CacheSecrets.config* e coloque-o numa localização onde não será registado com o código fonte da aplicação de exemplo. Para este início rápido, o ficheiro *CacheSecrets.config* está localizado aqui: *C:\AppSecrets\CacheSecrets.config*.

Edite o ficheiro *CacheSecrets.config* e adicione os seguintes conteúdos:

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,password=<access-key>"/>
</appSettings>
```

Substitua `<cache-name>` pelo nome do anfitrião da cache.

Substitua `<access-key>` pela chave primária da sua cache.


## <a name="create-a-console-app"></a>Criar uma aplicação de consola

No Visual Studio, clique em **Ficheiro** > **Novo** > **Projeto**.

No **Visual C#**, clique em **Ambiente de Trabalho Clássico do Windows** e, em seguida, clique em **Aplicação de Consola** e **OK**, para criar uma nova aplicação de consola.


<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>Configurar o cliente de cache

Nesta secção, vai configurar a aplicação de consola para utilizar o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) para o .NET.

No Visual Studio, clique em **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola do Gestor de Pacotes** e execute o seguinte comando a partir da janela Consola do Gestor de Pacotes.

```powershell
Install-Package StackExchange.Redis
```

Depois de concluída a instalação, o cliente de cache *StackExchange.Redis* está disponível para utilizar com o seu projeto.


## <a name="connect-to-the-cache"></a>Ligar à cache

No Visual Studio, abra o seu ficheiro *App.config* e atualize-o para incluir um atributo `appSettings` `file` que referencia o ficheiro *CacheSecrets.config*.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.1" />
    </startup>

    <appSettings file="C:\AppSecrets\CacheSecrets.config"></appSettings>  

</configuration>
```

No Explorador de Soluções, clique com o botão direito do rato em **Referências** e clique em **Adicionar uma referência**. Adicione uma referência à assemblagem **System.Configuration**.

Adicione as seguintes declarações `using` ao *Program.cs*:

```csharp
using StackExchange.Redis;
using System.Configuration;
```

A ligação à Cache de Redis do Azure é gerida pela classe `ConnectionMultiplexer`. Esta classe deve ser partilhada e reutilizada em toda a aplicação cliente. Não crie uma nova ligação para cada operação. 

Nunca guarde as credenciais no código fonte. Para simplificar este exemplo, estou a utilizar apenas um ficheiro de configuração de segredos externo. Uma melhor abordagem seria utilizar o [Azure Key Vault com certificados](https://docs.microsoft.com/rest/api/keyvault/certificate-scenarios).

Em *Program.cs*, adicione os seguintes membros à classe `Program` da sua aplicação de consola:

```csharp
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
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


Esta abordagem para partilhar uma instância `ConnectionMultiplexer` na aplicação utiliza uma propriedade estática que devolve uma instância ligada. O código oferece uma forma segura para os threads de modo a inicializar apenas uma única instância `ConnectionMultiplexer` ligada. `abortConnect` está definido como falso, o que significa que a chamada terá êxito mesmo se não for ligada à Cache de Redis do Azure. Uma funcionalidade-chave do `ConnectionMultiplexer` consiste no restauro automático da conectividade à cache assim que o problema de rede, ou outros problemas, tiverem sido resolvidos.

O valor da appSetting de *CacheConnection* serve para referenciar a cadeia de ligação da cache a partir do portal do Azure como o parâmetro de palavra-passe.

## <a name="executing-cache-commands"></a>Executar comandos de cache

Adicione o seguinte código ao procedimento `Main` da classe `Program` para a sua aplicação de consola:

```csharp
        static void Main(string[] args)
        {
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

            cacheCommand = "SET Message \"Hello! The cache is working from a .NET console app!\"";
            Console.WriteLine("\nCache command  : " + cacheCommand + " or StringSet()");
            Console.WriteLine("Cache response : " + cache.StringSet("Message", "Hello! The cache is working from a .NET console app!").ToString());

            // Demostrate "SET Message" executed as expected...
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

Os caches de Redis do Azure têm um número configurável de bases de dados (predefinição de 16) que pode ser utilizado para separar logicamente os dados dentro de uma cache de Redis. O código estabelece ligação à base de dados predefinida, DB 0. Para obter mais informações, veja [O que são as bases de dados Redis?](cache-faq.md#what-are-redis-databases) e [Configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration).

Os itens de cache podem ser obtidos com os métodos `StringSet` e `StringGet`.

O Redis armazena grande parte dos dados como cadeias de Redis. No entanto, estas cadeias podem conter vários tipos de dados, incluindo dados binários serializados, que podem ser utilizados ao armazenar objetos .NET na cache.

Prima **Ctrl+F5** para compilar e executar a aplicação de consola.

No exemplo abaixo, pode ver que a chave `Message` tinha anteriormente um valor em cache, o qual foi definido com a Consola Redis no portal do Azure. A aplicação atualizou esse valor em cache. A aplicação também executou os comandos `PING` e `CLIENT LIST`.

![Aplicação de consola parcial](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Trabalhar com objetos .NET na cache

A Cache de Redis do Azure pode colocar em cache objetos .NET e tipos de dados primitivos, mas para ser colocado em cache, o objeto .NET tem de ser serializado. Esta serialização do objeto .NET é da responsabilidade do programador da aplicação, podendo o programador escolher o serializador pretendido.

Uma forma simples de serializar objetos passa por utilizar os métodos de serialização `JsonConvert` no [Newtonsoft.Json.](https://www.nuget.org/packages/Newtonsoft.Json/) e serializar de e para o JSON. Nesta secção, irá adicionar um objeto de .NET à cache.

No Visual Studio, clique em **Ferramentas** > **Gestor de Pacotes NuGet** > **Consola do Gestor de Pacotes** e execute o seguinte comando a partir da janela Consola do Gestor de Pacotes.

```powershell
Install-Package Newtonsoft.Json
```

Adicione a seguinte declaração `using` na parte superior do ficheiro *Program.cs*:

```charp
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

Na parte inferior do `Main()` procedimento no *Program.cs* e antes da chamada para `Dispose()`, adicione as seguintes linhas de código à cache e obtenha um objeto serializado .NET:

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

Prima **Ctrl+F5** para compilar e executar a aplicação de consola para testar a serialização de objetos .NET. 

![Aplicação de consola concluída](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-complete.png)


## <a name="clean-up-resources"></a>Limpar recursos

Se avançar para o próximo tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se tiver concluído a aplicação de exemplo do início rápido, poderá eliminar os recursos do Azure criados neste início rápido, para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos nele contidos serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
>

Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...**, escreva o nome do grupo de recursos. As instruções neste artigo utilizaram um grupo de recursos denominado *TestResources*. No grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

![Eliminar](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos para confirmar e clique em **Eliminar**.

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.



<a name="next-steps"></a>

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a utilizar a Cache de Redis do Azure a partir de uma aplicação .NET. Continue para o início rápido seguinte para utilizar a Cache de Redis com uma aplicação Web ASP.NET.

> [!div class="nextstepaction"]
> [Criar uma aplicação Web ASP.NET que utiliza uma Cache de Redis do Azure](./cache-web-app-howto.md)


