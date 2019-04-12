---
title: 'Tutorial: Integração do Active Directory do Azure com Evernote | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 2ce05e904484a6d773a0132734208b87e161f960
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59499925"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Tutorial: Integração do Active Directory do Azure com Evernote

Neste tutorial, saiba como integrar Evernote com o Azure Active Directory (Azure AD).
Integrar Evernote no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Evernote.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Evernote (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Evernote, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Evernote logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Evernote **SP** e **IDP** iniciada SSO

## <a name="adding-evernote-from-the-gallery"></a>Adicionando Evernote da Galeria

Para configurar a integração do Evernote com o Azure AD, terá de adicionar Evernote a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Evernote a partir da galeria, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Evernote**, selecione **Evernote** a partir do painel de resultados, em seguida, clique nas **Add** botão para adicionar a aplicação.

     ![Evernote na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Evernote com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Evernote deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Evernote, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Evernote Single Sign-On](#configure-evernote-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Evernote](#create-evernote-test-user)**  - para ter um equivalente da Eduarda Almeida na Evernote que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Evernote, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Evernote** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, clique no **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar o passo seguinte:

    ![Evernote domínio e URLs únicas início de sessão em informações](common/idp-identifier.png)

    Na **identificador** caixa de texto, escreva um URL:  `https://www.evernote.com/saml2`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![image](common/both-preintegrated-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://www.evernote.com/Login.action`

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Para modificar a **assinatura** opções, clique nas **editar** botão para abrir o **certificado de assinatura SAML** caixa de diálogo.

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Selecione o **asserção e resposta SAML de início de sessão** opção para **opção assinatura**.

    b. Clicar em **Guardar**

8. Sobre o **configurar Evernote** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-evernote-single-sign-on"></a>Configurar Evernote Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Evernote como um administrador.

2. Aceda a **consola de administração**

    ![Consola de administração](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Do **consola de administração**, aceda à **"Segurança"** e selecione **"Single Sign-On"**

    ![Definição de SSO](./media/evernote-tutorial/tutorial_evernote_sso.png)

4. Configure os seguintes valores:

    ![Definição do certificado](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Ative o SSO:** SSO está ativado por predefinição (clique em **desativar o início de sessão único** para remover o requisito de SSO)

    b. Colar **URL de início de sessão** valor, que copiou do portal do Azure para o **URL de pedido de HTTP de SAML** caixa de texto.

    c. Abra o certificado transferido a partir do Azure AD no bloco de notas e copie o conteúdo, incluindo "BEGIN CERTIFICATE" e "END CERTIFICATE" e cole-o no **certificado X.509** caixa de texto. 

    d.Click **guardar alterações**

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Evernote.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Evernote**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Evernote**.

    ![A ligação de Evernote na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-evernote-test-user"></a>Criar utilizador de teste Evernote

Para habilitar logon Evernote de utilizadores do Azure AD, tem de ser aprovisionados em Evernote.  
No caso de Evernote, o aprovisionamento é uma tarefa manual.

**Para aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Evernote como administrador.

2. Clique nas **consola de administração**.

    ![Consola de administração](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Do **consola de administração**, aceda à **"Adicionar utilizadores"**.

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. **Adicionar membros da Equipe** no **E-Mail** caixa de texto, escreva o endereço de e-mail da conta de utilizador e clique em **convidar.**

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Depois de convite é enviado, o titular da conta do Azure Active Directory irá receber um e-mail para aceitar o convite.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Evernote no painel de acesso, deve ser automaticamente sessão iniciada no Evernote para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

