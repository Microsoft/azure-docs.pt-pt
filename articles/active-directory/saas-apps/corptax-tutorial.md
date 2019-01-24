---
title: 'Tutorial: Integração do Active Directory do Azure com CorpTax | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e CorpTax.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: fdad2893-41f4-4056-b6d2-ec3d050eb350
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: f372d0a1a9d16801103380559332bff282c7e096
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818222"
---
# <a name="tutorial-azure-active-directory-integration-with-corptax"></a>Tutorial: Integração do Active Directory do Azure com CorpTax

Neste tutorial, saiba como integrar CorpTax com o Azure Active Directory (Azure AD).
Integrar CorpTax no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao CorpTax.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para CorpTax (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com CorpTax, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* CorpTax logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta CorpTax **SP** iniciada SSO

## <a name="adding-corptax-from-the-gallery"></a>Adicionando CorpTax da Galeria

Para configurar a integração do CorpTax com o Azure AD, terá de adicionar CorpTax a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar CorpTax a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select_azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise_applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add_new_app.png)

4. Na caixa de pesquisa, escreva **CorpTax**, selecione **CorpTax** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![CorpTax na lista de resultados](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com CorpTax com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no CorpTax deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com CorpTax, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar CorpTax Single Sign-On](#configure-corptax-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Criar utilizador de teste CorpTax](#create-corptax-test-user)**  - para ter um equivalente da Eduarda Almeida na CorpTax que está ligado à representação de utilizador do Azure AD.
5. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com CorpTax, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **CorpTax** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select_sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select_saml_option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit_urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![CorpTax domínio e URLs únicas início de sessão em informações](common/sp_intiated.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://asp.corptax.com`

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir **XML de metadados de Federação** e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-corptax-single-sign-on"></a>Configurar CorpTax Single Sign-On

Para configurar o início de sessão único num **CorpTax** lado, terá de enviar o transferido **XML de metadados de Federação** para [equipa de suporte de CorpTax](mailto:bgraves@corptax.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new_user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user_properties.png)

    a. Na **Name** , insira **BrittaSimon**.

    b. Na **nome de utilizador** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para CorpTax.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **CorpTax**.

    ![Painel de aplicações empresariais](common/enterprise_applications.png)

2. Na lista de aplicativos, escreva e selecione **CorpTax**.

    ![A ligação de CorpTax na lista de aplicações](common/all_applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users_groups_blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add_assign_user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-corptax-test-user"></a>Criar utilizador de teste CorpTax

Nesta secção, vai criar um usuário chamado Eduarda Almeida no CorpTax. Trabalhar com [equipa de suporte de CorpTax](mailto:bgraves@corptax.com) para adicionar os utilizadores na plataforma CorpTax. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.
Quando clica no mosaico CorpTax no painel de acesso, deve ser redirecionado para o abaixo CorpTax página - 

![image](media/corptax-tutorial/corptaxlogin.png)

Na **ambiente** caixa de texto, escreva o seu ambiente apropriado, deve ter automaticamente sessão iniciada no CorpTax para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
