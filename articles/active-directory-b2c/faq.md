---
title: Perguntas frequentes (FAQ) para Azure Ative Directory B2C
description: Respostas a perguntas frequentes sobre o Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d84c5fff41a7f00405c29ab4b4921ed2e28bdfde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580101"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Perguntas frequentes (FAQ)

Esta página responde a perguntas frequentes sobre o Azure Ative Directory B2C (Azure AD B2C). Continue a verificar se há atualizações.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Por que não posso aceder à extensão Azure AD B2C no portal Azure?

Existem duas razões comuns para que a extensão AD do Azure não esteja a funcionar para si. O Azure AD B2C requer que o seu papel de utilizador no diretório seja administrador global. Por favor contacte o seu administrador se achar que deve ter acesso. Se tiver privilégios de administrador global, certifique-se de que está num diretório Azure AD B2C e não num diretório Azure Ative. Pode ver instruções para [criar um inquilino Azure AD B2C.](tutorial-create-tenant.md)

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Posso usar as funcionalidades Azure AD B2C no meu inquilino AZure AD existente e baseado em empregados?

A Azure AD e Azure AD B2C são ofertas de produtos separadas e não podem coexistir no mesmo inquilino. Um inquilino da AD Azure representa uma organização. Um inquilino Azure AD B2C representa uma coleção de identidades para ser usada com aplicações de partidos dependentes. Ao adicionar **o novo fornecedor OpenID Connect** ao abrigo dos **fornecedores de identidade > Azure AD B2C** ou com políticas personalizadas, o Azure AD B2C pode federar à Azure AD permitindo a autenticação de colaboradores numa organização.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-microsoft-365"></a>Posso utilizar o Azure AD B2C para fornecer login social (Facebook e Google+) na Microsoft 365?

O Azure AD B2C não pode ser utilizado para autenticar os utilizadores para o Microsoft 365. A azure AD é a solução da Microsoft para gerir o acesso dos colaboradores às aplicações SaaS e tem funcionalidades desenhadas para o efeito, como licenciamento e Acesso Condicional. O Azure AD B2C fornece uma plataforma de gestão de identidade e acesso para a construção de aplicações web e móveis. Quando a Azure AD B2C está configurada para federar a um inquilino da AD Azure, o inquilino da AD AZure gere o acesso dos colaboradores a aplicações que dependem do Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>O que são contas locais em Azure AD B2C? Como são diferentes das contas de trabalho ou da escola em Azure AD?

Num inquilino da AD Azure, os utilizadores que pertencem ao inquilino assinam com um endereço de e-mail do `<xyz>@<tenant domain>` formulário. É `<tenant domain>` um dos domínios verificados no arrendatário ou no `<...>.onmicrosoft.com` domínio inicial. Este tipo de conta é uma conta de trabalho ou escola.

Num inquilino Azure AD B2C, a maioria das aplicações pretende que o utilizador entre em sposição com qualquer endereço de e-mail arbitrário (por exemplo, joe@comcast.net bob@gmail.com , ou sarah@contoso.com jim@live.com . Este tipo de conta é uma conta local. Também apoiamos nomes de utilizadores arbitrários como contas locais (por exemplo, joe, bob, sarah ou jim). Pode escolher um destes dois tipos de conta locais ao configurar fornecedores de identidade para Azure AD B2C no portal Azure. No seu inquilino Azure AD B2C, selecione **fornecedores de identidade,** selecione **Conta Local** e, em seguida, selecione **Username**.

As contas dos utilizadores para aplicações podem ser criadas através de um fluxo de utilizador de inscrição, de inscrição ou de fluxo de utilizador de inscrição, da API do Microsoft Graph ou do portal Azure.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Que fornecedores de identidade social apoia agora? Quais planeias apoiar no futuro?

Atualmente apoiamos vários fornecedores de identidade social, incluindo Amazon, Facebook, GitHub (pré-visualização), Google, LinkedIn, Microsoft Account (MSA), QQ (pré-visualização), Twitter, WeChat (pré-visualização) e Weibo (pré-visualização). Avaliamos a adição de apoio a outros fornecedores populares de identidade social com base na procura do cliente.

A Azure AD B2C também suporta [políticas personalizadas.](custom-policy-overview.md) As políticas personalizadas permitem-lhe criar a sua própria política para qualquer fornecedor de identidade que suporte [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) ou SAML. Começa com as políticas personalizadas, verificando o nosso [pacote de iniciação de políticas personalizadas.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Posso configurar âmbitos para recolher mais informações sobre os consumidores de vários fornecedores de identidade social?

N.º Os âmbitos padrão utilizados para o nosso conjunto suportado de fornecedores de identidade social são:

* Facebook: e-mail
* Google+: e-mail
* Conta Microsoft: perfil de e-mail aberto
* Amazon: perfil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>A minha candidatura tem de ser executada no Azure para que funcione com o Azure AD B2C?

Não, pode hospedar a sua aplicação em qualquer lugar (na nuvem ou no local). Tudo o que precisa para interagir com a Azure AD B2C é a capacidade de enviar e receber pedidos HTTP em pontos finais acessíveis ao público.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tenho vários inquilinos Azure AD B2C. Como posso geri-los no portal Azure?

Antes de abrir o 'Azure AD B2C' no menu lateral esquerdo do portal Azure, tem de mudar para o diretório que pretende gerir. Mude os diretórios clicando na sua identidade no canto superior direito do portal Azure e, em seguida, escolha um diretório na queda que aparece.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Como personalizar e-mails de verificação (o conteúdo e o campo "From:" ) enviados pela Azure AD B2C?

Pode utilizar a [funcionalidade de marca da empresa](../active-directory/fundamentals/customize-branding.md) para personalizar o conteúdo dos e-mails de verificação. Especificamente, estes dois elementos do e-mail podem ser personalizados:

* **Logotipo do banner**: Mostrado no canto inferior direito.
* **Cor de fundo**: Mostrado na parte superior.

    ![Screenshot de um e-mail de verificação personalizado](./media/faq/company-branded-verification-email.png)

A assinatura do e-mail contém o nome do inquilino Azure AD B2C que forneceu quando criou o inquilino Azure AD B2C. Pode alterar o nome usando estas instruções:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como Administrador Global.
1. Abra o painel **Azure Active Directory**.
1. Clique no separador **Propriedades**.
1. Mude o campo **Nome.**
1. Clique em **Guardar** no início da página.

Atualmente não é possível alterar o campo "A partir:" no e-mail.

> [!TIP]
> Com [a política personalizada](custom-policy-overview.md)AZURE AD B2C, pode personalizar o e-mail que o Azure AD B2C envia aos utilizadores, incluindo o campo "A partir:" no e-mail. A verificação personalizada de e-mails requer a utilização de um fornecedor de e-mail de terceiros como [Mailjet,](custom-email-mailjet.md) [SendGrid](custom-email-sendgrid.md)ou [SparkPost](https://sparkpost.com).

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Como posso migrar os meus nomes de utilizador, senhas e perfis existentes da minha base de dados para Azure AD B2C?

Pode utilizar a API do Microsoft Graph para escrever a sua ferramenta de migração. Consulte o [guia de migração](user-migration.md) do Utilizador para obter mais detalhes.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Que fluxo de utilizador de palavra-passe é utilizado para contas locais em Azure AD B2C?

O fluxo de utilizadores de passwordS Azure AD B2C para contas locais baseia-se na política do Azure AD. A inscrição, inscrição ou inscrição do Azure AD B2C e fluxos de utilizador de redefinição de palavra-passe utilizam a força de senha "forte" e não expiram quaisquer palavras-passe. Para mais detalhes, consulte [as políticas e restrições da Palavra-Passe no Diretório Ativo Azure](../active-directory/authentication/concept-sspr-policy.md).

Para obter informações sobre bloqueios de contas e palavras-passe, consulte [Gerir ameaças a recursos e dados no Azure Ative Directory B2C](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Posso utilizar o Azure AD Connect para migrar identidades dos consumidores que estão armazenadas no meu Ative Directory para Azure AD B2C?

Não, o Azure AD Connect não foi concebido para funcionar com o Azure AD B2C. Considere utilizar a [API do Microsoft Graph](microsoft-graph-operations.md) para a migração do utilizador. Consulte o [guia de migração](user-migration.md) do Utilizador para obter mais detalhes.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>A minha aplicação pode abrir páginas Azure AD B2C dentro de um iFrame?

Esta funcionalidade está em pré-visualização pública. Para mais detalhes, consulte [a experiência de inscrição incorporada.](https://docs.microsoft.com/azure/active-directory-b2c/embedded-login)

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>O Azure AD B2C funciona com sistemas crm como o Microsoft Dynamics?

A integração com o Portal Microsoft Dynamics 365 está disponível. Consulte [o Portal De Configuração Dinâmica 365 para utilizar o Azure AD B2C para autenticação](/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>O Azure AD B2C trabalha com o SharePoint no local 2016 ou mais cedo?

O Azure AD B2C não se destina ao cenário de partilha externa de parceiros sharePoint; ver [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) em vez disso.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Devo utilizar o Azure AD B2C ou o B2B para gerir identidades externas?

Leia [Compare a colaboração B2B e b2C em Azure AD](../active-directory/external-identities/compare-with-b2c.md) para saber mais sobre a aplicação das funcionalidades apropriadas aos seus cenários de identidade externa.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Que funcionalidades de reporte e auditoria a Azure AD B2C fornece? São os mesmos que no Azure AD Premium?

Não, o Azure AD B2C não suporta o mesmo conjunto de relatórios que o Azure AD Premium. No entanto, existem muitas comunhões:

* **Os relatórios de inscrição** fornecem um registo de cada entrada com detalhes reduzidos.
* **Os relatórios de auditoria** incluem tanto a atividade de administração como a atividade de aplicação.
* **Os relatórios** de utilização incluem o número de utilizadores, o número de logins e o volume de MFA.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Posso localizar a UI das páginas servidas pelo Azure AD B2C? Que línguas são apoiadas?

Sim, consulte [a personalização da linguagem.](language-customization.md) Fornecemos traduções para 36 línguas, e você pode sobrepor qualquer cadeia para atender às suas necessidades.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Posso usar os meus próprios URLs nas minhas páginas de inscrição e inscrição que são servidas pelo Azure AD B2C? Por exemplo, posso mudar a URL de contoso.b2clogin.com para login.contoso.com?

Esta funcionalidade está disponível em visualização pública. Para mais informações, consulte [os domínios personalizados Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-domain?pivots=b2c-user-flow).

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Como apago o meu inquilino Azure AD B2C?

Siga estes passos para eliminar o seu inquilino Azure AD B2C.

Você pode usar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência legacy **Applications (Legacy).** [Saiba mais sobre a nova experiência.](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[Registos de aplicações](#tab/app-reg-ga/)

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como *Administrador de Subscrição*. Use a mesma conta de trabalho ou escola ou a mesma conta Microsoft que usou para se inscrever no Azure.
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Elimine todos os **fluxos de utilizador (políticas)** no seu inquilino Azure AD B2C.
1. Elimine todos os **Fornecedores de Identidade** do seu inquilino Azure AD B2C.
1. Selecione **as inscrições da App** e, em seguida, selecione o separador Todas as **aplicações.**
1. Elimine todas as aplicações que registou.
1. Eliminar a **aplicação de extensões b2c**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione cada utilizador por sua vez (exclua o utilizador *do Administrador de Subscrição* em que está atualmente inscrito como). Selecione **Eliminar** na parte inferior da página e selecione **Sim** quando solicitado.
1. Selecione **Azure Ative Directory** no menu da esquerda.
1. Em **Gestão**, selecione **Propriedades**
1. Em **Gestão de acesso dos recursos do Azure**, selecione **Sim** e, em seguida, **Guardar**.
1. Assine fora do portal Azure e, em seguida, inscreva-se de volta para refrescar o seu acesso.
1. Selecione **Azure Ative Directory** no menu da esquerda.
1. Na página **'Vista Geral',** selecione **Excluir inquilino**. Siga as instruções no ecrã para concluir o processo.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como *Administrador de Subscrição*. Use a mesma conta de trabalho ou escola ou a mesma conta Microsoft que usou para se inscrever no Azure.
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Elimine todos os **fluxos do Utilizador (políticas)** no seu inquilino Azure AD B2C.
1. Elimine todas as **Aplicações (Legado)** que registou no seu inquilino Azure AD B2C.
1. Selecione **Azure Ative Directory** no menu da esquerda.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione cada utilizador por sua vez (exclua o utilizador *do Administrador de Subscrição* em que está atualmente inscrito como). Selecione **Eliminar** na parte inferior da página e selecione **SIM** quando solicitado.
1. Em **Gerir**, selecione **Registos de aplicações**.
1. Selecione **Ver todas as aplicações**
1. Selecione a aplicação denominada **b2c-extensions-app**, selecione **Delete**, e, em seguida, selecione **Sim** quando solicitado.
1. Em **Gestão**, selecione **as definições do Utilizador**.
1. Se estiver presente, nas **ligações da conta LinkedIn**, selecione **Nº**, em seguida, **selecione Guardar**.
1. Em **Gestão**, selecione **Propriedades**
1. Em **Gestão de acesso dos recursos do Azure**, selecione **Sim** e, em seguida, **Guardar**.
1. Assine fora do portal Azure e, em seguida, inscreva-se de volta para refrescar o seu acesso.
1. Selecione **Azure Ative Directory** no menu da esquerda.
1. Na página **'Visão Geral',** selecione **Eliminar o diretório**. Siga as instruções no ecrã para concluir o processo.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Posso ter o Azure AD B2C como parte da Enterprise Mobility Suite?

Não, o Azure AD B2C é um serviço Azure pay-as-you-go e não faz parte da Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Como reporto problemas com a Azure AD B2C?

Consulte [os pedidos de suporte de ficheiros para o Azure Ative Directory B2C](support-options.md).
