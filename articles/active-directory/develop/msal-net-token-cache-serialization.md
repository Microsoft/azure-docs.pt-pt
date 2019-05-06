---
title: Token de serialização de cache na biblioteca de autenticação da Microsoft para o .NET | Azure
description: Saiba mais sobre a serialização e a serialização de cliente de cache de token com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f259d8438b7500d5d007bbb972f859e187ccd08a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65081032"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serialização de cache de token em MSAL.NET
Depois de um [token é obtido](msal-acquire-cache-tokens.md), é colocado em cache pelo Microsoft Authentication Library (MSAL).  Código da aplicação deve tentar obter um token da cache antes de adquirir um token por outro método.  Este artigo aborda o padrão e serialização personalizada da cache do token no MSAL.NET.

Este artigo é para MSAL.NET 3.x. Se estiver interessado em MSAL.NET 2.x, consulte [Token de serialização de cache em MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Serialização de padrão para plataformas móveis

MSAL.NET, uma cache de token na memória é fornecido por predefinição. Serialização é fornecida por predefinição para plataformas em que um armazenamento seguro está disponível para um utilizador como parte da plataforma. Este é o caso da plataforma Universal do Windows (UWP), xamarin. IOS e xamarin. Android.

> [!Note]
> Ao migrar um projeto xamarin. Android de MSAL.NET 1.x para MSAL.NET 3.x, convém adicionar `android:allowBackup="false"` ao seu projeto para evitar antigo em cache tokens a partir de vindo de volta quando implementações do Visual Studio acionam um restauro do armazenamento local. Ver [emitir #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Serialização personalizada para aplicativos de desktop do Windows e de aplicações web a APIs/web

Lembre-se que a serialização personalizada não está disponível em plataformas móveis (UWP, xamarin. IOS e xamarin. Android). A MSAL já define um mecanismo de serialização seguro e de elevado desempenho para estas plataformas. Ambiente de trabalho do .NET e aplicações de .NET Core, no entanto, tem diversificados arquiteturas e MSAL não é possível implementar um mecanismo de serialização para fins gerais. Por exemplo, os sites da web pode optar por armazenar tokens em cache de Redis ou tokens de loja de aplicações de ambiente de trabalho num ficheiro encriptado. Portanto, a serialização não foi fornecida out-of-the-box. Para que um aplicativo de cache de token persistente na área de trabalho do .NET ou .NET Core, terá de personalizar a serialização.

As interfaces e classes seguintes são utilizados na serialização de cache de token:

- `ITokenCache`, que define os eventos para subscrever a pedidos de serialização de cache de tokens, bem como métodos ao serializar ou anular a serialização do cache em vários formatos (v3.0 da ADAL, MSAL 2.x e MSAL 3.x = v5.0 da ADAL).
- `TokenCacheCallback` é um retorno de chamada passado para os eventos para que pode manipular a serialização. Serão chamadas com argumentos de tipo `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` apenas fornece a `ClientId` da aplicação e uma referência ao utilizador para o qual o token está disponível.

  ![Diagrama de classes](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET cria caches de token para e fornece-lhe com o `IToken` coloca em cache quando chamar um aplicativo `GetUserTokenCache` e `GetAppTokenCache` métodos. Não devem implementar a interface por conta própria. É sua responsabilidade, ao implementar uma serialização de cache de token personalizados:
> - Reaja aos `BeforeAccess` e `AfterAccess` "eventos". O `BeforeAccess` delegado é da responsabilidade de anular a serialização da cache, ao passo que o `AfterAccess` um é responsável pela serialização do cache.
> - Parte desses eventos armazenar ou carregar blobs, que são transmitidas o argumento do evento para qualquer armazenamento que pretende.

As estratégias são diferentes, dependendo se estiver escrevendo uma serialização de cache de token para uma [aplicativo cliente pública](msal-client-applications.md) (ambiente de trabalho), ou uma [aplicação de cliente confidencial](msal-client-applications.md)) (aplicação web / API, web daemon aplicação ).

### <a name="token-cache-for-a-public-client"></a>Cache de token para um cliente público 

Desde MSAL.NET v2.x tem várias opções para serializar o cache de tokens de um cliente público. Pode serializar o cache apenas para o formato MSAL.NET (o cache de formato unificado é comum em MSAL e as plataformas).  Também pode oferecer suporte a [herdados](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) serialização de cache da ADAL V3 do token.

Personalizar a serialização de cache de tokens para partilhar o estado único início de sessão entre ADAL.NET 3.x, ADAL.NET 5.x e MSAL.NET é explicado em parte do exemplo seguinte: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> O formato de cache de tokens de 1.1.4-preview MSAL.NET já não é suportado no MSAL 2.x. Se tiver aplicações que tiram partido MSAL.NET 1.x, os utilizadores terão a voltar-sessão-in. Em alternativa, é suportada a migração do ADAL 4.x (e 3.x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Serialização de cache de tokens simples (MSAL apenas)

Segue-se um exemplo de uma implementação ingênua de serialização personalizada de uma cache de token para aplicativos de desktop. Aqui, o cache de token de utilizador é um ficheiro na mesma pasta que o aplicativo.

Depois de compilar o aplicativo, habilitar a serialização, chamando o `TokenCacheHelper.EnableSerialization()` método e passando o aplicativo `UserTokenCache`.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

O `TokenCacheHelper` classe auxiliar é definido como:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
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

Uma pré-visualização de um serializador de baseado em ficheiros de cache de tokens do produto qualidade para aplicações de cliente público (para aplicações de ambiente de trabalho em execução no Windows, Mac e Linux) está disponível a partir da [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) biblioteca de código-fonte aberto. Pode incluí-lo em seus aplicativos a partir do pacote nuget do seguinte: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serialização de cache de tokens dupla (cache MSAL unificado e v3 da ADAL)

Se pretender implementar o cache de tokens serialização tanto com o formato de cache unificada (comuns a ADAL.NET 4.x, MSAL.NET 2.x e outros MSALs da mesma geração ou mais, na mesma plataforma), dê uma olhada no código a seguir:

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

Desta vez, a classe auxiliar, conforme definido como:

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
  /// Get the user token cache
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
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Cache de token para uma aplicação web (aplicação de cliente confidencial)

Em aplicações web ou web APIs, o cache poderia usar a sessão, uma cache de Redis ou um banco de dados.

Uma coisa importante a lembrar é que para aplicações web e web APIs, deve haver uma cache de tokens por utilizador (por conta). Precisa serializar o cache de token para cada conta.

Exemplos de como utilizar caches de token para aplicações web e web APIs estão disponíveis no [tutorial da aplicação web ASP.NET Core](https://ms-identity-aspnetcore-webapp-tutorial) na fase [Cache de tokens de 2 de 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Para as implementações têm uma olhada na seguinte pasta [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) no [microsoft-authentication-extensões-para-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) biblioteca (no [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) pasta. 

## <a name="next-steps"></a>Passos Seguintes
As seguintes amostras ilustram a serialização de cache de tokens.

| Exemplo | Plataforma | Descrição|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](http://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Ambiente de trabalho (WPF) | Aplicações de .NET de Desktop do Windows (WPF), chamar a API do Microsoft Graph. ![Topologia](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Ambiente de trabalho (consola) | Conjunto de soluções do Visual Studio que ilustra a migração de aplicativos de versão 1.0 do Azure AD (usando ADAL.NET) para aplicações de versão 2.0 do Azure AD, também denominadas convergido aplicativos (com MSAL.NET), em particular [Token de Cache de migração](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|