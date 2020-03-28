---
title: 'Tutorial: Integração de Diretório Ativo Azure com Controlo de Pontos de Trabalho [ Microsoft Docs'
description: Saiba como configurar um único sign-on para o Azure Ative Directory e workspot Control.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086684"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Tutorial: Integração de Diretório Ativo Azure com Controlo de Pontos de Trabalho

Neste tutorial, aprende-se a integrar o Controlo de Pontos de Trabalho com o Diretório Ativo Azure (Azure AD). Quando integrar o Controlo de Pontos de Trabalho com a AD Azure, pode:

* Utilize a AD Azure para controlar quem tem acesso ao Controlo de Pontos de Trabalho.
* Ativar os utilizadores para iniciar sessão automática no Workspot Control (entrada única [SSO]) utilizando as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para obter mais informações sobre a integração da aplicação SaaS com a Azure AD, consulte o [único sinal de inscrição para aplicações em Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Workspot Control, precisa das seguintes coisas:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

* Uma subscrição de sinal único de controlo de ponto de trabalho.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

> [!Note]
> Workspot Control suporta SSO iniciado por SP e IDP.


## <a name="adding-workspot-control-from-the-gallery"></a>Adicionar controlo de pontos de trabalho da galeria

Para configurar a integração do Controlo de Pontos de Trabalho em Azure AD, deve adicionar controlo de pontos de trabalho da galeria à sua lista de aplicações saaS geridas.

**Para adicionar controlo de pontos de trabalho a partir da galeria, siga estes passos:**

1. No painel esquerdo do [portal Azure,](https://portal.azure.com)selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá a **Aplicações Empresariais** e selecione **Todas as Aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

3. Selecione **Nova aplicação** na parte superior da janela.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o Controlo do **Ponto de Trabalho,** selecione Controlo de **Pontos** de Trabalho do painel de resultados e, em seguida, **selecione Adicionar**.

     !["Adicionar da galeria" janela](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com controlo de pontode trabalho para um utilizador de teste, Britta Simon.
Para que o início de sessão seja introduzido, deve estabelecer uma ligação entre um utilizador da AD Azure e o utilizador relacionado no Controlo do Ponto de Trabalho.

Para configurar e testar o único signo azure ad com controlo de pontos de trabalho, deve completar as seguintes tarefas:

1. [Configure](#configure-azure-ad-single-sign-on) o único sinal de ad da Azure para permitir que os seus utilizadores utilizem a funcionalidade.
2. [Configure](#configure-workspot-control-single-sign-on) o início único do Workspot Control para configurar as definições de inscrição únicas no lado da aplicação.
3. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o single sign-on azure AD para Britta Simon.
4. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que Britta Simon utilize um único sinal de AD Azure.
5. [Crie um utilizador](#create-a-workspot-control-test-user) de teste de Controlo de Pontos de Trabalho para estabelecer uma contrapartida da Britta Simon no Controlo de Pontos de Trabalho que esteja ligada à representação da AD Azure do utilizador.
6. [Teste um único sinal](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com controlo de pontos de trabalho, siga estes passos:

1. Na página de integração da aplicação **Workspot Control** no [portal Azure,](https://portal.azure.com/)selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. Na janela Select uma única janela de método de **sinalização,** selecione o modo **SAML** para ativar um único sinal.

    ![Selecione uma única janela de método seletiva de sinal](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, selecione o ícone **Editar** (lápis) para aceder à **configuração Básica do SAML**.

    ![Editar ícone destacado em "Basic SAML Configuration"](common/edit-urls.png)

4. Na secção **basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado pelo IDP, siga estes passos:

    ![Domínio de controlo de pontos de trabalho e informações únicas de inscrição](common/idp-intiated.png)

    1. Na caixa de texto **do identificador,** introduza um URL no seguinte padrão:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. Na caixa de texto URL de **resposta,** introduza um URL no seguinte padrão:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/afirmação***

5. Se pretender configurar a aplicação no modo iniciado por SP, selecione **Definir URLs adicionais**.

    ![Domínio de controlo de pontos de trabalho e informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** introduza um URL no seguinte padrão:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Estes valores não são reais. Substitua estes valores pelo identificador real, URL de resposta e URL de inscrição. Contacte a equipa de suporte ao [Cliente workspot Control](mailto:support@workspot.com) para obter estes valores. Ou também pode consultar os padrões na secção **de Configuração SAML Básica** do portal Azure.

6. Na **página set Up Single Sign-On com a página SAML,** na secção Certificado de **Assinatura SAML,** selecione **Download** para baixar **Certificado (Base64)** das opções disponíveis de acordo com os seus requisitos. Guarde para o seu computador.

    ![O link de descarregamento do Certificado (Base64)](common/certificatebase64.png)

7. Na secção de Controlo de Pontos de **Trabalho, copie** os URLs apropriados de acordo com os seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    - **Login URL**

    - **Identificador Azure AD**

    - **Logout URL**

### <a name="configure-workspot-control-single-sign-on"></a>Configure Workspot Control single sign-on

1. Numa janela de navegador web diferente, inscreva-se no Workspot Control como administrador de segurança.

2. Na barra de ferramentas na parte superior da página, **selecione Configurar** e, em seguida, **SAML**.

    ![Opções de configuração](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Na janela de configuração de configuração de configuração de configuração de configuração de configuração de configuração de configuração de configuração de configuração de configuração de configuração de configuração de **configuração**
 
    ![Janela de configuração de configuração de configuração de configuração de configuração de configuração de configuração de](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Na caixa **id** entidade, cola o **Identificador De** Anúncio Azure que copiou do portal Azure.

    1. Na caixa URL do **Serviço Signon,** colhe o URL de **Login** que copiou do portal Azure.

    1. Na caixa URL do **Serviço de Logout,** colhe o URL de **Logout** que copiou do portal Azure.

    1. Selecione **'Update File'** para fazer o upload do certificado X.509, o certificado codificado base-64 que descarregou do portal Azure.

    1. Selecione **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure.

1. No painel esquerdo do portal Azure, **selecione Azure Ative Directory**, **Users**, e depois **todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior da janela.

    ![O botão "Novo utilizador"](common/new-user.png)

3. Nas propriedades para o utilizador, siga estes passos:

    ![A janela de propriedades do Utilizador](common/user-properties.png)

    1. No campo **Nome,** entre **brittaSimon.**
  
    1. No campo de **nome do Utilizador,** introduza **brittasimon@* yourcompanydomínio.extension***. Por exemplo, introduza ** BrittaSimon@contoso.<i> </i> com**.

    1. Selecione a caixa de verificação de **palavra-passe Mostrar.** Em seguida, escreva o valor que está mostrado na caixa **password.**

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, você concede a Britta Simon acesso ao Workspot Control para permitir-lhe usar o único sign-on Azure.

1. No portal Azure, selecione **Aplicações Empresariais,** **Todas as aplicações,** e, em seguida, Controlo de Pontos de **Trabalho.**

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Workspot Control**.

    ![O link workspot Control na lista de Aplicações](common/all-applications.png)

3. A partir do menu do lado esquerdo, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione o botão **de utilizador Adicionar.** Em seguida, selecione **Utilizadores e grupos** na janela **de atribuição Adicionar.**

    ![A janela "Adicionar Atribuição"](common/add-assign-user.png)

5. Na janela **Utilizadores e grupos,** selecione **Britta Simon** da lista de **Utilizadores.** Em seguida, clique em **Selecionar**.

6. Se esperar algum valor de papel na afirmação do SAML, selecione a função adequada para o utilizador a partir da lista na janela **Select Role.** Em seguida, clique em **Selecionar** na parte inferior.

7. Na janela **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-a-workspot-control-test-user"></a>Criar um utilizador de teste de controlo de pontos de trabalho

Para permitir que os utilizadores de Anúncios Azure assinem o Controlo do Spot de Trabalho, devem ser aprovisionados no Controlo de Pontos de Trabalho. O provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, siga estes passos:**

1. Inscreva-se no Workspot Control como administrador de segurança.

2. Na barra de ferramentas na parte superior da página, selecione **Utilizadores** **e,** em seguida, adicione o Utilizador .

    ![Opções de "Utilizadores"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Na janela **Adicionar um Novo Utilizador,** siga estes passos:

    ![Janela "Adicionar um novo utilizador"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Na caixa **De Nome Próprio,** introduza o primeiro nome de um utilizador, como **a Britta**.

    1. Na caixa de texto **De Apelido,** introduza o último nome do utilizador, como **Simon**.

    1. Na caixa de **e-mail,** insira o endereço de e-mail do utilizador, como ** Brittasimon@contoso<i> </i> . com**.

    1. Selecione a função de utilizador apropriada da lista de abandono da **Função.**

    1. Selecione o grupo de utilizadores apropriado da lista de abandono do **Grupo.**

    1. Selecione **Adicionar utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testamos a nossa configuração de inscrição única Azure AD através *do Painel*de Acesso .

Quando clicar no azulejo **workspot Control** no Painel de Acesso, deve ser automaticamente inscrito no Controlo do Ponto de Trabalho para o qual configura o SSO. Para mais informações, consulte [Introduction to the Access Panel (Introdução ao Painel de Acesso)](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [Inscrição única para candidaturas no Diretório Ativo do Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
