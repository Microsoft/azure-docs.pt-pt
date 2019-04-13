---
title: 'Tutorial: Integração do Active Directory do Azure com a sintaxe CRM | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o CRM de sintaxe.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 5946656589baeb5aac649d63eef72d846c1c2658
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547576"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Tutorial: Integração do Active Directory do Azure com o CRM de sintaxe

Neste tutorial, saiba como integrar o CRM de sintaxe com o Azure Active Directory (Azure AD).
Integração de CRM de sintaxe com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao CRM de sintaxe.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a sintaxe de CRM (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o CRM de sintaxe, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Sintaxe CRM logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Sintaxe suporta CRM **SP** iniciada SSO

## <a name="adding-sugar-crm-from-the-gallery"></a>Adicionando o CRM de sintaxe da Galeria

Para configurar a integração de CRM de sintaxe para o Azure AD, terá de adicionar o CRM de sintaxe na Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o CRM de sintaxe da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **sintaxe CRM**, selecione **sintaxe CRM** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Sintaxe CRM na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o CRM de sintaxe, com base num utilizador de teste **Eduarda Almeida**.
Para início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na sintaxe CRM tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o CRM de sintaxe, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a sintaxe CRM início de sessão único](#configure-sugar-crm-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de sintaxe CRM](#create-sugar-crm-test-user)**  - para ter um equivalente da Eduarda Almeida no CRM de sintaxe que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o CRM de sintaxe, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **sintaxe CRM** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Sintaxe CRM domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de CRM sintaxe](https://support.sugarcrm.com/) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar sintaxe CRM** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-sugar-crm-single-sign-on"></a>Configurar a sintaxe CRM início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa sintaxe CRM como um administrador.

1. Aceda a **administrador**.

    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Na **Administration** secção, clique em **gestão de palavra-passe**.

    ![Administração](./media/sugarcrm-tutorial/ic795889.png "administração")

1. Selecione **ativar a autenticação SAML**.

    ![Administração](./media/sugarcrm-tutorial/ic795890.png "administração")

1. Na **autenticação SAML** secção, execute os seguintes passos:

    ![Autenticação SAML](./media/sugarcrm-tutorial/ic795891.png "autenticação SAML")  

    a. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.
  
    b. Na **URL de SLO** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.
  
    c. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole o certificado completo para **certificado X.509** caixa de texto.
  
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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao CRM de sintaxe.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **sintaxe CRM**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **sintaxe CRM**.

    ![A ligação de CRM de sintaxe na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sugar-crm-test-user"></a>Criar utilizador de teste de sintaxe CRM

Para habilitar os utilizadores do Azure AD iniciar sessão no CRM de sintaxe, tem de ser aprovisionados para CRM de sintaxe. No caso de sintaxe CRM, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **sintaxe CRM** site da empresa como administrador.

1. Aceda a **administrador**.

    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Na **Administration** secção, clique em **gestão de utilizadores**.

    ![Administração](./media/sugarcrm-tutorial/ic795893.png "administração")

1. Aceda a **usuários \> criar novo utilizador**.

    ![Criar novo usuário](./media/sugarcrm-tutorial/ic795894.png "criar novo utilizador")

1. Sobre o **perfil de utilizador** separador, execute os seguintes passos:

    ![Novo utilizador](./media/sugarcrm-tutorial/ic795895.png "novo utilizador")

    * Tipo de **nome de utilizador**, **Apelido**, e **endereço de e-mail** de um utilizador válido do Azure Active Directory para as caixas de texto relacionados.
  
1. Como **Status**, selecione **Active Directory**.

1. No separador de palavras-passe, execute os seguintes passos:

    ![Novo utilizador](./media/sugarcrm-tutorial/ic795896.png "novo utilizador")

    a. Escreva a palavra-passe na caixa de texto relacionada.

    b. Clique em **Guardar**.

> [!NOTE]
> Pode usar qualquer outras sintaxe CRM utilizador conta ferramentas de criação ou APIs fornecidas pelo CRM de sintaxe para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de CRM de sintaxe no painel de acesso, deve ser automaticamente sessão iniciada no CRM de sintaxe para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

