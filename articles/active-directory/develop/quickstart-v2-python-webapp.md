---
title: Adicione o sessão com a Microsoft a uma aplicação web da plataforma de identidade Microsoft Python  Azure
description: Saiba como implementar o Microsoft Sign-In numa aplicação web python usando o OAuth2
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: b219b507ac14b2a66b23f93d9fb1035f56cb8164
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018691"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Quickstart: Adicione o sessão com a Microsoft a uma aplicação web Python

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Neste arranque rápido, aprenderá a integrar uma aplicação web Python com a plataforma de identidade microsoft. Seu aplicativo entrará em um usuário, obterá um token de acesso para chamar a API de Microsoft Graph e fará uma solicitação para a API de Microsoft Graph.

Quando você tiver concluído o guia, seu aplicativo aceitará entradas de contas pessoais da Microsoft (incluindo outlook.com, live.com e outros) e contas corporativas ou de estudante de qualquer empresa ou organização que usa Azure Active Directory.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>Pré-requisitos

Para executar este exemplo, será necessário:

- [Python 2.7 +](https://www.python.org/downloads/release/python-2713) ou [Python 3 +](https://www.python.org/downloads/release/python-364/)
- Pedido de [balão,](http://flask.pocoo.org/) [flask-session](https://pythonhosted.org/Flask-Session/) [](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
>
> Tem duas opções para iniciar a sua aplicação quickstart: express (Opção 1) e manual (Opção 2)
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
>      - Sob a secção **Redirecionamento URI,** na lista de drop-down, selecione a plataforma **Web** e, em seguida, detete o valor para `http://localhost:5000/getAToken`.
>      - Selecione **Registar**. Na página **visão geral** do aplicativo, observe o valor da **ID do aplicativo (cliente)** para uso posterior.
> 1. No menu da mão esquerda, escolha **Certificados e segredos** e clique em **novo segredo** do cliente na secção Segredos do **Cliente:**
>
>      - Digite uma descrição chave (de caso de segredo de aplicação).
>      - Selecione uma duração chave de **1 ano**.
>      - Quando clicar em **Adicionar,** o valor-chave será apresentado.
>      - Copie o valor da chave. Precisará dele mais tarde.
> 1. Selecione a seção **permissões de API**
>
>      - Clique no botão **Adicionar uma permissão** e, em seguida,
>      - Certifique-se de que o separador **APIs** da Microsoft está selecionado
>      - Na secção *APIs da Microsoft comumente utilizada,* clique no **Microsoft Graph**
>      - Na secção de **permissões delegadas,** certifique-se de que as permissões certas são verificadas: **User.ReadBasic.All**. Use a caixa de pesquisa, se necessário.
>      - Selecione o botão **adicionar permissões**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal do Azure
>
> Para que o exemplo de código para este guia de início rápido funcione, você precisa:
>
> 1. Adicione um URL de resposta como `http://localhost:5000/getAToken`.
> 1. Crie um segredo do cliente.
> 1. Adicione o utilizador da Microsoft Graph API.ReadBasic.Todas as permissões delegadas.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça essas alterações para mim]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com este atributo

#### <a name="step-2-download-your-project"></a>Passo 2: Transferir o projeto

[Baixar o exemplo de código](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>Passo 3: Configurar a Aplicação

1. Extraia o ficheiro zip para uma pasta local próxima da pasta raiz, por exemplo, **C:\Azure-Samples**
1. Se você usar um ambiente de desenvolvimento integrado, abra o exemplo em seu IDE favorito (opcional).
1. Abra o ficheiro **app_config.py,** que pode ser encontrado na pasta raiz e substitua-o pelo seguinte código:

```python
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
```

> [!div renderon="docs"]
> Onde:
>
> - `Enter_the_Application_Id_here` - é o Id da Aplicação que registou.
> - `Enter_the_Client_Secret_Here` - é o Segredo do **Cliente** que criou em **Certificados e Segredos** para a aplicação que registou.
> - `Enter_the_Tenant_Name_Here` - é o valor de id do **Diretório (inquilino)** do pedido que registou.

#### <a name="step-4-run-the-code-sample"></a>Etapa 4: executar o exemplo de código

1. Você precisará instalar a biblioteca MSAL Python, estrutura do Balão, Flask-Sessions para gestão de sessões do lado do servidor e pedidos usando pip da seguinte forma:

   ```Shell
   pip install -r requirements.txt
   ```

2. Executar app.py a partir da concha ou linha de comando:

   ```Shell
   python app.py
   ```
   > [!IMPORTANT]
   > Este aplicativo de início rápido usa um segredo do cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto sem formatação aos arquivos do projeto, por motivos de segurança, é recomendável que você use um certificado em vez de um segredo do cliente antes de considerar o aplicativo como aplicativo de produção. Para obter mais informações sobre como utilizar um certificado, consulte [estas instruções](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Mais informações

### <a name="getting-msal"></a>Obtendo MSAL
MSAL é a biblioteca usada para assinar utilizadores e solicitar fichas usadas para aceder a uma API protegida pela Plataforma de Identidade da Microsoft.
Pode adicionar MSAL Python à sua aplicação utilizando pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL
Pode adicionar a referência ao MSAL Python adicionando o seguinte código à parte superior do ficheiro onde utilizará mSAL:

```Python
import msal
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre aplicações web que assinam nos utilizadores, e depois que chama APIs web:

> [!div class="nextstepaction"]
> [Cenário: Aplicações web que assinam nos utilizadores](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
