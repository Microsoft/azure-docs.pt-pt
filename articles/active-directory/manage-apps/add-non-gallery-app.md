---
title: Adicione uma aplicação não-galeria - plataforma de identidade Microsoft / Microsoft Docs
description: Adicione uma aplicação não-galeria ao seu inquilino Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063616"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Adicione uma aplicação não listada (não-galeria) à sua organização Azure AD

Além das escolhas na galeria de [aplicações da AD Azure,](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)tem a opção de adicionar uma **aplicação não-galeria.** Pode adicionar qualquer aplicação que já exista na sua organização, ou qualquer aplicação de terceiros de um vendedor que ainda não faça parte da galeria Azure AD. Dependendo do seu contrato de [licença,](https://azure.microsoft.com/pricing/details/active-directory/)estão disponíveis as seguintes capacidades:

- Integração self-service de qualquer aplicação que apoie fornecedores de identidade de marcação de afirmação de [segurança (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) (iniciado sp-iniciado ou iniciado com IDP)
- Integração self-service de qualquer aplicação web que tenha uma página de entrada baseada em HTML usando [SSO baseado em palavra-passe](what-is-single-sign-on.md#password-based-sso)
- Ligação self-service de aplicações que utilizam o protocolo Sistema de Gestão de [Identidade de Domínio SCIM (SCIM) para o fornecimento](../app-provisioning/use-scim-to-provision-users-and-groups.md) de utilizadores
- Capacidade de adicionar links a qualquer aplicação no lançador de [aplicações do Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) ou no [painel de acesso Azure AD](what-is-single-sign-on.md#linked-sign-on)

Este artigo descreve como adicionar uma aplicação não-galeria às **Aplicações Empresariais** no portal Azure sem código de escrita. Se em vez disso, procura orientação para o desenvolvedor sobre como integrar aplicações personalizadas com a AD Azure, consulte Cenários de [Autenticação para AD Azure](../develop/authentication-scenarios.md). Quando desenvolve uma aplicação que utiliza um protocolo moderno como o [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) para autenticar os utilizadores, pode registá-la com a plataforma de identidade da Microsoft utilizando a experiência de [registos](../develop/quickstart-register-app.md) da App no portal Azure.

## <a name="add-a-non-gallery-application"></a>Adicione uma aplicação não-galeria

1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com/) utilizando a sua conta de administrador de plataforma de identidade microsoft.

2. Selecione **Aplicações Empresariais** > **Nova aplicação.**

3. (Opcional, mas recomendado) Na caixa de pesquisa da **Galeria Browse Azure AD,** introduza o nome de exibição da aplicação. 

4. Selecione **Criar a sua própria aplicação**. A página De Criar a **sua própria aplicação** aparece.

   ![Adicionar aplicação](media/add-non-gallery-app/create-your-own-application.png)

5. Comece a digitar o nome do visor para a sua nova aplicação. Se houver aplicações de galeria com nomes semelhantes, aparecerão numa lista de resultados de pesquisa.

   > [!NOTE]
   > Recomendamos que utilize a versão da galeria da sua aplicação sempre que possível. Se a aplicação que pretende adicionar aparecer nos resultados da pesquisa, selecione a aplicação e ignore o resto deste procedimento.

6. O que pretende fazer com a **sua candidatura?** Esta opção é normalmente utilizada para aplicações SAML e WS-Fed.

   > [!NOTE]
   > As outras duas opções são utilizadas nos seguintes cenários:
   >* **Configurar o Proxy de Aplicação para acesso remoto seguro a uma aplicação no local** abre a página de configuração para procuração e conectores de aplicação AD Azure.
   >* **Registe uma aplicação em que está a trabalhar para integrar com a Azure AD** abre a página de registos da **App.** Esta opção é normalmente utilizada para aplicações OpenID Connect.

7. Selecione **Criar**. A página **de visão geral** da aplicação abre.

## <a name="configure-user-sign-in-properties"></a>Configurar as propriedades de início de sessão do utilizador

1. Selecione **Propriedades** para abrir o painel de propriedades para edição.

    ![Editar propriedades painele](media/add-non-gallery-app/edit-properties.png)

2. Defina as seguintes opções para determinar como os utilizadores que estão designados ou não atribuídos à aplicação podem iniciar sessão na aplicação e se um utilizador pode ver a aplicação no painel de acesso.

    - **Ativada para os utilizadores iniciarem sessão** determina se os utilizadores atribuídos à aplicação podem iniciar sessão.
    - **A atribuição do utilizador necessária** determina se os utilizadores que não estão designados para a aplicação podem iniciar sessão.
    - **Visível para o utilizador** determina se os utilizadores atribuídos a uma aplicação podem vê-la no painel de acesso e no iniciador do O365.

      Comportamento para utilizadores **atribuídos**:

       | Definições da propriedades da aplicação | | | Experiência para utilizadores atribuídos | |
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

       | Definições da propriedades da aplicação | | | Experiência para utilizadores não atribuídos | |
       |---|---|---|---|---|
       | Habilitado para os utilizadores iniciarem o seu insessão? | Atribuição do utilizador necessária? | Visível para os utilizadores? | Os utilizadores não atribuídos podem iniciar sessão? | Os utilizadores não atribuídos podem ver a aplicação?* |
       | sim | sim | sim | não  | não   |
       | sim | sim | não  | não  | não   |
       | sim | não  | sim | sim | não   |
       | sim | não  | não  | sim | não   |
       | não  | sim | sim | não  | não   |
       | não  | sim | não  | não  | não   |
       | não  | não  | sim | não  | não   |
       | não  | não  | não  | não  | não   |

     *O utilizador pode ver a aplicação no painel de acesso e no iniciador de aplicações do Office 365?

3. Para utilizar um logótipo personalizado, crie um logótipo que seja de 215 por 215 pixels e guarde-o em formato PNG. Em seguida, navegue para o seu logotipo e faça o upload.

    ![Alterar o logótipo](media/add-non-gallery-app/change-logo.png)

4. Quando terminar, selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Agora que adicionou a aplicação à sua organização Azure AD, [escolha um único método de inscrição](what-is-single-sign-on.md#choosing-a-single-sign-on-method) que deseja utilizar e consulte o artigo apropriado abaixo:

- [Configure o único sign-on baseado em SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configure o sinal único da palavra-passe](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurar o sign-on ligado](configure-linked-sign-on.md)
