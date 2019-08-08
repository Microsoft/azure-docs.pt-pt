---
title: Serialização de cache de token na biblioteca de autenticação da Microsoft para .NET | Azure
description: Saiba mais sobre serialização e serialização de cliente do cache de token usando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: caea0b7e64c7079156480aef0f65279989285ff3
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834975"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serialização de cache de token em MSAL.NET
Depois que um [token é adquirido](msal-acquire-cache-tokens.md), ele é armazenado em cache pela MSAL (biblioteca de autenticação da Microsoft).  O código do aplicativo deve tentar obter um token do cache antes de adquirir um token por outro método.  Este artigo aborda a serialização padrão e personalizada do cache de token em MSAL.NET.

Este artigo é para MSAL.NET 3. x. Se você estiver interessado em MSAL.NET 2. x, consulte [serialização de cache de token em MSAL.NET 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Serialização padrão para plataformas móveis

No MSAL.NET, um cache de token na memória é fornecido por padrão. A serialização é fornecida por padrão para plataformas em que o armazenamento seguro está disponível para um usuário como parte da plataforma. Esse é o caso para Plataforma Universal do Windows (UWP), Xamarin. iOS e Xamarin. Android.

> [!Note]
> Ao migrar um projeto Xamarin. Android de MSAL.NET 1. x para MSAL.net 3. x, talvez você queira adicionar `android:allowBackup="false"` ao seu projeto para evitar que tokens armazenados em cache antigos Voltem quando as implantações do Visual Studio disparam uma restauração do armazenamento local. Consulte o [problema #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Serialização personalizada para aplicativos da área de trabalho do Windows e aplicativos Web/APIs Web

Lembre-se de que a serialização personalizada não está disponível em plataformas móveis (UWP, Xamarin. iOS e Xamarin. Android). MSAL já define um mecanismo de serialização seguro e de alto desempenho para essas plataformas. No entanto, os aplicativos .NET desktop e .NET Core têm arquiteturas variadas e o MSAL não pode implementar um mecanismo de serialização de uso geral. Por exemplo, sites da Web podem optar por armazenar tokens em um cache Redis ou aplicativos da área de trabalho armazenar tokens em um arquivo criptografado. Portanto, a serialização não é fornecida pronta para uso. Para ter um aplicativo de cache de token persistente no .NET desktop ou no .NET Core, você precisa personalizar a serialização.

As seguintes classes e interfaces são usadas na serialização de cache de token:

- `ITokenCache`, que define os eventos para assinar solicitações de serialização de cache de token, bem como métodos para serializar ou desserializar o cache em vários formatos (ADAL v 3.0, MSAL 2. x e MSAL 3. x = ADAL v 5.0).
- `TokenCacheCallback`é um retorno de chamada passado para os eventos para que você possa manipular a serialização. Eles serão chamados com argumentos do tipo `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs`fornece apenas o `ClientId` do aplicativo e uma referência ao usuário para o qual o token está disponível.

  ![Diagrama de classe](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> O MSAL.NET cria caches de token para você e fornece o `IToken` cache quando você chama o `UserTokenCache` aplicativo e `AppTokenCache` as propriedades. Você não deve implementar a interface por conta própria. Sua responsabilidade, quando você implementa uma serialização personalizada de cache de token, é:
> - `BeforeAccess` Reagir e `AfterAccess` "eventos" (ou seus tipos assíncronos). O `BeforeAccess` delegado é responsável por desserializar o cache, `AfterAccess` enquanto aquele é responsável por serializar o cache.
> - Parte desses eventos armazena ou carrega BLOBs, que são passados pelo argumento de evento para qualquer armazenamento que você desejar.

As estratégias são diferentes dependendo se você estiver escrevendo uma serialização de cache de token para um [aplicativo cliente público](msal-client-applications.md) (Desktop) ou um [aplicativo cliente confidencial](msal-client-applications.md)) (aplicativo Web/API Web, aplicativo daemon).

### <a name="token-cache-for-a-public-client"></a>Cache de token para um cliente público 

Desde o MSAL.NET v2. x, você tem várias opções para serializar o cache de token de um cliente público. Você pode serializar o cache somente para o formato MSAL.NET (o cache de formato unificado é comum em MSAL e nas plataformas).  Você também pode dar suporte [](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) à serialização de cache de token herdado da Adal v3.

A personalização da serialização de cache de token para compartilhar o estado de logon único entre ADAL.NET 3. x, ADAL.NET 5. x e MSAL.NET é explicada em parte do seguinte exemplo: [Active-Directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> O formato de cache de token MSAL.NET 1.1.4-Preview não tem mais suporte no MSAL 2. x. Se você tiver aplicativos utilizando o MSAL.NET 1. x, os usuários precisarão entrar novamente. Como alternativa, há suporte para a migração do ADAL 4. x (e 3. x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Serialização de cache de token simples (somente MSAL)

Veja abaixo um exemplo de uma implementação ingênua de serialização personalizada de um cache de token para aplicativos de área de trabalho. Aqui, o cache do token de usuário é um arquivo na mesma pasta que o aplicativo.

Depois de criar o aplicativo, você habilita a serialização chamando o `TokenCacheHelper.EnableSerialization()` método e passando o aplicativo. `UserTokenCache`

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

A `TokenCacheHelper` classe auxiliar é definida como:

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

Uma visualização de um serializador baseado em arquivo de cache de token de qualidade de produto para aplicativos cliente públicos (para aplicativos de desktop em execução no Windows, Mac e Linux) está disponível na biblioteca de código-fonte aberto [Microsoft. Identity. Client. Extensions. MSAL](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . Você pode incluí-lo em seus aplicativos do seguinte pacote NuGet: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serialização de cache de token duplo (cache unificado MSAL e ADAL v3)

Se você quiser implementar a serialização de cache de token com o formato de cache unificado (comum a ADAL.NET 4. x, MSAL.NET 2. x e outros MSALs da mesma geração ou mais antigas, na mesma plataforma), dê uma olhada no seguinte código:

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

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Cache de token para um aplicativo Web (aplicativo cliente confidencial)

Em aplicativos Web ou APIs Web, o cache pode aproveitar a sessão, um cache Redis ou um banco de dados.

Um ponto importante a ser lembrado é que, para aplicativos Web e APIs Web, deve haver um cache de token por usuário (por conta). Você precisa serializar o cache de token para cada conta.

Exemplos de como usar caches de token para aplicativos Web e APIs Web estão disponíveis no [tutorial ASP.NET Core aplicativo Web](https://ms-identity-aspnetcore-webapp-tutorial) no [cache de token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)da fase 2-2. Para implementações, observe a seguinte pasta [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) na biblioteca [Microsoft-Authentication-Extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (na pasta [Microsoft. Identity. Client. Extensions. Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) . 

## <a name="next-steps"></a>Passos Seguintes
Os exemplos a seguir ilustram a serialização de cache de token.

| Exemplo | Plataforma | Descrição|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Área de trabalho (WPF) | Aplicativo do Windows desktop .NET (WPF) chamando a API do Microsoft Graph. ![Topologia](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Área de trabalho (console) | Conjunto de soluções do Visual Studio que ilustram a migração de aplicativos do Azure AD v 1.0 (usando o ADAL.NET) para aplicativos do Azure AD v 2.0, também chamados de aplicativos convergidos (usando o MSAL.NET), em particular [migração de cache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md) de tokens|
