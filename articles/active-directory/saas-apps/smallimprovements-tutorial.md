---
title: 'Tutorial: Integração do Active Directory do Azure com pequenas melhorias | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e pequenos aprimoramentos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 1b59d1c030df95a650124f9e9ea34e06db7d08af
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180862"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Tutorial: Integração do Active Directory do Azure com pequenas melhorias

Neste tutorial, saiba como integrar pequenas melhorias no Azure Active Directory (Azure AD).
Integração de pequenas melhorias com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao pequenas melhorias.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para pequenas melhorias (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com pequenas melhorias, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Pequenas melhorias único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a pequenas melhorias **SP** iniciada SSO

## <a name="adding-small-improvements-from-the-gallery"></a>Adicionando pequenas melhorias da Galeria

Para configurar a integração de pequenas melhorias para o Azure AD, terá de adicionar pequenas melhorias a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar pequenas melhorias a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **pequenas melhorias**, selecione **pequenas melhorias** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Pequenas melhorias na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com pequenas aperfeiçoamento com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em pequenas melhorias deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com pequenas melhorias, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar pequenas melhorias de início de sessão único](#configure-small-improvements-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de pequenas melhorias](#create-small-improvements-test-user)**  - para ter um equivalente da Eduarda Almeida em pequenas melhorias que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com pequenas melhorias, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **pequenas melhorias** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Pequenas melhorias de domínio e URLs único informações de início de sessão](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.small-improvements.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de pequenas melhorias cliente](mailto:support@small-improvements.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar pequenas melhorias** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-small-improvements-single-sign-on"></a>Configurar pequenas melhorias início de sessão único

1. Na outra janela do browser, inicie sessão site da sua empresa pequenas melhorias como administrador.

1. Na página dashboard principal, clique em **administração** botão à esquerda.

    ![Configurar o início de sessão único](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Clique nas **SAML SSO** botão **integrações** secção.

    ![Configurar o início de sessão único](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Na página de configuração de SSO, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Na **ponto final de HTTP** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    b. Abra o seu certificado transferido no bloco de notas, copiar o conteúdo e, em seguida, cole-o para o **x509 certificado** caixa de texto. 

    c. Se desejar ter início de sessão e SSO formulário opção de autenticação disponível para os utilizadores, em seguida, verifique os **ativar o acesso através do início de sessão/palavra-passe demasiado** opção.  

    d. Introduza o valor apropriado para mencionar o botão de início de sessão do SSO no **linha de comandos do SAML** caixa de texto.  

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a pequenas melhorias.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **pequenas melhorias**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **pequenas melhorias**.

    ![A ligação de pequenas melhorias na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-small-improvements-test-user"></a>Criar utilizador de teste de pequenas melhorias

Para ativar a utilizadores do Azure AD iniciar sessão no pequenas melhorias, tem de ser aprovisionados em pequenas melhorias. No caso de pequenas melhorias, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Início de sessão no site da sua empresa pequenas melhorias como administrador.

1. Na Home page, vá para o menu no lado esquerdo, clique em **administração**.

1. Clique nas **diretório de utilizador** botão da secção de gestão de utilizadores.

    ![Criar um utilizador de teste do Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Clique em **adicionar utilizadores**.

    ![Criar um utilizador de teste do Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Sobre o **adicionar usuários** caixa de diálogo, execute os seguintes passos: 

    ![Criar um utilizador de teste do Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Introduza o **nome próprio** do utilizador, como **Eduarda**.

    b. Introduza o **Apelido** do utilizador, como **Simon**.

    c. Introduza o **E-Mail** do utilizador, como **brittasimon@contoso.com**.

    d. Também é possível inserir a mensagem pessoal na **Enviar notificação por e-mail** caixa. Se não pretender enviar a notificação, em seguida, desmarque esta caixa de verificação.

    e. Clique em **criar utilizadores**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de pequenas melhorias no painel de acesso, deve ser automaticamente conectado para as pequenas melhorias para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)