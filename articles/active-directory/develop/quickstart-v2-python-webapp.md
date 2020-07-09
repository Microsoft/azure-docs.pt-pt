---
title: Adicione o sismo com a Microsoft a uma plataforma de identidade da Microsoft Python web app / Rio Azure
description: Saiba como implementar o Microsoft Sign-In numa aplicação web Python utilizando o OAuth2
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, tracking-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 0a2867b9b39634000366ec2a92f936a4e419e859
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558631"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Quickstart: Adicione o início de sôs com a Microsoft a uma aplicação web Python

Neste arranque rápido, aprenderás a integrar uma aplicação web Python com a plataforma de identidade da Microsoft. A sua aplicação irá iniciar seduca num utilizador, obter um token de acesso para ligar para a API do Gráfico da Microsoft e fazer um pedido para a API do Microsoft Graph.

Quando tiver concluído o guia, a sua aplicação aceitará inscrições de contas pessoais da Microsoft (incluindo outlook.com, live.com e outras) e contas de trabalho ou escola de qualquer empresa ou organização que utilize o Azure Ative Directory. (Ver [como funciona a amostra](#how-the-sample-works) para uma ilustração.)

## <a name="prerequisites"></a>Pré-requisitos

Para executar esta amostra, você precisará:

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
> 1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
> 1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
> 1. Navegue para a plataforma de identidade da Microsoft para programadores [Página de registos de aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
> 1. Selecione **Novo registo**.
> 1. Quando a página **Registar uma aplicação** for apresentada, introduza as informações de registo da aplicação:
>      - Na secção **Nome,** introduza um nome de aplicação significativo que será apresentado aos utilizadores da aplicação, por `python-webapp` exemplo.
>      - Nos **tipos de conta suportada**, selecione Contas em qualquer **diretório organizacional e contas pessoais da Microsoft.**
>      - Selecione **Registar**.
>      - Na página **geral** da aplicação, note o valor **de ID da Aplicação (cliente)** para posterior utilização.
> 1. Selecione a **Autenticação** do menu e, em seguida, adicione as seguintes informações:
>    - Adicione a configuração da plataforma **Web.** Adicione `http://localhost:5000/getAToken` como **Redirecionar URIs**.
>    - Selecione **Guardar**.
> 1. No menu da mão esquerda, escolha **Certificados & segredos** e clique em **Novo segredo de cliente** na secção Segredos do **Cliente:**
>
>      - Digite uma descrição chave (do segredo de aplicação de exemplo).
>      - Selecione uma duração chave de **In 1 ano**.
>      - Quando clicar em **Adicionar,** o valor da chave será apresentado.
>      - Copie o valor da chave. Precisará dele mais tarde.
> 1. Selecione a secção **de permissões API**
>
>      - Clique no botão **Adicionar um botão de permissão** e, em seguida,
>      - Certifique-se de que o separador **APIs da Microsoft** está selecionado
>      - Na secção APIs da *Microsoft comumente utilizada,* clique no **Gráfico do Microsoft**
>      - Na secção **permissões delegadas,** certifique-se de que as permissões certas são verificadas: **User.ReadBasic.All**. Utilize a caixa de pesquisa, se necessário.
>      - Selecione o botão **adicionar permissões**
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
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
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
> Em que:
>
> - `Enter_the_Application_Id_here` - é o Id da Aplicação que registou.
> - `Enter_the_Client_Secret_Here`- é o Segredo de **Cliente** que criou em **Certificados & Segredos** para a aplicação que registou.
> - `Enter_the_Tenant_Name_Here`- é o valor de identificação do **Diretório (inquilino)** do pedido que registou.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Passo 3: Executar a amostra de código

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Passo 4: Executar a amostra de código

1. Você precisará instalar a biblioteca MSAL Python, a estrutura do Frasco, as Sessões de Flask para gestão de sessão do lado do servidor e os pedidos utilizando o pip da seguinte forma:

    ```Shell
    pip install -r requirements.txt
    ```

2. Executar app.py a partir da concha ou linha de comando:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Esta aplicação quickstart usa um segredo de cliente para se identificar como cliente confidencial. Como o segredo do cliente é adicionado como um texto simples aos seus ficheiros do projeto, por razões de segurança, recomenda-se que utilize um certificado em vez de um segredo de cliente antes de considerar a aplicação como aplicação de produção. Para obter mais informações sobre como utilizar um certificado, consulte [estas instruções](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

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

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre aplicações web que assinam nos utilizadores e, em seguida, que chama APIs web:

> [!div class="nextstepaction"]
> [Cenário: aplicativos web que assinam nos utilizadores](scenario-web-app-sign-user-overview.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
