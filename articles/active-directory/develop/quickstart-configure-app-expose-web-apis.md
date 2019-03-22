---
title: Configurar uma aplicação para expor APIs Web (Pré-visualização) | Azure
description: Saiba como configurar uma aplicação para expor uma nova permissão/âmbito e função para disponibilizar a aplicação às aplicações cliente.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a8ff17656978e6e4e8741c19cda79743560481a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080850"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis-preview"></a>Início rápido: Configurar uma aplicação para expor web APIs (pré-visualização)

Pode programar uma API Web e expor [permissões/âmbitos](developer-glossary.md#scopes) e [funções](developer-glossary.md#roles) para a disponibilizar às aplicações cliente. As APIs Web configuradas corretamente são disponibilizadas tal e qual as outras APIs Web da Microsoft, incluindo a Graph API e as APIs do Office 365.

Neste início rápido, vai aprender a configurar uma aplicação para expor um novo âmbito para o disponibilizar às aplicações cliente.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, certifique-se de que cumpre estes pré-requisitos:

* Saiba mais sobre as [permissões e consentimentos](v2-permissions-and-consent.md) suportados, que é importante compreender se estiver a criar aplicações que têm de ser utilizadas por outros utilizadores ou aplicações.
* Ter um inquilino com aplicações registadas.
  * Se não tiver aplicações registadas, [saiba como registar aplicações na plataforma de identidade da Microsoft](quickstart-register-app.md).
* Inscreva-na experiência de Pré-visualização para registos de aplicações no portal do Azure. Os passos neste início rápido correspondem à nova IU e apenas funcionam se tiver optado pela experiência de Pré-visualização.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Iniciar sessão no portal do Azure e selecionar a aplicação

Antes de poder configurar a aplicação, siga estes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. Se a sua conta permitir aceder a mais de um inquilino, selecione-a no canto superior direito e defina a sua sessão no portal para o inquilino pretendido do Azure AD.
1. No painel de navegação do lado esquerdo, selecione o serviço **Azure Active Directory** e, em seguida, selecione **Registos de aplicações (Pré-visualização)**.
1. Encontre e selecione a aplicação que quer configurar. Depois de selecionar a aplicação, verá a página **Descrição geral** da aplicação ou a página de registo principal.
1. Escolha o método que quer utilizar, a IU ou o manifesto de aplicação para expor um novo âmbito:
    * [Expor um novo âmbito através da IU](#expose-a-new-scope-through-the-ui)
    * [Expor um novo âmbito ou função através do manifesto de aplicação](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Expor um novo âmbito através da IU

[![Expor uma API com a IU](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

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

1. Siga os passos para [verificar se a API Web está exposta a outras aplicações](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Expor um novo âmbito ou função através do manifesto de aplicação

[![Expor um novo âmbito com a coleção oauth2Permissions no manifesto](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Para expor um novo âmbito através do manifesto de aplicação:

1. A partir da página **Descrição geral** da aplicação, selecione a secção **Manifesto**. É aberto um editor de manifesto baseado na Web, que lhe permite **Editar** o manifesto no portal. Opcionalmente, pode selecionar **Transferir**, editar o manifesto localmente e, em seguida, utilizar **Carregar** para o reaplicar à aplicação.
    
    O exemplo seguinte mostra como expor um novo âmbito denominado `Employees.Read.All` no recurso/API ao adicionar o seguinte elemento JSON à coleção `oauth2Permissions`.

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

   > [!NOTE]
   > O valor de `id` tem de ser gerado programaticamente ou com uma ferramenta de geração de GUID, como o [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). `id` representa um identificador exclusivo para o âmbito conforme é exposto pela API Web. Após um cliente ser devidamente configurado com permissões para aceder à sua API Web, o Azure AD emite um token de acesso OAuth 2.0 ao mesmo. Quando o cliente chamar a API Web, apresenta o token de acesso que tem a afirmação de âmbito (scp) definida como as permissões pedidas no registo da aplicação.
   >
   > Se necessário, pode expor âmbitos adicionais mais tarde. Considere que a API Web poderá expor vários âmbitos associados a diversas funções diferentes. O seu recurso pode controlar o acesso à API Web no runtime ao avaliar a afirmação ou afirmações do âmbito (`scp`) no token de acesso OAuth 2.0 recebido.

1. Quando terminar, clique em **Guardar**. A sua API Web está agora configurada para ser utilizada por outras aplicações no seu diretório.
1. Siga os passos para [verificar se a API Web está exposta a outras aplicações](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Verificar se a API Web está exposta a outras aplicações

1. Regresse ao seu inquilino do Azure AD, selecione **Registos de aplicações**, localize e selecione a aplicação cliente que quer configurar.
1. Repita que os passos descritos em configurar uma aplicação de cliente para aceder a web APIs.
1. Quando for para o passo **Selecionar uma API**, selecione o recurso. Deverá ver o novo âmbito disponível para pedidos de permissão de cliente.

## <a name="more-on-the-application-manifest"></a>Mais sobre o manifesto da aplicação

O manifesto de aplicação funciona como um mecanismo para atualizar a entidade da aplicação, que define todos os atributos de configuração de identidade de uma aplicação do Azure AD. Para obter mais informações sobre a entidade Aplicação e respetivo esquema, veja a documentação [Entidade de Aplicação da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity). O artigo contém informações de referência completas sobre os membros da entidade Aplicação utilizados para especificar permissões para a sua API, incluindo:  

* O membro appRoles, que é uma coleção de entidades [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type), utilizado para definir as [permissões da aplicação](developer-glossary.md#permissions) de uma API Web.
* O membro oauth2Permissions, que é uma coleção de entidades [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type), utilizado para definir as [permissões delegadas](developer-glossary.md#permissions) de uma API Web.

Para obter mais informações sobre os conceitos gerais do manifesto de aplicação, veja [Compreender o manifesto de aplicação do Azure Active Directory](reference-app-manifest.md).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outros inícios rápidos de gestão de aplicações relacionados:

* [Registar uma aplicação na plataforma de identidade da Microsoft](quickstart-register-app.md)
* [Configurar uma aplicação cliente para aceder a APIs Web](quickstart-configure-app-access-web-apis.md)
* [Modificar as contas suportadas por uma aplicação](quickstart-modify-supported-accounts.md)
* [Remover uma aplicação registada na plataforma de identidade da Microsoft](quickstart-remove-app.md)

Para saber mais sobre os dois objetos do Azure AD que representam uma aplicação registada e o relacionamento entre os mesmos, veja [Application objects and service principal objects](app-objects-and-service-principals.md) (Objetos da aplicação e objetos do principal de serviço).

Para saber mais sobre as diretrizes de imagem corporativa que deve seguir quando desenvolver aplicações com o Azure Active Directory, veja [Diretrizes de imagem corporativa para aplicações](howto-add-branding-in-azure-ad-apps.md).
