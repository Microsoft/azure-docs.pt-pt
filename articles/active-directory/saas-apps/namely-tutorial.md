---
title: 'Tutorial: Integração do Active Directory do Azure com especificamente | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e saber.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9541d5c4-4c82-4b5b-b01a-6a3f75a2b7a1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 89f038bb8983c437f4185fc7115ff7d338420003
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57409827"
---
# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Tutorial: Integração do Active Directory do Azure com saber,

Neste tutorial, saiba como integrar, ou seja, com o Azure Active Directory (Azure AD).
Ou seja, a integração com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD com acesso ao saber.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para saber, (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar o Azure AD a integração com a saber, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Ou seja, início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Ou seja suporta **SP** iniciada SSO

## <a name="adding-namely-from-the-gallery"></a>Adicionando, ou seja, da Galeria

Para configurar a integração de saber para o Azure AD, terá de adicionar ou seja, a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar ou seja, a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **saber**, selecione **saber** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Ou seja, na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar do Azure AD início de sessão único com, ou seja, com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em saber, deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com ou seja, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a saber, início de sessão único](#configure-namely-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste ou seja](#create-namely-test-user)**  - para ter um equivalente da Eduarda Almeida em saber que está ligada à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com a saber, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **saber** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Ou seja, domínio e URLs informações de início de sessão único](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.namely.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.namely.com/saml/metadata`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente ou seja](https://www.namely.com/contact/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar ou seja** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-namely-single-sign-on"></a>Configurar a saber, início de sessão único

1. Na outra janela do browser, inicie sessão no seu site da empresa ou seja, como administrador.

2. Na barra de ferramentas na parte superior, clique em **empresa**.
   
    ![Configurar o início de sessão único](./media/namely-tutorial/tutorial_namely_06.png) 

3. Clique no separador **Definições**.
   
    ![Configurar o início de sessão único](./media/namely-tutorial/tutorial_namely_07.png) 

4. Clique em **SAML**.
   
    ![Configurar o início de sessão único](./media/namely-tutorial/tutorial_namely_08.png) 

5. Sobre o **definições de SAML** página, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/namely-tutorial/tutorial_namely_09.png)
 
    a. Clique em **ativar SAML**. 

    b. Na **url SSO de fornecedor de identidade** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.
    
    c. Abra o seu certificado transferido no bloco de notas, copiar o conteúdo e, em seguida, cole-o para o **certificado do fornecedor de identidade** caixa de texto.
     
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
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único ao conceder acesso a saber.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **nomeadamente**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **nomeadamente**.

    ![A ligação, ou seja, na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-namely-test-user"></a>Criar ou seja, utilizador de teste

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no, ou seja.

**Para criar um usuário chamado Eduarda Almeida no, ou seja, execute os seguintes passos:**

1. Início de sessão no seu site da empresa ou seja, como administrador.

2. Na barra de ferramentas na parte superior, clique em **pessoas**.
   
    ![Configurar o início de sessão único](./media/namely-tutorial/tutorial_namely_10.png) 

3. Clique nas **Directory** separador.
   
    ![Configurar o início de sessão único](./media/namely-tutorial/tutorial_namely_11.png) 

4. Clique em **Adicionar nova pessoa**.

    ![Configurar o início de sessão único](./media/namely-tutorial/tutorial_namely_12.png)

5. Sobre o **Adicionar nova pessoa** caixa de diálogo, execute os seguintes passos:

    a. Na **nome próprio** caixa de texto, tipo **Eduarda**.

    b. Na **Apelido** caixa de texto, tipo **Simon**.

    c. Na **E-Mail** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    d. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico ou seja, no painel de acesso, deve ter automaticamente sessão iniciada na saber, para que configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

