---
title: Usando navegadores web (MSAL.NET) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre considerações específicas ao utilizar o Xamarin Android com a Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4121d4b9ac73ed18da7dce0e397fe919589ac6f0
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478765"
---
# <a name="using-web-browsers-msalnet"></a>Usando navegadores web (MSAL.NET)

Os navegadores web são necessários para a autenticação interativa. Por padrão, MSAL.NET suporta o [navegador web](#system-web-browser-on-xamarinios-xamarinandroid) do sistema em Xamarin.iOS e Xamarin.Android. Mas [também pode ativar o navegador Web incorporado](#enable-embedded-webviews-on-ios-and-android) dependendo dos seus requisitos (UX, necessidade de um único sign-on (SSO), segurança) em aplicações [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e [Xamarin.Android.](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) E pode até [escolher de forma dinâmica](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) qual o navegador web a utilizar com base na presença do Chrome ou de um navegador que suporte separadores personalizados do Chrome no Android. MSAL.NET suporta apenas o navegador do sistema em aplicações de ambiente de trabalho .NET Core.

## <a name="web-browsers-in-msalnet"></a>Navegadores web em MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>A interação acontece num navegador web

É importante entender que ao adquirir um símbolo interativamente, o conteúdo da caixa de diálogo não é fornecido pela biblioteca, mas pelo STS (Serviço de Token de Segurança). O ponto final de autenticação envia de volta alguns HTML e JavaScript que controlam a interação, que é renderizada num navegador web ou controlo web. Permitir que o STS lide com a interação HTML tem muitas vantagens:

- A palavra-passe (se foi dactilografada) nunca é armazenada pela aplicação, nem pela biblioteca de autenticação.
- Permite redirecionamentos para outros fornecedores de identidade (por exemplo, login com uma conta de escola de trabalho ou uma conta pessoal com a MSAL, ou com uma conta social com Azure AD B2C).
- Permite ao STS controlar o Acesso Condicional, por exemplo, fazendo com que o utilizador faça [a autenticação multi-factor (MFA)](../authentication/concept-mfa-howitworks.md) durante a fase de autenticação (entrando num pin do Windows Hello ou sendo chamados no seu telemóvel, ou numa aplicação de autenticação no seu telemóvel). Nos casos em que a autenticação multi-factor necessária ainda não esteja configurada, o utilizador pode instalá-lo a tempo no mesmo diálogo.  O utilizador introduz o seu número de telemóvel e é guiado para instalar uma aplicação de autenticação e digitalizar uma etiqueta QR para adicionar a sua conta. Esta interação impulsionada pelo servidor é uma grande experiência!
- Permite ao utilizador alterar a sua palavra-passe neste mesmo diálogo quando a palavra-passe tiver expirado (fornecendo campos adicionais para a senha antiga e a nova senha).
- Permite a marcação do arrendatário, ou a aplicação (imagens) controlada pelo administrador/proprietário de aplicação Azure AD.
- Permite que os utilizadores consintam em permitir que a aplicação aceda a recursos/âmbitos em seu nome logo após a autenticação.

### <a name="embedded-vs-system-web-ui"></a>Incorporado vs System Web UI

MSAL.NET é uma biblioteca multi-quadro e tem um código específico-quadro para hospedar um navegador num controlo de UI (por exemplo, em .NET Classic usa WinForms, em Xamarin utiliza controlos móveis nativos, etc.). Este controlo chama-se `embedded` UI web. Em alternativa, MSAL.NET também é capaz de iniciar o navegador sistema OS.

Geralmente, recomenda-se que utilize o padrão da plataforma, e este é tipicamente o navegador do sistema. O navegador do sistema é melhor para lembrar os utilizadores que já iniciaram sessão. Para alterar este comportamento, use `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Síntese

| Arquitetura        | Incorporada | Sistema | Predefinição |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Yes | Sim, não só. | Incorporada |
| .NET Core     | No | Sim, não só. | Sistema |
| .NET Standard | No | Sim, não só. | Sistema |
| UWP | Yes | No | Incorporada |
| Xamarin.Android | Yes | Yes  | Sistema |
| Xamarin.iOS | Yes | Yes  | Sistema |
| Xamarin.Mac| Yes | No | Incorporada |

^ Requer http://localhost " redirecionar URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Navegador web do sistema em Xamarin.iOS, Xamarin.Android

Por padrão, MSAL.NET suporta o navegador web do sistema em Xamarin.iOS, Xamarin.Android e .NET Core. Para todas as plataformas que fornecem UI (isto é, não .NET Core), um diálogo é fornecido pela biblioteca incorporando um controlo do navegador Web. MSAL.NET também usa uma vista web incorporada para o .NET Desktop e WAB para a plataforma UWP. No entanto, aproveita por padrão o **navegador web** do sistema para aplicações Xamarin iOS e Xamarin Android. No iOS, até escolhe a vista web para utilizar dependendo da versão do Sistema Operativo (iOS12, iOS11 e anterior).

A utilização do navegador do sistema tem a vantagem significativa de partilhar o estado SSO com outras aplicações e com aplicações web sem precisar de um corretor (portal da empresa / Autenticador). O navegador do sistema foi utilizado, por padrão, em MSAL.NET para as plataformas Xamarin iOS e Xamarin Android porque, nestas plataformas, o navegador web do sistema ocupa todo o ecrã, e a experiência do utilizador é melhor. A vista web do sistema não distingue-se de um diálogo. No entanto, no iOS, o utilizador poderá ter de dar o seu consentimento para que o navegador reensete de volta a aplicação, o que pode ser irritante.

## <a name="system-browser-experience-on-net"></a>Experiência de navegador de sistema em .NET 

Em .NET Core, MSAL.NET iniciará o navegador do sistema como um processo separado. MSAL.NET não tem controlo sobre este navegador, mas assim que o utilizador termina a autenticação, a página web é redirecionada de forma a MSAL.NET possa intercetar o Uri.

Também pode configurar aplicações escritas para .NET Classic ou .NET 5 para utilizar este navegador especificando:

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET não é possível detetar se o utilizador navega ou simplesmente fecha o navegador. As aplicações que utilizam esta técnica são encorajadas a definir um tempo limite `CancellationToken` (via). Recomendamos um intervalo de pelo menos alguns minutos, para ter em conta os casos em que o utilizador é solicitado a alterar a palavra-passe ou a realizar a autenticação multi-factor.

### <a name="how-to-use-the-default-os-browser"></a>Como utilizar o navegador padrão de OS

MSAL.NET precisa de ouvir `http://localhost:port` e intercetar o código que a AAD envia quando o utilizador faz a autenticação (ver [código de autorização](v2-oauth2-auth-code-flow.md) para mais detalhes)

Para ativar o navegador do sistema:

1. Durante o registo da aplicação, configurar `http://localhost` como um uri redirecionado (não suportado atualmente por B2C)
2. Quando construir o seu PublicClientApplication, especifique este uri redirecionado:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Se configurar, `http://localhost` internamente MSAL.NET encontrarão uma porta aberta aleatória e a utilizarão.

### <a name="linux-and-mac"></a>Linux e MAC

No Linux, MSAL.NET abrirá o navegador OS padrão usando a ferramenta xdg-open. Para resolver problemas, executar a ferramenta a partir de um terminal, por exemplo, `xdg-open "https://www.bing.com"` . No Mac, o navegador é aberto `open <url>` invocando.

### <a name="customizing-the-experience"></a>Personalizar a experiência

> [!NOTE]
> A personalização está disponível em MSAL.NET 4.1.0 ou mais tarde.

MSAL.NET é capaz de responder com uma mensagem HTTP quando um token é recebido ou em caso de erro. Pode exibir uma mensagem HTML ou redirecionar para um url à sua escolha:

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

### <a name="opening-a-specific-browser-experimental"></a>Abertura de um navegador específico (Experimental)

Pode personalizar a forma como MSAL.NET abre o navegador. Por exemplo, em vez de utilizar qualquer navegador que seja o padrão, pode forçar a abertura de um navegador específico:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP não usa o Webview do Sistema

No entanto, para aplicações de desktop, o lançamento de um Webview do Sistema leva a uma experiência de utilizador subpar, como o utilizador vê o navegador, onde podem já ter outros separadores abertos. E quando a autenticação acontece, os utilizadores recebem uma página a pedir-lhes que fechem esta janela. Se o utilizador não prestar atenção, pode fechar todo o processo (incluindo outros separadores, que não estão relacionados com a autenticação). Aproveitar o navegador do sistema no ambiente de trabalho também exigiria abrir portas locais e ouvi-las, o que poderia exigir permissões avançadas para a aplicação. Você, como desenvolvedor, utilizador ou administrador, pode estar relutante com este requisito.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Ativar webviews incorporados em iOS e Android

Também pode ativar webviews incorporados em aplicações Xamarin.iOS e Xamarin.Android. Começando com MSAL.NET pré-visualização de 2.0.0, MSAL.NET também suporta usando a opção **webview incorporada.** Para ADAL.NET, o webview incorporado é a única opção suportada.

Como desenvolvedor que usa MSAL.NET direcionando xamarin, você pode optar por usar webviews incorporados ou navegadores de sistema. Esta é a sua escolha, dependendo da experiência do utilizador e das preocupações de segurança que pretende atingir.

Atualmente, MSAL.NET ainda não suporta os corretores Android e iOS. Portanto, para fornecer um único sinal de sso, o navegador do sistema ainda pode ser uma opção melhor. Os corretores de suporte com o navegador web incorporado estão no MSAL.NET atraso.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Diferenças entre webview incorporado e navegador de sistema
Existem algumas diferenças visuais entre webview incorporado e navegador de sistema em MSAL.NET.

**Insuse interativa com MSAL.NET utilizando o Webview Incorporado:**

![incorporada](media/msal-net-web-browsers/embedded-webview.png)

**Iniciar s-in interativo com MSAL.NET utilizando o Browser do Sistema:**

![Navegador do sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opções de Programador

Como desenvolvedor usando MSAL.NET, tem várias opções para exibir o diálogo interativo a partir de STS:

- **Navegador do sistema.** O navegador do sistema é definido por padrão na biblioteca. Se utilizar o Android, leia [os navegadores do sistema](msal-net-system-browser-android-considerations.md) para obter informações específicas sobre quais os navegadores suportados para autenticação. Ao utilizar o navegador do sistema no Android, recomendamos que o dispositivo tenha um navegador que suporte separadores personalizados do Chrome.  Caso contrário, a autenticação pode falhar.
- **Webview incorporado.** Para utilizar apenas webview incorporado em MSAL.NET, o `AcquireTokenInteractively` construtor de parâmetros contém um `WithUseEmbeddedWebView()` método.

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

Na sua aplicação iOS, `AppDelegate.cs` pode rubricar o `ParentWindow` to `null` . Não é usado no iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Escolha entre navegador web incorporado ou navegador de sistema em Xamarin.Android

Na sua aplicação Android, `MainActivity.cs` pode definir a atividade dos pais, para que o resultado da autenticação volte a ele:

```csharp
 App.ParentWindow = this;
```

Em `MainPage.xaml.cs` seguida, no:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Detetar a presença de separadores personalizados em Xamarin.Android

Se quiser utilizar o navegador web do sistema para ativar o SSO com as aplicações em execução no navegador, mas está preocupado com a experiência do utilizador para dispositivos Android não ter um navegador com suporte a separadores personalizados, tem a opção de decidir ligando para o `IsSystemWebViewAvailable()` `IPublicClientApplication` método. Este método retorna `true` se o PackageManager detetar separadores personalizados e `false` se não forem detetados no dispositivo.

Com base no valor devolvido por este método, e nos seus requisitos, pode tomar uma decisão:

- Pode devolver uma mensagem de erro personalizada ao utilizador. Por exemplo: "Por favor, instale o Chrome para continuar com a autenticação" -OR-
- Pode recorrer à opção webview incorporada e lançar a UI como uma webview incorporada.

O código abaixo mostra a opção webview incorporada:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core não suporta autenticação interativa com um navegador incorporado

Para .NET Core, a aquisição de tokens interativamente só está disponível através do navegador web do sistema, não com vistas web incorporadas. Na verdade, .NET Core ainda não fornece UI.
Se quiser personalizar a experiência de navegação com o navegador web do sistema, pode implementar a interface [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) e até fornecer o seu próprio navegador.
