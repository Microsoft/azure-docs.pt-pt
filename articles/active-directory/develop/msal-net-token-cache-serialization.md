---
title: Serialização de cache simbólica (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a serialização e a serialização do cliente da cache simbólica utilizando a Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.custom: aaddev
ms.openlocfilehash: 1aa7de4290d0050b9d6b1c8b048f9e5a2836790f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82127987"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serialização de cache simbólica em MSAL.NET
Depois de adquirido um [símbolo,](msal-acquire-cache-tokens.md)é cached pela Microsoft Authentication Library (MSAL).  O código de aplicação deve tentar obter um símbolo da cache antes de adquirir um símbolo por outro método.  Este artigo discute a serialização padrão e personalizada da cache simbólica em MSAL.NET.

Este artigo é para MSAL.NET 3.x. Se estiver interessado em MSAL.NET 2.x, consulte a serialização da [cache token em MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Serialização padrão para plataformas móveis

Em MSAL.NET, uma cache de ficha sinuosa é fornecida por padrão. A serialização é fornecida por padrão para plataformas onde o armazenamento seguro está disponível para um utilizador como parte da plataforma. É o caso da Universal Windows Platform (UWP), Xamarin.iOS e Xamarin.Android.

> [!Note]
> Ao migrar um projeto Xamarin.Android de MSAL.NET 1.x para MSAL.NET 3.x, é melhor adicionar `android:allowBackup="false"` ao seu projeto para evitar que os antigos tokens em cache voltem quando as implementações do Estúdio Visual desencadearem um restauro do armazenamento local. Ver [Emissão #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Serialização personalizada para aplicações de desktop windows e aplicações web/APIs web

Lembre-se, a serialização personalizada não está disponível em plataformas móveis (UWP, Xamarin.iOS e Xamarin.Android). A MSAL já define um mecanismo de serialização seguro e performante para estas plataformas. No entanto, as aplicações .NET desktop e .NET Core têm arquiteturas variadas e a MSAL não pode implementar um mecanismo de serialização para fins gerais. Por exemplo, os web sites podem optar por armazenar fichas numa cache Redis ou aplicações de desktop armazenarem fichas num ficheiro encriptado. Então a serialização não é fornecida fora da caixa. Para ter uma aplicação de cache token persistente em .NET desktop ou .NET Core, você precisa personalizar a serialização.

As seguintes classes e interfaces são utilizadas na cacheização simbólica:

- `ITokenCache`, que define eventos para subscrever pedidos de serialização de cache simbólicos, bem como métodos para serializar ou desserializar a cache em vários formatos (ADAL v3.0, MSAL 2.x e MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback`é um callback passado para os eventos para que você possa lidar com a serialização. Serão chamados com argumentos `TokenCacheNotificationArgs`de tipo.
- `TokenCacheNotificationArgs`apenas fornece `ClientId` a aplicação e uma referência ao utilizador para o qual o símbolo está disponível.

  ![Diagrama de classe](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> MSAL.NET cria caches simbólicos para si `IToken` e fornece-lhe a `UserTokenCache` `AppTokenCache` cache quando chama de uma aplicação e propriedades. Não é suposto implementares a interface sozinho. A sua responsabilidade, quando implementa uma cacheização personalizada, é:
> - Reagir `BeforeAccess` e `AfterAccess` "eventos" (ou seus sabores Async). O `BeforeAccess` delegado é responsável por desserializar `AfterAccess` a cache, enquanto que o responsável pela serialização da cache.
> - Parte destes eventos armazenam ou carregam bolhas, que são transmitidas através do argumento do evento para qualquer armazenamento que você quiser.

As estratégias são diferentes dependendo se estiver a escrever uma serialização de cache simbólica para uma [aplicação de cliente público](msal-client-applications.md) (desktop), ou uma [aplicação confidencial do cliente](msal-client-applications.md)) (aplicação web/web API, app daemon).

### <a name="token-cache-for-a-public-client"></a>Cache de token para um cliente público 

Desde MSAL.NET v2.x tem várias opções para serializar a cache simbólica de um cliente público. Pode serializar a cache apenas para o formato MSAL.NET (a cache de formato unificado é comum em mSAL e nas plataformas).  Também pode apoiar [a](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) legacy token cache serialization de ADAL V3.

A personalização da serialização da cache simbólica para partilhar o estado de inscrição único entre ADAL.NET 3.x, ADAL.NET 5.x e MSAL.NET é explicada em parte da seguinte amostra: [active-directy-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> O formato de cache token de 1.1.4 pré-visualização MSAL.NET já não é suportado em MSAL 2.x. Se tiver aplicações que MSAL.NET 1.x, os seus utilizadores terão de voltar a inscrever-se. Alternadamente, a migração de ADAL 4.x (e 3.x) é suportada.

#### <a name="simple-token-cache-serialization-msal-only"></a>Simples serialização de cache de token (apenas MSAL)

Abaixo está um exemplo de uma implementação ingénua de serialização personalizada de um cache simbólico para aplicações de ambiente de trabalho. Aqui, o cache token do utilizador é um ficheiro na mesma pasta que a aplicação.

Depois de construir a aplicação, permite `TokenCacheHelper.EnableSerialization()` a serialização `UserTokenCache`ligando para o método e passando a aplicação .

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
$"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
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

Uma pré-visualização de um serializador baseado em ficheiros de cache de qualidade do produto para aplicações de clientes públicos (para aplicações de desktop que executam windows, Mac e Linux) está disponível na biblioteca [microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) open-source. Pode incluí-lo nas suas aplicações a partir do seguinte pacote de nuget: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serialização de cache de símbolo duplo (cache unificada MSAL e ADAL v3)

Se quiser implementar a serialização de cache token tanto com o formato de cache unificado (comum a ADAL.NET 4.x, MSAL.NET 2.x, e outros MSALs da mesma geração ou mais velhos, na mesma plataforma), dê uma olhada no seguinte código:

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

Desta vez, a classe de ajudante supor como:

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

Em aplicações web ou APIs web o cache poderia alavancar a sessão, uma cache Redis ou uma base de dados.

Nas aplicações web ou nas APIs web, mantenha uma cache simbólica por conta.  Para aplicações web, o cache token deve ser chave da conta ID.  Para apis web, a conta deve ser chaveada pelo hash do token usado para chamar a API. MSAL.NET fornece serialização de cache token personalizada em .NET Framework e .NET Core subplataformas. Os eventos são disparados quando a cache é acedida, as aplicações podem escolher se serializar ou desserializar a cache. Em aplicações confidenciais de clientes que lidam com utilizadores (aplicações web que assinam nos utilizadores e ligam para apis web, e APIs web chamando APIs web a jusante), pode haver muitos utilizadores e os utilizadores são processados em paralelo. Por razões de segurança e desempenho, a nossa recomendação é serializar uma cache por utilizador. Os eventos de serialização calculam uma chave de cache com base na identidade do utilizador processado e serialize/deserialie uma cache simbólica para esse utilizador.

Exemplos de como usar caches simbólicos para aplicações web e APIs web estão disponíveis no [tutorial de aplicação web ASP.NET Core](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/) na fase [2-2 Token Cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Para implementações, veja a pasta [TokenCacheProviders](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/Microsoft.Identity.Web/TokenCacheProviders) na biblioteca [microsoft-autenticação-extensões-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) (na pasta [Microsoft.Identity.Client.Extensions.Web.](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) 

## <a name="next-steps"></a>Passos seguintes
As seguintes amostras ilustram a serialização da cache simbólica.

| Sample | Plataforma | Descrição|
| ------ | -------- | ----------- |
|[active-directy-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Ambiente de trabalho (WPF) | Aplicação Windows Desktop .NET (WPF) chamada Microsoft Graph API. ![Topologia](media/msal-net-token-cache-serialization/topology.png)|
|[active-directy-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Ambiente de trabalho (Consola) | Conjunto de soluções de Estúdio Visual que ilustram a migração de aplicações Azure AD v1.0 (usando ADAL.NET) para aplicações Azure AD v2.0, também chamadas aplicações convergentes (usando MSAL.NET), em particular [Token Cache Migration](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
