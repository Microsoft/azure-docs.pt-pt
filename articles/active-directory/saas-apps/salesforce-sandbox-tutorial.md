---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Salesforce Sandbox [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9d6d11b6b56483f954049fdc1858db31f35c14a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76290008"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com salesforce Sandbox

Neste tutorial, você vai aprender a integrar salesforce Sandbox com Azure Ative Directory (Azure AD). Quando integrar a Salesforce Sandbox com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Salesforce Sandbox.
* Ative que os seus utilizadores sejam automaticamente inscritos na Salesforce Sandbox com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Salesforce Sandbox única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Salesforce Sandbox suporta **SP e IDP** iniciadoS SSO
* Salesforce Sandbox suporta o provisionamento de utilizadores **justo no tempo**
* Salesforce Sandbox suporta fornecimento [ **automatizado** de utilizadores](salesforce-sandbox-provisioning-tutorial.md)
* Assim que configurar a Caixa de Areia Salesforce, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionando Salesforce Sandbox da galeria

Para configurar a integração da Salesforce Sandbox em Azure AD, você precisa adicionar Salesforce Sandbox da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Salesforce Sandbox** na caixa de pesquisa.
1. Selecione **Salesforce Sandbox** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Configure e teste Azure AD único sign-on para Salesforce Sandbox

Configure e teste Azure AD SSO com Salesforce Sandbox utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Salesforce Sandbox.

Para configurar e testar o Azure AD SSO com a Salesforce Sandbox, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Salesforce Sandbox SSO](#configure-salesforce-sandbox-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador de teste Salesforce Sandbox](#create-salesforce-sandbox-test-user)** - para ter uma contrapartida de B.Simon na Salesforce Sandbox que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Salesforce Sandbox,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **basic SAML Configuration,** se tiver ficheiro de **metadados do Fornecedor** de Serviços e pretender configurar no modo iniciado do **IDP,** execute os seguintes passos:

    a. Clique no **ficheiro de metadados de upload**.

    ![Upload ficheiro de metadados](common/upload-metadata.png)

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    ![escolher ficheiro de metadados](common/browse-upload-metadata.png)

    > [!NOTE]
    > Você receberá o ficheiro de metadados do fornecedor de serviços do portal de administração Salesforce Sandbox, que é explicado mais tarde no tutorial.

    c. Após o ficheiro de metadados ser carregado com sucesso, o valor URL de **resposta** será preenchido automaticamente na caixa de texto **URL de resposta.**

    ![image](common/both-replyurl.png)

    > [!Note]
    > Se o valor **do URL** de resposta não for automaticamente pollulado, preencha o valor manualmente de acordo com o seu requisito.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Metadata XML** a partir das opções dadas e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção **'Venda's Sandbox,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso à Salesforce Sandbox.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Salesforce Sandbox**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-salesforce-sandbox-sso"></a>Configure Salesforce Sandbox SSO

1. Abra um novo separador no seu navegador e inscreva-se na sua conta de administrador salesforce Sandbox.

2. Clique no ícone **configuração** no **canto** superior direito da página.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/configure1.png)

3. Desloque-se até às **DEFINIÇÕES** no painel de navegação esquerdo, clique em **Identidade** para expandir a secção relacionada. Em seguida, clique em **Definições de Sinal Único**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Na página **De definições de sinal único,** clique no botão **Editar.**

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selecione **SAML Ativado**, e, em seguida, clique em **Guardar**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Para configurar as definições de inscrição única sAML, clique em **Novo ficheiro de Metadados**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Clique **em Escolher Ficheiro** para carregar o ficheiro XML de metadados que descarregou a partir do portal Azure e clique em **Criar**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Na página **De Definições de Sinais Únicos SAML,** os campos povoam-se automaticamente e clicam em guardar.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Na página **Dedefinições de Sinais Únicos,** clique no botão **Descarregamento de Metadados** para descarregar o ficheiro de metadados do fornecedor de serviços. Utilize este ficheiro na secção **basic SAML Configuração** no portal Azure para configurar os URLs necessários, conforme explicado acima.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/configure4.png)

10. Se desejar configurar a aplicação no modo iniciado por **SP,** seguem-se os pré-requisitos para o seguinte:

    a. Devia ter um domínio verificado.

    b. Você precisa configurar e ativar o seu domínio na Salesforce Sandbox, os passos para isso são explicados mais tarde neste tutorial.

    c. No portal Azure, na secção **basic SAML De configuração,** clique em **Definir URLs adicionais** e executar o seguinte passo:
  
    ![Salesforce Sandbox Domain e URLs informações únicas de inscrição](common/both-signonurl.png)

    Na caixa de texto **smS sign-on** URL, escreva o valor utilizando o seguinte padrão:`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Este valor deve ser copiado do portal Salesforce Sandbox uma vez ativado o domínio.

11. Na secção certificado de **assinatura SAML,** clique em **Metadados da Federação XML** e, em seguida, guarde o ficheiro xml no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

12. Abra um novo separador no seu navegador e inscreva-se na sua conta de administrador salesforce Sandbox.

13. Clique no ícone **configuração** no **canto** superior direito da página.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/configure1.png)

14. Desloque-se até às **DEFINIÇÕES** no painel de navegação esquerdo, clique em **Identidade** para expandir a secção relacionada. Em seguida, clique em **Definições de Sinal Único**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Na página **De definições de sinal único,** clique no botão **Editar.**

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selecione **SAML Ativado**, e, em seguida, clique em **Guardar**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Para configurar as definições de inscrição única sAML, clique em **Novo ficheiro de Metadados**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Clique **em Escolher Ficheiro** para carregar o ficheiro XML dos metadados e clique em **Criar**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Na página De Definições de **Sinais Únicos SAML,** os campos povoam-se automaticamente, digitam o nome da configuração (por exemplo: *SPSSOWAAD_Test),* na caixa de texto **nome** e clique em guardar.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Para ativar o seu domínio na Salesforce Sandbox, execute os seguintes passos:

    > [!NOTE]
    > Antes de ativar o domínio, é necessário criar o mesmo na Salesforce Sandbox. Para mais informações, consulte [Definição](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)do seu nome de domínio . Uma vez criado o domínio, certifique-se de que está configurado corretamente.

21. No painel de navegação à esquerda na Salesforce Sandbox, clique em **Definições da Empresa** para expandir a secção relacionada e, em seguida, clique em **My Domain**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Na secção **de Configuração de Autenticação,** clique em **Editar**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Na secção de **Configuração de Autenticação,** como Serviço de **Autenticação,** selecione o nome da Definição de Sinal Único SAML que definiu durante a configuração SSO na Caixa de Areia Salesforce e clique em **Guardar**.

    ![Configurar um único sinal](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Criar o utilizador de teste Salesforce Sandbox

Nesta secção, um utilizador chamado Britta Simon é criado na Salesforce Sandbox. A Salesforce Sandbox suporta o fornecimento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador ainda não existir na Salesforce Sandbox, um novo é criado quando se tenta aceder à Salesforce Sandbox. A Salesforce Sandbox também suporta o fornecimento automático de utilizadores, pode encontrar mais detalhes [aqui](salesforce-sandbox-provisioning-tutorial.md) sobre como configurar o fornecimento automático de utilizadores.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Salesforce Sandbox no Painel de Acesso, deve ser automaticamente inscrito na Caixa de Areia Salesforce para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente salesforce Sandbox com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [Configurar o fornecimento de utilizadores](salesforce-sandbox-provisioning-tutorial.md)

- [Como proteger a Salesforce Sandbox com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
