---
title: 'Tutorial: Integração do Azure Ative Directory com o PerformanceCentre Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PerformanceCentre.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: d0a19a95a0a0d2353070068b2113e11efe471ccc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553984"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Tutorial: Integração do Azure Ative Directory com o PerformanceCentre

Neste tutorial, aprende-se a integrar o PerformanceCentre com o Azure Ative Directory (Azure AD).
Integrar o PerformanceCentre com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao PerformanceCentre.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no PerformanceCentre (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o PerformanceCentre, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada pelo PerformanceCentre

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* PerformanceCentre suporta **SSO** iniciado sp

## <a name="adding-performancecentre-from-the-gallery"></a>Adicionar PerformanceCentre da galeria

Para configurar a integração do PerformanceCentre no AD Azure, é necessário adicionar o PerformanceCentre da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar PerformanceCentre da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **PerformanceCentre**, selecione **PerformanceCentre** a partir do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![PerformanceCentre na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com PerformanceCentre baseado num utilizador de teste chamado **Britta Simon**.
Para um único pré-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no PerformanceCentre.

Para configurar e testar o Azure AD com o PerformanceCentre, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o DesempenhoCentre Single Sign-On](#configure-performancecentre-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create PerformanceCentre test user](#create-performancecentre-test-user)** - para ter uma contraparte de Britta Simon no PerformanceCentre que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o PerformanceCentre, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **PerformanceCentre,** selecione **'Único sinal de s-on'.**

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração de 'Sessão única' com** a página SAML, clique em **Editar** o ícone para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Informações de súmis de desempenho e URLs únicas](common/sp-identifier.png)

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `http://<companyname>.performancecentre.com/saml/SSO`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `http://<companyname>.performancecentre.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte ao Cliente Do PerformanceCentre](https://www.performio.co/contact-us) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração de 'Sessão Única' com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação das opções dadas de acordo com o seu requisito e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção Configurar o **PerformanceCentre,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-performancecentre-single-sign-on"></a>Configurar performanceCentre single sign-on

1. Iniciar súm em funções no site da empresa **PerformanceCentre** como administrador.

2. No separador do lado esquerdo, clique em **Configurar**.
   
    ![Azure AD Single Sign-On][10]

3. No separador do lado esquerdo, clique em **Miscelâneas**e, em seguida, clique **em Single Sign On**.
   
    ![Azure AD Single Sign-On][11]

4. Como **Protocolo**, selecione **SAML**.
   
    ![Azure AD Single Sign-On][12]

5. Abra o seu ficheiro de metadados descarregado no bloco de notas, copie o conteúdo, cole-o na caixa de texto **do Fornecedor de Identidade** e, em seguida, clique em **Guardar**.
   
    ![Azure AD Single Sign-On][13]

6. Verifique se os valores para o **URL base** da entidade e URL **de ID da entidade** estão corretos.
    
     ![Azure AD Single Sign-On][14]

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador****brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao PerformanceCentre.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **PerformanceCentre**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **PerformanceCentre**.

    ![O link PerformanceCentre na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-performancecentre-test-user"></a>Criar utilizador de teste PerformanceCentre

O objetivo desta secção é criar um utilizador chamado Britta Simon no PerformanceCentre.

**Para criar um utilizador chamado Britta Simon in PerformanceCentre, execute os seguintes passos:**

1. Inscreva-se no site da empresa PerformanceCentre como administrador.

2. No menu à esquerda, clique em **Interrelate**e, em seguida, clique em **Criar Participante**.
   
    ![Criar Utilizador][400]

3. No **Interrelate - Criar** diálogo de participante, execute os seguintes passos:
   
    ![Criar Utilizador][401]
    
    a. Digite os atributos necessários para Britta Simon em caixas de texto relacionadas.
    
    >[!IMPORTANT]
    >O atributo nome de utilizador da Britta no PerformanceCentre deve ser o mesmo que o Nome de Utilizador em Azure AD.
    
    b. Selecione **o administrador do cliente** como **Função de Escolha.**
    
    c. Clique em **Guardar**. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Do PerformanceCentre no Painel de Acesso, deverá ser automaticamente inscrito no PerformanceCentre para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png