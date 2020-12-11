---
title: 'Quickstart: Adicione o início de sing-in com a Microsoft a uma aplicação web Python / Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, saiba como uma aplicação web Python pode iniciar sôm nos utilizadores, obter um token de acesso a partir da plataforma de identidade da Microsoft e ligar para a Microsoft Graph API.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 383f7f37e93b4705419ba1f93f509c86eaab192b
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030642"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Quickstart: Adicione o início de sôs com a Microsoft a uma aplicação web Python

Neste quickstart, você descarrega e execute uma amostra de código que demonstra como uma aplicação web Python pode assinar nos utilizadores e obter um token de acesso para ligar para a Microsoft Graph API. Os utilizadores com uma Conta Microsoft pessoal ou uma conta em qualquer organização do Azure Ative (Azure AD) podem assinar na aplicação.

Veja [como funciona a amostra](#how-the-sample-works) para uma ilustração.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7+](https://www.python.org/downloads/release/python-2713) ou [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Frasco,](http://flask.pocoo.org/) [Flask-Session,](https://pypi.org/project/Flask-Session/) [pedidos](https://requests.kennethreitz.org/en/master/)
- [Pitão MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>Registar e transferir a aplicação do início rápido
>
> Tem duas opções para iniciar a sua aplicação de arranque rápido: expresso (Opção 1) e manual (Opção 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: registar e configurar automaticamente a sua aplicação e, em seguida, transferir o exemplo de código
>
> 1. Aceda ao [portal Azure - Inscrições de aplicações.](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs)
> 1. Introduza um nome para a sua aplicação e xelecione **Registar**.
> 1. Siga as instruções para descarregar e configurar automaticamente a sua nova aplicação.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: registar e configurar manualmente a aplicação e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Passo 1: Registar a aplicação
>
> Para registar a sua aplicação e adicionar as informações de registo da aplicação à sua solução manualmente, siga os passos a seguir:
>
> 1. Inicie sessão no [portal do Azure](https://portal.azure.com).
> 1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino no qual pretende registar uma candidatura.
> 1. Em **Gestão**, selecione **registos de aplicações**  >  **Novo registo**.
> 1. Introduza um **Nome** para a sua aplicação, por `python-webapp` exemplo. Os utilizadores da sua aplicação podem ver este nome, e pode alterá-lo mais tarde.
> 1. Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
> 1. Selecione **Registar**.
> 1. Na página **geral** da aplicação, note o valor **de ID da Aplicação (cliente)** para posterior utilização.
> 1. Em **Gestão**, **selecione Autenticação**.
> 1. **Selecione Adicionar uma plataforma**  >  **Web**.
> 1. Adicione `http://localhost:5000/getAToken` como **Redirecionar URIs**.
> 1. Selecione **Configurar**.
> 1. Em **Gestão**, selecione os **Certificados & segredos**  e da secção segredos do **Cliente,** selecione **Novo segredo do cliente**.
> 1. Digite uma descrição chave (por exemplo, o segredo da aplicação), deixe a expiração predefinitiva e selecione **Adicionar**.
> 1. Note o **valor** do Segredo do **Cliente** para posterior utilização.
> 1. Em **Gestão**, selecione **permissões API**  >  **Adicione uma permissão**.
>1.  Certifique-se de que o separador **APIs da Microsoft** está selecionado.
> 1. A partir da secção APIs da *Microsoft comumente utilizada,* selecione **Microsoft Graph**.
> 1. A partir da secção **de permissões delegadas,** certifique-se de que as permissões certas são verificadas: **User.ReadBasic.All**. Utilize a caixa de pesquisa, se necessário.
> 1. Selecione o botão **'Adicionar permissões'.**
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Passo 1: Configurar a aplicação no portal do Azure
>
> Para que a amostra de código para este arranque rápido funcione, você precisa:
>
> 1. Adicione um URL de resposta como `http://localhost:5000/getAToken` .
> 1. Criar um Segredo de Cliente.
> 1. Adicione o utilizador da Microsoft Graph API.ReadBasic.All permissão delegada.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça estas alterações para mim]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurada](media/quickstart-v2-aspnet-webapp/green-check.png) A sua aplicação está configurada com este atributo

#### <a name="step-2-download-your-project"></a>Passo 2: Transferir o projeto
> [!div renderon="docs"]
> [Descarregue a Amostra de Código](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Descarregue o projeto e extrate o ficheiro zip para uma pasta local mais próxima da pasta raiz - por exemplo, **C:\Azure-Samples**
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Descarregue a amostra de código](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Passo 3: Configurar a aplicação
>
> 1. Extraia o ficheiro zip para uma pasta local próxima da pasta raiz, por exemplo, **C:\Azure-Samples**
> 1. Se utilizar um ambiente de desenvolvimento integrado, abra a amostra no seu IDE favorito (opcional).
> 1. Abra o ficheiro **app_config.py,** que pode ser encontrado na pasta raiz e substituído pelo seguinte corte de código:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Onde:
>
> - `Enter_the_Application_Id_here` - é o Id da Aplicação que registou.
> - `Enter_the_Client_Secret_Here` - é o Segredo de **Cliente** que criou em **Certificados & Segredos**  para a aplicação que registou.
> - `Enter_the_Tenant_Name_Here` - é o valor de identificação do **Diretório (inquilino)** do pedido que registou.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Passo 3: Executar a amostra de código

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Passo 4: Executar a amostra de código

1. Você precisará instalar a biblioteca MSAL Python, estrutura flask, Flask-Sessions para gestão de sessão do lado do servidor e pedidos usando pip da seguinte forma:

    ```Shell
    pip install -r requirements.txt
    ```

2. Executar app.py a partir da concha ou linha de comando:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Esta aplicação quickstart usa um segredo de cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto simples aos seus ficheiros do projeto, por razões de segurança, recomenda-se que utilize um certificado em vez de um segredo de cliente antes de considerar a aplicação como aplicação de produção. Para obter mais informações sobre como utilizar um certificado, consulte [estas instruções](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Mais informações

### <a name="how-the-sample-works"></a>Como funciona a amostra
![Mostra como funciona a app de amostras gerada por este quickstart](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Obtenção de MSAL
MSAL é a biblioteca usada para assinar nos utilizadores e pedir fichas usadas para aceder a uma API protegida pela Plataforma de Identidade da Microsoft.
Pode adicionar MSAL Python à sua aplicação utilizando o Pip.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>Inicialização da MSAL
Pode adicionar a referência ao MSAL Python adicionando o seguinte código à parte superior do ficheiro onde utilizará o MSAL:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre aplicações web que assinam nos utilizadores na nossa série de cenários multi-partes.

> [!div class="nextstepaction"]
> [Cenário: aplicação web que assina nos utilizadores](scenario-web-app-sign-user-overview.md)
