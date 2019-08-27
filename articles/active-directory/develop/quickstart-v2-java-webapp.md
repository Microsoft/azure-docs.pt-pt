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
ms.date: 08/11/2019
ms.author: sagonzal
ms.custom: aaddev
ms.openlocfilehash: de2a59f878e0c0258b0619895d8f4c8bfd0670a8
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036431"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Início rápido: Adicionar entrada com a Microsoft a um aplicativo Web Java

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Neste guia de início rápido, você aprenderá a integrar um aplicativo Web Java com a plataforma de identidade da Microsoft. Seu aplicativo entrará em um usuário, obterá um token de acesso para chamar a API de Microsoft Graph e fará uma solicitação para a API de Microsoft Graph. 

Quando você tiver concluído o guia, seu aplicativo aceitará entradas de contas pessoais da Microsoft (incluindo outlook.com, live.com e outros) e contas corporativas ou de estudante de qualquer empresa ou organização que usa Azure Active Directory.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-java-webapp/java-quickstart.svg)

> ## <a name="prerequisites"></a>Pré-requisitos
> Para executar este exemplo, você precisará de: 
> - Uma conexão com a Internet.
> - Uma instalação funcional do Java e do Maven.
> - Um locatário do Azure Active Directory (Azure AD). Para obter mais informações sobre como obter um locatário do Azure AD, consulte [como obter um locatário do Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
> Tem duas opções para iniciar a aplicação de início rápido:
> * Express [Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código.](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Manual: [Opção 2: Registre e configure manualmente seu aplicativo e o exemplo de código.](#option-2-register-and-manually-configure-your-application-and-code-sample)
> 
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registre e configure automaticamente seu aplicativo e, em seguida, baixe seu exemplo de código
>
> 1. Vá para o [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para baixar e configurar automaticamente seu novo aplicativo.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente seu aplicativo e exemplo de código
> 
>
> #### <a name="step-1-download-the-code-sample"></a>Passo 1: Baixar o exemplo de código
> 
> - [Baixar o exemplo de código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
>
> #### <a name="step-2-open-applicationproperties"></a>Passo 2: Abrir Application. Properties
>
> 1. Extraia o arquivo zip para uma pasta local.
> 1. Adicional Se você usar um ambiente de desenvolvimento integrado, abra o exemplo em seu IDE favorito.
> 1. Abra o arquivo *Application. Properties* . Você inserirá valores para `aad.clientId`, `aad.authority`e `aad.secretKey` quando registrar seu aplicativo na próxima etapa.


> #### <a name="step-3-register-your-application"></a>Passo 3: Registar a sua aplicação
> Para registrar seu aplicativo e adicionar manualmente as informações de registro do aplicativo à sua solução, siga estas etapas:
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecione **novo registro**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>    - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `java-webapp`.
>    - Deixe **URI** de redirecionamento em branco por enquanto e selecione **registrar**.
> 1. Localize o valor da **ID do aplicativo (cliente)** do aplicativo. Atualize o valor de `Enter_the_Application_Id_here` no arquivo *Application. Properties* .
> 1. Localize o valor da **ID do diretório (locatário)** do aplicativo. Atualize o valor de `Enter_the_Tenant_Info_Here` no arquivo *Application. Properties* . 
> 1. Selecione o menu **autenticação** e, em seguida, adicione as seguintes informações:
>    - Em **URIs**de redirecionamento `https://localhost:8080/msal4jsamples/graph/users`, adicione `http://localhost:8080/msal4jsamples/secure/aad` e.
>    - Selecione **Guardar**.
> 1. No menu à esquerda, escolha **certificados & segredos** e clique em **novo segredo do cliente** na seção **segredos do cliente** :
>     
>    - Digite uma descrição de chave (do segredo do aplicativo de instância).
>    - Selecione uma duração de chave de **em 1 ano**.
>    - Quando você clicar em **Adicionar**, o valor da chave será exibido. 
>    - Copie o valor da chave. Abra o arquivo *Application. Properties* que você baixou anteriormente e atualize o valor `Enter_the_Client_Secret_Here` de com o valor da chave. 
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passo 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código para este guia de início rápido funcione, você precisa:
> 1. Adicione URLs de resposta `http://localhost:8080/msal4jsamples/secure/aad` como `https://localhost:8080/msal4jsamples/graph/users`e.
> 1. Crie um segredo do cliente.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com estes atributos.
> 
> #### <a name="step-2-download-the-code-sample"></a>Passo 2: Baixar o exemplo de código
> 
> - [Baixar o exemplo de código](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> #### <a name="step-3-configure-the-code-sample"></a>Passo 3: Configurar o exemplo de código 
> 
> 1. Extraia o arquivo zip para uma pasta local.
> 1. Se você usar um ambiente de desenvolvimento integrado, abra o exemplo em seu IDE favorito (opcional).
> 1. Abra o arquivo **Application. Properties** , que pode ser encontrado em *src/main/resources/* .
> 1. Substituir Propriedades do aplicativo.
>   1. Localize `aad.clientId` e atualize o valor de `Enter_the_Application_Id_here` com o valor de **ID do aplicativo (cliente)** do aplicativo registrado. 
>   1. Localize `aad.authority` e atualize o valor de `Enter_the_Tenant_Name_Here` com o valor da **ID do diretório (locatário)** do aplicativo registrado.
>   1. Localize `aad.secretKey` e atualize o valor de `Enter_the_Client_Secret_Here` com o **segredo do cliente** que você criou em **certificados & segredos** para o aplicativo registrado.

#### <a name="step-4-run-the-code-sample"></a>Passo 4: Executar o exemplo de código
1. Execute o exemplo de código e abra um navegador e navegue até *http://localhost:8080* .
1. A página inicial contém um botão de **entrada** . Clique no botão **entrar** para redirecionar para Azure Active Directory. As credenciais do usuário serão solicitadas.  
1. Após a autenticação bem-sucedida no Azure Active Directory, eles serão redirecionados para *http://localhost:8080/msal4jsamples/secure/aad* . Eles são oficialmente conectados ao aplicativo e a página deve mostrar informações para a conta conectada. Ele também conterá botões para: 
    - *Sair*: Desconectará o usuário atual do aplicativo e os redirecionará para o home page.
    - *Mostrar usuários*: Adquirirá um token para o Microsoft Graph, em seguida, chamará o Microsoft Graph com o token anexado à solicitação para obter todos os usuários no locatário.


## <a name="more-information"></a>Mais informações

### <a name="getting-msal"></a>Obtendo MSAL
MSAL4J é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft. Você pode adicionar o MSAL4J ao seu aplicativo usando o Maven ou o gradle para gerenciar suas dependências fazendo as seguintes alterações no arquivo pom. xml ou Build. gradle em seu aplicativo. 

```
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>0.5.0-preview</version>
</dependency>
```

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '0.5.0-preview'
```


### <a name="msal-initialization"></a>Inicialização do MSAL
Você pode adicionar a referência ao MSAL4J adicionando o seguinte código à parte superior do arquivo em que você usará o MSAL4J: 

```
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
