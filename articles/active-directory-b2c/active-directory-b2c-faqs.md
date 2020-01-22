---
title: Perguntas frequentes (FAQ) para Azure Active Directory B2C
description: Respostas para perguntas frequentes sobre Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 803c82c9926c3b75545b42a9a382fd5c327b7151
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289089"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: perguntas frequentes (FAQ)

Esta página responde às perguntas frequentes sobre o Azure Active Directory B2C (Azure AD B2C). Continue verificando as atualizações.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Por que não consigo acessar a extensão de Azure AD B2C no portal do Azure?

Há dois motivos comuns para o motivo pelo qual a extensão do Azure AD não está funcionando para você. Azure AD B2C exige que sua função de usuário no diretório seja administrador global. Entre em contato com seu administrador se você acredita que deve ter acesso. Se você tiver privilégios de administrador global, verifique se está em um diretório Azure AD B2C e não em um diretório Azure Active Directory. Você pode ver instruções para [criar um locatário Azure ad B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Posso usar Azure AD B2C recursos em meu locatário do Azure AD existente baseado em funcionários?

O Azure AD e o Azure AD B2C são ofertas de produtos separadas e não podem coexistir no mesmo locatário. Um locatário do Azure AD representa uma organização. Um locatário Azure AD B2C representa uma coleção de identidades a ser usada com aplicativos de terceira parte confiável. Ao adicionar o **novo provedor de OpenID Connect** em **Azure ad B2C > provedores de identidade** ou com políticas personalizadas, Azure ad B2C pode federar o AD do Azure, permitindo a autenticação de funcionários em uma organização.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Posso usar Azure AD B2C para fornecer logon social (Facebook e Google +) no Office 365?

Azure AD B2C não pode ser usado para autenticar usuários para Microsoft Office 365. O Azure AD é a solução da Microsoft para gerenciar o acesso dos funcionários a aplicativos SaaS e tem recursos projetados para essa finalidade, como licenciamento e acesso condicional. O Azure AD B2C fornece uma plataforma de gerenciamento de acesso e identidade para a criação de aplicativos Web e móveis. Quando Azure AD B2C é configurado para federar a um locatário do Azure AD, o locatário do Azure AD gerencia o acesso dos funcionários a aplicativos que dependem de Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>O que são contas locais no Azure AD B2C? Como eles são diferentes das contas corporativas ou de estudante no Azure AD?

Em um locatário do Azure AD, os usuários que pertencem à entrada do locatário com um endereço de email do formulário `<xyz>@<tenant domain>`. O `<tenant domain>` é um dos domínios verificados no locatário ou no domínio de `<...>.onmicrosoft.com` inicial. Esse tipo de conta é uma conta corporativa ou de estudante.

Em um locatário Azure AD B2C, a maioria dos aplicativos deseja que o usuário entre com qualquer endereço de email arbitrário (por exemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.comou jim@live.com). Esse tipo de conta é uma conta local. Também há suporte para nomes de usuário arbitrários como contas locais (por exemplo, Joe, Bob, Sarah ou Jim). Você pode escolher um desses dois tipos de conta local ao configurar provedores de identidade para Azure AD B2C no portal do Azure. No locatário do Azure AD B2C, selecione **provedores de identidade**, selecione **conta local**e, em seguida, selecione **nome de usuário**.

As contas de usuário para aplicativos sempre devem ser criadas por meio de um fluxo de usuário de inscrição, um fluxo de usuário de inscrição ou de entrada ou usando o API do Graph do Azure AD. As contas de usuário criadas no portal do Azure são usadas somente para administrar o locatário.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>A quais provedores de identidade social há suporte agora? Quais deles você planeja oferecer suporte no futuro?

Atualmente, damos suporte a vários provedores de identidade social, incluindo Amazon, Facebook, GitHub (versão prévia), Google, LinkedIn, Microsoft (MSA), QQ (versão prévia), Twitter, WeChat (versão prévia) e Weibo (versão prévia). Avaliamos a adição de suporte para outros provedores de identidade social populares com base na demanda do cliente.

O Azure AD B2C também dá suporte a [políticas personalizadas](active-directory-b2c-overview-custom.md). As políticas personalizadas permitem que você crie sua própria política para qualquer provedor de identidade que ofereça suporte a [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) ou SAML. Comece com políticas personalizadas conferindo nosso [pacote de início de política personalizado](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Posso configurar escopos para coletar mais informações sobre os consumidores de vários provedores de identidade social?

Não. Os escopos padrão usados para nosso conjunto de provedores de identidade social com suporte são:

* Facebook: email
* Google +: email
* Conta Microsoft: perfil de email do OpenID
* Amazon: perfil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Meu aplicativo precisa ser executado no Azure para que ele funcione com Azure AD B2C?

Não, você pode hospedar seu aplicativo em qualquer lugar (na nuvem ou local). Tudo o que precisa para interagir com o Azure AD B2C é a capacidade de enviar e receber pedidos HTTP em pontos finais acessíveis publicamente.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tenho vários locatários Azure AD B2C. Como posso gerenciá-los na portal do Azure?

Antes de abrir ' Azure AD B2C ' no menu do lado esquerdo da portal do Azure, você deve alternar para o diretório que deseja gerenciar. Alterne os diretórios clicando em sua identidade no canto superior direito do portal do Azure, em seguida, escolha um diretório na lista suspensa que aparece.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Como fazer personalizar os emails de verificação (o conteúdo e o campo "de:") enviados por Azure AD B2C?

Você pode usar o [recurso de identidade visual da empresa](../active-directory/fundamentals/customize-branding.md) para personalizar o conteúdo dos emails de verificação. Especificamente, esses dois elementos do email podem ser personalizados:

* **Logotipo de faixa**: mostrado na parte inferior direita.
* **Cor do plano de fundo**: mostrada na parte superior.

    ![Captura de tela de um email de verificação personalizado](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

A assinatura de email contém o nome do locatário de Azure AD B2C que você forneceu quando criou o locatário do Azure AD B2C pela primeira vez. Você pode alterar o nome usando estas instruções:

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global.
1. Abra o painel **Azure Active Directory**.
1. Clique nas **propriedades** separador.
1. Altere o campo **nome** .
1. Clique em **Guardar** no início da página.

Atualmente, não há como alterar o campo "de:" no email.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Como posso migrar meus nomes de usuário, senhas e perfis existentes do meu banco de dados para Azure AD B2C?

Você pode usar o API do Graph do Azure AD para escrever sua ferramenta de migração. Consulte o [Guia de migração do usuário](active-directory-b2c-user-migration.md) para obter detalhes.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Qual fluxo de usuário de senha é usado para contas locais no Azure AD B2C?

O fluxo de usuário de Azure AD B2C senha para contas locais é baseado na política do Azure AD. Os fluxos de inscrição, inscrição ou entrada e redefinição de senha do Azure AD B2C usam a força de senha "forte" e não expiram as senhas. Leia a [política de senha do Azure ad](/previous-versions/azure/jj943764(v=azure.100)) para obter mais detalhes. Para obter informações sobre bloqueios de conta e senhas, consulte [gerencia ameaças a recursos e dados no Azure Active Directory B2C](active-directory-b2c-reference-threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Posso usar Azure AD Connect para migrar identidades de consumidor que são armazenadas no meu Active Directory local para Azure AD B2C?

Não, o Azure AD Connect não foi projetado para funcionar com Azure AD B2C. Considere usar o [API do Graph do Azure ad](active-directory-b2c-devquickstarts-graph-dotnet.md) para migração de usuário. Consulte o [Guia de migração do usuário](active-directory-b2c-user-migration.md) para obter detalhes.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Meu aplicativo pode abrir Azure AD B2C páginas dentro de um iFrame?

Não, por motivos de segurança, Azure AD B2C páginas não podem ser abertas em um iFrame. Nosso serviço se comunica com o navegador para proibir iFrames. A comunidade de segurança em geral e a especificação do OAUTH2, recomenda-se contra o uso de iFrames para experiências de identidade devido ao risco de um clique.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C funciona com sistemas CRM, como o Microsoft Dynamics?

A integração com o portal do Microsoft Dynamics 365 está disponível. Consulte [Configurando o portal do Dynamics 365 para usar Azure ad B2C para autenticação](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Azure AD B2C trabalhar com o SharePoint local 2016 ou anterior?

Azure AD B2C não se destina ao cenário de compartilhamento de parceiros externos do SharePoint; consulte [Azure ad B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) em vez disso.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Devo usar Azure AD B2C ou B2B para gerenciar identidades externas?

Leia este artigo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para saber mais sobre como aplicar os recursos apropriados aos seus cenários de identidade externa.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Quais recursos de relatório e auditoria Azure AD B2C fornecem? Eles são os mesmos que no Azure AD Premium?

Não, Azure AD B2C não dá suporte ao mesmo conjunto de relatórios que Azure AD Premium. No entanto, há muitos semelhanças:

* Os **relatórios de entrada** fornecem um registro de cada entrada com detalhes reduzidos.
* Os **relatórios de auditoria** incluem atividade de administrador, bem como atividade do aplicativo.
* Os **relatórios de uso** incluem o número de usuários, o número de logons e o volume de MFA.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Posso localizar a interface do usuário das páginas servidas pelo Azure AD B2C? Que idiomas são suportados?

Sim, consulte [personalização de idioma](active-directory-b2c-reference-language-customization.md). Fornecemos traduções para 36 idiomas e você pode substituir qualquer cadeia de caracteres para atender às suas necessidades.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Posso usar minhas próprias URLs em minhas páginas de inscrição e de entrada que são servidas pelo Azure AD B2C? Por exemplo, posso alterar a URL de contoso.b2clogin.com para login.contoso.com?

Atualmente não. Esse recurso está em nosso roteiro. Verificar seu domínio na guia **domínios** no portal do Azure não atinge essa meta. No entanto, com o b2clogin.com, oferecemos um [domínio de nível superior neutro](b2clogin.md)e, portanto, a aparência externa pode ser implementada sem a menção da Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Como fazer excluir meu locatário Azure AD B2C?

Siga estas etapas para excluir seu locatário Azure AD B2C.

Você pode usar a experiência de **aplicativos** atual ou nossa nova experiência unificada de **registros de aplicativo (versão prévia)** . [Saiba mais sobre a nova experiência](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplicações](#tab/applications/)

1. Entre no [portal do Azure](https://portal.azure.com/) como o administrador da *assinatura*. Use a mesma conta corporativa ou de estudante ou a mesma conta Microsoft que você usou para se inscrever no Azure.
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Exclua todos os **fluxos de usuário (políticas)** no locatário Azure ad B2C.
1. Exclua todos os **aplicativos** que você registrou em seu locatário Azure ad B2C.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione cada usuário por vez (exclua o usuário *administrador de assinatura* no qual você está conectado no momento). Selecione **excluir** na parte inferior da página e selecione **Sim** quando solicitado.
1. Em **gerenciar**, selecione **Registros de aplicativo** (ou **registros de aplicativo (Herdado)** ).
1. Selecione **Exibir todos os aplicativos**
1. Selecione o aplicativo chamado **B2C-Extensions-app**, selecione **excluir**e, em seguida, selecione **Sim** quando solicitado.
1. Em **gerenciar**, selecione **configurações do usuário**.
1. Se houver, em **conexões de conta do LinkedIn**, selecione **não**e, em seguida, selecione **salvar**.
1. Em **gerenciar**, selecione **Propriedades**
1. Em **Gestão de acesso dos recursos do Azure**, selecione **Sim** e, em seguida, **Guardar**.
1. Saia do portal do Azure e, em seguida, entre novamente para atualizar seu acesso.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Na página **visão geral** , selecione **excluir diretório**. Siga as instruções na tela para concluir o processo.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Registros de aplicativo (versão prévia)](#tab/app-reg-preview/)

1. Entre no [portal do Azure](https://portal.azure.com/) como o administrador da *assinatura*. Use a mesma conta corporativa ou de estudante ou a mesma conta Microsoft que você usou para se inscrever no Azure.
1. Selecione o **diretório +** filtro de assinatura no menu superior e, em seguida, selecione o diretório que contém seu locatário de Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Exclua todos os **fluxos de usuário (políticas)** no locatário Azure ad B2C.
1. Selecione **registros de aplicativo (versão prévia)** e, em seguida, selecione a guia **todos os aplicativos** .
1. Exclua todos os aplicativos que você registrou.
1. Exclua o **B2C-Extensions-app**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione cada usuário por vez (exclua o usuário *administrador de assinatura* no qual você está conectado no momento). Selecione **excluir** na parte inferior da página e selecione **Sim** quando solicitado.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Em **gerenciar**, selecione **configurações do usuário**.
1. Se houver, em **conexões de conta do LinkedIn**, selecione **não**e, em seguida, selecione **salvar**.
1. Em **gerenciar**, selecione **Propriedades**
1. Em **Gestão de acesso dos recursos do Azure**, selecione **Sim** e, em seguida, **Guardar**.
1. Saia do portal do Azure e, em seguida, entre novamente para atualizar seu acesso.
1. Selecione **Azure Active Directory** no menu à esquerda.
1. Na página **visão geral** , selecione **excluir diretório**. Siga as instruções na tela para concluir o processo.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Posso obter Azure AD B2C como parte do Enterprise Mobility Suite?

Não, Azure AD B2C é um serviço pago conforme o uso do Azure e não faz parte do Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Como fazer relatar problemas com o Azure AD B2C?

Consulte [solicitações de suporte de arquivo para Azure Active Directory B2C](active-directory-b2c-support.md).
