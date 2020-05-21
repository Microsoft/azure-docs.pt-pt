---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com O Dia de Trabalho [ O SSO) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83fd986e31410e34871d1b795db0c8c83ece688e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648609"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com workday

Neste tutorial, você vai aprender a integrar o Workday com o Azure Ative Directory (Azure AD). Quando integrar o Workday com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Dia do Trabalho.
* Permita que os seus utilizadores sejam automaticamente inscritos no Workday com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal de assinatura (SSO) ativado por um dia de trabalho.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Workday suporta **SP** iniciado SSO.

* Assim que configurar o Workday, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Adicionar workday da galeria

Para configurar a integração do Workday em Azure AD, você precisa adicionar Workday da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **o Dia de Trabalho** na caixa de pesquisa.
1. Selecione **Workday** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Configure e teste Azure AD único sign-on para o Dia de Trabalho

Configure e teste Azure AD SSO com Workday utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Workday.

Para configurar e testar o Azure AD SSO com o Dia de Trabalho, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
2. **[Configure](#configure-workday)** o Workday para configurar as definições sSO no lado da aplicação.
    1. **[Crie um utilizador](#create-workday-test-user)** de teste Workday para ter uma contrapartida de B.Simon no Workday que está ligada à representação do utilizador da AD Azure.
3. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **do Dia de Trabalho,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`http://www.workday.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Estes valores não são os reais. Atualize estes valores com o URL de inscrição e url de resposta real. O URL de resposta deve ter um subdomínio, por exemplo: www,wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Usar algo como `http://www.myworkday.com` obras, mas `http://myworkday.com` não funciona. Contacte a equipa de apoio ao [cliente workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

6. A sua aplicação Workday espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** A aplicação Workday espera que o identificador de **nomes** seja mapeado com **user.mail**, **UPN,** etc., por isso precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Aqui mapeamos o ID de nome com UPN (user.userprincipalname) como padrão. Você precisa mapear o ID de nome com id de utilizador real na sua conta Workday (seu e-mail, UPN, etc.) para um trabalho bem sucedido do SSO.

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Para modificar as opções de **Assinatura** de acordo com o seu requisito, clique no botão **Editar** para abrir o diálogo do **Certificado de Assinatura SAML.**

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Selecione **A resposta e a afirmação do Sign SAML** para a **opção de assinatura**.

    b. Clique em **Guardar**

1. Na secção **set up Workday,** copie os URL(s) adequados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Workday.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Workday**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-workday"></a>Configure Dia de Trabalho

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Workday como administrador.

2. Na **caixa de pesquisa** procure com o nome **Editar Configuração de Inquilinos – Segurança** no lado superior esquerdo da página inicial.

    ![Editar Segurança do Inquilino](./media/workday-tutorial/IC782925.png "Editar Segurança do Inquilino")

3. Na secção **URLs de Redirecionamento,** execute os seguintes passos:

    ![URLs de reorientação](./media/workday-tutorial/IC7829581.png "URLs de reorientação")

    a. Clique em **adicionar linha**.

    b. No URL de **Redirecionamento de Login,** **URL de redirecionamento** do Timeout e caixa de texto de **redirecionamento móvel,** cola o URL de **Login** que copiou da secção **set-up Workday** do portal Azure.

    c. Na caixa de texto **logout Redirecion** URL, colá o URL de **logout** que copiou da secção **set up Workday** do portal Azure.

    d. Em **Used for Environments** textbox, selecione o nome ambiente.  

   > [!NOTE]
   > O valor do atributo ambiental está ligado ao valor do URL do arrendatário:  
   > -Se o nome de domínio do URL do inquilino do Workday começar com o impl, por exemplo: * https://www.myworkday.com/ "inquilino"/login-saml2.htmld),* **o** atributo ambiente deve ser definido para implementação.  
   > -Se o nome de domínio começar com outra coisa, precisa de contactar a equipa de suporte do [Cliente Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter o valor **ambiente** correspondente.

4. Na secção **de configuração SAML,** execute os seguintes passos:

    ![Configuração SAML](./media/workday-tutorial/IC782926.png "Configuração SAML")

    a.  **Selecione ativar a autenticação SAML**.

    b.  Clique em **adicionar linha**.

5. Na secção Fornecedores de **Identidade SAML,** execute os seguintes passos:

    ![Fornecedores de Identidade SAML](./media/workday-tutorial/IC7829271.png "Fornecedores de Identidade SAML")

    a. Na caixa de texto Nome do Fornecedor de **Identidade,** escreva um nome de fornecedor (por exemplo: *SPIniciadosSSO*).

    b. No portal Azure, na secção **set up Workday,** copie o valor do **Identificador AD Azure** e, em seguida, cole-o na caixa de texto **emitentes.**

    ![Fornecedores de Identidade SAML](./media/workday-tutorial/IC7829272.png "Fornecedores de Identidade SAML")

    c. No portal Azure, na secção **set up Workday,** copie o valor URL de **Logout** e, em seguida, cole-o na caixa de texto URL de **Resposta de Início.**

    d. No portal Azure, na secção **set up Workday,** copie o valor URL de **Login** e, em seguida, cole-o na caixa de texto URL do **serviço IDP SSO.**

    e. Em **Used for Environments** textbox, selecione o nome ambiente.

    f. Clique no Certificado de Chave Pública do **Fornecedor de Identidade**e, em seguida, clique em **Criar**.

    ![Criar](./media/workday-tutorial/IC782928.png "Criar")

    g. Clique **em Criar x509 Chave Pública**.

    ![Criar](./media/workday-tutorial/IC782929.png "Criar")

6. Na secção **View x509 Public Key,** execute os seguintes passos:

    ![Ver x509 Chave Pública](./media/workday-tutorial/IC782930.png "Ver x509 Chave Pública")

    a. Na caixa de texto **Name,** digite um nome para o seu certificado (por exemplo: *PPE \_ SP*).

    b. Na Caixa de Texto **Válida,** escreva o valor válido do valor do atributo do seu certificado.

    c.  Na caixa de texto **Válida para** Texto, escreva o valor válido para atribuir o seu certificado.

    > [!NOTE]
    > Pode obter o válido a partir da data e o válido até à data a partir do certificado descarregado clicando duas vezes nele.  As datas estão listadas no separador **Details.**
    >
    >

    d.  Abra o seu certificado codificado base-64 no bloco de notas e, em seguida, copie o conteúdo do mesmo.

    e.  Na caixa de texto **do Certificado,** colhe o conteúdo da sua pasta.

    f.  Clique em **OK**.

7. Execute os seguintes passos:

    ![Configuração SSO](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Configuração SSO")

    a.  Na caixa de texto ID do Prestador de **Serviços,** escreva **http://www.workday.com** .

    b. Selecione Não esvazie o **pedido de autenticação iniciado por SP**.

    c. Como método de **assinatura de pedido de autenticação,** selecione **SHA256**.

    ![Método de assinatura de pedido de autenticação](./media/workday-tutorial/WorkdaySSOConfiguration.png "Método de assinatura de pedido de autenticação")

    d. Clique em **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Certifique-se de que configura corretamente o início de inscrição. Caso ative um único início de sessão com configuração incorreta, pode não conseguir introduzir a aplicação com as suas credenciais e ficar bloqueado. Nesta situação, o Workday fornece um url de login de backup onde os utilizadores podem iniciar sessão usando o seu nome de utilizador normal e senha no seguinte formato:[O url do dia de trabalho]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Criar o utilizador do teste Workday

Nesta secção, cria-se um utilizador chamado B.Simon no Dia do Trabalho. Trabalhe com a equipe de suporte ao [Cliente Workday](https://www.workday.com/en-us/partners-services/services/support.html) para adicionar os utilizadores na plataforma Workday. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo do Dia de Trabalho no Painel de Acesso, deve ser automaticamente inscrito no Dia de Trabalho para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Dia de Trabalho com a AD Azure](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Dia de Trabalho com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-workday)