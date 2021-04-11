---
title: O que é a colaboração B2B no Azure Ative Directory?
description: A colaboração B2B do Azure Active Directory suporta o acesso do utilizador convidado para que possa partilhar recursos de forma segura e colaborar com parceiros externos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 03/19/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231d3ab81c2376aa8a2a4bf182e1b5e9799e51d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669785"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>O que é o acesso do utilizador convidado na colaboração B2B do Azure Active Directory?

A colaboração business-to-business (B2B) do Azure Ative Directory (Azure AD) é uma funcionalidade dentro das Identidades Externas que permite convidar utilizadores convidados a colaborar com a sua organização. Com a colaboração B2B, pode partilhar de forma segura as aplicações e serviços da sua empresa com utilizadores convidados de qualquer outra organização, mantendo o controlo sobre os seus próprios dados corporativos. Trabalhe de forma segura com parceiros externos, grandes ou pequenos, mesmo que não tenham o Azure Active Directory ou um departamento de TI. Um simples processo de convite e resgate permite que os parceiros utilizem as suas próprias credenciais para aceder aos recursos da sua empresa. Os programadores podem utilizar as APIs de empresa para empresa do Azure Active Directory para personalizar o processo de convite ou escrever aplicações, tais como portais de inscrição self-service. Para obter informações sobre licenciamento e preços relacionados com utilizadores convidados, consulte os [preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).  

> [!IMPORTANT]
> - **A partir de 4 de janeiro de 2021, a** Google está [a depreciar o suporte de sing-in webView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver a utilizar a federação do Google ou a inscrição de self-service com o Gmail, deverá [testar as suas aplicações nativas de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).
> - **A partir de outubro de 2021, a** Microsoft deixará de apoiar o resgate de convites através da criação de contas AZure AD não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optar em autenticação de senha única por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Colaborar com parceiros com recurso às identidades deles

Com a colaboração B2B do Azure Active Directory, o parceiro utiliza a sua própria solução de gestão de identidades, pelo que a sua organização não sofre nenhuma sobrecarga administrativa externa. Os utilizadores convidados iniciam sessão nas suas aplicações e serviços com as respetivas identidades das redes sociais, da escola ou do trabalho.

- O parceiro utiliza as suas próprias identidades e credenciais, não sendo necessário o Azure Active Directory.
- Não precisa de gerir contas externas nem palavras-passe.
- Não precisa de sincronizar contas nem de gerir os ciclos de vida da conta.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Convidar facilmente os utilizadores convidados do portal AD Azure

Como administrador, pode facilmente adicionar utilizadores convidados à sua organização no portal do Azure.

- [Crie um novo utilizador convidado](b2b-quickstart-add-guest-users-portal.md) em Azure AD, semelhante ao modo como adicionaria um novo utilizador.
- Atribua utilizadores convidados a apps ou grupos.
- Envie um e-mail de convite que contenha um link de resgate ou envie um link direto para uma aplicação que pretende partilhar.

![Screenshot mostrando a página de entrada do novo convidado do convite do utilizador](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Os utilizadores convidados seguem alguns [simples passos](redemption-experience.md) de resgate para iniciar sinsus.

![Screenshot mostrando a página de permissões de revisão](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Utilizar políticas para partilhar as suas aplicações e serviços de forma segura

Pode utilizar políticas de autorização para proteger o seu conteúdo corporativo. As políticas de acesso condicional, como a autenticação de vários fatores, podem ser aplicadas:

- Ao nível do inquilino.
- Ao nível da aplicação.
- Para os utilizadores convidados específicos protegerem aplicações e dados empresariais.

![Screenshot mostrando a opção de acesso condicional](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Permitir que os proprietários de aplicações e de grupos façam a gestão dos seus próprios utilizadores convidados

Pode delegar a gestão de utilizadores convidados aos proprietários da aplicação para que possam adicionar utilizadores convidados diretamente a qualquer aplicação que pretendem partilhar, quer se trate ou não de uma aplicação da Microsoft.

- Os administradores configuram a gestão self-service de aplicações e grupos.
- Os não administradores utilizam o respetivo [Painel de Acesso](https://myapps.microsoft.com) para adicionar utilizadores convidados a aplicações ou grupos.

![Screenshot mostrando o painel de acesso para um utilizador convidado](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Personalize a experiência de embarque para utilizadores convidados B2B

Traga os seus parceiros externos a bordo de formas personalizadas às necessidades da sua organização.

- Utilize [a gestão de direitos Azure AD](../governance/entitlement-management-overview.md) para configurar políticas que [gerem o acesso a utilizadores externos](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).
- Utilize o [convite de colaboração B2B APIs](/graph/api/resources/invitation) para personalizar as suas experiências de embarque.

## <a name="integrate-with-identity-providers"></a>Integrar-se com fornecedores de identidade

A Azure AD suporta fornecedores de identidade externos como Facebook, contas microsoft, Google ou fornecedores de identidade empresarial. Pode configurar a federação com fornecedores de identidade para que os seus utilizadores externos possam iniciar scontabilidade social ou empresarial em vez de criar uma nova conta apenas para a sua aplicação. Saiba mais sobre [os fornecedores de identidades externas.](identity-providers.md)

![Screenshot mostrando a página de fornecedores de identidade](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow"></a>Criar um fluxo de utilizador de inscrição de autosserviço

Com um fluxo de utilizador de inscrição de autosserviço, pode criar uma experiência de inscrição para utilizadores externos que queiram aceder às suas apps. Como parte do fluxo de inscrição, você pode fornecer opções para diferentes fornecedores de identidade social ou empresarial, e recolher informações sobre o utilizador. Saiba mais sobre [a inscrição de self-service e como a configurar.](self-service-sign-up-overview.md)

Também pode utilizar [conectores API](api-connectors-overview.md) para integrar os fluxos de utilizador de inscrição de autosserviço com sistemas de nuvem externa. Pode conectar-se com fluxos de trabalho de aprovação personalizados, realizar verificação de identidade, validar informações fornecidas pelo utilizador e muito mais.

![Screenshot mostrando a página de fluxos do utilizador](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Passos seguintes

- [Preços das Identidades Externas](external-identities-pricing.md)
- [Adicionar utilizadores convidados de colaboração B2B no portal](add-users-administrator.md)
- [Compreender o processo de resgate de convites](redemption-experience.md)