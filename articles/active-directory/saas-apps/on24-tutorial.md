---
title: 'Tutorial: Integração do Azure Ative Directory com ON24 Virtual Environment SAML Connection | Microsoft Docs'
description: Saiba como configurar um único sinal de acesso entre o Azure Ative Directory e o ON24 Virtual Environment SAML Connection.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 30fd3843b50ac6b075d33e961986b94ee2496fef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518527"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Tutorial: Integração do Azure Ative Directory com LIGAção SAML de Ambiente Virtual ON24

Neste tutorial, aprende-se a integrar a LIGAção SAML de AMBIENTE Virtual ON24 com o Azure Ative Directory (Azure AD).
Integrar a ON24 Virtual Environment SAML Connection com Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a ON24 Virtual Environment SAML Connection.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Ligação SAML de Ambiente Virtual ON24 (Único Sinal de Acesso) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a LIGAção SAML de Ambiente Virtual ON24, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* ON24 Virtual Environment SAML Connection única subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* ON24 Ambiente Virtual SAML Connection suporta **SP** e **IDP** iniciado SSO

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Adicionar ON24 Virtual Environment SAML Connection da galeria

Para configurar a integração da LIGAção SAML em Ambiente Virtual ON24 em AD AZure, é necessário adicionar a LIGAção SAML DE AMBIENTE Virtual ON24 da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar ON24 Virtual Environment SAML Connection da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ON24 Virtual Environment SAML Connection**, selecione **ON24 Virtual Environment SAML Connection** a partir do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![ON24 Ambiente Virtual SAML Ligação na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com uma única ligação SAML de ambiente virtual com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em ON24 Virtual Environment SAML Connection.

Para configurar e testar o Azure AD com uma única ligação SAML em ambiente virtual ON24, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configurar ON24 Virtual Environment SAML Ligação Única Sinal-On](#configure-on24-virtual-environment-saml-connection-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create ON24 Virtual Environment SAML Connection test user](#create-on24-virtual-environment-saml-connection-test-user)** - para ter uma contraparte de Britta Simon em ON24 Virtual Environment SAML Connection que está ligada à representação AD AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com ligação SAML de ambiente virtual ON24, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **ON24 Virtual Environment SAML Connection,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **De Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![ON24 Ambiente Virtual SAML Connection Domain e URLs informações únicas de acesso](common/idp-relay.png)

    a. Na caixa de texto **identifier,** digite um URL:

     **URL de Ambiente de Produção**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL de ambiente QA**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. Na caixa de texto **URL de resposta,** digite um URL:

     **URL de Ambiente de Produção**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL de ambiente QA**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Clique **em Definir URLs adicionais**. 

    d. Na caixa de texto **do Estado de Retransmissão,** digite um URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Se desejar configurar a aplicação no modo iniciado pela **SP,** execute o seguinte passo:

    ![Screenshot que mostra a secção "Definir U R Ls adicionais" com a caixa de texto "Sign on U R L" realçada.](common/both-signonurl.png)

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o estado real de retransmissão e URL de inscrição. Contacte a equipa de [suporte do cliente de ligação a ambiente virtual ON24](https://www.on24.com/contact-us/) PARA obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração ON24 Virtual Environment SAML Connection,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de Azure Ad

    c. Logout URL

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Configurar ON24 Ambiente Virtual SAML Ligação única Sign-On

Para configurar um único sign-on **on ON24 Virtual Environment SAML Connection** side, você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para [ON24 Virtual Environment SAML Connection equipe](https://www.on24.com/about-us/support/). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, permitindo o acesso à Ligação SAML de Ambiente Virtual ON24.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **ON24 Virtual Environment SAML Connection**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **ON24 Virtual Environment SAML Connection**.

    ![O link ON24 Virtual Environment SAML Connection na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Criar on24 Virtual Environment SAML Test utilizador

Nesta secção, cria um utilizador chamado Britta Simon em ON24 Virtual Environment SAML Connection. Trabalhe com a equipa de suporte para [ligação SAML ambiente virtual ON24](https://www.on24.com/about-us/support/) para adicionar os utilizadores na plataforma ON24 Virtual Environment SAML Connection. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo DE Ligação SAML de Ambiente Virtual ON24 no Painel de Acesso, deverá ser automaticamente inscrito na Ligação SAML de Ambiente Virtual ON24 para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)