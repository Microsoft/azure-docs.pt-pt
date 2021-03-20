---
title: 'Tutorial: Integração do Azure Ative Directory com a SAML SSO for Bamboo por resolução GmbH | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML SSO for Bamboo por resolução GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 8ec834d76692f78d1d7bc60ddbd4c73fe4adaede
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646120"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Tutorial: Integração do Azure Ative Directory com SAML SSO para Bambu por resolução GmbH

Neste tutorial, você aprenderá a integrar SAML SSO para Bambu através da resolução GmbH com Azure Ative Directory (Azure AD). Quando integrar o SAML SSO para Bambu através da resolução GmbH com Azure AD, pode:

* Control em Azure AD que tem acesso a SAML SSO para Bambu por resolução GmbH.
* Permita que os seus utilizadores sejam automaticamente inscritos no SSO SAML para Bamboo por resolução GmbH com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a SAML SSO para Bamboo por resolução GmbH, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* SAML SSO para Bambu por resolução Assinatura ativada pela GmbH

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SAML SSO para Bambu por resolução GmbH apoia **SP e IDP** iniciado SSO
* SAML SSO para Bambu por resolução A GmbH suporta o provisionamento do utilizador **Just In Time**

## <a name="add-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Adicione SSO SAML para Bambu por resolução GmbH da galeria

Para configurar a integração do SAML SSO para Bamboo através da resolução GmbH em Azure AD, precisa adicionar SSO SAML para Bambu por resolução GmbH da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** escreva **SSO SAML para Bambu por resolução GmbH** na caixa de pesquisa.
1. Selecione **SAML SSO para Bambu por resolução GmbH** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Configure e teste Azure AD SSO com SAML SSO para bambu por resolução GmbH

Configure e teste Azure AD SSO com SAML SSO para Bambu por resolução GmbH, utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado no SSO SAML para bambu por resolução GmbH.

Para configurar e testar o Azure AD SSO com SAML SSO para Bambu por resolução GmbH, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
     1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
     1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure o SAML SSO para bambu por resolução GmbH SSO](#configure-saml-sso-for-bamboo-by-resolution-gmbh-sso)** - para configurar as definições de Sign-On únicas no lado da aplicação.
    1. **[Criar SSO SAML para Bamboo por resolução Utilizador](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** de teste GmbH - para ter uma contraparte de Britta Simon em SAML SSO para Bamboo por resolução Resolução GmbHby resolução GmbH que está ligada à representação AZure AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa Azure AD SSO no portal Azure.
 
1. No portal Azure, no **SSO SAML para Bambu por resolução página** de integração de aplicações da GmbH, encontre a secção **Gestão** e selecione **Single Sign-On**.
1. Na página **'Selecione' Um Método de Sign-On Único,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, selecione o ícone de lápis para **configuração SAML básica** para editar as definições.

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)
4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

     Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a SAML SSO para bamboo através da resolução Da equipa de suporte ao cliente da GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na **configuração SAML SSO para bambu por resolução,** a secção GmbH, copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de senha show** e, em seguida, escreva a palavra-passe para baixo.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon utilize o Azure single sign-on, concedendo acesso ao SSO SAML para bambu por resolução GmbH.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **SAML SSO para bambu por resolução GmbH**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **de atribuição de adicionar,** selecione **Utilizadores e grupos**.
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-sso"></a>Configure SAML SSO para bambu por resolução GmbH SSO

1. Inscreva-se no seu SSO SAML para Bamboo por resolução do site da empresa GmbH como administrador.

1. No lado direito da barra de ferramentas principal, clique em **Add-ons de**  >  **Definições**.

    ![As Definições](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Vá à secção SECURITY, clique em **SAML SingleSignOn** na barra de menus.

    ![O Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Na **página de configuração do plugin SAML SIngleSignOn**, clique em **Adicionar idp**.

    ![O Add idp](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Na página escolha a sua página **de fornecedor de identidade SAML,** execute os seguintes passos:

    ![O fornecedor de identidade](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Selecione **Idp Type** como **AZURE AD**.

    b. Na caixa de texto **'Nome',** digite o nome.

    c. Na caixa de texto **Descrição,** digite a descrição.

    d. Clique em **Seguinte**.

1. Na página **de configuração do fornecedor de identidade** clique em **Seguinte**.

    ![A identidade config](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Na página **de metadados do Idp Import SAML,** clique em **Carregar Ficheiro** para carregar o ficheiro **METADADATA XML** que descarregou a partir do portal Azure.

    ![O idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Clique em **Seguinte**.

1. Clique em **Guardar definições**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Criar SSO SAML para Bambu por resolução Utilizador de teste GmbH

O objetivo desta secção é criar um utilizador chamado Britta Simon em SAML SSO for Bamboo por resolução GmbH. SAML SSO for Bamboo por resolução A GmbH suporta o provisionamento just-in-time e também os utilizadores podem ser criados manualmente, contacte [a SAML SSO for Bamboo através da resolução da equipa de suporte do Cliente GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) de acordo com o seu requisito.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para SAML SSO para Bambu através da resolução Sinal da GmbH no URL onde pode iniciar o fluxo de login.  

* Vá ao SSO SAML para Bamboo através da resolução GMBH Sign-on URL diretamente e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no SSO SAML para Bambu por resolução GmbH para a qual configura o SSO.

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no SSO SAML para Bambu por resolução, o azulejo gmbH nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no SSO SAML para Bambu por resolução GmbH para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado SAML SSO para Bamboo por resolução GmbH, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).