---
title: 'Tutorial: Integração do Active Directory do Azure com o Tableau Server | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 480e799e0fe307b39ce8a2f0b026a04f507a6beb
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408003"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Tutorial: Integração do Active Directory do Azure com o Tableau Server

Neste tutorial, saiba como integrar o Tableau Server no Azure Active Directory (Azure AD).
Integrar o Tableau Server no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao servidor da Tableau.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o servidor da Tableau (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Tableau Server, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Tableau Server logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* O servidor da tableau suporta **SP** iniciada SSO

## <a name="adding-tableau-server-from-the-gallery"></a>Adicionando o Tableau Server da Galeria

Para configurar a integração do Tableau Server no Azure AD, terá de adicionar o servidor da Tableau a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o servidor da Tableau a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **o servidor da Tableau**, selecione **Tableau Server** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Servidor da tableau na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Tableau Server com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Tableau Server deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Tableau Server, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Tableau Server início de sessão único](#configure-tableau-server-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do servidor da Tableau](#create-tableau-server-test-user)**  - para ter um equivalente da Eduarda Almeida no servidor da Tableau que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Tableau Server, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Tableau Server** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de servidor da tableau e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://azure.<domain name>.link`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão: `https://azure.<domain name>.link`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Os valores anteriores não são valores reais. Atualize os valores com o URL real e o identificador da página de configuração de servidor da Tableau, que é explicada mais tarde no tutorial.

5. Aplicação de servidor da tableau espera uma declaração personalizada **nome de utilizador** que deve ser definido conforme mostrado abaixo. Isso está sendo usado como identificador de utilizador em vez de afirmação de identificador de utilizador exclusivo. Pode gerir os valores destes atributos do **atributos de utilizador e afirmações** secção na página de integração de aplicações. Clique em **edite** botão para abrir **atributos de utilizador e afirmações** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador e afirmações** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name | Atributo de origem | Espaço de nomes |
    | ---------------| --------------- | ----------- |
    | username | user.userprincipalname | `https://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Introduza o **espaço de nomes** valor.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

8. Sobre o **configurar o servidor da Tableau** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-tableau-server-single-sign-on"></a>Configurar o Tableau Server início de sessão único

1. Para obter SSO configurado para a sua aplicação, terá de iniciar sessão no seu inquilino do servidor da Tableau como administrador.

2. Na **CONFIGURATION** separador, selecione **identidade de utilizador e acesso**e, em seguida, selecione o **autenticação** guia de método.

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Sobre o **configuração** página, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Para **método de autenticação**, selecione o SAML.

    b. Selecione a caixa de seleção de **ativar a autenticação de SAML para o servidor**.

    c. URL de retorno de servidor da tableau — o URL que os utilizadores do servidor da Tableau irão aceder aos, tais como <http://tableau_server>. Usando `http://localhost` não é recomendado. Utilizar um URL com um traço à direita (por exemplo, `http://tableau_server/`) não é suportada. Cópia **URL de retorno de servidor da Tableau** e cole-a para **URL de início de sessão** caixa de texto no **configuração básica de SAML** secção no portal do Azure

    d. ID de entidade SAML, o ID da entidade identifica de forma a instalação do servidor da Tableau para o IdP. Pode introduzir o URL de servidor da Tableau novamente aqui, se assim o desejar, mas ele não tem de ser o URL de servidor da Tableau. Cópia **ID de entidade SAML** e cole-a para **identificador** caixa de texto no **configuração básica de SAML** secção no portal do Azure

    e. Clique nas **transferir o ficheiro de metadados XML** e abri-lo no aplicativo de editor de texto. Localize o URL de serviço de consumidor de asserção com Http Post e de índice 0 e copie o URL. Agora cole-a para **URL de resposta** na caixa de texto **configuração básica de SAML** secção no portal do Azure

    f. Localize o ficheiro de metadados de Federação transferido a partir do portal do Azure e, em seguida, carregue-na **o ficheiro de metadados de SAML Idp**.

    g. Introduza os nomes para os atributos que o IdP utiliza para armazenar os nomes de utilizador, nomes a apresentar e endereços de e-mail.

    h. Clique em **Guardar**

    > [!NOTE]
    > Cliente tem de carregar qualquer certificado na configuração da Tableau Server SAML SSO e irá obter ignorada no fluxo SSO. Se precisar de ajudar a configurar o SAML no servidor da Tableau, em seguida, leia este artigo [configurar SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao servidor da Tableau.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Tableau Server**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Tableau Server**.

    ![A ligação de servidor da Tableau na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-tableau-server-test-user"></a>Criar utilizador de teste do servidor da Tableau

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no servidor da Tableau. Precisa de aprovisionar todos os utilizadores no servidor Tableau.

Esse nome de utilizador do utilizador deve corresponder ao valor que tiver configurado no atributo personalizado do Azure AD **nome de utilizador**. Com o mapeamento correto, a integração deve trabalhar Configuring Azure AD Single Sign-On.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, terá de contactar o administrador de servidor da Tableau na sua organização.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de servidor da Tableau no painel de acesso, deve ser automaticamente conectado para o servidor da Tableau para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

