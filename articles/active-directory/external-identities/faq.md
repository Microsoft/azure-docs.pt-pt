---
title: Colaboração B2B FAQs - Azure Ative Directory | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre a colaboração do Azure Ative Directory B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3308c2263f80a0772a389900e08c81cfe8da32a2
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952631"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Ative Directory B2B colaboração FAQs

Estas perguntas frequentes (FAQs) sobre a colaboração business-to-business (Azure AD) do Azure Ative Directory (Azure AD) são periodicamente atualizadas para incluir novos tópicos.

> [!IMPORTANT]
> - **A partir de 4 de janeiro de 2021, a** Google está [a depreciar o suporte de sing-in webView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver a utilizar a federação do Google ou a inscrição de self-service com o Gmail, deverá [testar as suas aplicações nativas de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).
> - **A partir de outubro de 2021, a** Microsoft deixará de apoiar o resgate de convites através da criação de contas AZure AD não geridas e inquilinos para cenários de colaboração B2B. Em preparação, encorajamos os clientes a optar em autenticação de senha única por [e-mail.](one-time-passcode.md) Congratulamo-nos com o seu feedback sobre esta funcionalidade de pré-visualização pública e estamos entusiasmados por criar ainda mais formas de colaborar.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Podemos personalizar a nossa página de inscrição para que seja mais intuitiva para os nossos utilizadores convidados de colaboração B2B?
Sem dúvida! Consulte a nossa [publicação de blog sobre esta funcionalidade.](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/) Para obter mais informações sobre como personalizar a página de insusição da sua organização, consulte [a marca Add company para iniciar sing e as páginas do Painel de Acesso.](../fundamentals/customize-branding.md)

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Os utilizadores de colaboração B2B podem aceder ao SharePoint Online e ao OneDrive?
Sim. No entanto, a capacidade de procurar utilizadores convidados existentes no SharePoint Online utilizando o picker de pessoas está **desligada** por padrão. Para ligar a opção de pesquisa de utilizadores convidados existentes, desempeciumos **o ShowPeoplePickerSuggestionsForGuestUsers** para **On**. Você pode ligar esta definição ao nível do inquilino ou ao nível de recolha do site. Pode alterar esta definição utilizando os Set-SPOTenant e Set-SPOSite cmdlets. Com estes cmdlets, os membros podem pesquisar todos os utilizadores convidados existentes no diretório. As alterações no âmbito do inquilino não afetam os sites SharePoint Online que já foram a provisionados.

### <a name="is-the-csv-upload-feature-still-supported"></a>A funcionalidade de upload do CSV ainda está suportada?
Sim. Para obter mais informações sobre a utilização da função de upload de ficheiros .csv, consulte [esta amostra PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Como posso personalizar os meus e-mails de convite?
Você pode personalizar quase tudo sobre o processo de convite usando o [convite B2B APIs](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Os utilizadores convidados podem redefinir o seu método de autenticação multi-factor?
Sim. Os utilizadores convidados podem redefinir o seu método de autenticação multi-factor da mesma forma que os utilizadores regulares.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Que organização é responsável por licenças de autenticação de vários fatores?
A organização convidativa realiza a autenticação de vários fatores. A organização convidativa deve certificar-se de que a organização tem licenças suficientes para os seus utilizadores B2B que estão a utilizar a autenticação multi-factor.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>E se uma organização parceira já tiver a autenticação multi-factor configurada? Podemos confiar na autenticação de vários fatores e não usar a nossa própria autenticação multi-factor?
Esta funcionalidade não é suportada atualmente. Se o acesso aos recursos da sua organização necessitar de autenticação multi-factor, a organização parceira terá de se registar para autenticação multi-fator na sua organização (a convidativa).

### <a name="how-can-i-use-delayed-invitations"></a>Como posso usar convites atrasados?
Uma organização pode querer adicionar utilizadores de colaboração B2B, fornecendo-os às aplicações conforme necessário, e depois enviar convites. Você pode usar o convite de colaboração B2B API para personalizar o fluxo de trabalho de embarque.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Posso tornar os utilizadores convidados visíveis na Lista de Endereços Globais do Exchange?
Sim. Os objetos convidados não estão visíveis na lista global de endereços (GAL) da sua organização por padrão, mas pode usar o Azure Ative Directory PowerShell para os tornar visíveis. Veja [se posso tornar os objetos convidados visíveis na lista global de endereços?](/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Posso fazer de um utilizador convidado um administrador limitado?
Claro que sim. Para obter mais informações, consulte [adicionar utilizadores convidados a um papel.](add-guest-to-role.md)

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>A colaboração Azure AD B2B permite aos utilizadores de B2B aceder ao portal Azure?
A menos que um utilizador seja atribuído ao papel de administrador limitado, os utilizadores de colaboração B2B não exigirão acesso ao portal Azure. No entanto, os utilizadores de colaboração B2B a quem for atribuído o papel de administrador limitado podem aceder ao portal. Além disso, se um utilizador convidado que não lhe foi atribuído uma destas funções de administração aceder ao portal, o utilizador poderá aceder a determinadas partes da experiência. A função de utilizador convidado tem algumas permissões no diretório.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Posso bloquear o acesso ao portal Azure para os utilizadores convidados?

Sim! Pode criar uma política de Acesso Condicional que impede todos os utilizadores convidados e externos de acederem ao portal Azure. Ao configurar esta política, tenha cuidado para evitar bloquear acidentalmente o acesso aos membros e administradores.

1. Inscreva-se no seu [portal Azure](https://portal.azure.com/) como administrador de segurança ou administrador de acesso condicional.
2. No portal do Azure, selecione **Azure Active Directory**. 
3. Em **Gestão**, **selecione Security**.
4. Em **Protect**, selecione **Acesso Condicional**. Selecione **Nova política**.
5. Na página **Nova,** na caixa de texto **Name,** insira um nome para a política (por exemplo, "Bloquear os hóspedes de acederem ao portal").
6. Em **Atribuições**, selecione **Utilizadores e grupos**.
7. No **separador Incluir,** escolha **utilizadores e grupos selete** e, em seguida, selecione **Todos os utilizadores convidados e externos (Preview)**.
9. Selecione **Concluído**.
10. Na página **Nova,** na secção **Atribuições,** selecione **aplicações cloud ou ações**.
11. Na página de **aplicações ou ações** cloud, escolha **selecione apps** e, em seguida, escolha **Selecione**.
12. Na página **Selecionar**, selecione **Microsoft Azure Management** e, em seguida, selecione **Selecionar**.
13. Na página de **aplicações ou ações** cloud, selecione **'Feito'.**

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>A colaboração Azure AD B2B suporta a autenticação de vários fatores e as contas de e-mail do consumidor?
Sim. A autenticação multi-factor e as contas de e-mail do consumidor são ambas suportadas pela colaboração Azure AD B2B.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Suporta o reset da palavra-passe para utilizadores de colaboração Azure AD B2B?
Se o seu inquilino Azure AD for o diretório de casa de um utilizador, pode [redefinir a palavra-passe do utilizador](../fundamentals/active-directory-users-reset-password-azure-portal.md) a partir do portal Azure. Mas não é possível redefinir diretamente uma palavra-passe para um utilizador convidado que se inscreve com uma conta que é gerida por outro diretório AD Azure ou fornecedor de identidade externa. Apenas o utilizador convidado ou um administrador no diretório do utilizador pode redefinir a palavra-passe. Aqui estão alguns exemplos de como o reset da palavra-passe funciona para os utilizadores convidados:
 
* Os utilizadores convidados de um inquilino AD Azure que esteja marcado como "Guest" (UserType==Guest) não podem registar-se para SSPR através [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) de . Este tipo de utilizador convidado só pode realizar SSPR através [https://aka.ms/sspr](https://aka.ms/sspr) de . 
* Os utilizadores convidados que iniciarem sômposições com uma conta Microsoft (por guestuser@live.com exemplo) podem redefinir as suas próprias palavras-passe utilizando o reset da palavra-passe de autosserviço da conta Microsoft (SSPR). Veja [como redefinir a sua palavra-passe da conta Microsoft.](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password)
* Os utilizadores convidados que iniciarem sômposições com uma conta Google ou outro fornecedor de identidade externo podem redefinir as suas próprias palavras-passe utilizando o método SSPR do seu fornecedor de identidade. Por exemplo, um utilizador convidado com a conta Google pode redefinir a guestuser@gmail.com sua palavra-passe seguindo as instruções no [Alterar ou redefinir a sua palavra-passe.](https://support.google.com/accounts/answer/41078)
* Se o inquilino de identidade for um inquilino just-in-time (JIT) ou "viral" (o que significa que é um inquilino Azure separado e não gerido), apenas o utilizador convidado pode redefinir a sua senha. Por vezes, uma organização [assume a gestão de inquilinos virais](../enterprise-users/domains-admin-takeover.md) que são criados quando os funcionários usam os seus endereços de e-mail de trabalho para se inscreverem para serviços. Após a organização assumir um inquilino viral, apenas um administrador nessa organização pode redefinir a palavra-passe do utilizador ou ativar o SSPR. Se necessário, como organização convidativa, pode remover a conta de utilizador do seu diretório e reensuitar um convite.

* Se o diretório de casa do utilizador convidado for o seu inquilino Azure AD, pode redefinir a senha do utilizador. Por exemplo, pode ter criado um utilizador ou sincronizado um utilizador a partir do seu Ative Directory no local e definir o Seu UserType para o Guest. Como este utilizador está aaprotendo o seu diretório, pode redefinir a sua palavra-passe a partir do portal Azure.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>O Microsoft Dynamics 365 fornece suporte online para a colaboração Azure AD B2B?
Sim, a Dynamics 365 (online) suporta a colaboração Azure AD B2B. Para mais informações, consulte o artigo Dynamics 365 [Convidar os utilizadores com colaboração Azure AD B2B](/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Qual é a vida útil de uma senha inicial para um recém-criado utilizador de colaboração B2B?
O Azure AD tem um conjunto fixo de caracteres, força de senha e requisitos de bloqueio de conta que se aplicam igualmente a todas as contas de utilizadores em nuvem Azure. Contas de utilizadores em nuvem são contas que não são federadas com outro fornecedor de identidade, tais como 
* Conta Microsoft
* Facebook
* Serviços de Federação do Active Directory
* Outro inquilino em nuvem (para colaboração B2B)

Para as contas federadas, a política de palavra-passe depende da política que é aplicada no arrendamento no local e nas definições da conta Microsoft do utilizador.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Uma organização pode querer ter experiências diferentes nas suas aplicações para utilizadores de inquilinos e utilizadores convidados. Há alguma orientação padrão para isto? A presença do fornecedor de identidade alega o modelo correto de utilização?
Um utilizador convidado pode utilizar qualquer fornecedor de identidade para autenticar. Para mais informações, consulte [as propriedades de um utilizador de colaboração B2B.](user-properties.md) Utilize a propriedade **UserType** para determinar a experiência do utilizador. A alegação **do UserType** não está atualmente incluída no token. As aplicações devem utilizar a API do Microsoft Graph para consultar o diretório para o utilizador e para obter o UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Onde posso encontrar uma comunidade de colaboração B2B para partilhar soluções e apresentar ideias?
Estamos constantemente a ouvir o seu feedback, para melhorar a colaboração B2B. Por favor, partilhe os seus cenários de utilizador, as melhores práticas e o que você gosta sobre a colaboração Azure AD B2B. Junte-se à discussão na [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Convidamo-lo também a submeter as suas ideias e a votar em funcionalidades futuras na [B2B Collaboration Ideas.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas)

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Podemos enviar um convite que seja automaticamente resgatado, para que o utilizador esteja apenas "pronto para ir"? Ou o utilizador tem sempre de clicar no URL de redenção?
Pode convidar outros utilizadores na organização parceira utilizando os scripts UI, PowerShell ou APIs. Em seguida, pode enviar ao utilizador convidado um link direto para uma aplicação partilhada. Na maioria dos casos, já não há necessidade de abrir o convite de e-mail e clicar num URL de resgate. Consulte [o convite de colaboração B2B do Azure Ative Directory B2B](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Como funciona a colaboração B2B quando o parceiro convidado está a usar a federação para adicionar a sua própria autenticação no local?
Se o parceiro tiver um inquilino AD Azure que é federado para a infraestrutura de autenticação no local, no local é automaticamente alcançado um único sign-on (SSO). Se o parceiro não tiver um inquilino AZure AD, é criada uma conta AZure AD para novos utilizadores. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Pensei que o Azure AD B2B não aceitasse gmail.com e outlook.com endereços de e-mail, e que o B2C era usado para este tipo de contas?
Estamos a eliminar as diferenças entre a colaboração B2B e o negócio-consumidor (B2C) em termos de apoio de identidades. A identidade utilizada não é uma boa razão para escolher entre usar b2B ou usar B2C. Para obter informações sobre a escolha da sua opção de colaboração, consulte [Compare B2B e B2C no Azure Ative Directory](compare-with-b2c.md).

### <a name="can-an-azure-ad-b2c-local-account-be-invited-to-an-azure-ad-tenant-for-b2b-collaboration"></a>Uma conta local Azure AD B2C pode ser convidada para um inquilino da AD Azure para colaboração B2B?
N.º Uma conta local Azure AD B2C só pode ser usada para assinar no inquilino Azure AD B2C. A conta não pode ser usada para assinar um inquilino da AD Azure. Convidar uma conta local Azure AD B2C para um inquilino da AD AZure para colaboração B2B não é apoiado.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Quais aplicações e serviços suportam utilizadores convidados Azure B2B?
Todas as aplicações integradas a Azure podem suportar utilizadores convidados Azure B2B, mas devem usar um ponto final configurado como inquilino para autenticar os utilizadores convidados. Poderá também ser necessário [personalizar as reclamações](claims-mapping.md) no token SAML que é emitido quando um utilizador convidado autentica para a aplicação. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Podemos forçar a autenticação de vários fatores para os utilizadores convidados B2B se os nossos parceiros não tiverem autenticação multi-factor?
Sim. Para obter mais informações, consulte [Acesso Condicional para utilizadores de colaboração B2B.](conditional-access.md)

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>No SharePoint, pode definir uma lista de "permitir" ou "negar" para utilizadores externos. Podemos fazer isto em Azure?
Sim. A colaboração Azure AD B2B suporta listas e listas de negação. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Que licenças precisamos para usar a Azure AD B2B?
Para obter informações sobre as licenças que a sua organização necessita para utilizar o Azure AD B2B, consulte [os preços das identidades externas.](external-identities-pricing.md)

### <a name="next-steps"></a>Passos seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)