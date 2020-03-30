---
title: Perguntas frequentes (FAQ) para O Diretório Ativo Azure B2C
description: Respostas a perguntas frequentes sobre o Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40285c811cd6f407c20c40bf3a90ec5b779a9c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264404"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Perguntas frequentes (FAQ)

Esta página responde frequentemente a perguntas sobre o Diretório Ativo Azure B2C (Azure AD B2C). Continue a verificar se há novidades.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Por que não posso aceder à extensão Azure AD B2C no portal Azure?

Existem duas razões comuns para que a extensão da AD Azure não esteja a funcionar para si. O Azure AD B2C exige que o seu papel de utilizador no diretório seja administrador global. Por favor contacte o seu administrador se achar que deve ter acesso. Se tiver privilégios de administrador global, certifique-se de que está num diretório Azure AD B2C e não num diretório Azure Ative Diretório. Pode ver instruções para a criação de [um inquilino Azure AD B2C.](tutorial-create-tenant.md)

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Posso usar as funcionalidades Azure AD B2C no meu inquilino Azure AD existente?

Azure AD e Azure AD B2C são ofertas separadas de produtos e não podem coexistir no mesmo inquilino. Um inquilino da AD Azure representa uma organização. Um inquilino Azure AD B2C representa uma coleção de identidades a serem usadas com aplicações partidárias dependentes. Ao adicionar **o novo fornecedor OpenID Connect** ao abrigo do **Azure AD B2C > Fornecedores** de identidade ou com políticas personalizadas, o Azure AD B2C pode federar-se para a Azure AD permitindo a autenticação de colaboradores numa organização.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Posso usar o Azure AD B2C para fornecer login social (Facebook e Google+) no Office 365?

O Azure AD B2C não pode ser utilizado para autenticar utilizadores para o Microsoft Office 365. A Azure AD é a solução da Microsoft para gerir o acesso dos colaboradores às aplicações SaaS e possui funcionalidades desenhadas para o efeito, como licenciamento e Acesso Condicional. O Azure AD B2C fornece uma plataforma de gestão de identidade e acesso para a construção de aplicações web e móveis. Quando o Azure AD B2C é configurado para federar um inquilino da AD Azure, o inquilino da Azure AD gere o acesso dos funcionários a aplicações que dependem do Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>O que são contas locais em Azure AD B2C? Como são diferentes do trabalho ou das contas escolares em Azure AD?

Num inquilino da AD Azure, os utilizadores que pertencem ao inquilino `<xyz>@<tenant domain>`sessão com um endereço de e-mail do formulário. Este `<tenant domain>` é um dos domínios verificados no `<...>.onmicrosoft.com` inquilino ou no domínio inicial. Este tipo de conta é uma conta de trabalho ou escola.

Num inquilino Azure AD B2C, a maioria das aplicações quer que o joe@comcast.netutilizador bob@gmail.com sarah@contoso.comassine jim@live.comqualquer endereço de e-mail arbitrário (por exemplo, , , ou ). Este tipo de conta é uma conta local. Também apoiamos nomes de utilizadores arbitrários como contas locais (por exemplo, joe, bob, sarah ou jim). Pode escolher um destes dois tipos de conta locais ao configurar fornecedores de identidade para O Azure AD B2C no portal Azure. No seu inquilino Azure AD B2C, selecione **fornecedores**de identidade, selecione **conta Local**e, em seguida, selecione **username**.

As contas de utilizador para aplicações podem ser criadas através de um fluxo de utilizador de inscrição, de inscrição ou de entrada de utilizador, do Microsoft Graph API ou do portal Azure.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Que fornecedores de identidade social apoia agora? Quais planeias apoiar no futuro?

Atualmente apoiamos vários fornecedores de identidade social, incluindo Amazon, Facebook, GitHub (pré-visualização), Google, LinkedIn, Microsoft Account (MSA), QQ (pré-visualização), Twitter, WeChat (pré-visualização) e Weibo (pré-visualização). Avaliamos a adição de apoio a outros fornecedores de identidade social populares com base na procura do cliente.

O Azure AD B2C também apoia [políticas personalizadas.](custom-policy-overview.md) As políticas personalizadas permitem-lhe criar a sua própria política para qualquer fornecedor de identidade que suporte o [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) ou SAML. Inicie-se com as políticas personalizadas verificando o nosso pacote de [iniciação de política personalizada.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Posso configurar os âmbitos para recolher mais informações sobre consumidores de vários fornecedores de identidade social?

Não. Os âmbitos padrão utilizados para o nosso conjunto de fornecedores de identidade social suportados são:

* Facebook: e-mail
* Google+: e-mail
* Conta Microsoft: perfil de e-mail aberto
* Amazon: perfil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>A minha candidatura tem de ser executada no Azure para que funcione com o Azure AD B2C?

Não, pode hospedar a sua aplicação em qualquer lugar (na nuvem ou no local). Tudo o que precisa para interagir com o Azure AD B2C é a capacidade de enviar e receber pedidos de HTTP em pontos finais acessíveis ao público.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tenho vários inquilinos Azure AD B2C. Como posso geri-los no portal Azure?

Antes de abrir o 'Azure AD B2C' no menu lateral esquerdo do portal Azure, tem de mudar para o diretório que pretende gerir. Mude os diretórios clicando na sua identidade na parte superior direita do portal Azure e, em seguida, escolha um diretório na gota que aparece.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Como personalizar e-mails de verificação (o conteúdo e o campo "From:") enviados pelo Azure AD B2C?

Pode utilizar a funcionalidade de branding da [empresa](../active-directory/fundamentals/customize-branding.md) para personalizar o conteúdo dos e-mails de verificação. Especificamente, estes dois elementos do e-mail podem ser personalizados:

* **Logotipo do banner**: Mostrado no canto inferior direito.
* **Cor de fundo**: Mostrado na parte superior.

    ![Screenshot de um e-mail de verificação personalizado](./media/faq/company-branded-verification-email.png)

A assinatura de e-mail contém o nome do inquilino Azure AD B2C que forneceu quando criou pela primeira vez o inquilino Azure AD B2C. Pode alterar o nome utilizando estas instruções:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como Administrador Global.
1. Abra o painel **Azure Active Directory**.
1. Clique no separador **Propriedades.**
1. Mude o campo **nome.**
1. Clique em **Guardar** no início da página.

Atualmente não há como alterar o campo "From:" no e-mail.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Como posso migrar os meus nomes de utilizadores, senhas e perfis existentes da minha base de dados para o Azure AD B2C?

Pode utilizar a API do Microsoft Graph para escrever a sua ferramenta de migração. Consulte o guia de [migração do Utilizador](user-migration.md) para mais detalhes.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Que fluxo de utilizador de senha é usado para contas locais em Azure AD B2C?

O fluxo de utilizador de senha SaqueA B2C azure para contas locais baseia-se na política para a AD Azure. Os fluxos de utilizador de inscrição, inscrição ou inscrição e reset de password suspons ida e volta a utilizar a força de senha "forte" do Azure AD B2C e não expiram quaisquer palavras-passe. Para mais detalhes, consulte [as políticas de password e restrições no Diretório Ativo Azure.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

Para obter informações sobre bloqueios de contas e senhas, consulte [Gere ameaças a recursos e dados no Diretório Ativo Azure B2C](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Posso usar o Azure AD Connect para migrar identidades de consumidores que são armazenadas no meu Diretório Ativo no local para Azure AD B2C?

Não, o Azure AD Connect não foi concebido para funcionar com o Azure AD B2C. Considere utilizar a [API](manage-user-accounts-graph-api.md) do Microsoft Graph para a migração dos utilizadores. Consulte o guia de [migração do Utilizador](user-migration.md) para mais detalhes.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>A minha aplicação pode abrir páginas Azure AD B2C dentro de um iFrame?

Não, por razões de segurança, as páginas Azure AD B2C não podem ser abertas dentro de um iFrame. O nosso serviço comunica com o navegador para proibir iFrames. A comunidade de segurança em geral e a especificação OAUTH2 recomendam contra a utilização de iFrames para experiências de identidade devido ao risco de jacking de cliques.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>O Azure AD B2C funciona com sistemas CRM como o Microsoft Dynamics?

A integração com o Portal Microsoft Dynamics 365 está disponível. Consulte [o Portal Configuring Dynamics 365 para utilizar o Azure AD B2C para autenticação.](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c)

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>O Azure AD B2C trabalha com o SharePoint no local 2016 ou mais cedo?

O Azure AD B2C não se destina ao cenário de partilha externa de parceiros sharePoint; ver [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) em vez disso.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Devo usar Azure AD B2C ou B2B para gerir identidades externas?

Leia [A colaboração B2B e a B2C em Azure AD](../active-directory/b2b/compare-with-b2c.md) para saber mais sobre a aplicação das funcionalidades apropriadas aos seus cenários de identidade externos.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Que funcionalidades de reporte e auditoria fornece o Azure AD B2C? São os mesmos que no Azure AD Premium?

Não, o Azure AD B2C não suporta o mesmo conjunto de relatórios que o Azure AD Premium. No entanto, existem muitas comunhões:

* **Os relatórios de inscrição** fornecem um registo de cada entrada com detalhes reduzidos.
* **Os relatórios de auditoria** incluem tanto a atividade administrativa como a atividade de aplicação.
* **Os relatórios** de utilização incluem o número de utilizadores, o número de logins e o volume de MFA.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Posso localizar o UI de páginas servidas por Azure AD B2C? Que línguas são apoiadas?

Sim, ver [personalização linguística.](user-flow-language-customization.md) Fornecemos traduções para 36 línguas, e você pode substituir qualquer corda de acordo com as suas necessidades.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Posso usar os meus urLs nas minhas páginas de inscrição e inscrição que são servidas pelo Azure AD B2C? Por exemplo, posso mudar o URL de contoso.b2clogin.com para login.contoso.com?

Atualmente não. Esta característica está no nosso roteiro. Verificar o seu domínio no separador **Domínios** no portal Azure não cumpre este objetivo. No entanto, com b2clogin.com, oferecemos um [domínio neutro de nível superior](b2clogin.md), e assim a aparência externa pode ser implementada sem a menção da Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Como posso apagar o meu inquilino Azure AD B2C?

Siga estes passos para eliminar o seu inquilino Azure AD B2C.

Pode utilizar a experiência atual de **Aplicações** ou a nossa nova experiência unificada de registos de **aplicações (Pré-visualização).** [Saiba mais sobre a nova experiência.](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Aplicações](#tab/applications/)

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como Administrador de *Assinatura.* Use a mesma conta de trabalho ou escola ou a mesma conta microsoft que usou para se inscrever no Azure.
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Elimine todos os **fluxos de utilizador (políticas)** no seu inquilino Azure AD B2C.
1. Elimine todas as **aplicações registadas** no seu inquilino Azure AD B2C.
1. Selecione **Azure Ative Directory** no menu à esquerda.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione cada utilizador por sua vez (exclua o utilizador do Administrador de *Subscrição* que está atualmente inscrito na medida em que). **Selecione Excluir** na parte inferior da página e selecione **SIM** quando solicitado.
1. Em **'Gerir',** selecione registos de **aplicações** (ou registos de **aplicações (Legacy)**).
1. Selecione **Ver todas as aplicações**
1. Selecione a aplicação denominada **b2c-extensions-app**, **selecione Delete**, e, em seguida, selecione **Sim** quando solicitado.
1. Em **'Gerir',** selecione **as definições do utilizador**.
1. Se estiver presente, sob **as ligações da conta LinkedIn,** selecione **Não,** então selecione **Guardar**.
1. Under **Manage**, selecione **Properties**
1. Em **Gestão de acesso dos recursos do Azure**, selecione **Sim** e, em seguida, **Guardar**.
1. Assine pelo portal Azure e depois volte a entrar para refrescar o seu acesso.
1. Selecione **Azure Ative Directory** no menu à esquerda.
1. Na página **'Visão Geral',** selecione **Excluir o diretório**. Siga as instruções no ecrã para completar o processo.

#### <a name="app-registrations-preview"></a>[Registos de aplicativos (Pré-visualização)](#tab/app-reg-preview/)

1. Inscreva-se no [portal Azure](https://portal.azure.com/) como Administrador de *Assinatura.* Use a mesma conta de trabalho ou escola ou a mesma conta microsoft que usou para se inscrever no Azure.
1. Selecione o filtro de **subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD AD B2C.
1. No menu esquerdo, **selecione Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Elimine todos os **fluxos de utilizador (políticas)** no seu inquilino Azure AD B2C.
1. Selecione **registos de aplicações (Pré-visualização)** e, em seguida, selecione o separador **Todas as aplicações.**
1. Elimine todas as aplicações que registou.
1. Elimine a **aplicação b2c-extensões**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione cada utilizador por sua vez (exclua o utilizador do Administrador de *Subscrição* que está atualmente inscrito na medida em que). **Selecione Excluir** na parte inferior da página e selecione **Sim** quando solicitado.
1. Selecione **Azure Ative Directory** no menu à esquerda.
1. Em **'Gerir',** selecione **as definições do utilizador**.
1. Se estiver presente, sob **as ligações da conta LinkedIn,** selecione **Não,** então selecione **Guardar**.
1. Under **Manage**, selecione **Properties**
1. Em **Gestão de acesso dos recursos do Azure**, selecione **Sim** e, em seguida, **Guardar**.
1. Assine pelo portal Azure e depois volte a entrar para refrescar o seu acesso.
1. Selecione **Azure Ative Directory** no menu à esquerda.
1. Na página **'Visão Geral',** selecione **Excluir o diretório**. Siga as instruções no ecrã para completar o processo.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Posso obter Azure AD B2C como parte da Enterprise Mobility Suite?

Não, o Azure AD B2C é um serviço Azure pay-as-you-go e não faz parte da Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Como reporto problemas com o Azure AD B2C?

Consulte pedidos de suporte de [ficheiros para o Diretório Ativo Azure B2C](support-options.md).
