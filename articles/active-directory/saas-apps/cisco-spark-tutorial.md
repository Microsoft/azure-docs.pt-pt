---
title: 'Tutorial: Azure Ative Directory Single sign-on (SSO) integração com a Cisco Webex Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cisco Webex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: b6da84c1be38dde72b663de1db3756e77f4db915
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91759852"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Tutorial: Azure Ative Directory Single sign-on (SSO) integração com a Cisco Webex

Neste tutorial, você vai aprender a integrar Cisco Webex com Azure Ative Directory (Azure AD). Quando integrar a Cisco Webex com Azure AD, pode:

* Controle em Azure AD que tem acesso à Cisco Webex.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Cisco Webex com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Cisco Webex assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Cisco Webex suporta SSO iniciado **SP.**
* A Cisco Webex suporta o fornecimento **automatizado** de utilizadores.
* Assim que configurar a Cisco Webex, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionar Cisco Webex da galeria

Para configurar a integração da Cisco Webex no Azure AD, é necessário adicionar a Cisco Webex da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** **digite Cisco Webex** na caixa de pesquisa.
1. Selecione **Cisco Webex** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Configurar e testar a Azure AD um único sign-on para Cisco Webex

Configure e teste Azure AD SSO com Cisco Webex usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Cisco Webex.

Para configurar e testar o Azure AD SSO com a Cisco Webex, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
2. **[Configure](#configure-cisco-webex)** a Cisco Webex para configurar as definições SSO no lado da aplicação.
    1. Crie o utilizador de **[teste Cisco Webex](#create-cisco-webex-test-user)** para ter uma contraparte de B.Simon na Cisco Webex que está ligada à representação AD AD do utilizador.
3. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Cisco Webex,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** faça o upload do ficheiro **de metadados do Fornecedor de Serviços** descarregado e configuure a aplicação realizando os seguintes passos:

    >[!Note]
    >Você receberá o ficheiro de metadados do Fornecedor de Serviços da secção **Configure Cisco Webex,** que é explicado mais tarde no tutorial. 

    a. Clique **em Carregar o ficheiro de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    c. Após a conclusão com sucesso do ficheiro de metadados do Fornecedor de Serviços, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção **de Configuração BÁSICA SAML:**

    No **Signo na** caixa de texto URL, cole o valor do URL de **resposta,** que é preenchido automaticamente por upload de ficheiros de metadados SP.

1. A aplicação Cisco Webex espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Cisco Webex espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
  
    | Nome |  Atributo de origem|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar Cisco **Webex,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Cisco Webex.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Cisco Webex**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cisco-webex"></a>Configurar Cisco Webex

1. Para automatizar a configuração dentro do Cisco Webex, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar o Cisco Webex** irá direcioná-lo para a aplicação Cisco Webex. A partir daí, forneça as credenciais de administração para assinar na Cisco Webex. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Cisco Webex manualmente, inscreva-se na [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) com as suas credenciais de administrador completo.

4. Selecione **Definições** e na secção **autenticação,** clique em **Modificar**.

    ![A screenshot mostra Definições de autenticação onde pode selecionar Modificar.](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. **Selecione Integrar um fornecedor de identidade de terceiros. (Avançado)** e ir para o próximo ecrã.

6. Na página **de Metadados do Idp Import,** ou arraste e deixe cair o ficheiro de metadados Azure AD na página ou utilize a opção do navegador de ficheiro para localizar e carregar o ficheiro de metadados AZure AD. Em seguida, **selecione Exigir certificado assinado por uma autoridade de certificado em Metadados (mais seguro)** e clique em **Seguinte**.

    ![A screenshot mostra a página de Metadados Import I d P.](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Selecione **Test SSO Connection**, e quando um novo separador de navegador abrir, autente com Azure AD ao iniciar sessão.

8. Volte ao separador de navegador **Cisco Cloud Collaboration Management.** Se o teste tiver sido bem sucedido, selecione **Este teste foi bem sucedido. Ativar a opção Single Sign-On** e clicar **em Seguinte**.

### <a name="create-cisco-webex-test-user"></a>Criar utilizador de teste Cisco Webex

Nesta secção, cria-se um utilizador chamado B.Simon na Cisco Webex. Nesta secção, cria-se um utilizador chamado B.Simon na Cisco Webex.

1. Vá à [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) com as suas credenciais de administrador completo.

2. Clique **nos Utilizadores** e, em seguida, **gerencie os Utilizadores.**
   
    ![A screenshot mostra a página do Utilizadores onde pode gerir os utilizadores.](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. Na janela **'Gerir** o utilizador', selecione **Manualmente adicionar ou modificar os utilizadores** e clique em **Seguinte**.

4. Selecione **Nomes e endereço de e-mail**. Em seguida, preencha a caixa de texto da seguinte forma:

    ![A screenshot mostra a caixa de diálogo dos Utilizadores de Mange onde pode adicionar ou modificar manualmente os utilizadores.](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. Na caixa de texto **Name Name,** escreva o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **Do Último Nome,** escreva o apelido de utilizador como **Simon**.

    c. Na caixa de texto **do endereço de e-mail,** escreva o endereço de e-mail do utilizador como b.simon@contoso.com .

5. Clique no sinal de mais para adicionar B.Simon. Em seguida, clique **em Seguinte**.

6. Na janela **'Adicionar Serviços para Utilizadores',** clique em **Guardar** e, em seguida, **terminar**.

## <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Cisco Webex no Painel de Acesso, deverá ser automaticamente inscrito no Cisco Webex para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Cisco Webex com Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Cisco Webex com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-webex)