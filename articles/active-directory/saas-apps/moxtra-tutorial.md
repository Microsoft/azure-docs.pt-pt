---
title: 'Tutorial: Integração do Active Directory do Azure com Moxtra | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 7b1fe34c390eecb77ff608a334eae2a8b2e9ae50
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840397"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Tutorial: Integração do Active Directory do Azure com Moxtra

Neste tutorial, saiba como integrar Moxtra com o Azure Active Directory (Azure AD).
Integrar Moxtra no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Moxtra.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Moxtra (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Moxtra, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Moxtra logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Moxtra **SP** iniciada SSO

## <a name="adding-moxtra-from-the-gallery"></a>Adicionando Moxtra da Galeria

Para configurar a integração do Moxtra com o Azure AD, terá de adicionar Moxtra a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Moxtra a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Moxtra**, selecione **Moxtra** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Moxtra na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Moxtra com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Moxtra deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Moxtra, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Moxtra Single Sign-On](#configure-moxtra-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Moxtra](#create-moxtra-test-user)**  - para ter um equivalente da Eduarda Almeida na Moxtra que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Moxtra, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Moxtra** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Moxtra domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://www.moxtra.com/service/#login`

5. Aplicação de Moxtra espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. Além dos acima, o aplicativo Moxtra espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir: 

    | Name | Atributo de origem|
    | ------------------- | -------------------- |    
    | FirstName | user.givenname |
    | Apelido | user.surname |
    | idpid    | < Identificador do azure AD >

    > [!Note]
    > O valor de **idpid** atributo não é real. Pode obter o valor real do **configurar Moxtra** secção do passo n. º 8. 

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar Moxtra** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-moxtra-single-sign-on"></a>Configurar Moxtra Single Sign-On

1. Na outra janela do browser, inicie sessão site da sua empresa Moxtra como um administrador.

2. Na barra de ferramentas do lado esquerdo, clique em **consola de administração > o início de sessão único SAML**e, em seguida, clique em **New**.
   
    ![Configurar o início de sessão único](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Sobre o **SAML** página, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Na **nome** caixa de texto, escreva um nome para a sua configuração (por exemplo: *SAML*). 
  
    b. Na **ID de entidade do IdP** caixa de texto, cole o valor de **do Azure AD identificador** que copiou do portal do Azure. 
 
    c. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure. 
 
    d. Na **AuthnContextClassRef** caixa de texto, tipo **urn: oasis: nomes: tc: SAML:2.0:ac:classes:Password**. 
 
    e. Na **formato NameID** caixa de texto, tipo **urn: oasis: nomes: tc: SAML:1.1:nameid-formato: endereço de correio eletrónico**. 
 
    f. Abrir certificado que transferiu a partir do portal do Azure no bloco de notas, copie o conteúdo e, em seguida, cole-o no **certificado** caixa de texto.    
 
    g. Na textbox de domínio de e-mail SAML, escreva o seu domínio de e-mail SAML.    
  
    >[!NOTE]
    >Para ver os passos para verificar o domínio, clique a "**eu**" abaixo.

    h. Clique em **Atualizar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Moxtra.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Moxtra**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Moxtra**.

    ![A ligação de Moxtra na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-moxtra-test-user"></a>Criar utilizador de teste Moxtra

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Moxtra.

**Para criar um usuário chamado Eduarda Almeida no Moxtra, execute os seguintes passos:**

1. Inicie sessão site da sua empresa Moxtra como um administrador.

1. Na barra de ferramentas do lado esquerdo, clique em **consola de administrador > Gestão de utilizadores**e, em seguida **adicionar utilizador**.
   
    ![Configurar o início de sessão único](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Sobre o **adicionar utilizador** caixa de diálogo, execute os seguintes passos:
  
    a. Na **nome próprio** caixa de texto, tipo **Eduarda**.
  
    b. Na **sobrenome** caixa de texto, tipo **Simon**.
  
    c. Na **E-Mail** caixa de texto, tipo da Eduarda endereço de e-mail mesmo que no portal do Azure.
  
    d. Na **divisão** caixa de texto, tipo **Dev**.
  
    e. Na **departamento** caixa de texto, tipo **IT**.
  
    f. Selecione **administrador**.
  
    g. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Moxtra no painel de acesso, deve ser automaticamente sessão iniciada no Moxtra para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

