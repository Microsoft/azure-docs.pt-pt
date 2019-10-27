---
title: Guia de início rápido do aplicativo Web para plataforma de identidade da Microsoft | Azure
description: Saiba como implementar a entrada da Microsoft em um aplicativo Web Java usando o OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java
ms.openlocfilehash: ff7563a9ebbbc996af56f94b8e23d349752ef9f4
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964107"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Início rápido: Adicionar entrada com a Microsoft a um aplicativo Web Java

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Neste guia de início rápido, você aprenderá a integrar um aplicativo Web Java com a plataforma de identidade da Microsoft. Seu aplicativo entrará em um usuário, obterá um token de acesso para chamar a API de Microsoft Graph e fará uma solicitação para a API de Microsoft Graph.

Quando você concluir este guia de início rápido, seu aplicativo aceitará entradas de contas pessoais da Microsoft (incluindo outlook.com, live.com e outros) e contas corporativas ou de estudante de qualquer empresa ou organização que usa Azure Active Directory.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-java-webapp/java-quickstart.svg)

## <a name="prerequisites"></a>Pré-requisitos

Para executar este exemplo, você precisará de:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 ou superior e [Maven](https://maven.apache.org/).
- Um locatário do Azure Active Directory (Azure AD). Para obter mais informações sobre como obter um locatário do Azure AD, consulte [como obter um locatário do Azure ad](https://azure.microsoft.com/documentation/articles/active-directory-howto-tenant/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Você tem duas opções para iniciar seu aplicativo de início rápido: Express (opção 1) ou manual (opção 2)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
> 
> 1. Vá para o [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para baixar e configurar automaticamente seu novo aplicativo.
> 
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
> 
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
> 
> Para registrar seu aplicativo e adicionar manualmente as informações de registro do aplicativo à sua solução, siga estas etapas:
> 
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 
> 1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/linkid=2083908) .
> 1. Selecione **novo registro**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>    - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `java-webapp`.
>    - Deixe **URI de redirecionamento** em branco por enquanto e selecione **registrar**.
> 1. Na página **visão geral** , localize a **ID do aplicativo (cliente)** e os valores de **ID do diretório (locatário)** do aplicativo. Copie esses valores para mais tarde.
> 1. Selecione a **autenticação** no menu e, em seguida, adicione as seguintes informações:
>    - Em **URIs de redirecionamento**, adicione `http://localhost:8080/msal4jsamples/secure/aad` e `https://localhost:8080/msal4jsamples/graph/me`.
>    - Selecione **Guardar**.
> 1. Selecione os **certificados & segredos** no menu e, na seção **segredos do cliente** , clique em **novo segredo do cliente**:
> 
>    - Digite uma descrição de chave (por exemplo, segredo do aplicativo).
>    - Selecione uma duração **de chave em 1 ano**.
>    - O valor da chave será exibido quando você selecionar **Adicionar**.
>    - Copie o valor da chave para mais tarde. Esse valor de chave não será exibido novamente, nem poderá ser recuperado por outros meios, portanto, registre-o assim que estiver visível na portal do Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Etapa 1: configurar seu aplicativo no portal do Azure
> 
> Para que o exemplo de código para este guia de início rápido funcione, você precisa:
> 
> 1. Adicione URLs de resposta como `http://localhost:8080/msal4jsamples/secure/aad` e `https://localhost:8080/msal4jsamples/graph/me`.
> 1. Crie um segredo do cliente.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.

#### <a name="step-2-download-the-code-sample"></a>Etapa 2: baixar o exemplo de código

 [Baixar o exemplo de código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

#### <a name="step-3-configure-the-code-sample"></a>Etapa 3: configurar o exemplo de código

 1. Extraia o arquivo zip para uma pasta local.
 1. Se você usar um ambiente de desenvolvimento integrado, abra o exemplo em seu IDE favorito (opcional).

 1. Abra o arquivo Application. Properties, que pode ser encontrado em src/main/resources/Folder e substitua o valor dos campos *AAD. clientId*, *AAD. Authority* e *AAD. SecretKey* pelos respectivos valores de **ID do aplicativo**, **ID do locatário** e **segredo do cliente** como o seguinte:

    ```file
    aad.clientId=Enter_the_Application_Id_here
    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Name_Here/
    aad.secretKey=Enter_the_Client_Secret_Here
    aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
    aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
    ```

> [!div renderon="docs"]
> Onde:
>
> - `Enter_the_Application_Id_here` - é o Id da Aplicação que registou.
> - `Enter_the_Client_Secret_Here`-é o **segredo do cliente** que você criou em **certificados & segredos** para o aplicativo registrado.
> - `Enter_the_Tenant_Name_Here`-é o valor da **ID do diretório (locatário)** do aplicativo que você registrou.

#### <a name="step-4-run-the-code-sample"></a>Etapa 4: executar o exemplo de código

Para executar o projeto, você pode:

Execute-o diretamente do seu IDE usando o servidor Spring boot inserido ou empacote-o em um arquivo WAR usando o [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) e implante-o em uma solução de contêiner J2EE, como o [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Executando do IDE

Se você estiver executando o aplicativo Web de um IDE, clique em executar e navegue até a home page do projeto. Para este exemplo, a URL de home page padrão é http://localhost:8080

1. Na página frontal, selecione o botão de **logon** para redirecionar para Azure Active Directory e solicite ao usuário suas credenciais.

1. Depois que o usuário é autenticado, ele é redirecionado para *http://localhost:8080/msal4jsamples/secure/aad* . Agora eles estão conectados e a página mostrará informações sobre a conta conectada. A interface do usuário de exemplo tem os seguintes botões:
    - *Sair*: assina o usuário atual do aplicativo e os redireciona para o Home Page.
    - *Mostrar informações do usuário*: adquire um token para Microsoft Graph e chama Microsoft Graph com uma solicitação que contém o token, que retorna informações básicas sobre o usuário conectado.

> [!IMPORTANT]
> Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto sem formatação aos arquivos do projeto, por motivos de segurança, é recomendável que você use um certificado em vez de um segredo do cliente antes de considerar o aplicativo como aplicativo de produção. Para obter mais informações sobre como usar um certificado, consulte [credenciais de certificado para autenticação de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Mais informações

### <a name="getting-msal"></a>Obtendo MSAL

MSAL4J é a biblioteca Java usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft.

Adicione o MSAL4J ao seu aplicativo usando o Maven ou o gradle para gerenciar suas dependências fazendo as seguintes alterações no arquivo pom. XML (Maven) ou Build. gradle (gradle) do aplicativo.

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.6.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Adicione uma referência ao MSAL4J adicionando o seguinte código à parte superior do arquivo em que você usará o MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Próximos Passos

Saiba mais sobre as permissões e o consentimento:

> [!div class="nextstepaction"]
> [Permissões e consentimento](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Para saber mais sobre o fluxo de autenticação para este cenário, consulte o fluxo do código de autorização do OAuth 2,0:

> [!div class="nextstepaction"]
> [Fluxo OAuth do código de autorização](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
