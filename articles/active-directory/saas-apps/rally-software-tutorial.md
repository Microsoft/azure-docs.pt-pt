---
title: 'Tutorial: Integração do Active Directory do Azure com Rally Software | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Rally Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: de84d03c3e0e433dbe7bc24c47b1766b32ad7bc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093180"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Integração do Active Directory do Azure com Rally Software

Neste tutorial, saiba como integrar Rally Software com o Azure Active Directory (Azure AD).
Integrar o Rally Software no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Rally Software.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada ao Software Rally (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Rally Software, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Rally única início de sessão ativada a subscrição do Software

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Rally suporta de Software **SP** iniciada SSO

## <a name="adding-rally-software-from-the-gallery"></a>Adicionar Rally Software a partir da Galeria

Para configurar a integração da Rally Software para o Azure AD, terá de adicionar Rally Software a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Rally Software a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Rally Software**, selecione **Rally Software** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Rally Software na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Software Rally com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Rally Software deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Software da Rally, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Rally Software início de sessão único](#configure-rally-software-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de Rally Software](#create-rally-software-test-user)**  - para ter um equivalente da Eduarda Almeida no Software Rally, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Software de Rally, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Rally Software** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Rally Software domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.rally.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.rally.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente de Software Rally](https://help.rallydev.com/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar Rally Software** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-rally-software-single-sign-on"></a>Configurar Rally Software início de sessão único

1. Inicie sessão no seu **Rally Software** inquilino.

2. Na barra de ferramentas na parte superior, clique em **programa de configuração**e, em seguida, selecione **subscrição**.
   
    ![Subscrição](./media/rally-software-tutorial/ic769531.png "subscrição")

3. Clique nas **ação** botão. Selecione **editar subscrição** no lado superior direito da barra de ferramentas.

4. Sobre o **subscrição** página de diálogo, execute os seguintes passos e, em seguida, clique em **guardar e fechar**:
   
    ![Autenticação](./media/rally-software-tutorial/ic769542.png "autenticação")
   
    a. Selecione **autenticação Rally ou SSO** no menu pendente de autenticação.

    b. No **URL de fornecedor de identidade** caixa de texto, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure. 

    c. Na **fim de sessão de SSO** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Rally Software.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Rally Software**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Rally Software**.

    ![A ligação da Rally Software na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-rally-software-test-user"></a>Criar utilizador de teste de Rally Software

Para utilizadores do Azure AD possam iniciar sessão, tem de ser aprovisionados para a aplicação de Rally Software com seus nomes de utilizador do Azure Active Directory.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu inquilino Rally Software.

2. Aceda a **programa de configuração \> USUÁRIOS**e, em seguida, clique em **+ adicionar novo**.
   
    ![Os utilizadores](./media/rally-software-tutorial/ic781039.png "utilizadores")

3. Escreva o nome na caixa de texto novo utilizador e, em seguida, clique em **adicionar com detalhes**.

4. Na **criar utilizador** secção, execute os seguintes passos:
   
    ![Criar utilizador](./media/rally-software-tutorial/ic781040.png "criar utilizador")

    a. Na **nome de utilizador** como a caixa de texto, escreva o nome de utilizador **Brittsimon**.
   
    b. Na **endereço de email** caixa de texto, introduza o e-mail do utilizador, como brittasimon@contoso.com.

    c. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    d. Na **sobrenome** texto, digite o apelido do utilizador, como **Simon**.

    e. Clique em **guardar e fechar**.

   >[!NOTE]
   >Pode utilizar quaisquer outras ferramentas de criação da conta de utilizador de Rally Software ou APIs fornecidas pelo Rally Software para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Rally Software no painel de acesso, deve ser automaticamente conectado ao Software da Rally, para que configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

