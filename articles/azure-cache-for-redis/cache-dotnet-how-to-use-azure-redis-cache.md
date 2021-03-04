---
title: 'Quickstart: Use Azure Cache para Redis em .NET Framework'
description: Neste quickstart, aprenda a aceder a Azure Cache para Redis a partir das suas aplicações .NET
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp, mvc
ms.date: 06/18/2020
ms.openlocfilehash: ffc5c8ea647d4cadd2d151eb880c794ac5f4ebd4
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121444"
---
# <a name="quickstart-use-azure-cache-for-redis-in-net-framework"></a>Quickstart: Use Azure Cache para Redis em .NET Framework

Neste quickstart, incorpora o Azure Cache para Redis numa aplicação .NET Framework para ter acesso a uma cache segura e dedicada que esteja acessível a partir de qualquer aplicação dentro do Azure. Utilize especificamente o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) com código C# numa aplicação de consola .NET.

## <a name="skip-to-the-code-on-github"></a>Salte para o código no GitHub

Se quiser saltar diretamente para o código, consulte o [quickstart .NET Framework](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/dotnet) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://www.visualstudio.com/downloads/)
- [.NET Framework 4 ou superior](https://www.microsoft.com/net/download/dotnet-framework-runtime), o que é exigido pelo cliente StackExchange.Redis.

## <a name="create-a-cache"></a>Criar uma cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]

No computador, crie um ficheiro denominado *CacheSecrets.config* e coloque-o numa localização onde não será registado com o código fonte da aplicação de exemplo. Para este início rápido, o ficheiro *CacheSecrets.config* está localizado aqui: *C:\AppSecrets\CacheSecrets.config*.

Edite o ficheiro *CacheSecrets.config* e adicione os seguintes conteúdos:

```xml
<appSettings>
    <add key="CacheConnection" value="<cache-name>.redis.cache.windows.net,abortConnect=false,ssl=true,allowAdmin=true,password=<access-key>"/>
</appSettings>
```

Substitua `<cache-name>` pelo nome do anfitrião da cache.

Substitua `<access-key>` pela chave primária da sua cache.


## <a name="create-a-console-app"></a>Criar uma aplicação de consola

No Estúdio Visual, clique em **File**  >  **New**  >  **Project**.

Selecione **App de Consola (.NET Framework)** e **Next** to configure a sua aplicação. Digite um **nome de projeto** e clique em **Criar** para criar uma nova aplicação de consola.

<a name="configure-the-cache-clients"></a>

## <a name="configure-the-cache-client"></a>Configurar o cliente de cache

Nesta secção, vai configurar a aplicação de consola para utilizar o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) para o .NET.

No Estúdio Visual, clique em **Tools**  >  **NuGet Package Manager** Package Manager  >  **Consola** e execute o seguinte comando a partir da janela da consola do gestor de pacotes.

```powershell
Install-Package StackExchange.Redis
```

Depois de concluída a instalação, o cliente de cache *StackExchange.Redis* está disponível para utilizar com o seu projeto.


## <a name="connect-to-the-cache"></a>Ligar-se à cache

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

A ligação à Cache Azure para Redis é gerida pela `ConnectionMultiplexer` classe. Esta classe deve ser partilhada e reutilizada em toda a aplicação cliente. Não crie uma nova ligação para cada operação. 

Nunca guarde as credenciais no código fonte. Para simplificar este exemplo, estou a utilizar apenas um ficheiro de configuração de segredos externo. Uma melhor abordagem seria utilizar o [Azure Key Vault com certificados](/rest/api/keyvault/certificate-scenarios).

Em *Program.cs*, adicione os seguintes membros à classe `Program` da aplicação de consola:

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


Esta abordagem para partilhar uma instância `ConnectionMultiplexer` na aplicação utiliza uma propriedade estática que devolve uma instância ligada. O código oferece uma forma segura para os threads de modo a inicializar apenas uma única instância `ConnectionMultiplexer` ligada. `abortConnect` é definido como falso, o que significa que a chamada tem sucesso mesmo que não seja estabelecida uma ligação com a Cache Azure para Redis. Uma funcionalidade-chave do `ConnectionMultiplexer` consiste no restauro automático da conectividade à cache assim que o problema de rede, ou outros problemas, tiverem sido resolvidos.

O valor da appSetting de *CacheConnection* serve para referenciar a cadeia de ligação da cache a partir do portal do Azure como o parâmetro de palavra-passe.

## <a name="executing-cache-commands"></a>Executar comandos de cache

Adicione o seguinte código ao procedimento `Main` da classe `Program` para a sua aplicação de consola:

```csharp
static void Main(string[] args)
{
    // Connection refers to a property that returns a ConnectionMultiplexer
    // as shown in the previous example.
    IDatabase cache = Connection.GetDatabase();

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

    // Demonstrate "SET Message" executed as expected...
    cacheCommand = "GET Message";
    Console.WriteLine("\nCache command  : " + cacheCommand + " or StringGet()");
    Console.WriteLine("Cache response : " + cache.StringGet("Message").ToString());

    // Get the client list, useful to see if connection list is growing...
    // Note that this requires the allowAdmin=true
    cacheCommand = "CLIENT LIST";
    Console.WriteLine("\nCache command  : " + cacheCommand);
    var endpoint = (System.Net.DnsEndPoint) Connection.GetEndPoints()[0];
    var server = Connection.GetServer(endpoint.Host, endpoint.Port);

    var clients = server.ClientList(); 
    Console.WriteLine("Cache response :");
    foreach (var client in clients)
    {
        Console.WriteLine(client.Raw);
    }

    lazyConnection.Value.Dispose();
}
```

A azure Cache para Redis tem um número configurável de bases de dados (padrão de 16) que podem ser usadas para separar logicamente os dados dentro de uma Cache Azure para Redis. O código estabelece ligação à base de dados predefinida, DB 0. Para obter mais informações, veja [O que são as bases de dados Redis?](cache-development-faq.md#what-are-redis-databases) e [Configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration).

Os itens de cache podem ser armazenados com os métodos `StringSet` e `StringGet`.

O Redis armazena grande parte dos dados como cadeias de Redis. No entanto, estas cadeias podem conter vários tipos de dados, incluindo dados binários serializados, que podem ser utilizados ao armazenar objetos .NET na cache.

Prima **Ctrl+F5** para compilar e executar a aplicação de consola.

No exemplo abaixo, pode ver que a chave `Message` tinha anteriormente um valor em cache, o qual foi definido com a Consola Redis no portal do Azure. A aplicação atualizou esse valor em cache. A aplicação também executou os comandos `PING` e `CLIENT LIST`.

![Aplicação de consola parcial](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-console-app-partial.png)


## <a name="work-with-net-objects-in-the-cache"></a>Trabalhar com objetos .NET na cache

O Azure Cache for Redis pode cache tanto objetos .NET como tipos de dados primitivos, mas antes que um objeto .NET possa ser em cache deve ser serializado. Esta serialização do objeto .NET é da responsabilidade do programador da aplicação, podendo o programador escolher o serializador pretendido.

Uma forma simples de serializar objetos passa por utilizar os métodos de serialização `JsonConvert` no [Newtonsoft.Json.](https://www.nuget.org/packages/Newtonsoft.Json/) e serializar de e para o JSON. Nesta secção, vai adicionar um objeto .NET à cache.

No Estúdio Visual, clique em **Tools**  >  **NuGet Package Manager** Package Manager  >  **Consola** e execute o seguinte comando a partir da janela da consola do gestor de pacotes.

```powershell
Install-Package Newtonsoft.Json
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


## <a name="clean-up-resources"></a>Limpar os recursos

Se avançar para o próximo tutorial, pode manter os recursos que criou neste início rápido e reutilizá-los.

Caso contrário, se tiver concluído a aplicação de exemplo do início rápido, pode eliminar os recursos do Azure criados neste início rápido, para evitar encargos. 

> [!IMPORTANT]
> A eliminação de um grupo de recursos é irreversível e o grupo de recursos e todos os recursos contidos no mesmo serão permanentemente eliminados. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados. Se tiver criado os recursos para alojar este exemplo num grupo de recursos existente que contém os recursos que pretende manter, poderá eliminar cada recurso individualmente nos respetivos painéis em vez de eliminar o grupo de recursos.
>

Inicie sessão no [Portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...**, escreva o nome do grupo de recursos. As instruções neste artigo utilizaram um grupo de recursos denominado *TestResources*. No seu grupo de recursos na lista de resultados, clique em **...** e em **Eliminar grupo de recursos**.

![Eliminar](./media/cache-dotnet-how-to-use-azure-redis-cache/cache-delete-resource-group.png)

É-lhe pedido que confirme a eliminação do grupo de recursos. Escreva o nome do grupo de recursos para confirmar e clique em **Eliminar**.

Após alguns instantes, o grupo de recursos e todos os recursos contidos no mesmo são eliminados.



<a name="next-steps"></a>

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a usar a Cache Azure para Redis a partir de uma aplicação .NET. Continue até ao próximo quickstart para usar Azure Cache para Redis com uma aplicação web ASP.NET.

> [!div class="nextstepaction"]
> [Crie uma aplicação web ASP.NET que utilize um Cache Azure para Redis.](./cache-web-app-howto.md)

Quer otimizar e economizar nos gastos na nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar custos com a Gestão de Custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)