---
title: 'Tutorial: Integração do Active Directory do Azure com o arco de Dome9 | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Dome9 arco.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: da98268d0a6ee015c848e9fe91cb3deecf28c4f4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56875706"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Tutorial: Integração do Active Directory do Azure com Dome9 arco

Neste tutorial, saiba como integrar o arco de Dome9 com o Azure Active Directory (Azure AD).
Integrar o arco de Dome9 no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Dome9 arco.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada em arco de Dome9 (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Dome9 arco, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Arco de Dome9 logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o arco de Dome9 **SP** e **IDP** iniciada SSO

## <a name="adding-dome9-arc-from-the-gallery"></a>Adicionando Dome9 arco da Galeria

Para configurar a integração do arco Dome9 com o Azure AD, terá de adicionar Dome9 arco a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Dome9 arco a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Dome9 arco**, selecione **Dome9 arco** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Arco de Dome9 na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o arco de Dome9 com base num utilizador de teste **Eduarda Almeida**.
Para início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Dome9 arco tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o arco de Dome9, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Dome9 arco início de sessão único](#configure-dome9-arc-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de arco Dome9](#create-dome9-arc-test-user)**  - para ter um equivalente da Eduarda Almeida em arco Dome9 que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o arco de Dome9, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Dome9 arco** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Dome9 arco domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://secure.dome9.com/`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Irá selecionar o valor de nome de empresa no portal de administração dome9, que é explicado mais tarde no tutorial.

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Dome9 arco domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e URL de resposta real. Contacte [equipa de suporte de cliente de arco Dome9](mailto:support@dome9.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. Aplicação de arco Dome9 espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos: 

    | Name |  Atributo de origem|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Sobre o **configurar Dome9 arco** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-dome9-arc-single-sign-on"></a>Configurar Dome9 arco início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Dome9 arco como um administrador.

2. Clique nas **definições de perfil** no canto superior direito e clique em **definições da conta**. 

    ![Configuração de arco Dome9](./media/dome9arc-tutorial/configure1.png)

3. Navegue para **SSO** e, em seguida, clique em **ATIVAR**.

    ![Configuração de arco Dome9](./media/dome9arc-tutorial/configure2.png)

4. Na secção de configuração de SSO, execute os seguintes passos:

    ![Configuração de arco Dome9](./media/dome9arc-tutorial/configure3.png)

    a. Introduza o nome da empresa no **ID da conta** caixa de texto. Este valor é a ser utilizado na resposta de que URL mencionado na secção URL portal do Azure.

    b. Na **emissor** caixa de texto, cole o valor de **identificador do Azure Ad**, que tenha copiado o formulário do portal do Azure.

    c. Na **url de ponto final de Idp** caixa de texto, cole o valor de **URL de início de sessão**, que tenha copiado o formulário do portal do Azure.

    d. Abra o seu certificado codificado em Base64 transferido no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509** caixa de texto.

    e. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso Dome9 arco.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Dome9 arco**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Dome9 arco**.

    ![A ligação de arco Dome9 na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-dome9-arc-test-user"></a>Criar utilizador de teste Dome9 arco

Para permitir que os utilizadores do Azure AD iniciem sessão arco Dome9, tem de ser aprovisionados no aplicativo. Arco de Dome9 suporta o aprovisionamento de just-in-time, mas para que isso funcione corretamente, o utilizador tem de selecionar particular **função** e atribuir o mesmo para o usuário.

   >[!Note]
   >Para **função** criação e outros detalhes de contacto [equipa de suporte de cliente de arco Dome9](https://dome9.com/about/contact-us/).

**Para Aprovisionar uma conta de utilizador manualmente, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Dome9 arco como administrador.

2. Clique nas **utilizadores e funções** e, em seguida, clique em **utilizadores**.

    ![Adicionar o funcionário](./media/dome9arc-tutorial/user1.png)

3. Clique em **adicionar utilizador**.

    ![Adicionar o funcionário](./media/dome9arc-tutorial/user2.png)

4. Na **criar utilizador** secção, execute os seguintes passos:

    ![Adicionar o funcionário](./media/dome9arc-tutorial/user3.png)

    a. Na **E-Mail** caixa de texto, como o tipo de e-mail do utilizador Brittasimon@contoso.com.

    b. Na **nome próprio** caixa de texto, tipo primeiro nome do utilizador, como a Eduarda.

    c. Na **Apelido** apelido tipo do utilizador, como Simon, caixa de texto.

    d. Tornar **utilizador SSO** como **no**.

    e. Clique em **criar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de arco Dome9 no painel de acesso, deve ser automaticamente sessão iniciada no arco de Dome9 para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

