---
title: B2B colaboração FAQs - Diretório Ativo Azure [ Diretório Ativo Azure ] Microsoft Docs
description: Obtenha respostas a perguntas frequentes sobre a colaboração azure Ative Directory B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92c3e0d77a26db406e24d6d2fa07e96349613634
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80050809"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>FaQs de colaboração azure Ative Directory B2B

Estas perguntas frequentes (PERGUNTAS) sobre a colaboração azure Ative Directory (Azure AD) business-to-business (B2B) são periodicamente atualizadas para incluir novos tópicos.

   > [!IMPORTANT]
   > A partir de 31 de março de **2021,** a Microsoft deixará de apoiar o resgate de convites através da criação de contas AD Azure não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optarem pela autenticação de código de acesso único por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Podemos personalizar a nossa página de sessão para que seja mais intuitivo para os nossos utilizadores convidados de colaboração B2B?
Sem dúvida! Consulte a nossa publicação de [blog sobre esta funcionalidade.](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/) Para obter mais informações sobre como personalizar a página de acesso da sua organização, consulte adicionar marcas da [empresa para iniciar sessão e páginas do Painel](../fundamentals/customize-branding.md)de Acesso .

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Os utilizadores de colaboração B2B podem aceder ao SharePoint Online e ao OneDrive?
Sim. No entanto, a capacidade de pesquisar os utilizadores de hóspedes existentes no SharePoint Online, utilizando o picker de pessoas, está **desligada** por defeito. Para ativar a opção de procurar utilizadores de hóspedes existentes, detete **showPeoplePickerSuggestionsForGuestUsers** para **On**. Você pode ligar esta configuração ao nível do inquilino ou ao nível da recolha do site. Pode alterar esta definição utilizando os cmdlets Set-SPOTenant e Set-SPOSite. Com estes cmdlets, os membros podem pesquisar todos os utilizadores convidados existentes no diretório. As alterações no âmbito do arrendatário não afetam os sites SharePoint Online que já foram provisionados.

### <a name="is-the-csv-upload-feature-still-supported"></a>A funcionalidade de upload do CSV ainda está suportada?
Sim. Para obter mais informações sobre a utilização da funcionalidade de upload de ficheiros .csv, consulte [esta amostra PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Como posso personalizar os meus e-mails de convite?
Você pode personalizar quase tudo sobre o processo de convite usando as [APIs convite B2B](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Os utilizadores convidados podem redefinir o seu método de autenticação multifactor?
Sim. Os utilizadores podem redefinir o seu método de autenticação multifactor da mesma forma que os utilizadores regulares.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Que organização é responsável por licenças de autenticação multifactor?
A organização convidativa realiza a autenticação multi-factor. A organização convidativa deve certificar-se de que a organização tem licenças suficientes para os seus utilizadores B2B que estão a usar a autenticação multifactor.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>E se uma organização parceira já tiver a autenticação multifactor configurada? Podemos confiar na sua autenticação multi-factor, e não usar a nossa própria autenticação multifactor?
Esta funcionalidade não é suportada atualmente. Se o acesso aos recursos da sua organização necessitar de autenticação multifactor, a organização parceira terá de se registar para autenticação multifactor na sua (a organização convidativa).

### <a name="how-can-i-use-delayed-invitations"></a>Como posso usar convites atrasados?
Uma organização pode querer adicionar utilizadores de colaboração B2B, disponibilizá-los a aplicações conforme necessário, e depois enviar convites. Você pode usar o convite de colaboração B2B API para personalizar o fluxo de trabalho de embarque.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Posso tornar os utilizadores convidados visíveis na Lista de Endereços Globais do Exchange?
Sim. Os objetos de hóspedes não são visíveis na lista de endereços globais da sua organização (GAL) por padrão, mas você pode usar o Azure Ative Directory PowerShell para torná-los visíveis. Posso [tornar os objetos convidados visíveis na lista de endereços globais?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Posso fazer de um utilizador convidado um administrador limitado?
Com certeza. Para mais informações, consulte [Adicionar utilizadores convidados a um papel](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>A colaboração Azure AD B2B permite aos utilizadores do B2B aceder ao portal Azure?
A menos que um utilizador seja atribuído ao cargo de administrador limitado, os utilizadores de colaboração B2B não exigirão acesso ao portal Azure. No entanto, os utilizadores de colaboração B2B que lhe forem atribuídos o papel de administrador limitado podem aceder ao portal. Além disso, se um utilizador convidado que não tenha sido atribuído uma destas funções de administrador acede ao portal, o utilizador poderá ter acesso a certas partes da experiência. O papel de utilizador convidado tem algumas permissões no diretório.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Posso bloquear o acesso ao portal Azure para utilizadores convidados?

Sim! Pode criar uma política de Acesso Condicional que bloqueie todos os utilizadores convidados e externos de aceder ao portal Azure. Quando configurar esta política, tenha cuidado para evitar bloquear acidentalmente o acesso a membros e administradores.

1. Inscreva-se no seu [portal Azure](https://portal.azure.com/) como administrador de segurança ou administrador de Acesso Condicional.
2. No portal do Azure, selecione **Azure Active Directory**. 
3. Under **Manage**, selecione **Security**.
4. Sob **proteção,** selecione **Acesso Condicional**. Selecione **Nova política.**
5. Na página **Nova,** na caixa de texto **Name,** insira um nome para a política (por exemplo, "Bloqueie os hóspedes de aceder ao portal").
6. Em **Atribuições**, selecione **Utilizadores e grupos**.
7. No separador **Incluir,** escolha **Utilizadores e grupos Selecionados**e, em seguida, selecione **Todos os utilizadores convidados e externos (Pré-visualização)**.
9. Selecione **Done** (Concluído).
10. Na página **Nova,** na secção **Assignments,** selecione **aplicações ou ações cloud**.
11. Na página **de aplicações ou ações da Cloud,** escolha **as aplicações Select,** e depois escolha **Select**.
12. Na página **Selecionar**, selecione **Microsoft Azure Management** e, em seguida, selecione **Selecionar**.
13. Na página de **aplicações ou ações cloud,** selecione **Done**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>A colaboração azure AD B2B suporta a autenticação de vários fatores e as contas de email dos consumidores?
Sim. A autenticação multifactor e as contas de email do consumidor são ambas suportadas para a colaboração Azure AD B2B.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Suporta o reset de palavra-passe para utilizadores de colaboração Azure AD B2B?
Se o seu inquilino Azure AD for o diretório inicial para um utilizador, pode [redefinir a palavra-passe do utilizador](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) a partir do portal Azure. Mas não é possível redefinir diretamente uma palavra-passe para um utilizador convidado que se instinure com uma conta gerida por outro diretório da AD Azure ou fornecedor de identidade externa. Apenas o utilizador convidado ou um administrador no diretório inicial do utilizador podem redefinir a palavra-passe. Aqui estão alguns exemplos de como o reset de palavra-passe funciona para os utilizadores convidados:
 
* Os utilizadores convidados que iniciarem guestuser@live.comsessão com uma conta Microsoft (por exemplo) podem redefinir as suas próprias palavras-passe utilizando o reset de palavra-passe de autosserviço da conta da Microsoft (SSPR). Ver [Como redefinir a sua palavra-passe da conta microsoft](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Os utilizadores convidados que acedam a uma conta da Google ou a outro fornecedor de identidade externa podem redefinir as suas próprias palavras-passe utilizando o método SSPR do seu fornecedor de identidade. Por exemplo, um utilizador convidado guestuser@gmail.com com a conta Google pode redefinir a sua palavra-passe seguindo as instruções em [Alterar ou redefinir a sua palavra-passe](https://support.google.com/accounts/answer/41078).
* Se o inquilino de identidade for um inquilino justo (JIT) ou "viral" (o que significa que é um inquilino Azure separado e não gerido), apenas o utilizador hóspede pode redefinir a sua palavra-passe. Por vezes, uma organização assume a [gestão de inquilinos virais](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) que são criados quando os funcionários usam os seus endereços de e-mail de trabalho para se inscreverem para serviços. Depois de a organização assumir um inquilino viral, apenas um administrador dessa organização pode redefinir a palavra-passe do utilizador ou ativar o SSPR. Se necessário, como organização convidativa, pode remover a conta de utilizador convidado do seu diretório e reenviar um convite.

* Se o diretório do utilizador convidado for o seu inquilino Azure AD, pode redefinir a palavra-passe do utilizador. Por exemplo, pode ter criado um utilizador ou sincronizado um utilizador a partir do seu Diretório Ativo no local e definir o seu UserType para O Convidado. Como este utilizador está alojado no seu diretório, pode redefinir a sua palavra-passe a partir do portal Azure.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>O Microsoft Dynamics 365 fornece suporte online para a colaboração Azure AD B2B?
Sim, a Dynamics 365 (online) suporta a colaboração Azure AD B2B. Para mais informações, consulte o artigo Da Dynamics 365 [Convidar utilizadores com colaboração Azure AD B2B.](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration)

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Qual é a vida útil de uma senha inicial para um recém-criado utilizador de colaboração B2B?
A Azure AD tem um conjunto fixo de requisitos de caráter, resistência de senha e bloqueio de conta que se aplicam igualmente a todas as contas de utilizadores da nuvem Azure AD. As contas de utilizadores na nuvem são contas que não são federadas com outro fornecedor de identidade, como 
* Conta Microsoft
* Facebook
* Serviços de Federação do Active Directory
* Outro inquilino em nuvem (para colaboração B2B)

Para contas federadas, a política de passwords depende da política que é aplicada no arrendamento no local e nas definições da conta Microsoft do utilizador.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Uma organização pode querer ter experiências diferentes nas suas aplicações para utilizadores de inquilinos e utilizadores convidados. Há orientação padrão para isto? A presença do fornecedor de identidade alega o modelo correto a utilizar?
Um utilizador convidado pode usar qualquer fornecedor de identidade para autenticar. Para mais informações, consulte [Propriedades de um utilizador de colaboração B2B.](user-properties.md) Utilize a propriedade **UserType** para determinar a experiência do utilizador. A alegação **userType** não está atualmente incluída no símbolo. As aplicações devem utilizar a API do Microsoft Graph para consultar o diretório para o utilizador e para obter o UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Onde posso encontrar uma comunidade de colaboração B2B para partilhar soluções e apresentar ideias?
Estamos constantemente a ouvir o teu feedback, para melhorar a colaboração b2B. Por favor, partilhe os seus cenários de utilizador, as melhores práticas e o que gosta na colaboração Azure AD B2B. Junte-se à discussão na [Microsoft Tech Community.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)
 
Convidamo-lo também a apresentar as suas ideias e a votar em futuras funcionalidades no [B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Podemos enviar um convite que seja automaticamente reembolsado, para que o utilizador esteja apenas "pronto para ir"? Ou o utilizador tem sempre de clicar no URL de resgate?
Pode convidar outros utilizadores na organização parceira utilizando os scripts UI, PowerShell ou APIs. Em seguida, pode enviar ao utilizador convidado um link direto para uma aplicação partilhada. Na maioria dos casos, já não há necessidade de abrir o convite por e-mail e clicar num URL de redenção. Ver [Azure Ative Directory B2B colaboração convite redenção](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Como funciona a colaboração b2B quando o parceiro convidado está a usar a federação para adicionar a sua própria autenticação no local?
Se o parceiro tiver um inquilino Azure AD federado para a infraestrutura de autenticação no local, no local é automaticamente alcançado um único sinal on (SSO) no local. Se o parceiro não tiver um inquilino DaD Azure, é criada uma conta Azure AD para novos utilizadores. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Pensei que o Azure AD B2B não aceitava gmail.com e outlook.com endereços de e-mail, e que o B2C era usado para esse tipo de contas?
Estamos a eliminar as diferenças entre a colaboração B2B e a colaboração entre o B2B e o consumidor (B2C) em termos de apoio das identidades. A identidade usada não é uma boa razão para escolher entre usar B2B ou usar B2C. Para obter informações sobre a escolha da sua opção de colaboração, consulte [Compare a colaboração B2B e a B2C no Diretório Ativo Azure.](compare-with-b2c.md)

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Quais as aplicações e serviços que suportam os utilizadores convidados do Azure B2B?
Todas as aplicações integradas em Azure AD podem suportar utilizadores convidados do Azure B2B, mas devem utilizar um ponto final criado como inquilino para autenticar os utilizadores convidados. Também poderá ser necessário [personalizar as reclamações](claims-mapping.md) no token SAML que é emitida quando um utilizador convidado autentica a aplicação. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Podemos forçar a autenticação de vários fatores para os utilizadores convidados b2B se os nossos parceiros não tiverem a autenticação multifactor?
Sim. Para mais informações, consulte Acesso Condicional para utilizadores de [colaboração B2B.](conditional-access.md)

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>No SharePoint, pode definir uma lista de "permitir" ou "negar" para utilizadores externos. Podemos fazer isto em Azure?
Sim. Os suportes de colaboração Azure AD B2B permitem listas e listas de negação. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Que licenças precisamos para usar o Azure AD B2B?
Para obter informações sobre quais as licenças que a sua organização precisa para utilizar o Azure AD B2B, consulte a orientação de licenciamento de [colaboração Azure Ative Directory B2B.](licensing-guidance.md)

### <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)

