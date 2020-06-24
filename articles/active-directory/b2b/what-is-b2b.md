---
title: O que é a colaboração B2B no Azure Ative Directory?
description: A colaboração B2B do Azure Active Directory suporta o acesso do utilizador convidado para que possa partilhar recursos de forma segura e colaborar com parceiros externos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89e1868d8e9a346d5a791c9fa8ae7682ca2b6807
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905141"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>O que é o acesso do utilizador convidado na colaboração B2B do Azure Active Directory?

A colaboração de empresa para empresa (B2B) do Azure Active Directory permite-lhe partilhar em segurança as aplicações e serviços da sua empresa com utilizadores convidados de outra organização e manter o controlo sobre os seus próprios dados empresariais. Trabalhe de forma segura com parceiros externos, grandes ou pequenos, mesmo que não tenham o Azure Active Directory ou um departamento de TI. Um simples processo de convite e resgate permite que os parceiros utilizem as suas próprias credenciais para aceder aos recursos da sua empresa. Os programadores podem utilizar as APIs de empresa para empresa do Azure Active Directory para personalizar o processo de convite ou escrever aplicações, tais como portais de inscrição self-service.

Veja o vídeo para saber como pode colaborar de forma segura com utilizadores convidados ao convidá-los para iniciar sessão nas aplicações e serviços da sua empresa através das suas próprias identidades.

O seguinte vídeo apresenta uma visão geral útil.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

   > [!IMPORTANT]
   > **A partir de 31 de março de 2021, a**Microsoft deixará de apoiar o resgate de convites através da criação de contas Ead AZure não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optar em autenticação de senha única por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Colaborar com parceiros com recurso às identidades deles

Com a colaboração B2B do Azure Active Directory, o parceiro utiliza a sua própria solução de gestão de identidades, pelo que a sua organização não sofre nenhuma sobrecarga administrativa externa.

- O parceiro utiliza as suas próprias identidades e credenciais, não sendo necessário o Azure Active Directory.
- Não precisa de gerir contas externas nem palavras-passe.
- Não precisa de sincronizar contas nem de gerir os ciclos de vida da conta.  

![Screenshot mostrando a página de membros adicionar](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Convidar utilizadores convidados através de um simples processo de convite e resgate

Os utilizadores convidados iniciam sessão nas suas aplicações e serviços com as respetivas identidades das redes sociais, da escola ou do trabalho. Se o utilizador convidado não tiver uma conta Microsoft ou uma conta AD Azure, uma delas é criada para eles quando resgatam o seu convite. 

- Convide utilizadores convidados através da identidade de e-mail à escolha deles.
- Envie uma ligação direta para uma aplicação ou envie um convite para o próprio Painel de Acesso do utilizador convidado.
- Os utilizadores convidados seguem alguns passos simples de resgate para iniciar sessão.

![Screenshot mostrando a página de permissões de revisão](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Utilizar políticas para partilhar as suas aplicações e serviços de forma segura

Pode utilizar políticas de autorização para proteger o seu conteúdo corporativo. As políticas de acesso condicional, como a autenticação de vários fatores, podem ser aplicadas:

- Ao nível do inquilino.
- Ao nível da aplicação.
- Para os utilizadores convidados específicos protegerem aplicações e dados empresariais.

![Screenshot mostrando a opção de acesso condicional](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Adicionar facilmente utilizadores convidados no portal do Azure Active Directory

Como administrador, pode facilmente adicionar utilizadores convidados à sua organização no portal do Azure.

- Crie um novo utilizador convidado no Azure Active Directory da mesma forma que adicionaria um novo utilizador.
- O utilizador convidado recebe imediatamente um convite personalizável que lhe permite iniciar sessão no respetivo Painel de Acesso.
- Os utilizadores convidados no diretório podem ser atribuídos a aplicações ou grupos.  

![Screenshot mostrando a página de entrada do novo convidado do convite do utilizador](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Permitir que os proprietários de aplicações e de grupos façam a gestão dos seus próprios utilizadores convidados

Pode delegar a gestão de utilizadores convidados aos proprietários da aplicação para que possam adicionar utilizadores convidados diretamente a qualquer aplicação que pretendem partilhar, quer se trate ou não de uma aplicação da Microsoft.

- Os administradores configuram a gestão self-service de aplicações e grupos.
- Os não administradores utilizam o respetivo [Painel de Acesso](https://myapps.microsoft.com) para adicionar utilizadores convidados a aplicações ou grupos.

![Screenshot mostrando o painel de acesso para um utilizador convidado](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Personalize a experiência de embarque para utilizadores convidados B2B

Traga os seus parceiros externos a bordo de formas personalizadas às necessidades da sua organização.

- Utilize [a gestão de direitos Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) para configurar políticas que [gerem o acesso a utilizadores externos](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users).
- Utilize o [convite de colaboração B2B APIs](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) para personalizar as suas experiências de embarque.

## <a name="integrate-with-identity-providers"></a>Integrar-se com fornecedores de identidade

A Azure AD suporta fornecedores de identidade externos como Facebook, contas microsoft, Google ou fornecedores de identidade empresarial. Pode configurar a federação com fornecedores de identidade para que os seus utilizadores externos possam iniciar scontabilidade social ou empresarial em vez de criar uma nova conta apenas para a sua aplicação. Saiba mais sobre fornecedores de identidades externas.

![Screenshot mostrando a página de fornecedores de identidade](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow-preview"></a>Criar um fluxo de utilizador de inscrição de autosserviço (Pré-visualização)

Com um fluxo de utilizador de inscrição de autosserviço, pode criar uma experiência de inscrição para utilizadores externos que queiram aceder às suas apps. Como parte do fluxo de inscrição, você pode fornecer opções para diferentes fornecedores de identidade social ou empresarial, e recolher informações sobre o utilizador. Saiba mais sobre [a inscrição de self-service e como a configurar.](self-service-sign-up-overview.md)

Também pode utilizar [conectores API](api-connectors-overview.md) para integrar os fluxos de utilizador de inscrição de autosserviço com sistemas de nuvem externa. Pode conectar-se com fluxos de trabalho de aprovação personalizados, realizar verificação de identidade, validar informações fornecidas pelo utilizador e muito mais.

![Screenshot mostrando a página de fluxos do utilizador](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Passos seguintes

- [Orientação sobre o licenciamento para a colaboração B2B do Azure Active Directory](licensing-guidance.md)
- [Adicionar utilizadores convidados de colaboração B2B no portal](add-users-administrator.md)
- [Compreender o processo de resgate de convites](redemption-experience.md)
- E, como sempre, contacte a equipa do produto para dar feedback e sugestões e debater questões através do nosso [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
