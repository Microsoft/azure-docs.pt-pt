---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com firewall fortiWeb Web Application Firewall | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o FortiWeb Web Application Firewall.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: e34664bd81023da7a50b8ff4645c670146ef2554
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731940"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Tutorial: Azure Ative Directory integração única (SSO) com firewall de aplicação web fortiWeb

Neste tutorial, você vai aprender a integrar FortiWeb Web Application Firewall com Azure Ative Directy (Azure AD). Quando integrar firewall de aplicação web FortiWeb com Azure AD, pode:

* Controle em Azure AD que tem acesso a Firewall de aplicações web FortiWeb.
* Ativar os seus utilizadores a serem automaticamente inscritos no Firewall de Aplicações Web FortiWeb com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* FortiWeb Web Application Firewall subscrição única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* FortiWeb Web Application Firewall suporta SSO iniciado **SP.**

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>Adicionar firewall de aplicação web fortiWeb da galeria

Para configurar a integração do Firewall de aplicações web FortiWeb em AD Azure, precisa adicionar Firewall fortiWeb Web Application Firewall da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** digite **Firewall de aplicação web FortiWeb** na caixa de pesquisa.
1. Selecione **FortiWeb Web Application Firewall** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>Configure e teste Azure AD SSO para firewall de aplicações web fortiWeb

Configure e teste Azure AD SSO com FortiWeb Web Application Firewall usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Firewall de Aplicações Web FortiWeb.

Para configurar e testar O Azure AD SSO com firewall fortiWeb Web Application Firewall, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure FortiWeb Web Application Firewall SSO](#configure-fortiweb-web-application-firewall-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create FortiWeb Web Application Firewall test user](#create-fortiweb-web-application-firewall-test-user)** - para ter uma contraparte de B.Simon em FortiWeb Web Application Firewall que está ligada à representação AD Ad do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **FortiWeb Web Application Firewall,** encontre a secção **'Gerir'** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

   a. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://www.<CUSTOMER_DOMAIN>.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://www.<CUSTOMER_DOMAIN>.com`

    d. Na caixa de texto **URL logout,** digite um URL utilizando o seguinte padrão: `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` é um identificador de nome que será usado mais tarde ao fornecer configuração para FortiWeb.
    > Contacte [a equipa de suporte do FortiWeb Web Application Firewall](mailto:support@fortinet.com) para obter os valores reais de URL. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use o Azure single sign-on, concedendo acesso ao Firewall de aplicações web fortiWeb.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **FortiWeb Web Application Firewall**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-fortiweb-web-application-firewall-sso"></a>Configurar fortiWeb Web Application Firewall SSO

1.  Navegue para `https://<address>:8443` onde `<address>` está o FQDN ou o endereço IP público atribuído ao FortiWeb VM.

2.  Iniciar sação utilizando as credenciais de administrador fornecidas durante a implementação do FortiWeb VM.

1. Execute os seguintes passos na página seguinte.

    ![Página do servidor SAML](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  No menu da esquerda, clique no **Utilizador**.

    b.  No Utilizador, clique **no Servidor Remoto**.

    c.  Clique **no SERVIDOR SAML**.

    d.  Clique **em Criar Novo**.

    e.  No campo **Nome,** forneça o valor `<fwName>` para usado na secção Configure Azure AD.

    f.  Na caixa de texto ID da **Entidade,** cole o valor **identificador Azure AD** que copiou do portal Azure.

    exemplo, Ao lado dos **Metadados,** clique em **Escolher Ficheiro** e selecione o ficheiro **XML dos metadados** da Federação que descarregou a partir do portal Azure.

    h.  Clique em **OK**.

### <a name="create-a-site-publishing-rule"></a>Criar uma regra de publicação de site

1.  Navegue para `https://<address>:8443` onde `<address>` está o FQDN ou o endereço IP público atribuído ao FortiWeb VM.

1.  Iniciar sação utilizando as credenciais de administrador fornecidas durante a implementação do FortiWeb VM.
1. Execute os seguintes passos na página seguinte.

    ![Regra de publicação do site](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  No menu à esquerda, clique em **'Entrega de Aplicação'.**
    
    b.  Em **Entrega de Aplicações**, clique **em Site Publicar.**
    
    c.  Em **Site Publish,** clique **em Site Publish.**
    
    d.  Clique **na regra de publicação do site.**
    
    e.  Clique **em Criar Novo**.
    
    f.  Forneça um nome para a regra de publicação do site.
    
    exemplo,  Ao lado **do Tipo de Sítio Publicado,** clique em Expressão **Regular**.
    
    i.  Ao lado **do Site Publicado,** forneça uma sequência que corresponda ao cabeçalho do anfitrião do site que está a publicar.
    
    j.  Junto ao **Caminho,** forneça um /.
    
    k.  Junto ao Método de **Autenticação do Cliente,** selecione **Autenticação SAML**.
    
    l.  No drop-down do **SERVIDOR SAML,** selecione o SERVIDOR SAML que criou anteriormente.
    
    m.  Clique em **OK**.


### <a name="create-a-site-publishing-policy"></a>Criar uma política de publicação de sites

1.  Navegue para `https://<address>:8443` onde `<address>` está o FQDN ou o endereço IP público atribuído ao FortiWeb VM.

2.  Iniciar sação utilizando as credenciais de administrador fornecidas durante a implementação do FortiWeb VM.

1. Execute os seguintes passos na página seguinte.

    ![Política de Publicação de Sites](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  No menu à esquerda, clique em **'Entrega de Aplicação'.**

    b.  Em **Entrega de Aplicações**, clique **em Site Publicar.**

    c.  Em **Site Publish,** clique **em Site Publish.**

    d.  Clique **na Política de Publicação do Site.**

    e.  Clique **em Criar Novo**.

    f.  Fornecer um nome para a Política de Publicação do Site.

    exemplo,  Clique em **OK**.

    h.  Clique **em Criar Novo**.

    i.  Na **regra** de entrega, selecione a regra de publicação do site que criou anteriormente.

    j.  Clique em **OK**.

### <a name="create-and-assign-a-web-protection-profile"></a>Criar e atribuir um perfil de proteção web

1.  Navegue para `https://<address>:8443` onde `<address>` está o FQDN ou o endereço IP público atribuído ao FortiWeb VM.

2.  Iniciar sação utilizando as credenciais de administrador fornecidas durante a implementação do FortiWeb VM.
3.  No menu da esquerda, clique em **Política**.
4.  Em **Política**, clique **no Perfil de Proteção Web**.
5.  Clique **em Inline Standard Protection** e clique em **Clone**.
6.  Forneça um nome para o novo perfil de proteção web e clique **em OK**.
7.  Selecione o novo perfil de proteção web e clique em **Editar.**
8.  Ao lado **da Site Publish,** selecione a política de publicação do site que criou anteriormente.
9.  Clique em **OK**.
 
    ![publicação do site](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. No menu da esquerda, clique em **Política**.
11. Em **'Política'** clique na **Política do Servidor**.
12. Selecione a política do servidor utilizada para publicar o web site para o qual deseja utilizar o Azure Ative Directory para autenticação.
13. Clique em **Editar**.
14. No drop-down do Perfil de **Proteção Web,** selecione o perfil de proteção web que acabou de criar.
15. Clique em **OK**.
16. Tente aceder ao URL externo ao qual o FortiWeb publica o site. Deve ser redirecionado para o Azure Ative Directory para autenticação.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>Criar o utilizador de teste de firewall de aplicação web FortiWeb

Nesta secção, cria um utilizador chamado Britta Simon em FortiWeb Web Application Firewall. Trabalhe com a [equipa de suporte fortiWeb Web Application Firewall](mailto:support@fortinet.com) para adicionar os utilizadores na plataforma FortiWeb Web Application Firewall. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição na web do FortiWeb, onde pode iniciar o fluxo de login. 

* Aceda diretamente ao URL de inscrição na Web fortiWeb e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo da Aplicação Web FortiWeb nas Minhas Apps, este será redirecionado para o URL de inscrição web fortiWeb. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado FortiWeb Web Application Firewall, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).