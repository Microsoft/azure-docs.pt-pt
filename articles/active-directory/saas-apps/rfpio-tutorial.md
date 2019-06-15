---
title: 'Tutorial: Integração do Active Directory do Azure com RFPIO | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d7b6af8ff76c890b98c29ded0e8bdc637b45dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092854"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Tutorial: Integração do Active Directory do Azure com RFPIO

Neste tutorial, saiba como integrar RFPIO com o Azure Active Directory (Azure AD).
Integrar RFPIO no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao RFPIO.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para RFPIO (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com RFPIO, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* RFPIO logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta RFPIO **SP e IDP** iniciada SSO

## <a name="adding-rfpio-from-the-gallery"></a>Adicionando RFPIO da Galeria

Para configurar a integração do RFPIO com o Azure AD, terá de adicionar RFPIO a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar RFPIO a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **RFPIO**, selecione **RFPIO** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![RFPIO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com RFPIO com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no RFPIO deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com RFPIO, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar RFPIO Single Sign-On](#configure-rfpio-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste RFPIO](#create-rfpio-test-user)**  - para ter um equivalente da Eduarda Almeida na RFPIO que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com RFPIO, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **RFPIO** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar o passo seguinte:

    ![RFPIO domínio e URLs únicas início de sessão em informações](common/idp-identifier.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://www.rfpio.com`

    b. Clique em **definir URLs adicionais**.

    c. Na **estado de reencaminhamento** caixa de texto, introduza um valor de cadeia de caracteres. Contacte [equipa de suporte de RFPIO](https://www.rfpio.com/contact/) para obter este valor.

    ![RFPIO domínio e URLs únicas início de sessão em informações](common/idp-preintegrated-relay.png)

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![image](common/both-preintegrated-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://www.app.rfpio.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador e início de sessão real. Contacte [equipa de suporte de cliente RFPIO](https://www.rfpio.com/contact/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar RFPIO** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-rfpio-single-sign-on"></a>Configurar RFPIO início de sessão único

1. Numa janela do browser web diferente, inicie sessão para o **RFPIO** Web site como um administrador.

1. Clique no menu pendente na parte inferior esquerda canto.

    ![Configurar o início de sessão único](./media/rfpio-tutorial/app1.png)

1. Clique nas **definições de organização**. 

    ![Configurar o início de sessão único](./media/rfpio-tutorial/app2.png)

1. Clique nas **funcionalidades e integração**.

    ![Configurar o início de sessão único](./media/rfpio-tutorial/app4.png)

1. Na **configuração de SAML SSO** clique em **editar**.

    ![Configurar o início de sessão único](./media/rfpio-tutorial/app3.png)

1. Nesta secção executa as ações seguintes:

    ![Configurar o início de sessão único](./media/rfpio-tutorial/app5.png)
    
    a. Copie o conteúdo do **XML de metadados baixado** e cole-o para o **configuração de identidade** campo.

    > [!NOTE]
    >Para copiar o conteúdo de transferido **XML de metadados de Federação** utilização **bloco de notas + +** ou adequado **Editor de XML**.

    b. Clique em **validar**.

    c. Depois de clicar em **validar**, Flip **SAML(Enabled)** como ativado.

    d. Clique em **Submit** (Submeter).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para RFPIO.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **RFPIO**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **RFPIO**.

    ![A ligação RFPIO na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-rfpio-test-user"></a>Criar utilizador de teste RFPIO

1. Inicie sessão no site da sua empresa RFPIO como um administrador.

1. Clique no menu pendente na parte inferior esquerda canto.

    ![Configurar o início de sessão único](./media/rfpio-tutorial/app1.png)

1. Clique nas **definições de organização**. 

    ![Configurar o início de sessão único](./media/rfpio-tutorial/app2.png)

1. Clique em **os integrantes da EQUIPE**.

    ![Configurar o início de sessão único](./media/rfpio-tutorial/app6.png)

1. Clique em **adicionar membros**.

    ![Configurar o início de sessão único](./media/rfpio-tutorial/app7.png)

1. Na **adicionar novos membros** secção. Execute as ações seguintes:

    ![Configurar o início de sessão único](./media/rfpio-tutorial/app8.png)

    a. ENTER **endereço de E-Mail** no **introduza um e-mail por linha** campo.

    b. Selecione **função** de acordo com os seus requisitos.

    c. Clique em **adicionar membros**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico RFPIO no painel de acesso, deve ser automaticamente sessão iniciada no RFPIO para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

