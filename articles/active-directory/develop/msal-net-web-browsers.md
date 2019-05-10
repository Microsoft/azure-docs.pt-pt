---
title: Web browsers na biblioteca de autenticação da Microsoft para .NET | Azure
description: Saiba mais sobre as considerações específicas ao utilizar o Xamarin Android com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4b4c4cd4dbab10a9d4796a8393cc7f479b90cc4
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406774"
---
# <a name="using-web-browsers-in-msalnet"></a>Com navegadores da web no MSAL.NET
Navegadores da Web são necessários para a autenticação interativa. Por predefinição, MSAL.NET suporta o [navegador da web de sistema](#system-web-browser-on-xamarinios-and-xamarinandroid) no xamarin. IOS e [xamarin. Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser). Mas [também pode ativar o navegador da Web incorporado](#enable-embedded-webviews) dependendo das suas necessidades (UX, necessidade de início de sessão único (SSO), segurança), no [xamarin. IOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e [xamarin. Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) aplicações. E pode até mesmo [escolha dinamicamente](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) qual navegador da web para utilizar com base na presença de Chrome ou um browser que suporte guias personalizadas do Chrome no Android.

## <a name="web-browsers-in-msalnet"></a>Navegadores da Web em MSAL.NET

É importante compreender que quando adquirir um token de forma interativa, o conteúdo da caixa de diálogo não foi fornecido pela biblioteca, mas pelo STS (serviço de Token de segurança). O ponto final de autenticação retorna o HTML e JavaScript que controla a interação do utilizador, que é processada num navegador da web ou o controle da web. Permitir que o STS tratar a interação de HTML tem muitas vantagens:

- A palavra-passe (se um foi digitado) nunca é armazenada pelo aplicativo, nem a biblioteca de autenticação.
- Permite que os redirecionamentos de outros fornecedores de identidade (por exemplo início de sessão-in com uma conta da instituição de ensino de trabalho ou uma conta pessoal MSAL ou com uma conta de redes sociais com o Azure AD B2C).
- Permite que o STS controlar o acesso condicional, por exemplo, fazendo com que o usuário faça vários-factor authentication (MFA) durante a fase de autenticação (introduzir um pin do Windows Hello ou a ser chamado no seu telefone ou numa aplicação de autenticação no seu telemóvel). Em casos em que a autenticação multifator necessária não está configurada-lo ainda, o utilizador pode configurá-lo just-in-time na mesma caixa de diálogo.  O usuário digita o respetivo número de telefone celular e é orientado para instalar uma aplicação de autenticação e analisar uma marca de QR para adicionar a conta. Este servidor controlados por interação é uma excelente experiência!
- Permite ao utilizador alterar a palavra-passe nesta caixa de diálogo mesmo quando a palavra-passe expirou (fornecendo campos adicionais para a palavra-passe antiga e a nova palavra-passe).
- Permite a imagem corporativa do inquilino ou o aplicativo (imagens) controladas pelo administrador de inquilino do Azure AD / proprietário da aplicação.
- Permite que os utilizadores dar consentimento para permitir que a aplicação aceder aos recursos / âmbitos no respetivo nome, apenas após a autenticação.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Browser de sistema no xamarin. IOS e xamarin. Android

Por predefinição, o MSAL.NET suporta o navegador da web de sistema no xamarin. IOS e xamarin. Android. Para todas as plataformas que fornecem a interface do Usuário (ou seja, não .NET Core), uma caixa de diálogo é fornecida pela biblioteca incorporar um controle de navegador da Web. MSAL.NET também utiliza uma vista web incorporadas para a área de trabalho do .NET e WAB para a plataforma UWP. No entanto, ela aproveita por predefinição o **navegador da web de sistema** para Xamarin iOS e aplicações Xamarin Android. No iOS, até mesmo escolhe a vista web a utilizar consoante a versão do sistema operativo (iOS12, iOS11 e versões anteriores).

Usando o navegador do sistema tem a vantagem significativa de partilhar o estado SSO com outros aplicativos e com aplicativos web sem a necessidade de um mediador (portal da empresa / Authenticator). O browser de sistema foi utilizado, por predefinição, no MSAL.NET para as plataformas de Xamarin Android e Xamarin iOS porque, nessas plataformas, o navegador da web de sistema ocupa a tela toda e a experiência do usuário é melhor. A vista de web do sistema não é distintas de uma caixa de diálogo. No iOS, no entanto, o utilizador poderá ter de dar consentimento para o browser para o aplicativo, que pode ser um pouco de retorno de chamada.

### <a name="uwp-does-not-use-the-system-webview"></a>UWP não utiliza o System Webview

Aplicativos de desktop, no entanto, iniciar um System Webview leva a uma experiência de utilizador abaixo da média comparados, conforme o usuário vê o browser, onde eles poderão já ter outras guias abertos. E quando a autenticação tiver ocorrido, os utilizadores obtém uma página onde-los para fechar esta janela. Se o utilizador não tenha despesas com atenção, pode fechar o todo o processo (incluindo outros guias, que não estão relacionadas com a autenticação). Tirar partido do navegador de sistema no ambiente de trabalho também requer a abertura de portas locais e à escuta nos mesmos, que pode exigir permissões avançadas para a aplicação. , Como um desenvolvedor, um utilizador ou administrador, poderá ser relutante sobre este requisito.

## <a name="enable-embedded-webviews"></a>Ativar incorporadas 
Também pode ativar incorporadas nas aplicações xamarin. IOS e xamarin. Android. Começando com MSAL.NET 2.0.0-preview, MSAL.NET também suporta a utilização a **embedded** opção de webview. Para ADAL.NET, embedded webview é a única opção suportada.

Como desenvolvedor com MSAL.NET direcionamento Xamarin, pode optar por utilizar incorporadas ou browsers de sistema. Esta é a opção consoante as preocupações de segurança e a experiência de utilizador de destino.

Atualmente, MSAL.NET ainda não suporta os mediadores de Android e iOS. Portanto, se tiver de fornecer início de sessão único (SSO), o navegador de sistema ainda poderá ser uma opção melhor. Suporte a mediadores com o browser incorporado é sobre o registo de segurança MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Diferenças entre o embedded webview e o navegador de sistema 
Existem algumas diferenças visual entre embedded webview e o navegador de sistema na MSAL.NET.

**Interativo início de sessão com MSAL.NET usando o Webview Embedded:**

![Incorporado](media/msal-net-web-browsers/embedded-webview.png)

**Interativo início de sessão com MSAL.NET usando o navegador de sistema:**

![Browser de sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opções de programador

Como desenvolvedor com MSAL.NET, tem várias opções para exibir a caixa de diálogo interativa do STS:

- **Browser de sistema.** O navegador de sistema é definido por predefinição na biblioteca. Se utilizar o Android, leia [navegadores de sistema](msal-net-system-browser-android-considerations.md) para obter informações específicas sobre os quais os browsers são suportados para autenticação. Ao usar o navegador de sistema no Android, recomendamos que o dispositivo tem um browser que suporte guias personalizadas do Chrome.  Caso contrário, poderá falhar a autenticação.
- **Webview incorporado.** Para utilizar apenas a incorporado webview no MSAL.NET, o `AcquireTokenInteractively` parâmetros builder contém um `WithUseEmbeddedWebView()` método.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Escolhendo entre o navegador da web incorporado ou browser de sistema no xamarin. IOS

Na sua aplicação iOS, no `AppDelegate.cs` pode inicializar o `ParentWindow` para `null`. Não é utilizado no iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Escolhendo entre o navegador da web incorporado ou browser de sistema no xamarin. Android

Na sua aplicação Android, no `MainActivity.cs` pode definir a atividade principal, para que os resultados de autenticação recebe de volta para o mesmo:

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

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Detetar a presença de guias personalizadas no xamarin. Android

Se quiser usar o navegador da web de sistema para ativar o SSO com as aplicações em execução no navegador, mas está preocupados com a experiência do usuário para dispositivos Android não ter um navegador com suporte a guia personalizada, tem a opção de decidir chamando o `IsSystemWebViewAvailable()` método na < c 2 > `IPublicClientApplication` . Este método devolve `true` se o PackageManager detetar guias personalizadas e `false` se eles não são detetados no dispositivo.

Com base no valor retornado por esse método e os seus requisitos, pode tomar uma decisão:

- Pode retornar uma mensagem de erro personalizada para o usuário. Por exemplo: "Instalar o Chrome para continuar com a autenticação de" - OR-
- Pode reverter para a opção de embedded webview e iniciar a interface do Usuário como uma webview incorporado.

O código abaixo mostra a opção de embedded webview:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

## <a name="net-core-does-not-support-interactive-authentication-out-of-the-box"></a>.NET core não suporta a autenticação interativa prontos a utilizar

Para .NET Core, aquisição de tokens de forma interativa não está disponível. Na verdade, o .NET Core não fornece ainda da interface do Usuário. Se quiser fornecer o início de sessão interativo para uma aplicação .NET Core, poderá deixar a aplicação apresentar ao utilizador um código e um URL de destino para iniciar sessão interativamente (consulte [fluxo de código de dispositivo](msal-authentication-flows.md#device-code)).

Em alternativa, pode implementar o [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) de interface e fornecer seu próprio navegador