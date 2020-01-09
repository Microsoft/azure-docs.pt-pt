---
title: Perguntas frequentes sobre colaboração B2B-Azure Active Directory | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre Azure Active Directory colaboração B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 11/07/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8be047caf8631cda4e48b7bf10987db616a9b86f
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75608613"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Perguntas frequentes sobre colaboração B2B Azure Active Directory

Essas perguntas frequentes (FAQs) sobre a colaboração B2B (entre empresas) do Azure Active Directory (Azure AD) são atualizadas periodicamente para incluir novos tópicos.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Podemos personalizar nossa página de entrada para que seja mais intuitiva para nossos usuários convidados de colaboração B2B?
Totalmente! Veja nossa [postagem de blog sobre este recurso](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Para obter mais informações sobre como personalizar a página de entrada da sua organização, consulte [Adicionar identidade visual da empresa às páginas de entrada e do painel de acesso](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Os usuários de colaboração B2B podem acessar o SharePoint Online e o OneDrive?
Sim. No entanto, a capacidade de Pesquisar usuários convidados existentes no SharePoint online usando o seletor de pessoas está **desativada** por padrão. Para ativar a opção para procurar usuários convidados existentes, defina **ShowPeoplePickerSuggestionsForGuestUsers** como **on**. Você pode ativar essa configuração em qualquer nível de locatário ou no nível de conjunto de sites. Você pode alterar essa configuração usando os cmdlets Set-SPOTenant e Set-SPOSite. Com esses cmdlets, os membros podem pesquisar todos os usuários convidados existentes no diretório. As alterações no escopo do locatário não afetam os sites do SharePoint Online que já foram provisionados.

### <a name="is-the-csv-upload-feature-still-supported"></a>O recurso de upload de CSV ainda tem suporte?
Sim. Para obter mais informações sobre como usar o recurso de upload de arquivo. csv, consulte [Este exemplo do PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Como posso personalizar meus emails de convite?
Você pode personalizar quase tudo sobre o processo do emissor do convite usando as [APIs de convite B2B](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Os usuários convidados podem redefinir o método de autenticação multifator?
Sim. Os usuários convidados podem redefinir o método de autenticação multifator da mesma forma que os usuários normais.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Qual organização é responsável pelas licenças de autenticação multifator?
A organização que convida executa a autenticação multifator. A organização que está convidando deve garantir que a organização tenha licenças suficientes para seus usuários B2B que estão usando a autenticação multifator.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>E se uma organização parceira já tiver a autenticação multifator configurada? Podemos confiar em sua autenticação multifator e não usar nossa própria autenticação multifator?
Não há suporte para este recurso no momento. Se o acesso aos recursos de sua organização exigir a autenticação multifator, a organização parceira precisará se registrar para a autenticação multifator na organização (a convite).

### <a name="how-can-i-use-delayed-invitations"></a>Como posso usar convites atrasados?
Uma organização pode querer adicionar usuários de colaboração B2B, provisioná-los para aplicativos conforme necessário e, em seguida, enviar convites. Você pode usar a API de convite de colaboração B2B para personalizar o fluxo de trabalho de integração.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Posso tornar os usuários convidados visíveis na lista de endereços global do Exchange?
Sim. Os objetos convidados não são visíveis na GAL (lista de endereços global) de sua organização por padrão, mas você pode usar Azure Active Directory PowerShell para torná-los visíveis. Veja posso [tornar os objetos convidados visíveis na lista de endereços global?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Posso tornar um usuário convidado um administrador limitado?
Claramente. Para obter mais informações, consulte [adicionando usuários convidados a uma função](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>A colaboração B2B do Azure AD permite que usuários B2B acessem o portal do Azure?
A menos que um usuário receba a função de administrador limitado, os usuários de colaboração B2B não precisarão acessar o portal do Azure. No entanto, os usuários de colaboração B2B que recebem a função de administrador limitado podem acessar o Portal. Além disso, se um usuário convidado ao qual não foi atribuída uma dessas funções de administrador acessar o portal, o usuário poderá acessar determinadas partes da experiência. A função de usuário convidado tem algumas permissões no diretório.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Posso bloquear o acesso ao portal do Azure para usuários convidados?

Sim! Você pode criar uma política de acesso condicional que impede que todos os usuários convidados e externos acessem o portal do Azure. Ao configurar essa política, tenha cuidado para evitar o bloqueio acidental do acesso a membros e administradores.

1. Entre em seu [portal do Azure](https://portal.azure.com/) como um administrador de segurança ou um administrador de acesso condicional.
2. No portal do Azure, selecione **Azure Active Directory**. 
3. Em **gerenciar**, selecione **segurança**.
4. Em **proteger**, selecione **acesso condicional**. Selecione **nova política**.
5. Na página **novo** , na caixa de texto **nome** , insira um nome para a política (por exemplo, "bloquear convidados de acessar o portal").
6. Em **Atribuições**, selecione **Utilizadores e grupos**.
7. Na guia **incluir** , escolha **Selecionar usuários e grupos**e, em seguida, selecione **todos os usuários convidados e externos (versão prévia)** .
9. Selecione **Done** (Concluído).
10. Na página **novo** , na seção **atribuições** , selecione **aplicativos de nuvem ou ações**.
11. Na página **aplicativos de nuvem ou ações** , escolha **selecionar aplicativos**e escolha **selecionar**.
12. Na página **Selecionar**, selecione **Microsoft Azure Management** e, em seguida, selecione **Selecionar**.
13. Na página **aplicativos de nuvem ou ações** , selecione **concluído**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>A colaboração B2B do Azure AD dá suporte a autenticação multifator e contas de email do consumidor?
Sim. A autenticação multifator e as contas de email do consumidor têm suporte para a colaboração B2B do Azure AD.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Você dá suporte à redefinição de senha para usuários de colaboração B2B do Azure AD?
Se o seu locatário do Azure AD for o diretório base de um usuário, você poderá [redefinir a senha do usuário](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) do portal do Azure. Mas você não pode redefinir diretamente uma senha para um usuário convidado que entra com uma conta que é gerenciada por outro diretório do Azure AD ou provedor de identidade externo. Somente o usuário convidado ou um administrador no diretório base do usuário pode redefinir a senha. Aqui estão alguns exemplos de como a redefinição de senha funciona para usuários convidados:
 
* Os usuários convidados que entram com um conta Microsoft (por exemplo, guestuser@live.com) podem redefinir suas próprias senhas usando conta Microsoft SSPR (redefinição de senha de autoatendimento). Consulte [como redefinir sua senha de conta Microsoft](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Os usuários convidados que entram com uma conta do Google ou outro provedor de identidade externo podem redefinir suas próprias senhas usando o método SSPR do provedor de identidade. Por exemplo, um usuário convidado com a conta do Google guestuser@gmail.com pode redefinir sua senha seguindo as instruções em [alterar ou redefinir sua senha](https://support.google.com/accounts/answer/41078).
* Se o locatário de identidade for um locatário JIT (just-in-time) ou "viral" (ou seja, um locatário do Azure separado e não gerenciado), somente o usuário convidado poderá redefinir sua senha. Às vezes, uma organização [assumirá o gerenciamento de locatários viral](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) que são criados quando os funcionários usam seus endereços de email de trabalho para se inscreverem nos serviços. Depois que a organização assume um locatário viral, somente um administrador na organização pode redefinir a senha do usuário ou habilitar o SSPR. Se necessário, como a organização que convida, você pode remover a conta de usuário convidado do seu diretório e reenviar um convite.
* Se o diretório base do usuário convidado for seu locatário do Azure AD, você poderá redefinir a senha do usuário. Por exemplo, você pode ter criado um usuário ou sincronizado um usuário a partir de seu Active Directory local e definir seu UserType como convidado. Como esse usuário é hospedado em seu diretório, você pode redefinir sua senha do portal do Azure.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>O Microsoft Dynamics 365 fornece suporte online para a colaboração B2B do Azure AD?
Sim, o Dynamics 365 (online) dá suporte à colaboração B2B do Azure AD. Para obter mais informações, consulte o artigo do Dynamics 365 [convidar usuários com a colaboração B2B do Azure ad](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Qual é o tempo de vida de uma senha inicial para um usuário de colaboração B2B criado recentemente?
O Azure AD tem um conjunto fixo de requisitos de caracteres, intensidade de senha e bloqueio de conta que se aplicam igualmente a todas as contas de usuário de nuvem do Azure AD. As contas de usuário de nuvem são contas que não são federadas com outro provedor de identidade, como 
* Conta Microsoft
* Facebook
* Serviços de Federação do Active Directory
* Outro locatário de nuvem (para colaboração B2B)

Para contas federadas, a política de senha depende da política aplicada na locação local e nas configurações de conta Microsoft do usuário.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Uma organização pode querer ter experiências diferentes em seus aplicativos para usuários de locatários e usuários convidados. Há diretrizes padrão para isso? A presença da declaração do provedor de identidade é o modelo correto a ser usado?
Um usuário convidado pode usar qualquer provedor de identidade para autenticar. Para obter mais informações, consulte [Propriedades de um usuário de colaboração B2B](user-properties.md). Use a propriedade **UserType** para determinar a experiência do usuário. A Declaração **UserType** não está incluída no token no momento. Os aplicativos devem usar a API do Graph para consultar o diretório do usuário e obter o UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Onde posso encontrar uma comunidade de colaboração B2B para compartilhar soluções e enviar ideias?
Estamos constantemente ouvindo seus comentários para melhorar a colaboração B2B. Compartilhe seus cenários de usuário, práticas recomendadas e o que você gosta sobre a colaboração B2B do Azure AD. Participe da discussão na [comunidade de tecnologia da Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Também convidamos você a enviar suas ideias e votar em recursos futuros em [ideias de colaboração B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Podemos enviar um convite que é resgatado automaticamente, para que o usuário esteja apenas "pronto para começar"? Ou o usuário sempre precisa clicar na URL de resgate?
Você pode convidar outros usuários na organização parceira usando a interface do usuário, scripts do PowerShell ou APIs. Em seguida, você pode enviar ao usuário convidado um link direto para um aplicativo compartilhado. Na maioria dos casos, não há mais necessidade de abrir o convite por email e clicar em uma URL de resgate. Confira [Azure Active Directory resgate de convite de colaboração B2B](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Como funciona a colaboração B2B quando o parceiro convidado está usando a Federação para adicionar sua própria autenticação local?
Se o parceiro tiver um locatário do Azure AD federado à infraestrutura de autenticação local, o SSO (logon único) local será obtido automaticamente. Se o parceiro não tiver um locatário do Azure AD, uma conta do Azure AD será criada para novos usuários. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Pensei que o Azure AD B2B não aceitou os endereços de email gmail.com e outlook.com, e esse B2C foi usado para esses tipos de contas?
Estamos removendo as diferenças entre a colaboração B2B e Business-to-Consumer (B2C) em termos de quais identidades têm suporte. A identidade usada não é um bom motivo para escolher entre usar B2B ou usar B2C. Para obter informações sobre como escolher sua opção de colaboração, consulte [comparar a colaboração B2B e B2C em Azure Active Directory](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Quais aplicativos e serviços dão suporte aos usuários convidados do Azure B2B?
Todos os aplicativos integrados ao Azure AD podem dar suporte a usuários convidados do Azure B2B, mas eles devem usar um ponto de extremidade configurado como um locatário para autenticar usuários convidados. Talvez você também precise [Personalizar as declarações](claims-mapping.md) no token SAML que é emitido quando um usuário convidado é autenticado no aplicativo. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Podemos forçar a autenticação multifator para usuários convidados B2B se nossos parceiros não tiverem a autenticação multifator?
Sim. Para obter mais informações, consulte [acesso condicional para usuários de colaboração B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>No SharePoint, você pode definir uma lista "permitir" ou "negar" para usuários externos. Podemos fazer isso no Azure?
Sim. A colaboração B2B do Azure AD dá suporte a listas de permissões e listas de negações. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Quais licenças precisamos usar o Azure AD B2B?
Para obter informações sobre quais licenças sua organização precisa para usar o Azure AD B2B, consulte [diretrizes de licenciamento de colaboração b2b Azure Active Directory](licensing-guidance.md).

### <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)

