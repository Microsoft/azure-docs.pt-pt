---
title: O Azure AD Xamarin, introdução | Documentos da Microsoft
description: Crie aplicações Xamarin que se integram com o Azure AD para início de sessão e chamam APIs do Azure AD protegida através de OAuth.
services: active-directory
documentationcenter: xamarin
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e25848359de91d67925f49901c6c170978ea592
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078708"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Início rápido: Criar uma aplicação Xamarin que integra o início de sessão no Microsoft

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Com o Xamarin, pode escrever aplicações móveis em c# que pode ser executado no iOS, Android e Windows (dispositivos móveis e PCs). Se estiver a criar uma aplicação com o Xamarin, o Azure Active Directory (Azure AD) facilita autenticar os utilizadores com as suas contas do Azure AD. A aplicação com a segurança também pode consumir qualquer web API que está protegido pelo Azure AD, como as APIs do Office 365 ou a API do Azure.

Para aplicações Xamarin que precisam de aceder a recursos protegidos, o Azure AD fornece o Active Directory Authentication Library (ADAL). É o único propósito da ADAL facilitar para aplicações obter os tokens de acesso. Para demonstrar como é fácil, este artigo mostra como criar aplicações de DirectorySearcher que:

* Execute no iOS, Android, ambiente de trabalho do Windows, Windows Phone e Windows Store.
* Utilize uma biblioteca de único de classes portáteis (PCL) para autenticar utilizadores e obter tokens para o Azure AD Graph API.
* Procure um diretório para os utilizadores com um determinado UPN.

## <a name="prerequisites"></a>Pré-requisitos

* Transfira o [projeto de estrutura](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip), ou Baixe o [exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Cada download é uma solução do Visual Studio 2013.
* Também precisa de um inquilino do Azure AD na qual pretende criar utilizadores e registar a aplicação. Se ainda não tiver um inquilino, [saiba como obter um](quickstart-create-new-tenant.md).

Quando estiver pronto, siga os procedimentos nas próximas quatro seções.

## <a name="step-1-set-up-your-xamarin-development-environment"></a>Passo 1: Configurar o ambiente de desenvolvimento do Xamarin

Uma vez que este tutorial inclui projetos para iOS, Android e Windows, terá do Visual Studio e Xamarin. Para criar o ambiente necessário, conclua o processo no [definir a cópia de segurança e instale o Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) no MSDN. As instruções incluem material que poderá consultar para obter mais informações sobre o Xamarin enquanto está aguardando para as instalações para serem concluídas.

Depois de concluir a configuração, abra a solução no Visual Studio. Lá, encontrará seis projetos: cinco projetos específicos à plataforma e uma PCL, DirectorySearcher.cs, que será partilhada em todas as plataformas.

## <a name="step-2-register-the-directorysearcher-app"></a>Passo 2: Registar a aplicação de DirectorySearcher

Para ativar a aplicação obter os tokens, tem primeiro de registá-lo no seu inquilino do Azure AD e conceder permissão para aceder à API do Azure AD Graph. Eis como:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na barra superior, clique na sua conta. Em seguida, no **Directory** , selecione o inquilino do Active Directory onde pretende registar a aplicação.
3. Clique em **Todos os serviços** no painel esquerdo e selecione **Azure Active Directory**.
4. Clique em **Registos de aplicações** e selecione **Adicionar**.
5. Para criar um novo **aplicação cliente nativa**, siga as instruções.
   * **Nome** descreve a aplicação aos utilizadores.
   * **URI de Redirecionamento** é uma combinação de esquema e cadeia de caracteres que o Azure AD utiliza para devolver respostas de tokens. Introduza um valor (por exemplo, `http://DirectorySearcher`).
6. Depois de concluir o registo, o Azure AD atribui a aplicação um ID de aplicação único. Copie o valor a partir da **aplicativo** separador, porque irá precisar dele mais tarde.
7. Sobre o **definições** página, selecione **permissões obrigatórias**e, em seguida, selecione **Add**.
8. Selecione **Microsoft Graph** como a API. Sob **permissões delegadas**, adicione o **Read Directory Data** permissão. 
   Esta ação permite que a aplicação consultar a Graph API para os utilizadores.

## <a name="step-3-install-and-configure-adal"></a>Passo 3: Instale e configure a ADAL

Agora que tem uma aplicação no Azure AD, pode instalar o ADAL e escrever seu código relacionadas com a identidade. Para ativar a ADAL comunicar com o Azure AD, dar a ele algumas informações sobre o registo de aplicações.

1. Adicione a ADAL para o projeto de DirectorySearcher utilizando a consola do Gestor de pacotes.

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS
    `

    `
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal
    `

    Tenha em atenção que as duas referências de biblioteca são adicionadas para cada projeto: a parte PCL da ADAL e uma parte específica da plataforma.
2. No projeto DirectorySearcherLib, abra DirectorySearcher.cs.
3. Substitua os valores de membro de classe com os valores que introduziu no portal do Azure. O código refere-se a esses valores sempre que utilizar a ADAL.

   * O *inquilino* é o domínio de inquilino do Azure AD (por exemplo, contoso.onmicrosoft.com).
   * O *clientId* é o ID de cliente da aplicação, que copiou do portal.
   * O *returnUri* é o URI que introduziu no portal de redirecionamento (por exemplo, `http://DirectorySearcher`).

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Passo 4: Utilizar a ADAL para obter os tokens do Azure AD

Quase todos os de lógica de autenticação da aplicação está na `DirectorySearcher.SearchByAlias(...)`. Tudo o que é necessário nos projetos específicos à plataforma é passar um parâmetro contextual para o `DirectorySearcher` PCL.

1. Abra DirectorySearcher.cs e, em seguida, adicione um novo parâmetro para o `SearchByAlias(...)` método. `IPlatformParameters` é o parâmetro contextual que encapsula os objetos específicos da plataforma que tem de efetuar a autenticação ADAL.

    ```csharp
    public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
    {
    ```

2. Inicializar `AuthenticationContext`, que é a classe principal da ADAL. Esta ação passa ADAL as coordenadas necessita para comunicar com o Azure AD.
3. Chamar `AcquireTokenAsync(...)`, que aceita o `IPlatformParameters` de objeto e invoca o fluxo de autenticação que é necessário para devolver um token para a aplicação.

    ```csharp
    ...
        AuthenticationResult authResult = null;
        try
        {
            AuthenticationContext authContext = new AuthenticationContext(authority);
            authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
        }
        catch (Exception ee)
        {
            results.Add(new User { error = ee.Message });
            return results;
        }
    ...
    ```

    `AcquireTokenAsync(...)` primeiro tenta devolver um token para o recurso pedido (a Graph API neste caso) sem pedir aos utilizadores para introduzir as respetivas credenciais (por meio de colocação em cache ou atualizar os tokens antigos). Conforme necessário, ele mostra os utilizadores a página de início de sessão do Azure AD antes de adquirir o token pedido.
4. Anexar o token de acesso para o pedido da Graph API no **autorização** cabeçalho:

    ```csharp
    ...
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
    ...
    ```

Isso é tudo para o `DirectorySearcher` PCL e a aplicação do relacionadas com a identidade código. Tudo o que resta é chamar o `SearchByAlias(...)` método em modos de exibição de cada plataforma e, quando necessário, para adicionar código para lidar corretamente com o ciclo de vida da interface do Usuário.

### <a name="android"></a>Android
1. No MainActivity.cs, adicione uma chamada para `SearchByAlias(...)` manipulador de clique no botão:

    ```csharp
    List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
    ```
2. Substituir o `OnActivityResult` método de ciclo de vida para encaminhar qualquer autenticação redireciona para o método apropriado. ADAL fornece um método auxiliar para isso no Android:

    ```csharp
    ...
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }
    ...
    ```

### <a name="windows-desktop"></a>Ambiente de trabalho do Windows

No MainWindow.xaml.cs, fazer uma chamada para `SearchByAlias(...)` passando um `WindowInteropHelper` na área de trabalho `PlatformParameters` objeto:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

#### <a name="ios"></a>iOS
No DirSearchClient_iOSViewController.cs, o iOS `PlatformParameters` objeto pega uma referência para o controlador de vista:

```csharp
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

### <a name="windows-universal"></a>Windows Universal
No Windows Universal, abra MainPage.xaml.cs e, em seguida, implementar o `Search` método. Este método utiliza um método auxiliar num projeto partilhado para atualizar a interface do Usuário conforme necessário.

```csharp
...
List<User> results = await DirectorySearcherLib.DirectorySearcher.SearchByAlias(SearchTermText.Text, new PlatformParameters(PromptBehavior.Auto, false));
...
```

Agora tem uma aplicação Xamarin que pode autenticar utilizadores e chamar com segurança as APIs web ao utilizar o OAuth 2.0 em cinco diferentes plataformas.

## <a name="step-5-populate-your-tenant"></a>Passo 5: Preencher o seu inquilino 

Se ainda não tiver já preenchida seu inquilino com os utilizadores, agora é o tempo para fazer isso.

1. Executar a aplicação de DirectorySearcher e, em seguida, inicie sessão com um dos utilizadores.
2. Procure outros utilizadores com base no respetivo UPN.

## <a name="next-steps"></a>Passos Seguintes

ADAL torna mais fácil incorporar funcionalidades de identidade comum para a aplicação. Ele cuida de todo o trabalho sujo para, como a gestão de cache, suporte de protocolo de OAuth, apresentando ao usuário um início de sessão da interface do Usuário, e atualizar a expirou tokens. Precisa saber apenas uma única chamada à API, `authContext.AcquireToken*(…)`.

* Transfira o [exemplo concluído](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip) (sem os valores de configuração).
* Saiba como [proteger uma API com o Azure AD de Web do .NET](quickstart-v1-dotnet-webapi.md).
