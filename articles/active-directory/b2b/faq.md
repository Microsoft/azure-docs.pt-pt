---
title: Colaboração B2B FAQs - Azure Active Directory | Documentos da Microsoft
description: Obtenha respostas para perguntas mais frequentes sobre a colaboração B2B do Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 10/29/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: e70a74be372e4374fac5aaa2bfda8ca955e6d305
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671867"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Colaboração do Azure Active Directory B2B FAQs

Estas perguntas mais frequentes (FAQ) sobre a colaboração do Azure Active Directory (Azure AD) empresa-empresa (B2B) periodicamente são atualizadas para incluir novos tópicos.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Podemos personalizar nossa página de início de sessão, portanto, é muito mais intuitivo para os nossos utilizadores de convidados de colaboração do B2B?
Absolutamente! Consulte nossos [mensagem de blogue sobre esta funcionalidade](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Para obter mais informações sobre como personalizar a página de início de sessão da sua organização, consulte [adicionar imagem corporativa para iniciar sessão e painel de acesso](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Utilizadores de colaboração B2B podem acessar o SharePoint Online e OneDrive?
Sim. No entanto, é a capacidade de pesquisar para utilizadores convidados existentes no SharePoint Online utilizando o selecionador de pessoas **desativar** por predefinição. Para ativar a opção para procurar utilizadores convidados existentes, defina **ShowPeoplePickerSuggestionsForGuestUsers** ao **no**. Pode ativar esta definição ao nível do inquilino ou ao nível da coleção de sites. Pode alterar esta definição utilizando os cmdlets Set-SPOTenant e SPOSite do conjunto. Com os cmdlets, os membros podem pesquisar todos os utilizadores convidados existentes no diretório. As alterações no âmbito do inquilino não afetam sites do SharePoint Online que já tiverem sido aprovisionadas.

### <a name="is-the-csv-upload-feature-still-supported"></a>A funcionalidade de carregamento CSV é ainda suportada?
Sim. Para obter mais informações sobre como utilizar a funcionalidade de carregamento de ficheiro. csv, consulte [este exemplo de PowerShell](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Como posso personalizar meu e-mails de convite?
Pode personalizar praticamente tudo sobre o processo de autor do convite, utilizando o [B2B convite APIs](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Os utilizadores convidados podem repor o seu método de autenticação multifator?
Sim. Os utilizadores convidados podem repor o método de autenticação multifator da mesma forma que os utilizadores regulares fazer.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Organização da qual é responsável por licenças de autenticação multifator?
A organização que o convidou efetua a autenticação multifator. A organização que o convidou deve certificar-se de que a organização tem licenças suficientes para os seus utilizadores B2B que estão a utilizar a autenticação multifator.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>E se uma organização de parceiro já tem de configurar a autenticação multifator? Podemos confiar a autenticação multifator e não utilize nossa própria autenticação multifator?
Esta funcionalidade está planejada para uma versão futura, pelo que, em seguida, pode selecionar parceiros específicos a excluir da sua a autenticação multifator (o convite da organização).

### <a name="how-can-i-use-delayed-invitations"></a>Como posso utilizar convites atrasados?
Uma organização poderá querer adicionar utilizadores de colaboração B2B, aprovisioná-los para aplicações, conforme necessário e, em seguida, enviar convites. Pode usar o convite de colaboração do B2B API para personalizar o fluxo de trabalho de integração.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Posso fazer os utilizadores convidados visível na lista de endereços Global do Exchange?
Sim. Objetos de convidado não são visíveis na lista de endereços global da sua organização por predefinição, mas pode utilizar o Azure Active Directory PowerShell para que fiquem visíveis. Ver **posso fazer objetos de convidado visível na lista de endereços global?** na [acesso de convidado em grupos do Office 365](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ).

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Posso fazer um utilizador convidado um administrador limitado?
Com certeza. Para obter mais informações, consulte [adicionar utilizadores convidados a uma função](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Colaboração B2B do Azure AD permite que utilizadores B2B aceder ao portal do Azure?
A menos que um utilizador é atribuído a função de administrador limitado ou administrador global, os utilizadores de colaboração do B2B não necessitam de acesso ao portal do Azure. No entanto, os utilizadores de colaboração do B2B que estão atribuídos a função de administrador limitado ou administrador global podem aceder ao portal. Além disso, se um utilizador convidado que não foi atribuído uma destas funções de administrador acessa o portal, o utilizador poderá ser capaz de aceder a determinadas partes da experiência. A função de utilizador convidado tem algumas permissões no diretório.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Posso bloquear o acesso ao portal do Azure para utilizadores convidados?
Sim! Quando configurar esta política, tenha cuidado para evitar a acidentalmente bloquear o acesso para membros e administradores.
Para bloquear o acesso de um utilizador convidado para o [portal do Azure](https://portal.azure.com), utilizar uma política de acesso condicional na API de modelo de implementação clássica do Windows Azure:
1. Modificar a **todos os utilizadores** para que ele contém apenas os membros de grupo.
  ![modificar a captura de ecrã do grupo](media/faq/modify-all-users-group.png)
2. Crie um grupo dinâmico que contenha os utilizadores convidados.
  ![Criar grupo captura de ecrã](media/faq/group-with-guest-users.png)
3. Configure uma política de acesso condicional para bloquear os utilizadores de convidado de aceder ao portal, conforme mostrado no vídeo seguinte:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Colaboração B2B do Azure AD suporta autenticação multifator e contas de e-mail de consumidor?
Sim. Contas de e-mail de multi-factor authentication e de consumo são suportadas para a colaboração B2B do Azure AD.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Suporta para os utilizadores de colaboração B2B do Azure AD de reposição de palavra-passe?
Se o seu inquilino do Azure AD é o diretório raiz de um utilizador, pode [reposição de palavra-passe do utilizador](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) do portal do Azure. Mas diretamente não é possível repor uma palavra-passe para um utilizador convidado que inicia sessão com uma conta que seja gerenciada por outro fornecedor de identidade externa ou diretório do Azure AD. Apenas o utilizador convidado ou um administrador no diretório raiz do utilizador, pode repor a palavra-passe. Aqui estão alguns exemplos de como funciona para os utilizadores convidados a reposição de palavra-passe:
 
* Utilizadores convidados que iniciam sessão com uma conta Microsoft (por exemplo guestuser@live.com) pode repor a palavra-passe utilizando a conta Microsoft, reposição de palavra-passe self-service (SSPR). Ver [como repor a palavra-passe de conta do Microsoft](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Utilizadores convidados que inicie sessão com uma conta do Google ou outro fornecedor de identidade externo podem repor a palavra-passe através do método SSPR do seu fornecedor de identidade. Por exemplo, um utilizador convidado com a conta Google guestuser@gmail.com pode repor a palavra-passe seguindo as instruções [alterar ou repor a palavra-passe](https://support.google.com/accounts/answer/41078).
* Se o inquilino de identidade for um just-in-time (JIT) ou "viral" do inquilino (ou seja, é um inquilino do Azure separado e não gerenciado), apenas o utilizador convidado pode repor a palavra-passe. Por vezes, uma organização será [assuma a gestão de inquilinos virais](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) que são criadas quando os funcionários utilizam os respetivos endereços de e-mail de trabalho para se inscrever para os serviços. Depois da organização assume-se um inquilino viral, apenas um administrador nessa organização pode repor a palavra-passe do utilizador ou ativar a SSPR. Se necessário, como a organização que o convidou, pode remover a conta de utilizador convidado a partir do seu diretório e volte a enviar um convite.
* Se o diretório raiz do utilizador convidado é o seu inquilino do Azure AD, pode redefinir a senha do usuário. Por exemplo, pode ter criado um utilizador ou sincronizados um utilizador do Active Directory no local e defina suas UserType como convidado. Uma vez que esse usuário está hospedado no seu diretório, pode repor a palavra-passe do portal do Azure.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>O Microsoft Dynamics 365 fornece suporte online para a colaboração B2B do Azure AD?
Sim, do Dynamics 365 (online) suporta a colaboração B2B do Azure AD. Para obter mais informações, consulte o artigo do Dynamics 365 [convidar utilizadores com a colaboração B2B do Azure AD](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>O que é o tempo de vida de uma palavra-passe inicial para um utilizador recentemente criada de colaboração B2B?
O Azure AD tem um conjunto fixo de caracteres, a força da senha e a conta de requisitos de bloqueio que aplicam-se igualmente para todas as do Azure AD na cloud a contas de utilizador. Contas de utilizador de cloud são contas que não estão federadas com outros fornecedores de identidade, como 
* Conta Microsoft
* Facebook
* Serviços de Federação do Active Directory
* Outro inquilino de cloud (para a colaboração B2B)

Para contas federadas, política de palavra-passe depende da política que está aplicada o inquilinos no local e as definições da conta Microsoft do utilizador.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Uma organização talvez queira ter experiências diferentes nas respetivas aplicações para utilizadores de inquilino e os utilizadores convidados. Há diretrizes padrão para que isso? É a presença do fornecedor de identidade de afirmações o modelo correto a utilizar?
Um utilizador convidado pode utilizar qualquer fornecedor de identidade para autenticar. Para obter mais informações, consulte [propriedades de um utilizador de colaboração do B2B](user-properties.md). Utilize o **UserType** propriedade para determinar a experiência do usuário. O **UserType** afirmação atualmente não está incluída no token. Aplicativos devem usar a API de gráfico para consultar o diretório para o utilizador e obter o UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Onde posso encontrar uma Comunidade de colaboração do B2B para partilhar soluções de e para submeter ideias?
Estamos constantemente atentos aos comentários, para melhorar a colaboração B2B. Partilhe o seu utilizador de situações, melhores práticas e o que lhe agrada do colaboração B2B do Azure AD. Junte-se a discussão no [Comunidade tecnológica da Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Também convidamos para enviar suas idéias e vote para futuras funcionalidades em [ideias de colaboração do B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Podemos enviar um convite que é resgatado automaticamente, para que o utilizador é "pronto para ir"? Ou o utilizador sempre tem de clicar para o URL de resgate?
Pode convidar outros utilizadores na organização do parceiro através da IU, scripts do PowerShell, ou APIs. Em seguida, pode enviar o utilizador convidado uma ligação direta para uma aplicação partilhada. Na maioria dos casos, já não existe a necessidade de abrir o convite por e-mail e clique num URL de resgate. Ver [resgate de convite de colaboração do Azure Active Directory B2B](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Como funciona a colaboração B2B quando o parceiro convidado está a utilizar o Federação para adicionar sua própria autenticação no local?
Se o parceiro tem um inquilino do Azure AD que está federado para a infraestrutura de autenticação no local, no local início de sessão único (SSO) é automaticamente obtido. Se o parceiro não tiver um inquilino do Azure AD, é criada uma conta do Azure AD para os novos utilizadores. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Pensei B2B do Azure AD não aceitar gmail.com e outlook.com endereços de e-mail e que o B2C era utilizada para esses tipos de contas?
Estamos a remover as diferenças entre o B2B e a colaboração do empresa-consumidor (B2C) em termos de que as identidades são suportadas. A identidade utilizada não é um bom motivo para escolher entre utilizar B2B ou com o B2C. Para obter informações sobre como escolher a opção de colaboração, consulte [colaboração B2B comparar e B2C no Azure Active Directory](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Que aplicações e serviços suportam utilizadores convidados B2B do Azure?
Todas as aplicações do Azure integradas no AD podem suportar utilizadores convidados B2B do Azure, mas tem de utilizar um ponto de final de inquilinos para autenticar os utilizadores convidados. Também poderá ter de [personalizar as declarações](claims-mapping.md) no token SAML emitido quando um utilizador convidado efetua a autenticação à aplicação. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Podemos forçar autenticação multifator para utilizadores convidados B2B se nossos parceiros não tem a autenticação multifator?
Sim. Para obter mais informações, consulte [acesso condicional para utilizadores de colaboração B2B](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>No SharePoint, pode definir uma lista de "Permitir" ou "Negar" para utilizadores externos. Podemos fazer isso no Azure?
Sim. Suporta a colaboração do Azure AD B2B permite listas e listas de negação. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Licenças de que precisamos utilizar o Azure AD B2B?
Para obter informações sobre as suas necessidades de organização utilizar o Azure AD B2B de licenças, consulte [colaboração do Azure Active Directory B2B orientação de licenciamento](licensing-guidance.md).

### <a name="next-steps"></a>Passos Seguintes

- [O que é a colaboração B2B do Azure AD?](what-is-b2b.md)

