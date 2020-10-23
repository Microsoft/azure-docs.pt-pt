---
title: 'Tutorial: Integração do Azure Ative Directory com a Everbridge Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Everbridge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 732362ef7099e93697320d8e47180c1207e8cb32
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92453863"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Integração do Azure Ative Directory com a Everbridge

Neste tutorial, aprende-se a integrar a Everbridge com o Azure Ative Directory (Azure AD).
Quando integrar o Everbridge com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Everbridge.
* Permita que os seus utilizadores sejam automaticamente inscritos na Everbridge com as suas contas AD Azure. Este controlo de acesso chama-se único sinal de acesso (SSO).
* Gerencie as suas contas numa localização central utilizando o portal Azure.
Para obter mais informações sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte [o que é o acesso à aplicação e o único acesso ao Azure Ative Directory?](../manage-apps/what-is-single-sign-on.md)
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Everbridge, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura Everbridge que usa um único sinal de acesso.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Everbridge apoia SSO iniciado pelo IDP.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Adicione Everbridge do Mercado Azure

Para configurar a integração da Everbridge no AD Azure, adicione a Everbridge do Azure Marketplace à sua lista de aplicações geridas pelo SaaS.

Para adicionar Everbridge do Azure Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![Botão de diretório ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![Novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Everbridge.** Selecione **Everbridge** do painel de resultados e **selecione Adicionar**.

     ![Everbridge na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com a Everbridge com base no utilizador de teste Britta Simon.
Para um único sinal de sação a funcionar, estabeleça uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Everbridge.

Para configurar e testar a Azure AD um único sinal de acesso com a Everbridge, complete os seguintes blocos de construção:

- [Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on) permitir que os seus utilizadores utilizem esta funcionalidade.
- [Configure o Everbridge como portal de gestão everbridge único para](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) configurar as definições de inscrição única no lado da aplicação.
- [Configure o Everbridge como porta-membro da Everbridge um único sinal](#configure-everbridge-as-everbridge-member-portal-single-sign-on) para configurar as definições de inscrição única no lado da aplicação.
- [Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user) para testar o Azure AD com Britta Simon.
- [Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user) para permitir que a Britta Simon utilize um único sinal de Ad AD.
- Crie um utilizador de [teste de Everbridge](#create-an-everbridge-test-user) para ter uma contraparte de Britta Simon em Everbridge que está ligada à representação AD AZure do utilizador.
- [Teste um único sinal para](#test-single-sign-on) verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar a Azure AD um único sinal de inscrição com Everbridge, siga estes passos.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **everbridge,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. Na caixa de diálogo **de método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de entrada.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione **Editar** para abrir a caixa de diálogo **de configuração DE SAML básica.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

    >[!NOTE]
    >Configure a aplicação como portal do gestor *ou* como portal de membros no portal Azure e no portal Everbridge.

4. Para configurar a aplicação **Everbridge** como **portal de gerente da Everbridge,** na secção De **Configuração Básica SAML,** siga estes passos:

    ![Everbridge domínio e URLs informações únicas de súmis](common/idp-intiated.png)

    a. Na caixa **de identificador,** introduza um URL que segue o padrão `https://sso.everbridge.net/<API_Name>`

    b. Na caixa **URL de resposta,** insira um URL que segue o padrão `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com os valores reais de IDENTIFICAÇÃO e URL de resposta. Para obter estes valores, contacte a equipa de apoio da [Everbridge.](mailto:support@everbridge.com) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Para configurar a aplicação **Everbridge** como portal membro da **Everbridge,** na secção **De Configuração Básica SAML,** siga estes passos:

  * Se pretender configurar a aplicação no modo iniciado pelo IDP, siga estes passos:

     ![Everbridge domínio e URLs informações únicas de início de súps para o modo iniciado pelo IDP](common/idp-intiated.png)

    a. Na caixa **de identificador,** introduza um URL que segue o padrão `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Na caixa **URL de resposta,** insira um URL que segue o padrão `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Se pretender configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais** e siga este passo:

     ![Everbridge domínio e URLs informações únicas de início para o modo iniciado pelo SP](common/both-signonurl.png)

     a. Na **caixa de URL** sign in, insira um URL que segue o padrão `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e assine os valores de URL. Para obter estes valores, contacte a equipa de apoio da [Everbridge.](mailto:support@everbridge.com) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. Na **configuração single Sign-On com página SAML,** na secção certificado de assinatura **SAML,** selecione **Descarregue** para descarregar o **Metadadata XML da Federação**. Guarde no seu computador.

    ![Link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar Everbridge,** copie os URLs de que necessita para os seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    - URL de Inicio de Sessão
    - Identificador de Azure Ad
    - Logout URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Configure Everbridge como porta de gestão everbridge único sign-on

Para configurar o SSO em **Everbridge** como uma aplicação **do portal de gerentes da Everbridge,** siga estes passos.
 
1. Numa janela diferente do navegador, inscreva-se no Everbridge como administrador.

1. No menu em cima, selecione o **separador Definições.** Em **Segurança**, selecione **Single Sign-On**.
   
     ![Configurar o início de sessão único](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Na caixa **Nome,** insira o nome do fornecedor de identificador. Um exemplo é o nome da sua empresa.
   
     b. Na caixa **API Name,** insira o nome da API.
   
     c. Selecione **Escolha o Ficheiro** para carregar o ficheiro de metadados que descarregou a partir do portal Azure.
   
     d. Para **a Localização de Identidade SAML,** selecione **Identidade está no elemento identificador de nome da declaração do sujeito**.
   
     e. Na caixa **URL do Fornecedor de Identidade,** cole o valor URL de **login** que copiou do portal Azure.
   
     f. Para **o prestador de serviços iniciado, o pedido de ligação**, selecione HTTP **Redirect**.

     exemplo, Selecione **Guardar**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Configure Everbridge como porta de everbridge único sign-on

Para configurar um único sign-on em **Everbridge** como um **portal membro da Everbridge,** envie o **Metdata XML da Federação** descarregado para a [equipa de suporte everbridge](mailto:support@everbridge.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

Para criar a utilizadora de teste Britta Simon no portal Azure, siga estes passos.

1. No portal Azure, no painel esquerdo, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.

    ![Utilizadores e todos os links dos utilizadores](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Na caixa de diálogo **do Utilizador,** siga estes passos.

    ![Caixa de diálogo do utilizador](common/user-properties.png)

    a. Na caixa **Nome,** **insira BrittaSimon**.
  
    b. Na caixa **Nome de utilizador**, introduza `brittasimon@yourcompanydomain.extension`. Um exemplo é BrittaSimon@contoso.com.

    c. Selecione a caixa **de verificação 'Mostrar Palavra-passe'.** Anota o valor que exibe na caixa **de palavra-passe.**

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Permita a Britta Simon utilizar o Azure single sign-on, concedendo acesso a Everbridge.

1. No portal Azure, selecione **aplicações Enterprise**  >  **Todas as aplicações**  > **Everbridge**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Everbridge**.

    ![Ligação Everbridge na lista de candidaturas](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![Ligação de utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

    ![Adicionar caixa de diálogo de atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **de Utilizadores e grupos,** selecione **Britta Simon** na lista de utilizadores. Escolha **Selecione** na parte inferior do ecrã.

6. Se espera algum valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Escolha **Selecione** na parte inferior do ecrã.

7. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-an-everbridge-test-user"></a>Criar um utilizador de teste everbridge

Nesta secção, cria-se a utilizadora de testes Britta Simon em Everbridge. Para adicionar utilizadores na plataforma Everbridge, trabalhe com a equipa de apoio da [Everbridge.](mailto:support@everbridge.com) Os utilizadores devem ser criados e ativados em Everbridge antes de utilizar uma única s ativação. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Teste a sua configuração de sinal único AD AD através do Painel de Acesso.

Quando selecionar o azulejo Everbridge no Painel de Acesso, deverá ser automaticamente inscrito na conta Everbridge para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com Diretório Ativo Azure](./tutorial-list.md)
- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)