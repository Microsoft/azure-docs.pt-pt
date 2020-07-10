---
title: Adicione uma aplicação não-galeria - Plataforma de identidade da Microsoft / Microsoft Docs
description: Adicione um pedido de não galeria ao seu inquilino Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5db8aed0a47e7d8d928ef3287010d60efbc5e5da
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200459"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Adicione uma aplicação não cotada (não-galeria) à sua organização AZure AD

Além das escolhas na galeria de [aplicações AZure AD,](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)tem a opção de adicionar uma **aplicação não-galeria.** Pode adicionar qualquer aplicação que já exista na sua organização, ou qualquer aplicação de terceiros de um fornecedor que já não faça parte da galeria AZure AD. Dependendo do seu [contrato de licença,](https://azure.microsoft.com/pricing/details/active-directory/)estão disponíveis as seguintes capacidades:

- Integração de autosserviço de qualquer aplicação que suporte a linguagem de marcação de afirmação de [segurança (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) fornecedores de identidade (iniciados por SP ou iniciados pelo IdP)
- Integração de self-service de qualquer aplicação web que tenha uma página de s-in baseada em HTML usando [SSO baseado em palavra-passe](what-is-single-sign-on.md#password-based-sso)
- Ligação self-service de aplicações que utilizam o [protocolo System for Cross-Domain Identity Management (SCIM) para o fornecimento de utilizadores](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Capacidade de adicionar links a qualquer aplicação no [launcher da aplicação Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [painel de acesso AZure AD](what-is-single-sign-on.md#linked-sign-on)

Este artigo descreve como adicionar uma aplicação não-galeria às **Aplicações empresariais** no portal Azure sem código de escrita. Se em vez disso estiver à procura de orientação do programador sobre como integrar aplicações personalizadas com AD AZure, consulte [Cenários de Autenticação para AZure AD](../develop/authentication-scenarios.md). Quando desenvolve uma aplicação que utiliza um protocolo moderno como [o OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) para autenticar os utilizadores, pode registá-la com a plataforma de identidade da Microsoft utilizando a experiência de [registos](../develop/quickstart-register-app.md) da App no portal Azure.

## <a name="add-a-non-gallery-application"></a>Adicionar uma aplicação que não está na galeria

1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com/) utilizando a sua conta de administrador da plataforma de identidade Microsoft.

2. Selecione **Aplicações empresariais**  >  **Nova aplicação**.

3. (Opcional, mas recomendado) Na caixa de pesquisa **da Galeria BrowSe Ad, insira** o nome de exibição da aplicação. 

4. Selecione **Crie a sua própria aplicação.** Aparece a página **de aplicação Create your own.**

   ![Adicionar aplicação](media/add-non-gallery-app/create-your-own-application.png)

5. Comece a escrever o nome do visor para a sua nova aplicação. Se houver aplicações de galeria com nomes semelhantes, elas aparecerão numa lista de resultados de pesquisa.

   > [!NOTE]
   > Recomendamos a utilização da versão de galeria da sua aplicação sempre que possível. Se a aplicação que pretende adicionar aparecer nos resultados da pesquisa, selecione a aplicação e ignore o resto deste procedimento.

6. Em **Que pretende fazer com a sua candidatura?** Escolha Integrar qualquer outra **aplicação que não encontre na galeria.** Esta opção é normalmente utilizada para aplicações SAML e WS-Fed.

   > [!NOTE]
   > As outras duas opções são utilizadas nos seguintes cenários:
   >* **Configure Aplicação Proxy para acesso remoto seguro a uma aplicação no local** abre a página de configuração para Azure AD Application Proxy e conectores.
   >* **Registar uma aplicação em que está a trabalhar para integrar com a Azure AD** abre a página **de registos** da App. Esta opção é normalmente utilizada para aplicações OpenID Connect.

7. Selecione **Criar**. A página **geral da** aplicação abre.

## <a name="configure-user-sign-in-properties"></a>Configurar as propriedades de início de sessão do utilizador

1. Selecione **Propriedades** para abrir o painel de propriedades para edição.

    ![Editar painel de propriedades](media/add-non-gallery-app/edit-properties.png)

2. Defina as seguintes opções para determinar como os utilizadores que estão atribuídos ou não designados para a aplicação podem assinar na aplicação e se um utilizador pode ver a aplicação no painel de acesso.

    - **Ativada para os utilizadores iniciarem sessão** determina se os utilizadores atribuídos à aplicação podem iniciar sessão.
    - **A atribuição do utilizador necessária** determina se os utilizadores que não estão atribuídos à aplicação podem iniciar sessão.
    - **Visível para o utilizador** determina se os utilizadores atribuídos a uma aplicação podem vê-la no painel de acesso e no iniciador do O365.

      Comportamento para utilizadores **atribuídos**:

       | Propriedade de aplicação | Propriedade de aplicação | Propriedade de aplicação | Experiência para utilizadores atribuídos | Experiência para utilizadores atribuídos |
       |---|---|---|---|---|
       | Ativado para os utilizadores iniciarem sessão? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Os utilizadores atribuídos podem iniciar sessão? | Os utilizadores atribuídos podem ver a aplicação?* |
       | sim | sim | sim | sim | sim  |
       | sim | sim | não  | sim | não   |
       | sim | não  | sim | sim | sim  |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

      Comportamento para utilizadores **não atribuídos**:

       | Propriedade de aplicação | Propriedade de aplicação | Propriedade de aplicação | Experiência para utilizadores não atribuídos | Experiência para utilizadores não atribuídos |
       |---|---|---|---|---|
       | Habilitado para os utilizadores fazerem o s.a.? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Os utilizadores não atribuídos podem iniciar sessão? | Os utilizadores não atribuídos podem ver a aplicação?* |
       | sim | sim | sim | não  | não   |
       | sim | sim | não  | não  | não   |
       | sim | não  | sim | sim | não   |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

     *O utilizador pode ver a aplicação no painel de acesso e no iniciador de aplicações do Office 365?

3. Para usar um logótipo personalizado, crie um logótipo que seja de 215 por 215 pixels e guarde-o em formato PNG. Em seguida, navegue no seu logotipo e faça o upload.

    ![Alterar o logótipo](media/add-non-gallery-app/change-logo.png)

4. Quando terminar, **selecione Save**.

## <a name="next-steps"></a>Passos seguintes

Agora que adicionou a aplicação à sua organização Azure AD, [escolha um único método de inscrição](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que pretende utilizar e consulte o artigo apropriado abaixo:

- [Configurar o início de sessão único baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurar o sign-on único da palavra-passe](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar o início de sessão ligado](configure-linked-sign-on.md)
