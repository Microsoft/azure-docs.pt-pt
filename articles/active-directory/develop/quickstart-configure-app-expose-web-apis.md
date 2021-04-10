---
title: 'Quickstart: Registe e exponha uma | web da API Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste quickstart, regista uma API web com a plataforma de identidade da Microsoft e configura os seus âmbitos, expondo-o aos clientes para acesso baseado em permissões aos recursos da API.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 820bc7dfe9123db495c151cd5cd0ea5ae337619f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103979"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Quickstart: Configurar uma aplicação para expor uma API web

Neste quickstart, regista-se uma API web com a plataforma de identidade da Microsoft e expõe-a a aplicações de clientes adicionando um exemplo de âmbito. Ao registar a sua API web e expô-la através de âmbitos, pode fornecer acesso baseado em permissões aos seus recursos a utilizadores autorizados e aplicações de clientes que acedam à sua API.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa - [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Conclusão do [Quickstart: Criar um inquilino](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>Registar a API web

Para fornecer acesso alargado aos recursos na sua API web, primeiro precisa de registar a API com a plataforma de identidade da Microsoft.

1. Execute os passos no Registo uma secção de **aplicação** do [Quickstart: Registe uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md).
1. Ignore as secções **de configuração de URI** e **Configure** de redirecionamento de redirecionamento. Não precisa de configurar um URI de redirecionamento para uma API web, uma vez que nenhum utilizador é registado interativamente.
1. Ignore a secção **de credenciais Adicionar** por enquanto. Só se a sua API acessasse a uma API a jusante precisaria das suas próprias credenciais, um cenário não abrangido por este artigo.

Com a sua API web registada, está pronta para adicionar os âmbitos que o código da API pode usar para fornecer permissão granular aos consumidores da sua API.

## <a name="add-a-scope"></a>Adicionar um âmbito

O código de uma aplicação de cliente solicita permissão para realizar operações definidas pela sua API web, passando um token de acesso juntamente com os seus pedidos ao recurso protegido (a API web). A sua API web só executa a operação solicitada se o token de acesso que recebe contiver os âmbitos necessários para a operação.

Em primeiro lugar, siga estes passos para criar um exemplo de âmbito `Employees.Read.All` chamado:

1. Inicie sessão no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se tiver acesso a vários inquilinos, utilize o filtro **de subscrição Diretório +** :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o inquilino que contém o registo da sua aplicação do cliente.
1. Selecione as inscrições da **App Azure Ative Directory**  >  App e, em seguida, selecione o registo da sua aplicação da API.
1. Selecione **Expor uma API**  >  **Adicione um âmbito**.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Um registo de aplicações expõe um painel de API no portal Azure":::

1. É-lhe pedido que estabeleça um **ID URI** de aplicação se ainda não tiver configurado um.

   O ID URI da App funciona como o prefixo para os âmbitos que irá referir no código da sua API, e deve ser globalmente único. Pode utilizar o valor predefinido fornecido, que está na `api://<application-client-id>` forma, ou especificar um URI mais legível como `https://contoso.com/api` .

1. Em seguida, especifique os atributos do âmbito no painel de âmbito Adicionar um painel de **âmbito.** Para este walk-through, pode utilizar os valores do exemplo ou especificar os seus próprios valores.

    | Campo | Descrição | Exemplo |
    |-------|-------------|---------|
    | **Nome do âmbito** | O nome da sua mira. Uma convenção comum de nomeação de âmbito é `resource.operation.constraint` . | `Employees.Read.All` |
    | **Quem pode consentir** | Se este âmbito pode ser consentido pelos utilizadores ou se é necessário o consentimento da administração. Selecione **Apenas administradores** para permissões com mais privilégios. | **Administradores e utilizadores** |
    | **Nome a apresentar do consentimento do administrador** | Uma breve descrição do propósito do âmbito que só os administradores verão. | `Read-only access to Employee records` |
    | **Descrição do consentimento do administrador** | Uma descrição mais detalhada da autorização concedida pelo âmbito que só os administradores verão. | `Allow the application to have read-only access to all Employee data.` |
    | **Nome a apresentar do consentimento do utilizador** | Uma breve descrição do propósito do âmbito. Mostrado aos utilizadores apenas se definir **Quem pode consentir com** Administradores e **utilizadores**. | `Read-only access to your Employee records` |
    | **Descrição do consentimento do utilizador** | Uma descrição mais detalhada da autorização concedida pelo âmbito de aplicação. Mostrado aos utilizadores apenas se definir **Quem pode consentir com** Administradores e **utilizadores**. | `Allow the application to have read-only access to your Employee data.` |

1. Desa estaca o **Estado** **para ativar** e, em seguida, selecione **adicionar o âmbito**.

1. (Opcional) Para suprimir o pedido de consentimento dos utilizadores da sua app para os âmbitos definidos, pode *pré-autorizar* a aplicação do cliente a aceder à sua API web. Pré-autorizar *apenas* as aplicações de cliente em que confia, uma vez que os seus utilizadores não terão a oportunidade de recusar o consentimento.
    1. Sob **aplicações de cliente autorizadas,** selecione **Adicionar uma aplicação ao cliente**
    1. Introduza o ID de **Aplicação (cliente)** da aplicação do cliente que pretende pré-autorizar. Por exemplo, a de uma aplicação web que já registou anteriormente.
    1. Nos **âmbitos autorizados,** selecione os âmbitos para os quais pretende suprimir o pedido de consentimento e, em seguida, selecione **Adicionar a aplicação**.

    Se seguiu este passo opcional, a aplicação do cliente é agora uma aplicação de cliente pré-autorizada (APC), e os utilizadores não serão solicitados para o seu consentimento ao iniciar a sua assinatura.

## <a name="add-a-scope-requiring-admin-consent"></a>Adicionar um âmbito que requer consentimento administrativo

Em seguida, adicione outro exemplo de âmbito nomeado `Employees.Write.All` que apenas os administradores podem consentir. Os âmbitos que requerem consentimento administrativo são normalmente utilizados para fornecer acesso a operações privilegiadas mais elevadas, e muitas vezes por aplicações de clientes que funcionam como serviços de backend ou daemons que não assinam interativamente um utilizador.

Para adicionar o âmbito de `Employees.Write.All` exemplo, siga os passos na secção [Adicionar um âmbito](#add-a-scope) e especifique estes valores no painel de âmbito Adicionar um **âmbito:**

| Campo                          | Valor de exemplo                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **Nome do âmbito**                 | `Employees.Write.All`                                              |
| **Quem pode consentir**            | **Apenas administradores**                                                    |
| **Nome a apresentar do consentimento do administrador** | `Write access to Employee records`                                 |
| **Descrição do consentimento do administrador**  | `Allow the application to have write access to all Employee data.` |
| **Nome a apresentar do consentimento do utilizador**  | *Nenhum (deixar vazio)*                                               |
| **Descrição do consentimento do utilizador**   | *Nenhum (deixar vazio)*                                               |

## <a name="verify-the-exposed-scopes"></a>Verifique os âmbitos expostos

Se tiver adicionado com sucesso ambos os âmbitos de exemplo descritos nas secções anteriores, eles aparecerão no painel **de API expondo um** painel API do registo de aplicações da API da web, semelhante a esta imagem:

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="Screenshot do painel De exposição de API mostrando dois âmbitos expostos.":::

Como mostrado na imagem, a cadeia completa de um âmbito é a concatenação do **ID URI** de aplicação da sua Web API e o **nome** scope do âmbito .

Por exemplo, se o ID URI de aplicação da sua Web for `https://contoso.com/api` e o nome do âmbito `Employees.Read.All` for, o âmbito completo é:

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>Utilizando os âmbitos expostos

No próximo artigo da série, configura o registo de uma aplicação de clientes com acesso à sua API web e os âmbitos definidos seguindo os passos deste artigo.

Uma vez que um registo de aplicações do cliente é autorizado a aceder à sua API web, o cliente pode ser emitido um token de acesso OAuth 2.0 pela plataforma de identidade Microsoft. Quando o cliente liga para a API web, apresenta um token de acesso cujo âmbito de aplicação ( `scp` ) reivindicação está definido para as permissões especificadas no registo de aplicações do cliente.

Se necessário, pode expor âmbitos adicionais mais tarde. Considere que a sua API web pode expor vários âmbitos associados a várias operações. O seu recurso pode controlar o acesso à API web em tempo de execução, avaliando o âmbito `scp` (s) claim(s) no token de acesso OAuth 2.0 que recebe.

## <a name="next-steps"></a>Passos seguintes

Agora que expôs a sua API web configurando os seus âmbitos, configurar o registo da sua aplicação cliente com permissão para aceder aos âmbitos.

> [!div class="nextstepaction"]
> [Configurar um registo de aplicações para acesso web api](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
