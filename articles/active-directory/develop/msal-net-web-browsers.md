---
title: Utilização de navegadores web (MSAL.NET) [ Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao utilizar o Xamarin Android com a Microsoft Authentication Library para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262740"
---
# <a name="using-web-browsers-msalnet"></a>Utilização de navegadores web (MSAL.NET)

Os navegadores da Web são necessários para autenticação interativa. Por padrão, MSAL.NET suporta o [navegador web](#system-web-browser-on-xamarinios-xamarinandroid) do sistema em Xamarin.iOS e Xamarin.Android. Mas [também pode ativar o navegador Web incorporado](#enable-embedded-webviews-on-ios-and-android) dependendo dos seus requisitos (UX, necessidade de inscrição única (SSO), segurança) nas aplicações [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e [Xamarin.Android.](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) E pode até [escolher de forma dinâmica](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) qual o navegador web a utilizar com base na presença do Chrome ou de um navegador que suporta separadores personalizados do Chrome no Android. MSAL.NET suporta apenas o navegador do sistema em aplicações de ambiente de trabalho .NET Core.

## <a name="web-browsers-in-msalnet"></a>Navegadores web em MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>A interação acontece num navegador web

É importante entender que ao adquirir um símbolo interativamente, o conteúdo da caixa de diálogo não é fornecido pela biblioteca, mas pelo STS (Security Token Service). O ponto final de autenticação envia de volta algum HTML e JavaScript que controla a interação, que é renderizada num navegador web ou controlo web. Permitir que o STS lide com a interação HTML tem muitas vantagens:

- A palavra-passe (se uma foi dada) nunca é armazenada pela aplicação, nem pela biblioteca de autenticação.
- Permite reorientações para outros fornecedores de identidade (por exemplo, iniciar sessão com uma conta de escola de trabalho ou uma conta pessoal com MSAL, ou com uma conta social com o Azure AD B2C).
- Permite que o STS controle o Acesso Condicional, por exemplo, ao ter o utilizador a fazer a autenticação de múltiplos fatores (MFA) durante a fase de autenticação (introduzir um pin o Windows Hello, ou ser chamado para o telemóvel, ou numa aplicação de autenticação no seu telemóvel). Nos casos em que a autenticação de vários fatores necessário ainda não esteja configurada, o utilizador pode configurar apenas a tempo no mesmo diálogo.  O utilizador introduz o seu número de telemóvel e é orientado para instalar uma aplicação de autenticação e digitalizar uma etiqueta QR para adicionar a sua conta. Esta interação orientada pelo servidor é uma grande experiência!
- Permite ao utilizador alterar a sua palavra-passe neste mesmo diálogo quando a palavra-passe tiver expirado (fornecendo campos adicionais para a palavra-passe antiga e a nova palavra-passe).
- Permite a marcação do inquilino, ou a aplicação (imagens) controlada pelo inquilino/proprietário da admissão da AD Azure.
- Permite que os utilizadores consintam em permitir que a aplicação aceda aos recursos/âmbitos de acesso em seu nome logo após a autenticação.

### <a name="embedded-vs-system-web-ui"></a>Incorporado vs System Web UI

MSAL.NET é uma biblioteca multi-quadro e tem código específico para hospedar um navegador num controlo ui (por exemplo, em .Net Classic usa WinForms, em Xamarin usa controlos móveis nativos, etc.). Este controlo `embedded` chama-se UI web. Em alternativa, MSAL.NET também é capaz de iniciar o navegador SISTEMA OS.

Geralmente, recomenda-se que utilize o padrão da plataforma, e este é tipicamente o navegador do sistema. O navegador do sistema é melhor a lembrar os utilizadores que já iniciaram sessão. Se precisa mudar este comportamento, use`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Num relance

| Arquitetura        | Embutido | Sistema | Predefinição |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Sim | Sim, | Embutido |
| .NET Core     | Não | Sim, | Sistema |
| .NET Standard | Não | Sim, | Sistema |
| UWP | Sim | Não | Embutido |
| Xamarin.Android | Sim | Sim  | Sistema |
| Xamarin.iOS | Sim | Sim  | Sistema |
| Xamarin.Mac| Sim | Não | Embutido |

^ Requerhttp://localhost" redirecionamento URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Navegador web do sistema em Xamarin.iOS, Xamarin.Android

Por padrão, MSAL.NET suporta o navegador web do sistema em Xamarin.iOS, Xamarin.Android e .NET Core. Para todas as plataformas que fornecem UI (isto é, não .NET Core), um diálogo é fornecido pela biblioteca incorporando um controlo de navegador Web. MSAL.NET também usa uma visão web incorporada para o .NET Desktop e WAB para a plataforma UWP. No entanto, aproveita por padrão o **navegador web** do sistema para aplicações Xamarin iOS e Xamarin Android. No iOS, até escolhe a visão web para utilizar dependendo da versão do Sistema Operativo (iOS12, iOS11 e anterior).

A utilização do navegador do sistema tem a vantagem significativa de partilhar o estado SSO com outras aplicações e com aplicações web sem precisar de um corretor (portal da empresa/Autenticador). O navegador do sistema foi utilizado, por padrão, em MSAL.NET para as plataformas Xamarin iOS e Xamarin Android porque, nestas plataformas, o navegador web do sistema ocupa todo o ecrã, e a experiência do utilizador é melhor. A visão web do sistema não é distinguível de um diálogo. No iOS, no entanto, o utilizador poderá ter de dar consentimento para que o navegador volte a ligar para a aplicação, o que pode ser irritante.

## <a name="system-browser-experience-on-net-core"></a>Experiência do navegador do sistema em .NET Core

No .NET Core, MSAL.NET iniciará o navegador do sistema como um processo separado. MSAL.NET não tem controlo sobre este navegador, mas assim que o utilizador termina a autenticação, a página web é redirecionada de forma a MSAL.NET pode intercetar o Uri.

Também pode configurar aplicações escritas para .NET Classic para usar este navegador, especificando

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET não consegue detetar se o utilizador navega ou simplesmente fecha o navegador. As aplicações que utilizam esta técnica são `CancellationToken`encorajadas a definir um intervalo (via ). Recomendamos um tempo de tempo de pelo menos alguns minutos, para ter em conta os casos em que o utilizador é solicitado a alterar a palavra-passe ou a realizar a autenticação de vários fatores.

### <a name="how-to-use-the-default-os-browser"></a>Como utilizar o Navegador Os Padrão

MSAL.NET precisa de `http://localhost:port` ouvir e intercetar o código que a AAD envia quando o utilizador é feito autenticando (Ver [Código de Autorização](v2-oauth2-auth-code-flow.md) para detalhes)

Para ativar o navegador do sistema:

1. Durante o registo `http://localhost` da aplicação, configure como um uri redireccionador (não atualmente suportado por B2C)
2. Quando construir a sua PublicClientApplication, especifique este uri redirecionamento:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Se configurar, `http://localhost`MSAL.NET internamente encontrará uma porta aberta aleatória e a utilizará.

### <a name="linux-and-mac"></a>Linux e MAC

No Linux, MSAL.NET abrirá o navegador OS padrão utilizando a ferramenta xdg-open. Para resolver problemas, executar a ferramenta a partir de um terminal, por exemplo,`xdg-open "https://www.bing.com"`  
No Mac, o navegador é aberto invocando`open <url>`

### <a name="customizing-the-experience"></a>Personalizando a experiência

> [!NOTE]
> A personalização está disponível em MSAL.NET 4.1.0 ou mais tarde.

MSAL.NET é capaz de responder com uma mensagem HTTP quando um token é recebido ou em caso de erro. Pode apresentar uma mensagem HTML ou redirecionar para um url à sua escolha:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Abrir um navegador específico (Experimental)

Pode personalizar a forma como MSAL.NET abre o navegador. Por exemplo, em vez de usar qualquer navegador que seja o padrão, pode forçar a abertura de um navegador específico:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>A UWP não usa o Webview do Sistema

No entanto, para aplicações de desktop, o lançamento de um System Webview leva a uma experiência de utilizador subpar, uma vez que o utilizador vê o navegador, onde pode já ter outros separadores abertos. E quando a autenticação acontece, os utilizadores recebem uma página a pedir-lhes para fecharem esta janela. Se o utilizador não prestar atenção, pode encerrar todo o processo (incluindo outros separadores, que não estão relacionados com a autenticação). Aproveitar o navegador do sistema no ambiente de trabalho também exigiria a abertura de portas locais e ouvi-las, o que poderia exigir permissões avançadas para a aplicação. Você, como desenvolvedor, utilizador ou administrador, pode estar relutante com este requisito.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Ativar webviews incorporados em iOS e Android

Também pode ativar webviews incorporados em aplicações Xamarin.iOS e Xamarin.Android. Começando com MSAL.NET pré-visualização de 2.0.0, MSAL.NET também suporta a utilização da opção webview **incorporada.** Para ADAL.NET, webview incorporado é a única opção suportada.

Como desenvolvedor que usa MSAL.NET direcionando Xamarin, pode optar por utilizar webviews incorporados ou navegadores de sistema. Esta é a sua escolha, dependendo da experiência do utilizador e das preocupações de segurança que pretende atingir.

Atualmente, MSAL.NET ainda não suporta os corretores Android e iOS. Portanto, se precisar de fornecer um único sinal (SSO), o navegador do sistema ainda pode ser uma opção melhor. Os corretores de suporte com o navegador web incorporado estão no MSAL.NET atraso.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Diferenças entre webview incorporado e navegador de sistema
Existem algumas diferenças visuais entre webview incorporado e navegador de sistema em MSAL.NET.

**Iniciar sessão interativa com MSAL.NET utilizando o Webview incorporado:**

![incorporada](media/msal-net-web-browsers/embedded-webview.png)

**Iniciar sessão interativa com MSAL.NET utilizando o Browser do Sistema:**

![Navegador do sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opções de Desenvolvimento

Como desenvolvedor que usa MSAL.NET, tem várias opções para exibir o diálogo interativo a partir de STS:

- **Navegador do sistema.** O navegador do sistema é definido por padrão na biblioteca. Se utilizar o Android, leia [os navegadores](msal-net-system-browser-android-considerations.md) do sistema para obter informações específicas sobre quais os navegadores que são suportados para autenticação. Ao utilizar o navegador do sistema no Android, recomendamos que o dispositivo tenha um navegador que suporta separadores personalizados do Chrome.  Caso contrário, a autenticação pode falhar.
- **Webview embutido.** Para utilizar apenas webview `AcquireTokenInteractively` incorporado em MSAL.NET, o construtor de parâmetros contém um `WithUseEmbeddedWebView()` método.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Escolha entre navegador web incorporado ou navegador de sistema em Xamarin.iOS

Na sua aplicação `AppDelegate.cs` iOS, pode `ParentWindow` `null`inicializar o para . Não é usado no iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Escolha entre navegador web incorporado ou navegador de sistema em Xamarin.Android

Na sua aplicação `MainActivity.cs` Android, pode definir a atividade dos pais, para que o resultado da autenticação volte a fazê-lo:

```csharp
 App.ParentWindow = this;
```

Em seguida, no `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Detetar a presença de separadores personalizados em Xamarin.Android

Se pretende utilizar o navegador web do sistema para ativar o SSO com as aplicações em execução no navegador, mas está preocupado com `IsSystemWebViewAvailable()` a `IPublicClientApplication`experiência do utilizador para dispositivos Android que não têm um browser com suporte personalizado para tab, tem a opção de decidir através da chamada do método . Este método `true` retorna se o PackageManager `false` detetar separadores personalizados e se não forem detetados no dispositivo.

Com base no valor devolvido por este método, e nos seus requisitos, pode tomar uma decisão:

- Pode devolver uma mensagem de erro personalizada ao utilizador. Por exemplo: "Por favor, instale o Chrome para continuar com a autenticação" -OR -
- Você pode voltar à opção webview incorporada e lançar o UI como uma webview incorporada.

O código abaixo mostra a opção webview incorporada:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core não suporta autenticação interativa com um navegador incorporado

Para .NET Core, a aquisição de tokens interativamente só está disponível através do navegador web do sistema, e não com vistas web incorporadas. Na verdade, o .NET Core ainda não fornece UI.
Se pretender personalizar a experiência de navegação com o navegador web do sistema, pode implementar a interface [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) e até fornecer o seu próprio navegador.
