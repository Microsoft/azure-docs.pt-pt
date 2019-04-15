---
title: 'Tutorial: Integração do Active Directory do Azure com Kintone | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: a66d1cb16f47ebe7aeba3685b3567620ae4a8a51
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564854"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: Integração do Active Directory do Azure com Kintone

Neste tutorial, saiba como integrar Kintone com o Azure Active Directory (Azure AD).
Integrar Kintone no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso para Kintone.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Kintone (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Kintone, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Kintone logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Kintone **SP** iniciada SSO

## <a name="adding-kintone-from-the-gallery"></a>Adicionando Kintone da Galeria

Para configurar a integração do Kintone com o Azure AD, terá de adicionar Kintone a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Kintone a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Kintone**, selecione **Kintone** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Kintone na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Kintone com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Kintone deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Kintone, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Kintone Single Sign-On](#configure-kintone-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Kintone](#create-kintone-test-user)**  - para ter um equivalente da Eduarda Almeida na Kintone que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Kintone, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Kintone** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Kintone domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.kintone.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.cybozu.com` |
    | `https://<companyname>.kintone.com` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente Kintone](https://www.kintone.com/contact/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Kintone** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-kintone-single-sign-on"></a>Configurar Kintone Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no seu **Kintone** site da empresa como administrador.

1. Clique em **ícone de definições**.

    ![As definições](./media/kintone-tutorial/ic785879.png "definições")

1. Clique em **utilizadores e de administração do sistema**.

    ![Utilizadores e de administração do sistema](./media/kintone-tutorial/ic785880.png "de utilizadores e de administração do sistema")

1. Sob **de administração do sistema \> Security** clique em **início de sessão**.

    ![Login](./media/kintone-tutorial/ic785881.png "Login")

1. Clique em **autenticação SAML ativar**.

    ![Autenticação SAML](./media/kintone-tutorial/ic785882.png "autenticação SAML")

1. Na secção autenticação SAML, execute os seguintes passos:

    ![Autenticação SAML](./media/kintone-tutorial/ic785883.png "autenticação SAML")

    a. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    b. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    c. Clique em **procurar** para carregar o ficheiro de certificado transferido a partir do portal do Azure.

    d. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Kintone.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Kintone**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Kintone**.

    ![A ligação de Kintone na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-kintone-test-user"></a>Criar utilizador de teste Kintone

Para ativar a utilizadores do Azure AD iniciar sessão para Kintone, tem de ser aprovisionados para Kintone. No caso de Kintone, o aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para Aprovisionar uma conta de utilizador, execute os seguintes passos:

1. Inicie sessão no seu **Kintone** site da empresa como administrador.

1. Clique em **ícone de definições**.

    ![As definições](./media/kintone-tutorial/ic785879.png "definições")

1. Clique em **utilizadores e de administração do sistema**.

    ![Administração de sistemas e usuários](./media/kintone-tutorial/ic785880.png "administração do sistema & utilizador")

1. Sob **administração de utilizadores**, clique em **departamentos e usuários**.

    ![Departamento e utilizadores](./media/kintone-tutorial/ic785888.png "departamento & utilizadores")

1. Clique em **novo utilizador**.

    ![Novos utilizadores](./media/kintone-tutorial/ic785889.png "novos utilizadores")

1. Na **novo utilizador** secção, execute os seguintes passos:

    ![Novos utilizadores](./media/kintone-tutorial/ic785890.png "novos utilizadores")

    a. Introduza um **nome a apresentar**, **nome de início de sessão**, **nova palavra-passe**, **Confirmar palavra-passe**, **endereço de correio eletrónico**, e outros detalhes de um válido do Azure AD da conta pretende aprovisionar em caixas de texto relacionadas.

    b. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras Kintone utilizador conta criação ferramentas ou APIs fornecidas pelo Kintone para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Kintone no painel de acesso, deve ser automaticamente conectado para Kintone para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
