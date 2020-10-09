---
title: 'Tutorial: Integração do Azure Ative Directory com o Workspot Control Microsoft Docs'
description: Saiba como configurar um único sign-on para O Azure Ative Directory e Workspot Control.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: bfbecc71638e6feaaf29809f09dda752dd29b2ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88526623"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Tutorial: Integração do Diretório Ativo Azure com o Workspot Control

Neste tutorial, aprende-se a integrar o Workspot Control com o Azure Ative Directory (Azure AD). Quando integrar o Workspot Control com Azure AD, pode:

* Use a Azure AD para controlar quem tem acesso ao Controle de Workspot.
* Permitir que os utilizadores inscrevam-se automaticamente no Workspot Control (sso único de sso]) utilizando as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para obter mais informações sobre a integração da aplicação SaaS com a Azure AD, consulte [o single sign-on para aplicações em Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Workspot Control, precisa das seguintes coisas:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

* Uma subscrição única de sinal on-enabled do Workspot Control.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

> [!Note]
> O Workspot Control suporta sSO iniciado pelo SP e iniciado pelo IDP.


## <a name="adding-workspot-control-from-the-gallery"></a>Adicionar Controle de Workspot da galeria

Para configurar a integração do Workspot Control em Azure AD, tem de adicionar o Workspot Control da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar o Control do Workspot da galeria, siga estes passos:**

1. No painel esquerdo do [portal Azure,](https://portal.azure.com)selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá a **Aplicações Empresariais** e selecione **Todas as Aplicações.**

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

3. Selecione **Nova aplicação** na parte superior da janela.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **o Controlo do Ponto de Trabalho,** selecione Controle de **Workspot** no painel de resultados e, em seguida, selecione **Adicionar**.

     ![Janela "Adicionar da galeria"](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com o Workspot Control para um utilizador de teste, Britta Simon.
Para um único sinal de sação a funcionar, deve estabelecer uma ligação entre um utilizador AZure AD e o utilizador relacionado no Controlo do Ponto de Trabalho.

Para configurar e testar o Azure AD com o Workspot Control, tem de completar as seguintes tarefas:

1. [Configurar o Azure AD um único sinal para](#configure-azure-ad-single-sign-on) permitir que os seus utilizadores utilizem a funcionalidade.
2. [Configure o controlo do ponto de trabalho de uma única sposição](#configure-workspot-control-single-sign-on) para configurar as definições de inscrição única no lado da aplicação.
3. [Crie um utilizador de teste AZure AD](#create-an-azure-ad-test-user) para testar o Azure AD um único sinal de sessão para Britta Simon.
4. [Atribua ao utilizador de teste AZure AD](#assign-the-azure-ad-test-user) para permitir que a Britta Simon utilize um único sinal de Ad AD.
5. Crie um utilizador de [teste workspot control](#create-a-workspot-control-test-user) para estabelecer uma contrapartida de Britta Simon no Workspot Control que está ligada à representação AD AZure do utilizador.
6. [Teste um único sinal para](#test-single-sign-on) verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Workspot Control, siga estes passos:

1. Na página de integração da aplicação **Workspot Control** no [portal Azure,](https://portal.azure.com/)selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. Na **janela do método de inscrição única,** selecione o modo **SAML** para ativar um único sinal de súmido.

    ![Selecione uma única janela de método de s seletor de inscrição](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** selecione o ícone **Edit** (lápis) para aceder à **Configuração Básica SAML**.

    ![Ícone de edição destacado em "Configuração Básica SAML"](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo IDP, siga estes passos:

    ![Domínio de controlo de depósitos de trabalho e informações únicas de assinatura](common/idp-intiated.png)

    1. Na caixa de texto do **identificador,** introduza um URL no seguinte padrão:<br/>
    ***https://<<i></i> INSTANCENAME>-saml.workspot.com/saml/metadados***

    1. Na caixa de texto **URL de resposta,** introduza um URL no seguinte padrão:<br/>
    ***https://<<i></i> INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Se pretender configurar a aplicação no modo iniciado pelo SP, selecione **Definir URLs adicionais**.

    ![Domínio de controlo de depósitos de trabalho e informações únicas de assinatura](common/metadata-upload-additional-signon.png)

    Na caixa de texto URL de entrada de **url,** introduza um URL no seguinte padrão:<br/>
    ***https://<<i></i> INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Estes valores não são reais. Substitua estes valores pelo identificador real, URL de resposta e URL de inscrição. Contacte a equipa de suporte do [Cliente do Controle de Workspot](mailto:support@workspot.com) para obter estes valores. Ou também pode consultar os padrões na secção **de Configuração SAML Básica** do portal Azure.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione **Descarregamento** para descarregar **Certificado (Base64)** das opções disponíveis de acordo com os seus requisitos. Guarde para o seu computador.

    ![O link de descarregamento do Certificado (Base64)](common/certificatebase64.png)

7. Na secção **De Controlo de Depósitos de Trabalho,** copie os URLs apropriados de acordo com os seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    - **Login URL**

    - **Identificador de Azure Ad**

    - **Logout URL**

### <a name="configure-workspot-control-single-sign-on"></a>Configurar o controle do depósito de trabalho único

1. Numa janela diferente do navegador web, inscreva-se no Workspot Control como administrador de segurança.

2. Na barra de ferramentas no topo da página, selecione **Configurar** e, em seguida, **SAML**.

    ![Opções de configuração](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Na janela de **configuração de configuração de configuração de marcação de afirmação de segurança,** siga estes passos:
 
    ![Janela de configuração de configuração de configuração de marcação de afirmação de segurança](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Na caixa **de ID da Entidade,** cole o **Identificador Ad Azure** que copiou do portal Azure.

    1. Na caixa **URL do Serviço de Sinalização,** cole o URL de **login** que copiou do portal Azure.

    1. Na caixa **URL do serviço Logout,** cole o **URL logout** que copiou do portal Azure.

    1. Selecione **Update File** para carregar no certificado X.509 o certificado codificado base-64 que descarregou a partir do portal Azure.

    1. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**, **Utilizadores,** e depois **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior da janela.

    ![O botão "Novo utilizador"](common/new-user.png)

3. Nas propriedades para o utilizador, siga estes passos:

    ![A janela de propriedades do utilizador](common/user-properties.png)

    1. No campo **Nome,** **insira BrittaSimon**.
  
    1. No campo nome do **utilizador,** introduza **brittasimon@* sua empresa.extension***. Por exemplo, insira ** BrittaSimon@contoso . <i></i> com**.

    1. Selecione a caixa **de verificação de senha show.** Em seguida, anote o valor que é mostrado na caixa **de senha.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você concede a Britta Simon acesso ao Workspot Control para permitir-lhe usar a Azure um único sinal.

1. No portal Azure, selecione **Aplicações empresariais**, **todas as aplicações,** e depois **o Controle do Pote de Trabalho**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Workspot Control**.

    ![A ligação de Controlo do Workspot na lista de Aplicações](common/all-applications.png)

3. A partir do menu do lado esquerdo, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione o botão **de utilizador Adicionar.** Em seguida, selecione **Utilizadores e grupos** na janela **de atribuição Adicionar.**

    ![A janela "Adicionar Atribuição"](common/add-assign-user.png)

5. Na janela **de Utilizadores e grupos,** selecione **Britta Simon** da lista **de Utilizadores.** Em seguida, clique em **Selecionar**.

6. Se espera algum valor de função na afirmação SAML, selecione a função adequada para o utilizador a partir da lista na janela **'Função' Select.** Em seguida, clique em **Selecionar** na parte inferior.

7. Na janela **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-a-workspot-control-test-user"></a>Criar um utilizador de teste de controlo de workspot

Para permitir que os utilizadores da Azure AD entrem no Workspot Control, devem ser a provisionados no Workspot Control. O provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, siga estes passos:**

1. Inscreva-se no Workspot Control como Administrador de Segurança.

2. Na barra de ferramentas no topo da página, selecione **Utilizadores** e, em seguida, **Adicione Utilizador**.

    ![Opções de "Utilizadores"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Na janela **Adicionar um Novo Utilizador,** siga estes passos:

    ![Janela "Adicionar um novo utilizador"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Na caixa **First Name,** insira o primeiro nome de um utilizador, como **Britta**.

    1. Na caixa de texto **do último nome,** insira o último nome do utilizador, tal como **Simon**.

    1. Na caixa **de e-mail,** insira o endereço de e-mail do utilizador, tal como ** Brittasimon@contoso . <i></i> com**.

    1. Selecione a função de utilizador apropriada **da** lista de drop-down Role.

    1. Selecione o grupo de utilizadores apropriado da lista de abandono do **Grupo.**

    1. Selecione **Adicionar Utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testamos a nossa configuração de inscrição única AD AD através do Painel de *Acesso.*

Quando clicar no azulejo **do Controle do Ponto de** Trabalho no Painel de Acesso, deverá ser automaticamente inscrito no Controlo do Ponto de Trabalho para o qual configura sSO. Para mais informações, consulte [Introduction to the Access Panel (Introdução ao Painel de Acesso)](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [Inscrição única para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
