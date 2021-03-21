---
title: 'Tutorial: Integração do Diretório Ativo Azure com o AuditBoard | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AuditBoard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 670ceb9f342bc629ebad4630f2ecdff311e54d1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689135"
---
# <a name="tutorial-azure-active-directory-integration-with-auditboard"></a>Tutorial: Integração do Diretório Ativo Azure com o AuditBoard

Neste tutorial, aprende a integrar o AuditBoard com o Azure Ative Directory (Azure AD).
Integrar o AuditBoard com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao AuditBoard.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no AuditBoard (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o AuditBoard, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Subscrição ativada por um único sign-on do AuditBoard

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* AuditBoard suporta **SP e IDP** iniciado SSO

## <a name="adding-auditboard-from-the-gallery"></a>Adicionar AuditBoard da galeria

Para configurar a integração do AuditBoard no AD Azure, é necessário adicionar o AuditBoard da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar AuditBoard da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite AuditBoard**, selecione **AuditBoard** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![AuditBoard na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com AuditBoard com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no AuditBoard.

Para configurar e testar o Azure AD com o AuditBoard, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Quadro de Auditoria único Sign-On](#configure-auditboard-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create AuditBoard test user](#create-auditboard-test-user)** - ter uma contraparte de Britta Simon no AuditBoard que está ligada à representação AD AZure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o AuditBoard, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **do AuditBoard,** selecione **'Único sinal de súb'.**

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo **iniciado pelo IDP,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.auditboardapp.com/api/v1/sso/saml/metadata.xml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.auditboardapp.com/api/v1/sso/saml/assert`

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo **iniciado sp:**

    d. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<SUBDOMAIN>.auditboardapp.com/`

    ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente AuditBoard](mailto:support@auditboard.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

### <a name="configure-auditboard-single-sign-on"></a>Configurar auditboard único Sign-On

Para configurar um único sinal de acesso ao lado **do AuditBoard,** é necessário enviar o **Url de Metadados da Federação de Aplicações** para a equipa de suporte do [AuditBoard](mailto:support@auditboard.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite que Britta Simon utilize o Azure como único sinal, concedendo acesso ao AuditBoard.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **AuditBoard**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **AuditBoard**.

    ![O link AuditBoard na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-auditboard-test-user"></a>Criar utilizador de teste auditBoard

Nesta secção, cria-se um utilizador chamado Britta Simon no AuditBoard. Trabalhe com [a equipa de suporte do AuditBoard](mailto:support@auditboard.com) para adicionar os utilizadores na plataforma AuditBoard. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no telha Do AuditBoard no Painel de Acesso, deverá ser automaticamente inscrito no AuditBoard para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)