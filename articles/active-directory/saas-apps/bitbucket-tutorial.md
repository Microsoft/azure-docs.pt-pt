---
title: 'Tutorial: Integração do Azure Ative Directory com a SAML SSO para a Bitbucket por resolução GmbH | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML SSO para a Bitbucket por resolução GmbH.
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
ms.openlocfilehash: aa5e3e88ceb957728799f27482de7477ba6b7b48
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621251"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Tutorial: Integração do Azure Ative Directory com a SAML SSO para a Bitbucket por resolução GmbH

Neste tutorial, você aprenderá a integrar SAML SSO para Bitbucket por resolução GmbH com Azure Ative Directory (Azure AD). Quando integrar o SSO SAML para bitbucket por resolução GmbH com Azure AD, pode:

* Control em Azure AD que tem acesso a SSO da Bitbucket por resolução GmbH.
* Permita que os seus utilizadores sejam automaticamente inscritos no SSO SSO da Bitbucket por resolução gmbH com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a SAML SSO para a Bitbucket por resolução GmbH, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO para Bitbucket por resolução Assinatura ativada pela GmbH

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SAML SSO para Bitbucket por resolução GmbH suporta **SP e IDP** iniciado SSO
* SAML SSO para Bitbucket por resolução A GmbH suporta o fornecimento do utilizador **Just In Time**


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Adicione SSO SAML para Bitbucket por resolução GmbH da galeria

Para configurar a integração do SSO SAML para a Bitbucket através da resolução GmbH em Azure AD, precisa de adicionar SSO SAML para a Bitbucket através da resolução GmbH da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Ative Directory**.
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** escreva **SAML SSO para Bitbucket por resolução GmbH** na caixa de pesquisa.
1. Selecione **SAML SSO para Bitbucket por resolução GmbH** a partir dos resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Configure e teste Azure AD SSO para SAML SSO para Bitbucket por resolução GmbH

Configure e teste Azure AD SSO com SAML SSO para Bitbucket por resolução GmbH, utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado no SSO SAML para a Bitbucket por resolução GmbH.

Para configurar e testar o Azure AD SSO com SAML SSO para Bitbucket por resolução GmbH, execute os seguintes passos:


1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure o SSO SAML para a Bitbucket por resolução GmbH SSO](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** - para configurar as definições de Sign-On únicas no lado da aplicação.
    1. **[Crie o SAML SSO para bitbucket por resolução utilizador](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** de teste GmbH - para ter uma contraparte de Britta Simon em SSO SAML para a Bitbucket por resolução GmbH que está ligada à representação AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa Azure AD SSO no portal Azure.
 
1. No portal Azure, no **SSO SAML para Bitbucket por resolução página** de integração de aplicações GmbH, encontre a secção **Gerir** e selecione **Single Sign-On**.
1. Na página **'Selecione' Um Método de Sign-On Único,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, selecione o ícone de lápis para **configuração SAML básica** para editar as definições.

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** execute os seguintes passos se desejar configurar a aplicação no modo iniciado pelo **IDP:**


    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:
    
    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a SAML SSO para a Bitbucket por resolução a equipa de suporte do cliente gmbH](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

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

Nesta secção, você permite que B.Simon utilize o Azure single sign-on, concedendo acesso a SSO SAML para Bitbucket por resolução GmbH.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **SAML SSO para Bitbucket por resolução GmbH**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **de atribuição de adicionar,** selecione **Utilizadores e grupos**.
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>Configure SAML SSO para Bitbucket por resolução GmbH SSO

1. Iniciar s-on no seu SSO SAML para Bitbucket por resolução do site da empresa GmbH como administrador.

2. No lado direito da barra de ferramentas principal, clique em **Definições**.

3. Aceda à secção CONTAS, clique em **SAML SingleSignOn** na barra de menus.

    ![O Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Na **página de configuração do plugin SAML SIngleSignOn**, clique em **Adicionar idp**. 

    ![O Add idp](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Na página escolha a sua página **de fornecedor de identidade SAML,** execute os seguintes passos:

    ![O fornecedor de identidade](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Selecione **Idp Type** como **AZURE AD**.

    b. Na caixa de texto **'Nome',** digite o nome.

    c. Na caixa de texto **Descrição,** digite a descrição.

    d. Clique em **Seguinte**.

6. Na **página de configuração do fornecedor de identidade**, clique em **Seguinte**.

    ![A identidade config](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Na página **de metadados do Idp Import SAML,** clique em **Carregar Ficheiro** para carregar o ficheiro **METADADATA XML** que descarregou a partir do portal Azure.

    ![O idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Clique em **Seguinte**.

9. Clique em **Guardar definições**.

    ![A poupança](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Criar SSO SAML para Bitbucket por resolução Utilizador de teste GmbH

O objetivo desta secção é criar um utilizador chamado Britta Simon na SAML SSO para a Bitbucket por resolução GmbH. SAML SSO for Bitbucket por resolução A GmbH suporta o provisionamento just-in-time e também os utilizadores podem ser criados manualmente, contacte [a SAML SSO para a Bitbucket através da resolução da equipa de suporte do Cliente GmbH,](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) conforme o seu requisito.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para SAML SSO para Bitbucket por resolução Sinal gmbH no URL onde pode iniciar o fluxo de login.  

* Vá ao SSO SAML para Bitbucket por resolução GMBH Sign-on URL diretamente e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no SSO SAML para Bitbucket por resolução GmbH para a qual configura o SSO.

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no SSO SAML para Bitbucket por resolução, o azulejo GmbH nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no SSO SAML para Bitbucket por resolução GmbH para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o SSO SAML para Bitbucket por resolução, a GmbH pode impor controlos de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).