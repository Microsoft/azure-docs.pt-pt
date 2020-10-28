---
title: 'Tutorial: Integração do Azure Ative Directory com a Autenticação de Identidade da Plataforma CLOUD SAP Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a Autenticação de Identidade da Plataforma CLOUD SAP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: fc5e0270c4533eaae1310dc84b616d675a79c8ea
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895096"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Azure Ative Directy integração única (SSO) com autenticação de identidade da plataforma de nuvem SAP

Neste tutorial, você vai aprender como integrar a Autenticação de Identidade da Plataforma cloud SAP com O Diretório Ativo Azure (Azure AD). Quando integrar a Autenticação de Identidade da Plataforma CLOUD SAP com Azure AD, pode:

* Controlo em Azure AD que tem acesso à Autenticação de Identidade da Plataforma CLOUD SAP.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Autenticação de Identidade da Plataforma CLOUD SAP com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por autenticação de identidade da plataforma de nuvem SAP Cloud (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* A Autenticação de Identidade da Plataforma cloud SAP suporta **sSO** iniciado SP e **IDP**
* Assim que configurar a Autenticação de Identidade da Plataforma cloud SAP, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

Antes de mergulhar nos detalhes técnicos, é vital entender os conceitos que vai ver. Os Serviços de Autenticação de Identidade da Plataforma CLOUD SAP e Serviços da Federação de Diretórios Ativos permitem-lhe implementar SSO em aplicações ou serviços protegidos pela Azure AD (como idP) com aplicações e serviços SAP protegidos pela Autenticação de Identidade da Plataforma DE Nuvem SAP.

Atualmente, a Autenticação de Identidade da Plataforma CLOUD SAP atua como Fornecedor de Identidade Proxy para aplicações SAP. O Azure Ative Directory, por sua vez, atua como o principal Fornecedor de Identidade nesta configuração. 

O seguinte diagrama ilustra esta relação:

![Criar um utilizador de teste AZure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Com esta configuração, o seu inquilino de autenticação de identidade da plataforma SAP Cloud está configurado como uma aplicação fidedigna no Diretório Ativo Azure.

Todas as aplicações e serviços SAP que pretende proteger desta forma são posteriormente configurados na consola de gestão de autenticação de identidade da plataforma SAP Cloud.

Por isso, a autorização para o acesso a aplicações e serviços SAP tem de ser efetuada na Autenticação de Identidade da Plataforma SAP Cloud (em oposição ao Diretório Ativo do Azure).

Ao configurar a Autenticação de Identidade da Plataforma CLOUD SAP como uma aplicação através do Azure Ative Directory Marketplace, não precisa de configurar reclamações individuais ou afirmações SAML.

> [!NOTE]
> Atualmente apenas o Web SSO foi testado por ambas as partes. Os fluxos necessários para a comunicação App-to-API ou API-a-API devem funcionar, mas ainda não foram testados. Serão testados durante as atividades subsequentes.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionar autenticação de identidade da plataforma de nuvem SAP da galeria

Para configurar a integração da Autenticação de Identidade da Plataforma CLOUD SAP em Ad Azure, é necessário adicionar a Autenticação de Identidade da Plataforma SAP Cloud da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações** .
1. Para adicionar nova aplicação, selecione **Nova aplicação** .
1. Na secção Adicionar a partir da secção **de galeria,** **escreva a autenticação de identidade da plataforma de nuvem SAP** na caixa de pesquisa.
1. Selecione **a autenticação de identidade** da plataforma de nuvem SAP do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Configurar e testar a Azure AD um único sinal para autenticação de identidade da plataforma de nuvem SAP

Configure e teste Azure AD SSO com a autenticação de identidade da plataforma de nuvem SAP usando um utilizador de teste chamado **B.Simon** . Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Autenticação de Identidade da Plataforma de Nuvem SAP.

Para configurar e testar o Azure AD SSO com a Autenticação de Identidade da Plataforma de Nuvem SAP, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a autenticação de identidade da plataforma de nuvem SAP SSO](#configure-sap-cloud-platform-identity-authentication-sso)** - para configurar as definições de sinalização única no lado da aplicação.
    * Criar utilizador de teste de **[autenticação de identidade da plataforma SAP Cloud](#create-sap-cloud-platform-identity-authentication-test-user)** - para ter uma contrapartida de B.Simon na Autenticação de Identidade da Plataforma DE Nuvem SAP que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da **aplicação de autenticação de identidade da plataforma DA Nuvem SAP,** encontre a secção **Gerir** e selecione um único sinal de **sação** .
1. Na página de método **de inscrição** única, selecione **SAML** .
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Screenshot que mostra a secção "Configuração Básica S A M L" com as caixas de texto "Identifier" e "Answer U R L" realçadas e o botão "Save" selecionado.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e URL de resposta. Contacte a equipa de suporte do [Cliente de Autenticação de Identidade da Plataforma NUVEM SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter estes valores. Se não compreender o valor do Identificador, leia a documentação de autenticação de identidade da plataforma SAP Cloud sobre a [configuração do Inquilino SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado por **SP:**

    ![Domínio de autenticação de identidade da plataforma de nuvem SAP e informações únicas de acesso](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL de inscrição real. Por favor, use a sua aplicação de negócio específica URL de inscrição. Contacte a equipa de suporte do [Cliente de Autenticação de Identidade da Plataforma NUVEM SAP,](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) caso tenha alguma dúvida.

1. A aplicação de autenticação de identidade da plataforma SAP Cloud espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação de autenticação de identidade da plataforma SAP Cloud espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | nomePróprio | user.givenname |

8. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

9. Na secção configurar a **autenticação de identidade da plataforma de nuvem SAP,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory** , selecione **Utilizadores** , e, em seguida, selecione **Todos os utilizadores** .
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome** , introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso à Autenticação de Identidade da Plataforma cloud SAP.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações** .
1. Na lista de aplicações, selecione **A Autenticação de Identidade da Plataforma de Nuvem SAP** .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos** .

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Configurar a autenticação de identidade da plataforma SAP Cloud SSO

1. Para obter SSO configurado para a sua aplicação, vá à consola de administração de autenticação de identidade da plataforma SAP Cloud. O URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin` . Em seguida, leia a documentação sobre a autenticação de identidade da plataforma SAP Cloud na [integração com o Microsoft Azure AD](https://developers.sap.com/tutorials/cp-ias-azure-ad.html).

2. No portal Azure, selecione o botão **Guardar.**

3. Continue com o seguinte apenas se quiser adicionar e ativar sSO para outra aplicação SAP. Repita os passos sob a secção **Adicionar a Autenticação de Identidade da Plataforma de Nuvem SAP da galeria** .

4. No portal Azure, na página de integração da **aplicação de autenticação de identidade** da plataforma de nuvem SAP, selecione **Linked Sign-on** .

    ![Configurar Sign-On Ligados](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Guarde a configuração.

> [!NOTE]
> A nova aplicação aproveita a configuração única de inscrição da aplicação SAP anterior. Certifique-se de que utiliza os mesmos Fornecedores de Identidade Corporativa na consola de administração de autenticação de identidade da plataforma SAP Cloud.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Criar utilizador de teste de autenticação de identidade da plataforma de nuvem SAP

Não é necessário criar um utilizador na Autenticação de Identidade da Plataforma CLOUD SAP. Os utilizadores que se encontrem na loja de utilizadores Azure AD podem utilizar a funcionalidade SSO.

A autenticação de identidade da plataforma de nuvem SAP suporta a opção Federação de Identidade. Esta opção permite que a aplicação verifique se os utilizadores que são autenticados pelo fornecedor de identidade corporativa existem na loja de utilizadores da Sap Cloud Platform Authentication.

A opção Federação de Identidade é desativada por padrão. Se a Federação de Identidade estiver ativada, apenas os utilizadores que são importados na Autenticação de Identidade da Plataforma DE Nuvem SAP podem aceder à aplicação.

Para obter mais informações sobre como ativar ou desativar a Federação de Identidade com a Autenticação de Identidade da Plataforma DE Nuvem SAP, consulte "Enable Identity Federation with SAP Cloud Platform Authentication" in [Configure Identity Federation with the User Store of SAP Cloud Platform Authentication](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de autenticação de identidade da plataforma CLOUD SAP no Painel de Acesso, deverá ser automaticamente inscrito na Autenticação de Identidade da Plataforma cloud SAP para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente a autenticação de identidade da plataforma de nuvem SAP com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger a autenticação de identidade da plataforma de nuvem SAP com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)