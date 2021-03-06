---
title: 'Tutorial: Integração do Diretório Ativo Azure com iQualify LMS | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o iQualify LMS.
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
ms.openlocfilehash: bdd68a4f0dafd3b336bf5a3fb34399d725c9ff5c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626117"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Tutorial: Integração do Diretório Ativo Azure com iQualify LMS

Neste tutorial, aprende-se a integrar o iQualify LMS com o Azure Ative Directory (Azure AD).
A integração do IQualify LMS com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a IQualify LMS.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no iQualify LMS (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com iQualify LMS, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* iQualify LMS assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* IQualify LMS suporta **SP e IDP** iniciado SSO
* IQualify LMS suporta **provisão de** utilizadores just in time

## <a name="adding-iqualify-lms-from-the-gallery"></a>Adicionar iQualify LMS da galeria

Para configurar a integração do iQualify LMS em AD AZure, precisa adicionar iQualify LMS da galeria à sua lista de aplicações geridas de SaaS.

**Para adicionar iQualify LMS da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite lMS iQualify,** selecione **iQualify LMS** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![IQualificar LMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sinal de IQualify LMS com base num utilizador de teste chamado **Britta Simon**.
Para um único sign-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no iQualify LMS.

Para configurar e testar o Azure AD com lMS iQualify, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure iQualificar o Sign-On Único LMS](#configure-iqualify-lms-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create iQualify LMS test user](#create-iqualify-lms-test-user)** - para ter uma contraparte de Britta Simon em IQualify LMS que está ligada à representação AD AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com iQualify LMS, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **iQualify LMS,** selecione **Single sign-on**.

   ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

   ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

   ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)
   
   1. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

      * Ambiente de Produção: `https://<yourorg>.iqualify.com/`
      * Ambiente de teste: `https://<yourorg>.iqualify.io`

   2. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

      * Ambiente de Produção: `https://<yourorg>.iqualify.com/auth/saml2/callback` 
      * Ambiente de teste: `https://<yourorg>.iqualify.io/auth/saml2/callback`

5. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

   ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

   Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:

      * Ambiente de Produção: `https://<yourorg>.iqualify.com/login`
      * Ambiente de teste: `https://<yourorg>.iqualify.io/login`

   > [!NOTE]
   > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do cliente IQualify LMS](https://www.iqualify.com/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. A sua aplicação IQualify LMS espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo **dos Atributos do Utilizador.**

    ![A screenshot mostra atributos do utilizador com o ícone editar selecionado.](common/edit-attribute.png)

7. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** edite as alegações utilizando o **ícone Editar** ou adicione as alegações utilizando **adicionar uma nova alegação** para configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos:

    | Name | Atributo de origem|
    | --- | --- |
    | e-mail | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | utilizador.sobrenome |
    | person_id | "o seu atributo" |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![A Screenshot mostra as reclamações do Utilizador com a opção de adicionar uma nova reclamação.](common/new-save-attribute.png)

    ![A screenshot mostra a caixa de diálogo de reclamações do utilizador Manage onde pode introduzir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

    > [!Note]
    > O **atributo person_id** é **opcional**

8. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

9. Na secção **Configuração iQualify LMS,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-iqualify-lms-single-sign-on"></a>Configurar iQualificar LMS Single Sign-On

1. Abra uma nova janela do navegador e, em seguida, inscreva-se no seu ambiente iQualify como administrador.

1. Assim que iniciar sessão, clique no seu avatar no topo direito e, em seguida, clique nas **definições de Conta**

    ![Definições da conta](./media/iqualify-tutorial/setting1.png)

1. Na área de definições de conta, clique no menu de fita à esquerda e clique em **INTEGRAÇÕES**

    ![INTEGRAÇÕES](./media/iqualify-tutorial/setting2.png)

1. Em INTEGRAÇÕES, clique no ícone **SAML.**

    ![Ícone DE SAML](./media/iqualify-tutorial/setting3.png)

1. Na caixa de diálogo de definições de **autenticação SAML,** execute os seguintes passos:

    ![Definições de autenticação SAML](./media/iqualify-tutorial/setting4.png)

    a. Na caixa **DE URL DE SERVIÇO ÚNICO SAML SIGN-ON,** cole o valor URL de **login** copiado da janela de configuração da aplicação AD Azure.

    b. Na caixa **DE URL DO TIPOL SAM,** cole o valor **URL logout** copiado da janela de configuração da aplicação AD Azure.

    c. Abra o ficheiro de certificado descarregado no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa **DE CERTIFICADO** PÚBLICO.

    d. No **BOTÃO DE INÍCIO,** INTRODUZA o nome do botão a visualizar na página de login.

    e. Clique **em SAVE**.

    f. Clique **em UPDATE**.

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

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, permitindo o acesso ao IQualify LMS.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **iQualify LMS**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **iQualify LMS**.

    ![O link IQualify LMS na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-iqualify-lms-test-user"></a>Criar utilizador de teste iQualify LMS

Nesta secção, um utilizador chamado Britta Simon é criado em IQualify LMS. O iQualify LMS suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no IQualify LMS, um novo é criado após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo IQualify LMS no Painel de Acesso, deverá obter a página de login da sua aplicação IQualify LMS. 

   ![página de login](./media/iqualify-tutorial/login.png) 

Clique **em Iniciar sômbed com** o botão AD Azure e deverá iniciar uma inscrição automática na sua aplicação IQualify LMS.

Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md) 

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)