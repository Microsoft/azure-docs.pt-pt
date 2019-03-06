---
title: Perguntas mais frequentes sobre o Azure Active Directory B2C | Documentos da Microsoft
description: Perguntas freqüentes (FAQ) sobre o Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 12b8fc2fb8df7fd730e220a3c1b60097ed7748a2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433727"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Perguntas Mais Frequentes (FAQ) 
Esta página responde às perguntas mais frequentes sobre o Azure Active Directory (Azure AD) B2C. Manter a verificação de volta para as atualizações.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Por que não é possível a aceder a extensão do Azure AD B2C no portal do Azure?
Existem dois motivos comuns para por que a extensão do Azure AD não está a funcionar para.  O Azure AD B2C requer a sua função de utilizador no diretório para ser administrador global.  Contacte o administrador se acha que devem ter acesso.  Se tiver privilégios de administrador global, certifique-se de que está num diretório do Azure AD B2C e não um diretório do Azure Active Directory.  Pode ver as instruções para [criar um inquilino do Azure AD B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Pode utilizar funcionalidades do Azure AD B2C no meu inquilino do Azure AD existente, com base em funcionários?
O Azure AD e do Azure AD B2C são ofertas de produtos separada e não podem coexistir no mesmo inquilino.  Um inquilino do Azure AD representa uma organização.  Um inquilino do Azure AD B2C representa uma coleção de identidades a serem utilizadas com aplicações das entidades confiadoras.  Com as políticas personalizadas (em pré-visualização pública), o Azure AD B2C pode federar com o Azure AD, que permite a autenticação de funcionários numa organização.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Posso utilizar do Azure AD B2C para fornecer início de sessão social (Facebook e Google +) no Office 365?
O Azure AD B2C não pode ser utilizado para autenticar os utilizadores do Microsoft Office 365.  O Azure AD é a solução da Microsoft para gerir o acesso dos colaboradores às aplicações SaaS e tem recursos projetados para essa finalidade, como o acesso condicional e de licenciamento.  O Azure AD B2C fornece uma plataforma de gestão de identidades e acessos para a criação de aplicações web e móveis.  Quando o Azure AD B2C está configurado para federar a um inquilino do Azure AD, o inquilino do Azure AD gere o acesso do funcionário para aplicativos que dependem do Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Quais são as contas locais no Azure AD B2C? Como eles são diferentes das contas profissionais ou escolares no Azure AD?
No inquilino do Azure AD, os utilizadores que pertencem ao inquilino início de sessão com um endereço de e-mail do formulário `<xyz>@<tenant domain>`.  O `<tenant domain>` é um dos domínios verificados do inquilino ou inicial `<...>.onmicrosoft.com` domínio. Este tipo de conta é uma conta escolar ou profissional.

Num inquilino do Azure AD B2C, a maioria das aplicações deseja que o usuário para iniciar sessão com qualquer endereço de e-mail arbitrário (por exemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com, ou jim@live.com). Este tipo de conta é uma conta local.  Também suportamos nomes de usuário arbitrários como contas locais (por exemplo, o João, bob, sarah ou jim). Pode escolher um destes dois tipos de conta local ao configurar fornecedores de identidade para o Azure AD B2C no portal do Azure. No seu inquilino do Azure AD B2C, clique em **fornecedores de identidade** e, em seguida, selecione **Username** em contas locais. 

Sempre é necessário criar contas de utilizador para aplicações através de um fluxo de inscrição de utilizador, o fluxo de utilizador de inscrição ou início de sessão, ou ao utilizar o Azure AD Graph API. Contas de utilizador criadas no portal do Azure só são utilizadas para administrar o inquilino.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Os fornecedores de identidade social suporta agora? Quais planeia suportar no futuro?
Suportamos, atualmente, o Facebook, Google +, LinkedIn, Amazon, Twitter (pré-visualização), WeChat (pré-visualização), Weibo (pré-visualização) e QQ (pré-visualização). Vamos adicionar suporte para outros fornecedores de identidade de redes sociais populares com base na procura dos clientes.

O Azure AD B2C também adicionou suporte para [políticas personalizadas](active-directory-b2c-overview-custom.md).  Estes [políticas personalizadas](active-directory-b2c-overview-custom.md) permitir que um desenvolvedor crie sua própria política de que com qualquer fornecedor de identidade que suporta [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) ou SAML. 

Comece com as políticas personalizadas ao dar uma olhada em nosso [pacote de iniciante da política personalizada](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Pode configurar âmbitos para obter mais informações sobre os consumidores de vários fornecedores de identidade de redes sociais?
Não. Os âmbitos de padrão usados para nosso conjunto de fornecedores de identidade social de suportados são:

* Facebook: e-mail
* Google+: email
* Conta Microsoft: perfil de e-mail do openid
* Amazon: perfil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>O meu aplicativo tem de ser executado no Azure, para que funcione com o Azure AD B2C?
Não, não é possível hospedar seu aplicativo em qualquer lugar (na cloud ou no local). Tudo o que precisa interagir com o Azure AD B2C é a capacidade de enviar e receber pedidos HTTP em pontos de extremidade acessíveis publicamente.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tenho vários inquilinos do Azure AD B2C. Como posso geri-las no portal do Azure?
Antes de abrir o "Azure AD B2C" no menu do lado esquerdo do portal do Azure, tem de mudar para o diretório que pretende gerir.  Mudar de diretórios, clicando em sua identidade no canto superior direito do portal do Azure, em seguida, escolha um diretório na lista pendente que é apresentada.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Como personalizar mensagens de correio eletrónico de verificação (o conteúdo e o "de:" campo) enviada pelo Azure AD B2C?
Pode utilizar o [funcionalidade de imagem corporativa](../active-directory/fundamentals/customize-branding.md) para personalizar o conteúdo de mensagens de e-mail de verificação. Especificamente, esses dois elementos da mensagem de e-mail podem ser personalizados:

* **Logótipo de faixa**: Apresentado no canto inferior direito.
* **Cor de fundo**: Mostrado na parte superior.

    ![Captura de ecrã de um e-mail de verificação personalizada](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

A assinatura de e-mail contém o nome do inquilino do Azure AD B2C que indicou quando criou pela primeira vez o inquilino do Azure AD B2C. Pode alterar o nome a utilizar estas instruções:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/) como Administrador Global.
1. Abra o **do Azure Active Directory** painel.
1. Clique nas **propriedades** separador.
1. Alteração da **nome** campo.
1. Clique em **Guardar** no início da página.

Atualmente não é possível alterar o "de:" campo no email.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Como posso migrar meu os nomes de utilizador existente, as palavras-passe e perfis do banco de dados para o Azure AD B2C?
Pode utilizar o Azure AD Graph API para escrever a sua ferramenta de migração. Consulte a [guia de migração de utilizador](active-directory-b2c-user-migration.md) para obter detalhes.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>O fluxo de utilizador de palavra-passe é utilizado para contas locais no Azure AD B2C?
O fluxo de utilizador de palavra-passe do Azure AD B2C para contas locais é baseado na política para o Azure AD. O Azure AD B2C da inscrição, inscreva-se ou fluxos de utilizador de reposição de início de sessão e palavra-passe, utilizam a força da palavra-passe "segura" e não expirarem as palavras-passe. Leitura a [política de palavra-passe do Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx) para obter mais detalhes. Para obter informações sobre bloqueios de conta e palavras-passe, consulte [gere as ameaças aos recursos e dados no Azure Active Directory B2C](active-directory-b2c-reference-threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Pode utilizar o Azure AD Connect para migrar as identidades de consumidor que estão armazenadas no meu diretório de Active Directory no local ao Azure AD B2C?
Não, do Azure AD Connect não foi concebido para funcionar com o Azure AD B2C. Considere utilizar o [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) para a migração de utilizador.  Consulte a [guia de migração de utilizador](active-directory-b2c-user-migration.md) para obter detalhes.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Meu aplicativo pode abrir páginas do Azure AD B2C numa iFrame?
Não, por motivos de segurança, as páginas do Azure AD B2C não podem ser abertas num iFrame.  Nosso serviço se comunica com o navegador não ser proibidas em iFrames.  A Comunidade de segurança em geral e a especificação do OAUTH2, recomenda-se contra a utilização de iFrames para experiências de identidade devido ao risco de jacking de clique.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Azure AD B2C funciona com sistemas CRM, como o Microsoft Dynamics?
Integração com o Portal do Microsoft Dynamics 365 está disponível.  Ver [configurar o Portal do Dynamics 365 para utilizar o Azure AD B2C para autenticação](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Faz do Azure AD B2C funcionam com o SharePoint no local 2016 ou anterior?
O Azure AD B2C, não se destina a ser o SharePoint externo parceiro cenário de compartilhamento de; ver [do Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) em vez disso.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Posso utilizar do Azure AD B2C ou B2B para gerir identidades externas?
Leia este artigo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para saber mais sobre como aplicar os recursos adequados aos seus cenários de identidade externo.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Que recursos de auditoria e relatórios do Azure AD B2C fornece? Eles são os mesmos que no Azure AD Premium?
Não, o Azure AD B2C não suporta o mesmo conjunto de relatórios como o Azure AD Premium. No entanto, existem muitos aspectos de comuns:

* **Relatórios de início de sessão** são um registro de cada início de sessão com detalhes reduzidas.
* **Relatórios de auditoria** incluem tanto atividade de administrador e a atividade das aplicações. 
* **Relatórios de utilização** incluem o número de utilizadores, o número de inícios de sessão e volume de MFA. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Eu localizo a interface do Usuário de páginas fornecidas pelo Azure AD B2C? Que idiomas são suportados?
Sim!  Leia sobre [personalização de idioma](active-directory-b2c-reference-language-customization.md), que está em pré-visualização pública.  Fornecemos traduções para os 36 idiomas, e pode substituir qualquer cadeia de caracteres de acordo com as suas necessidades.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Posso utilizar o meus próprio URLs em minhas páginas de inscrição e início de sessão que são servidas pelo Azure AD B2C? Por exemplo, altere o URL de login.microsoftonline.com para login.contoso.com?
Não é atualmente. Esta funcionalidade está no nosso plano. Verificar o seu domínio no **domínios** separador no portal do Azure não realizar esse objetivo.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Como posso eliminar o inquilino B2C do Azure AD?
Siga estes passos para eliminar o inquilino do Azure AD B2C:

1. Elimine todas as políticas no seu inquilino do Azure AD B2C.
1. Agora iniciar sessão para o [portal do Azure](https://portal.azure.com/) como o administrador da subscrição. (Utilizar o mesmo ou conta profissional ou a mesma conta Microsoft que utilizou para se inscrever no Azure.)
1. Comutador para o inquilino do Azure AD B2C que pretende eliminar.
2. Navegue para o menu à esquerda do Active Directory.
3. Selecionar **Utilizadores e grupos**.
4. Selecione cada utilizador, por sua vez (excluir o utilizador de administrador da subscrição tem atualmente sessão iniciada como). Clique em **elimine** na parte inferior da página e clique em **Sim** quando lhe for pedido.
5. Clique nas **registos das aplicações**.
6. Selecione aplicativo chamado **b2c-extensions-app**. Clique em **elimine** e clique em **Sim** quando lhe for pedido.
7. Selecione **Descrição geral**.
8. Clique em **eliminar o diretório**. Para concluir o processo, siga as instruções no ecrã.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Pode obter do Azure AD B2C como parte do Enterprise Mobility Suite?
Não, o Azure AD B2C é uma serviço do Azure pay as you go e não faz parte do Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Como relato problemas com o Azure AD B2C?
Ver [pedidos de suporte de ficheiros do Azure Active Directory B2C](active-directory-b2c-support.md).
