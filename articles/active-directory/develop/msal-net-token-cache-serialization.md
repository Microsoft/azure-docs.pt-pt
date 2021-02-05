---
title: '| de serialização de cache token (MSAL.NET) Rio Azure'
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a serialização e serialização do cliente da cache simbólica utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 60ce3d32ffa20fc9117890528eac053d1af9fdf2
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583913"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serialização da cache simbólica em MSAL.NET
Depois de adquirido um [token,](msal-acquire-cache-tokens.md)é em cache pela Microsoft Authentication Library (MSAL).  O código de aplicação deve tentar obter um símbolo da cache antes de adquirir um token por outro método.  Este artigo discute a serialização padrão e personalizada da cache simbólica em MSAL.NET.

Este artigo é para MSAL.NET 3.x. Se estiver interessado em MSAL.NET 2.x, consulte [a serialização da cache token em MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Serialização padrão para plataformas móveis

Em MSAL.NET, uma cache simbólica na memória é fornecida por padrão. A serialização é fornecida por padrão para plataformas onde o armazenamento seguro está disponível para um utilizador como parte da plataforma. É o caso da Universal Windows Platform (UWP), Xamarin.iOS e Xamarin.Android.

> [!Note]
> Quando migrar um projeto Xamarin.Android de MSAL.NET 1.x para MSAL.NET 3.x, talvez queira adicionar ao seu projeto para evitar que `android:allowBackup="false"` os tokens em cache antigos voltem quando as implementações do Visual Studio desencadearem um restabelecemento do armazenamento local. Ver [Edição #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Serialização personalizada para aplicações de desktop do Windows e aplicações web/APIs web

Lembre-se, a serialização personalizada não está disponível nas plataformas móveis (UWP, Xamarin.iOS e Xamarin.Android). A MSAL já define um mecanismo de serialização seguro e performante para estas plataformas. No entanto, as aplicações .NET desktop e .NET Core têm arquiteturas variadas e a MSAL não consegue implementar um mecanismo de serialização para fins gerais. Por exemplo, os sites podem optar por armazenar fichas numa cache Redis ou aplicações de desktop armazenar fichas num ficheiro encriptado. Então a serialização não é fornecida fora da caixa. Para ter uma aplicação de cache token persistente em .NET desktop ou .NET Core, personalize a serialização.

As seguintes classes e interfaces são utilizadas na serialização da cache simbólica:

- `ITokenCache`, que define eventos para subscrever pedidos de serialização de cache token, bem como métodos para serializar ou des-serializar a cache em vários formatos (ADAL v3.0, MSAL 2.x e MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback` é uma chamada transmitida para os eventos para que possa lidar com a serialização. Serão chamados com argumentos do `TokenCacheNotificationArgs` tipo.
- `TokenCacheNotificationArgs` apenas fornece a `ClientId` aplicação e uma referência ao utilizador para o qual o token está disponível.

  ![Diagrama de classe](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET cria caches simbólicos para si e fornece-lhe a `IToken` cache quando chama as propriedades e propriedades de uma `UserTokenCache` `AppTokenCache` aplicação. Não é suposto implementares a interface sozinho. A sua responsabilidade, quando implementa uma serialização de cache de símbolo personalizado, é:
> - Reagir `BeforeAccess` e `AfterAccess` "eventos" (ou seus sabores Async). O `BeforeAccess` delegado é responsável por desseializar a cache, enquanto que o responsável pela `AfterAccess` serialização da cache.
> - Parte destes eventos armazenam ou carregam bolhas, que são passadas através do argumento do evento para qualquer armazenamento que você deseja.

As estratégias são diferentes dependendo se estiver a escrever uma serialização de cache simbólica para uma [aplicação de cliente público](msal-client-applications.md) (desktop), ou uma [aplicação confidencial do cliente](msal-client-applications.md)( web app/web API, daemon app).

### <a name="token-cache-for-a-public-client"></a>Cache simbólico para um cliente público

Desde MSAL.NET v2.x tem várias opções para serializar a cache simbólica de um cliente público. Pode serializar a cache apenas para o formato MSAL.NET (a cache de formato unificado é comum em todo o MSAL e nas plataformas).  Também pode suportar a serialização da cache [simbólica do](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) ADAL V3.

A personalização da serialização da cache simbólica para partilhar o estado de sinalização única entre ADAL.NET 3,x, ADAL.NET 5,x e MSAL.NET é explicado em parte da seguinte amostra: [active-directy-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> O formato de cache de MSAL.NET 1.1.4-preview já não é suportado em MSAL 2.x. Se tiver aplicações aproveitando MSAL.NET 1.x, os seus utilizadores terão de se recandido. Alternadamente, a migração de ADAL 4.x (e 3.x) é suportada.

#### <a name="simple-token-cache-serialization-msal-only"></a>Serialização simples da cache simbólica (apenas MSAL)

Abaixo está um exemplo de uma implementação ingénua da serialização personalizada de uma cache simbólica para aplicações de ambiente de trabalho. Aqui, a cache de ficha do utilizador é um ficheiro na mesma pasta que a aplicação.

Depois de construir a aplicação, permite a serialização chamando o `TokenCacheHelper.EnableSerialization()` método e passando a aplicação `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

A `TokenCacheHelper` classe de ajudante é definida como:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Um serializador baseado em cache de qualidade do produto para aplicações de clientes públicos (para aplicações de desktop em execução no Windows, Mac e Linux) está disponível na biblioteca [microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) open-source. Pode incluí-lo nas suas aplicações a partir do seguinte pacote NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serialização de cache de token duplo (cache unificado MSAL e ADAL v3)

Se pretender implementar a serialização da cache simbólica tanto com o formato de cache unificado (comum a ADAL.NET 4.x, MSAL.NET 2.x, e outros MSALs da mesma geração ou mais, na mesma plataforma), veja o seguinte código:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Desta vez, a classe de ajudante, tal como definida como:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Cache token para uma aplicação web (aplicação confidencial do cliente)

Em aplicativos web ou APIs web, o cache poderia aproveitar a sessão, uma cache Redis ou uma base de dados. Deve manter um cache simbólico por conta em aplicações web ou APIs web. 

Para aplicações web, a cache simbólica deve ser chaveda pelo ID da conta.

Para as APIs web, a conta deve ser chaveizada pelo haxixe do símbolo usado para chamar a API.

MSAL.NET fornece serialização de cache de fichas personalizadas em subplataformas .NET Framework e .NET Core. Os eventos são disparados quando a cache é acedida, as aplicações podem escolher se serializam ou desserizam a cache. Em aplicações confidenciais de clientes que tratam os utilizadores (aplicações web que assinam em utilizadores e chamam APIs web, e APIs web chamando APIs a jusante da web), pode haver muitos utilizadores e os utilizadores são processados em paralelo. Por razões de segurança e desempenho, a nossa recomendação é serializar uma cache por utilizador. Os eventos de serialização calculam uma chave de cache com base na identidade do utilizador processado e serializam/deserializem uma cache simbólica para esse utilizador.

A biblioteca [Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web) fornece um pacote de pré-visualização Do [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) contendo a serialização da cache simbólica:

| Método de Extensão | Microsoft.Identity.Web subconsecontos | Description  |
| ---------------- | --------- | ------------ |
| `AddInMemoryTokenCaches` | `TokenCacheProviders.InMemory` | Na memória, a serialização da cache simbólica. Esta implementação é ótima em amostras. Também é bom em aplicações de produção desde que não se importe se a cache de token é perdida quando a aplicação web é reiniciada. `AddInMemoryTokenCaches` toma um parâmetro opcional de tipo `MsalMemoryTokenCacheOptions` que lhe permite especificar a duração após a qual a entrada da cache expirará a menos que seja usada.
| `AddSessionTokenCaches` | `TokenCacheProviders.Session` | A cache simbólica está ligada à sessão do utilizador. Esta opção não é ideal se o token de ID contiver muitas reclamações, uma vez que o cookie se tornaria demasiado grande.
| `AddDistributedTokenCaches` | `TokenCacheProviders.Distributed` | A cache simbólica é um adaptador contra a implementação do núcleo `IDistributedCache` ASP.NET, permitindo-lhe escolher entre uma cache de memória distribuída, uma cache Redis, um NCache distribuído ou uma cache SQL Server. Para mais detalhes sobre as `IDistributedCache` implementações, consulte https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache .

Aqui está um exemplo de utilização da cache na memória no método [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) da classe [Startup](/aspnet/core/fundamentals/startup) numa aplicação core ASP.NET:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddInMemoryTokenCaches();
```

Exemplos de possíveis caches distribuídos:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

A sua utilização é apresentada no tutorial de [aplicações web core ASP.NET](/aspnet/core/tutorials/first-mvc-app/) na fase [2-2 Token Cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache).

## <a name="next-steps"></a>Passos seguintes

As seguintes amostras ilustram a serialização da cache simbólica.

| Sample | Plataforma | Description|
| ------ | -------- | ----------- |
|[active-directório-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Desktop (WPF) | Aplicação Windows Desktop .NET (WPF) chamada API do Gráfico microsoft. ![O diagrama mostra uma topologia com a Desktop App W P F TodoListClient fluindo para Azure A D, adquirindo um símbolo interativamente e para o Microsoft Graph.](media/msal-net-token-cache-serialization/topology.png)|
|[active-directy-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Desktop (Consola) | Conjunto de soluções visual Studio que ilustram a migração de aplicações Azure AD v1.0 (utilizando ADAL.NET) para aplicações de plataforma de identidade da Microsoft (utilizando MSAL.NET). Em particular, ver [migração de Cache Token](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
