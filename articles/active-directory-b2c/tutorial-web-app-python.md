---
title: 'Tutorial: Ativar a autenticação numa aplicação web Python'
titleSuffix: Azure AD B2C
description: Neste tutorial, aprenda a usar o Azure Ative Directory B2C para fornecer login do utilizador para uma aplicação web Python Flask.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 56e92a698446bd417c74820d6da662ad9ee55c77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555674"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>Tutorial: Ativar a autenticação numa aplicação web Python com Azure AD B2C

Este tutorial mostra-lhe como usar o Azure Ative Directory B2C (Azure AD B2C) para se inscrever e inscrever-se nos utilizadores numa aplicação web Python Flask.

Neste tutorial:

> [!div class="checklist"]
> * Adicione um URL de resposta a um pedido registado no seu inquilino Azure AD B2C
> * Faça o download de uma amostra de código do GitHub
> * Modifique o código do pedido de amostra para trabalhar com o seu inquilino
> * Inscreva-se usando o fluxo do utilizador de inscrição/inscrição

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos Azure AD B2C em vigor antes de continuar com os passos neste tutorial:

* [Inquilino Azure AD B2C](tutorial-create-tenant.md)
* [Inscrição registada](tutorial-register-applications.md) no seu inquilino, e sua *Aplicação (cliente) ID* e *segredo de cliente*
* [Fluxos de utilizador criados](tutorial-create-user-flows.md) no seu inquilino

Além disso, precisa do seguinte no seu ambiente de desenvolvimento local:

* [Código de Estúdio Visual](https://code.visualstudio.com/) ou outro editor de código
* [Python](https://nodejs.org/en/download/) 2.7+ ou 3+

## <a name="add-a-redirect-uri"></a>Adicione um URI de redirecionamento

No segundo tutorial que completou como parte dos pré-requisitos, registou uma aplicação web em Azure AD B2C. Para permitir a comunicação com a amostra de código neste tutorial, adicione um URL de resposta (também chamado de URI redirecionado) ao registo de pedidos.

Para atualizar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **registos de Aplicações**, selecione o **separador aplicações Próprias** e, em seguida, selecione a aplicação *webapp1.*
1. Em **Gestão**, **selecione Autenticação**.
1. Na **Web,** selecione o link **Add URI** e, em seguida, introduza `http://localhost:5000/getAToken` na caixa de texto.
1. Selecione **Guardar**.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Selecione **Todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações (Legado)** e, em seguida, selecione a aplicação *webapp1.*
1. Em **URL de resposta**, adicione `http://localhost:5000/getAToken` .
1. Selecione **Guardar**.
* * *

## <a name="get-the-sample-code"></a>Obter o código de exemplo

Neste tutorial, você configura uma amostra de código que você descarrega do GitHub para trabalhar com o seu inquilino B2C. A amostra demonstra como uma aplicação web Python Flask pode usar Azure AD B2C para inscrição e inscrição do utilizador.

[Faça o download de um . ZIP arquivar](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) ou clonar o [repositório](https://github.com/Azure-Samples/ms-identity-python-webapp) de amostra de código do GitHub.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>Atualizar a amostra

Assim que tiver obtido a amostra, configuure o pedido para utilizar o seu inquilino Azure AD B2C, registo de candidaturas e fluxos de utilizador.

No diretório de raiz do projeto:

1. Mude o nome do ficheiro *app_config.py* para *app_config.py.OLD*
1. Mude o nome da *app_config_b2c.py* para *app_config.py*

Atualize o recém-renomeado *app_config.py* com valores para o seu inquilino AZure AD B2C e registo de inscrição que criou como parte dos pré-requisitos.

1. Abra o ficheiro *app_config.py* no seu editor.
1. Atualize o `b2c_tenant` valor com o nome do seu inquilino Azure AD B2C, por exemplo *contosob2c*.
1. Atualizar cada um dos `*_user_flow` valores para corresponder aos nomes dos fluxos de utilizador que criou como parte dos pré-requisitos.
1. Atualizar o `CLIENT_ID` valor com o **ID da Aplicação (cliente)** da aplicação web que registou como parte dos pré-requisitos.
1. Atualize o `CLIENT_SECRET` valor com o valor do segredo de **cliente** que criou nos pré-requisitos. Para uma maior segurança, considere guardá-la em vez de uma **variável ambiental** como recomendado nos comentários.

A parte superior da *app_config.py* deve agora ser semelhante ao seguinte corte de código:

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> Como indicado nos comentários de código snippet, recomendamos que **não guarde segredos em texto simples no** seu código de aplicação. A variável codificada é utilizada apenas na amostra de código para *conveniência*. Considere usar uma variável ambiental ou uma loja secreta como a Azure Key Vault.

## <a name="run-the-sample"></a>Executar o exemplo

1. Na sua consola ou terminal, mude para o diretório que contém a amostra. Por exemplo:

    ```console
    cd ms-identity-python-webapp
    ```
1. Executar os seguintes comandos para instalar os pacotes necessários a partir do PyPi e executar a aplicação web na sua máquina local:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    A janela da consola apresenta o número de porta da aplicação de funcionamento local:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. Navegue para `http://localhost:5000` ver a aplicação web em execução na sua máquina local.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="Web browser mostrando aplicação web Python Flask executando localmente":::

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

Esta aplicação de amostra suporta a inscrição, o início e o reset da palavra-passe. Neste tutorial, inscreva-se através de um endereço de e-mail.

1. Selecione **Iniciar sôs-in** para iniciar o fluxo de utilizador *B2C_1_signupsignin1* especificado num passo anterior.
1. Azure AD B2C apresenta uma página de inscrição que inclui um link de inscrição. Uma vez que ainda não tem uma conta, selecione o link **'Iniciar s-se).**
1. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador usando um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a palavra-passe do utilizador e os atributos solicitados definidos no fluxo do utilizador.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Inscreva-se na página exibida pelo fluxo de utilizador Azure AD B2C":::

1. Selecione **Criar** para criar uma conta local no diretório Azure AD B2C.

Quando seleciona **Criar,** a aplicação mostra o nome do utilizador assinado.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="Navegador web mostrando aplicação web Python Flask com registrado no utilizador":::

Se quiser testar o início de sação, selecione o link **Logout,** então selecione **Iniciar sôm entrada** e faça o sôm com o endereço de e-mail e a palavra-passe que inseriu quando se inscreveu.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você configura uma aplicação web Python Flask para trabalhar com um fluxo de utilizador no seu inquilino Azure AD B2C para fornecer inscrição e iniciar sing na capacidade. Completou estes passos:

> [!div class="checklist"]
> * Adicionei um URL de resposta a um pedido registado no seu inquilino Azure AD B2C
> * Descarregou uma amostra de código do GitHub
> * Modificou o código do pedido de amostra para trabalhar com o seu inquilino
> * Inscreva-se usando o fluxo de utilizador de inscrição/inscrição

Em seguida, saiba como personalizar o UI das páginas de fluxo do utilizador apresentadas aos seus utilizadores por Azure AD B2C:

> [!div class="nextstepaction"]
> [Personalize a interface das experiências do utilizador em Azure AD B2C >](customize-ui.md)