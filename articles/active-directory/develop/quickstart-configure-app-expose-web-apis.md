---
title: 'Quickstart: Configurar uma app para expor uma API web Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste arranque rápido, aprende-se a configurar uma aplicação para expor uma nova permissão/âmbito e papel para disponibilizar a aplicação às aplicações dos clientes.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830296"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Quickstart: Configurar uma aplicação para expor uma API web

Pode programar uma API Web e expor [permissões/âmbitos](developer-glossary.md#scopes) e [funções](developer-glossary.md#roles) para a disponibilizar às aplicações cliente. As APIs Web configuradas corretamente são disponibilizadas tal e qual as outras APIs Web da Microsoft, incluindo a Graph API e as APIs do Office 365.

Neste arranque rápido, aprende-se a configurar uma aplicação para expor uma nova margem de manobra para a disponibilizar às aplicações dos clientes.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Conclusão do [Quickstart: Registar uma aplicação com a plataforma de identidade microsoft](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Iniciar sessão no portal do Azure e selecionar a aplicação

Antes de poder configurar a aplicação, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure** e, em seguida, selecione **registos de Aplicações**.
1. Encontre e selecione a aplicação que quer configurar. Depois de selecionar a aplicação, verá a página **Descrição Geral** da aplicação ou a página de registo principal.
1. Escolha o método que quer utilizar, a IU ou o manifesto de aplicação para expor um novo âmbito:
    * [Expor um novo âmbito através da IU](#expose-a-new-scope-through-the-ui)
    * [Expor um novo âmbito ou função através do manifesto de aplicação](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Expor um novo âmbito através da IU

[![Mostra como expor uma API usando a UI](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Para expor um novo âmbito através da IU:

1. A partir da página **Descrição geral** da aplicação, selecione a secção **Expor uma API**.

1. Selecione **Adicionar âmbito**.

1. Se não tiver definido um **URI do ID da aplicação**, verá uma mensagem para introduzir um. Introduza o URI do ID da aplicação ou utilize o fornecido e, em seguida, selecione **Guardar e continuar**.

1. Quando for apresentada a página **Adicionar âmbito**, introduza as informações do âmbito:

    | Campo | Descrição |
    |-------|-------------|
    | **Nome do âmbito** | Introduza um nome significativo para o âmbito.<br><br>Por exemplo, `Employees.Read.All`. |
    | **Quem pode consentir** | Selecione se o âmbito pode ser permitido por utilizadores ou se é necessário o consentimento do administrador. Selecione **Apenas administradores** para permissões com mais privilégios. |
    | **Nome a apresentar do consentimento do administrador** | Introduza uma descrição significativa para o âmbito, que os administradores irão ver.<br><br>Por exemplo, `Read-only access to Employee records` |
    | **Descrição do consentimento do administrador** | Introduza uma descrição significativa para o âmbito, que os administradores irão ver.<br><br>Por exemplo, `Allow the application to have read-only access to all Employee data.` |

    Se os utilizadores puderem dar consentimento ao seu âmbito, adicione também valores para os seguintes campos:

    | Campo | Descrição |
    |-------|-------------|
    | **Nome a apresentar do consentimento do utilizador** | Introduza um nome significativo para o âmbito, que os utilizadores irão ver.<br><br>Por exemplo, `Read-only access to your Employee records` |
    | **Descrição do consentimento do utilizador** | Introduza uma descrição significativa para o âmbito, que os utilizadores irão ver.<br><br>Por exemplo, `Allow the application to have read-only access to your Employee data.` |

1. Defina o **Estado** e selecione **Adicionar âmbito** quando terminar.

1. (Opcional) Para suprimir o pedido de consentimento dos utilizadores da sua app para os âmbitos definidos, pode "pré-autorizar" a aplicação do cliente a aceder à sua API web. Deve pré-autorizar *apenas* as aplicações de cliente em que confia, uma vez que os seus utilizadores não terão a oportunidade de recusar o consentimento.
    1. Sob **aplicações de cliente autorizadas,** selecione **Adicionar uma aplicação ao cliente**
    1. Introduza o ID de **Aplicação (cliente)** da aplicação do cliente que pretende pré-autorizar. Por exemplo, a de uma aplicação web que já registou anteriormente.
    1. Nos **âmbitos autorizados,** selecione os âmbitos para os quais pretende suprimir o pedido de consentimento e, em seguida, selecione **Adicionar a aplicação**.

    A aplicação do cliente é agora uma aplicação de cliente pré-autorizada (PCA), e os utilizadores não serão solicitados para consentimento ao iniciar sessão.

1. Siga os passos para [verificar se a API Web está exposta a outras aplicações](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Expor um novo âmbito ou função através do manifesto de aplicação

O manifesto de aplicação serve como um mecanismo de atualização da entidade de aplicação que define os atributos de um registo de aplicações AD AZure.

[![Expor um novo âmbito utilizando a coleção de oauth2Permissions no manifesto](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Para expor um novo âmbito editando o manifesto de aplicação:

1. A partir da página **Descrição geral** da aplicação, selecione a secção **Manifesto**. É aberto um editor de manifesto baseado na Web, que lhe permite **Editar** o manifesto no portal. Opcionalmente, pode selecionar **Transferir**, editar o manifesto localmente e, em seguida, utilizar **Carregar** para o reaplicar à aplicação.

    O exemplo seguinte mostra como expor um novo âmbito denominado `Employees.Read.All` no recurso/API ao adicionar o seguinte elemento JSON à coleção `oauth2Permissions`.

    Gere o `id` valor programáticamente ou utilizando uma ferramenta de geração GUID como [o guidgen](https://www.microsoft.com/download/details.aspx?id=55984).

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

1. Quando terminar, clique em **Guardar**. A sua API Web está agora configurada para ser utilizada por outras aplicações no seu diretório.
1. Siga os passos para [verificar se a API Web está exposta a outras aplicações](#verify-the-web-api-is-exposed-to-other-applications).

Para obter mais informações sobre a entidade de aplicação e o seu esquema, consulte a documentação de referência do tipo de recurso do [Microsoft][ms-graph-application] Graph.

Para obter mais informações sobre o manifesto da aplicação, incluindo a sua referência de esquema, consulte [compreender o manifesto da aplicação AD AZure.](reference-app-manifest.md)

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Verificar se a API Web está exposta a outras aplicações

1. Volte ao seu inquilino Azure AD, selecione **registos de Aplicações**e, em seguida, encontre e selecione a aplicação do cliente que pretende configurar.
1. Repita os passos descritos em [Configurar uma aplicação cliente para aceder a APIs Web](quickstart-configure-app-access-web-apis.md).
1. Quando chegar ao passo para [selecionar uma API,](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)selecione o seu recurso (o registo de aplicações da API na Web).
    * Se criou o registo de aplicações da API web utilizando o portal Azure, o seu recurso API está listado no separador **My APIs.**
    * Se permitiu que o Visual Studio criasse o registo de aplicações da API na web durante a criação do projeto, o seu recurso API está listado no **separador APIs que** a minha organização utiliza.

Uma vez selecionado o recurso API web, deverá ver o novo âmbito disponível para pedidos de permissão do cliente.

## <a name="using-the-exposed-scopes"></a>Utilizando os âmbitos expostos

Uma vez que um cliente esteja devidamente configurado com permissões para aceder à sua API web, pode ser emitido um token de acesso OAuth 2.0 pela Azure AD. Quando o cliente liga para a API web, apresenta o token de acesso que tem o âmbito de aplicação ( `scp` ) reclamação definido para as permissões solicitadas no seu registo de inscrição.

Se necessário, pode expor âmbitos adicionais mais tarde. Considere que a API Web poderá expor vários âmbitos associados a diversas funções diferentes. O seu recurso pode controlar o acesso à API Web no runtime ao avaliar a afirmação ou afirmações do âmbito (`scp`) no token de acesso OAuth 2.0 recebido.

Nas suas aplicações, o valor de âmbito completo é uma concatenação do **ID URI** de aplicação da API web (o recurso) e do nome de **âmbito.**

Por exemplo, se o ID URI de aplicação da sua Web for `https://contoso.com/api` e o nome do seu âmbito `Employees.Read.All` for, o âmbito completo é:

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>Passos seguintes

Agora que expôs a sua API web configurando os seus âmbitos, configure o registo da sua aplicação cliente com permissão para aceder a esses âmbitos.

> [!div class="nextstepaction"]
> [Configurar um registo de aplicações para acesso web api](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
