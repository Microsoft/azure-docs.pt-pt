---
title: Guia de início rápido do aplicativo Web do Python da plataforma Microsoft Identity | Azure
description: Saiba como implementar a entrada da Microsoft em um aplicativo Web Python usando o OAuth2
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: 254a1fd8644015de33855e13f78ab122d28f1e35
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817122"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Início rápido: Adicionar entrada com a Microsoft a um aplicativo Web Python

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Neste guia de início rápido, você aprenderá a integrar um aplicativo Web Python com a plataforma de identidade da Microsoft. Seu aplicativo entrará em um usuário, obterá um token de acesso para chamar a API de Microsoft Graph e fará uma solicitação para a API de Microsoft Graph.

Quando você tiver concluído o guia, seu aplicativo aceitará entradas de contas pessoais da Microsoft (incluindo outlook.com, live.com e outros) e contas corporativas ou de estudante de qualquer empresa ou organização que usa Azure Active Directory.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Pré-requisitos

Para executar este exemplo, será necessário:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) ou [Python 3 +](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https://pythonhosted.org/Flask-Session/), [solicitações](https://requests.kennethreitz.org//en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
>
> Você tem duas opções para iniciar seu aplicativo de início rápido: Express (opção 1) e manual (opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Vá para o [registros de aplicativo de portal do Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps).
> 1. Selecione **novo registro**.
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para baixar e configurar automaticamente seu novo aplicativo.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue até a página da plataforma Microsoft Identity para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecione **novo registro**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na secção **Nome**, introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por exemplo `python-webapp`.
>      - Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
>      - Na seção **URI de redirecionamento** , na lista suspensa, selecione a plataforma **da Web** e defina o valor como `http://localhost:5000/getAToken`.
>      - Selecione **Registar**. Na página **visão geral** do aplicativo, observe o valor da **ID do aplicativo (cliente)** para uso posterior.
> 1. No menu à esquerda, escolha **certificados & segredos** e clique em **novo segredo do cliente** na seção **segredos do cliente** :
>
>      - Digite uma descrição de chave (do segredo do aplicativo de instância).
>      - Selecione uma duração de chave de **em 1 ano**.
>      - Quando você clicar em **Adicionar**, o valor da chave será exibido.
>      - Copie o valor da chave. Precisará dele mais tarde.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal do Azure
>
> Para que o exemplo de código para este guia de início rápido funcione, você precisa:
>
> 1. Adicione uma URL de resposta como `http://localhost:5000/getAToken`.
> 1. Crie um segredo do cliente.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer esta alteração por mim]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com este atributo

#### <a name="step-2-download-your-project"></a>Passo 2: Transferir o projeto

[Baixar o exemplo de código](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Etapa 3: configurar o aplicativo

1. Extraia o ficheiro zip para uma pasta local próxima da pasta raiz, por exemplo, **C:\Azure-Samples**
1. Se você usar um ambiente de desenvolvimento integrado, abra o exemplo em seu IDE favorito (opcional).
1. Abra o arquivo **app_config. py** , que pode ser encontrado na pasta raiz e substitua pelo seguinte trecho de código:

```python
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
```

> [!div renderon="docs"]
> Onde:
>
> - `Enter_the_Application_Id_here` - é o Id da Aplicação que registou.
> - `Enter_the_Client_Secret_Here`-é o **segredo do cliente** que você criou em **certificados & segredos** para o aplicativo registrado.
> - `Enter_the_Tenant_Name_Here`-é o valor da **ID do diretório (locatário)** do aplicativo que você registrou.

#### <a name="step-4-run-the-code-sample"></a>Etapa 4: executar o exemplo de código

1. Você precisará instalar o MSAL Python library, Flask Framework, Flask-Sessions para gerenciamento de sessão do lado do servidor e solicitações usando Pip da seguinte maneira:

   ```Shell
   pip install -r requirements.txt
   ```

2. Executar app.py do Shell ou da linha de comando:

   ```Shell
   python app.py
   ```
   > [!IMPORTANT]
   > Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto sem formatação aos arquivos do projeto, por motivos de segurança, é recomendável que você use um certificado em vez de um segredo do cliente antes de considerar o aplicativo como aplicativo de produção. Para obter mais informações sobre como usar um certificado, consulte [estas instruções](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials).

   ## <a name="more-information"></a>Mais informações

   ### <a name="getting-msal"></a>Obtendo MSAL
   MSAL é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pela plataforma de identidade da Microsoft.
   Você pode adicionar MSAL Python ao seu aplicativo usando Pip.

   ```Shell
   pip install msal
   ```

   ### <a name="msal-initialization"></a>Inicialização da MSAL
   Você pode adicionar a referência ao MSAL Python adicionando o seguinte código à parte superior do arquivo em que você usará o MSAL:

   ```Python
   import msal
   ```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os aplicativos Web que conectam usuários e que chama APIs da Web:

> [!div class="nextstepaction"]
> [Cenário: aplicativos Web que conectam usuários](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
