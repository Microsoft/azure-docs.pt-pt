---
title: O que é a colaboração B2B no Azure Active Directory?
description: A colaboração B2B do Azure Active Directory suporta o acesso do utilizador convidado para que possa partilhar recursos de forma segura e colaborar com parceiros externos.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 09/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18d60614a07895c7e9468d35b2d389a28129af4d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271656"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>O que é o acesso do utilizador convidado na colaboração B2B do Azure Active Directory?

A colaboração de empresa para empresa (B2B) do Azure Active Directory permite-lhe partilhar em segurança as aplicações e serviços da sua empresa com utilizadores convidados de outra organização e manter o controlo sobre os seus próprios dados empresariais. Trabalhe de forma segura com parceiros externos, grandes ou pequenos, mesmo que não tenham o Azure Active Directory ou um departamento de TI. Um simples processo de convite e resgate permite que os parceiros utilizem as suas próprias credenciais para aceder aos recursos da sua empresa. Os programadores podem utilizar as APIs de empresa para empresa do Azure Active Directory para personalizar o processo de convite ou escrever aplicações, tais como portais de inscrição self-service.

Veja o vídeo para saber como pode colaborar de forma segura com utilizadores convidados ao convidá-los para iniciar sessão nas aplicações e serviços da sua empresa através das suas próprias identidades.

O seguinte vídeo apresenta uma visão geral útil.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="collaborate-with-any-partner-using-their-identities"></a>Colaborar com parceiros com recurso às identidades deles
Com a colaboração B2B do Azure Active Directory, o parceiro utiliza a sua própria solução de gestão de identidades, pelo que a sua organização não sofre nenhuma sobrecarga administrativa externa. 
- O parceiro utiliza as suas próprias identidades e credenciais, não sendo necessário o Azure Active Directory. 
- Não precisa de gerir contas externas nem palavras-passe. 
- Não precisa de sincronizar contas nem de gerir os ciclos de vida da conta.  

![Captura de tela mostrando a página adicionar membros](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Convidar utilizadores convidados através de um simples processo de convite e resgate
Os utilizadores convidados iniciam sessão nas suas aplicações e serviços com as respetivas identidades das redes sociais, da escola ou do trabalho. Se o utilizador convidado não tiver uma conta Microsoft ou uma conta do Azure Active Directory, ser-lhe-á criada uma automaticamente quando resgatar o convite. 
- Convide utilizadores convidados através da identidade de e-mail à escolha deles.
- Envie uma ligação direta para uma aplicação ou envie um convite para o próprio Painel de Acesso do utilizador convidado. 
- Os utilizadores convidados seguem alguns passos simples de resgate para iniciar sessão.

![Captura de tela mostrando a página examinar permissões](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Utilizar políticas para partilhar as suas aplicações e serviços de forma segura
Você pode usar políticas de autorização para proteger seu conteúdo corporativo. As políticas de acesso condicional, como a autenticação multifator, podem ser impostas:
- Ao nível do inquilino.
- Ao nível da aplicação.
- Para os utilizadores convidados específicos protegerem aplicações e dados empresariais.

![Captura de tela mostrando a opção de acesso condicional](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>Adicionar facilmente utilizadores convidados no portal do Azure Active Directory

Como administrador, pode facilmente adicionar utilizadores convidados à sua organização no portal do Azure.
- Crie um novo utilizador convidado no Azure Active Directory da mesma forma que adicionaria um novo utilizador.
- O utilizador convidado recebe imediatamente um convite personalizável que lhe permite iniciar sessão no respetivo Painel de Acesso.
- Os utilizadores convidados no diretório podem ser atribuídos a aplicações ou grupos.  

![Captura de tela mostrando a página nova entrada de convite de usuário convidado](media/what-is-b2b/adding-b2b-users-admin.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Permitir que os proprietários de aplicações e de grupos façam a gestão dos seus próprios utilizadores convidados

Pode delegar a gestão de utilizadores convidados aos proprietários da aplicação para que possam adicionar utilizadores convidados diretamente a qualquer aplicação que pretendem partilhar, quer se trate ou não de uma aplicação da Microsoft. 
 - Os administradores configuram a gestão self-service de aplicações e grupos.
 - Os não administradores utilizam o respetivo [Painel de Acesso](https://myapps.microsoft.com) para adicionar utilizadores convidados a aplicações ou grupos.

![Captura de tela mostrando o painel de acesso para um usuário convidado](media/what-is-b2b/access-panel-manage-app.png)

## <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Utilizar APIs e código de exemplo para criar facilmente aplicações para integrar

Integre os seus parceiros externos de forma personalizada de acordo com as necessidades da sua organização.
- Utilize as [APIs de convite da colaboração B2B](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) para personalizar as suas experiências de integração, incluindo a criação de portais de inscrição self-service. 
- Use o código de exemplo que fornecemos para um portal de autoatendimento [no GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![Captura de tela mostrando o portal de inscrição de exemplo](media/what-is-b2b/sign-up-portal.png)

## <a name="next-steps"></a>Passos seguintes

- [Orientação sobre o licenciamento para a colaboração B2B do Azure Active Directory](licensing-guidance.md)
- [Adicionar utilizadores convidados de colaboração B2B no portal](add-users-administrator.md)
- [Compreender o processo de resgate de convites](redemption-experience.md)
- E, como sempre, contacte a equipa do produto para dar feedback e sugestões e debater questões através do nosso [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
