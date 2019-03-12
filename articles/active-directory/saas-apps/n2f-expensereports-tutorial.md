---
title: 'Tutorial: Relatórios de despesas de integração do Active Directory do Azure com N2F - | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e N2F - relatórios de despesas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 67678c560564d762a2c1a6d472cd4b05315540f7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57769724"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Tutorial: Relatórios de despesas de integração do Active Directory do Azure com N2F-

Neste tutorial, saiba como integrar N2F - relatórios de despesas no Azure Active Directory (Azure AD).
Integrando N2F - relatórios de despesas com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao N2F - relatórios de despesas.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para N2F - relatórios de despesas (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com N2F - relatórios de despesas, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* N2F - relatórios de despesas de início de sessão de subscrição de ativado individual

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Relatórios de despesas de N2F - suporta **SP** e **IDP** iniciada SSO

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Adicionar N2F - relatórios de despesas da Galeria

Para configurar a integração do N2F - relatórios de despesas no Azure AD, tem de adicionar N2F - relatórios a partir da Galeria à sua lista de aplicações de SaaS geridas de despesas.

**Para adicionar N2F - relatórios de despesas a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **N2F - relatórios de despesas**, selecione **N2F - relatórios de despesas** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![N2F - relatórios de despesas na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com N2F - relatórios de despesas com base num utilizador de teste chamado **Eduarda Almeida**.
Para o início de sessão único para o trabalho, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no N2F - despesas relatórios tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com N2F - relatórios de despesas, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar N2F - relatórios de despesas início de sessão único](#configure-n2f---expense-reports-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar N2F - utilizador de teste de relatórios de despesas](#create-n2f---expense-reports-test-user)**  - para ter um equivalente da Eduarda Almeida na N2F - despesas relatórios que está ligada à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com N2F - relatórios de despesas, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **N2F - relatórios de despesas** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação na **IDP** modo iniciado, o utilizador não tem de efetuar outros passos de dado que a aplicação já está pré-integrado com o Azure.

    ![Informações de início de sessão de único N2F - relatórios de despesas, domínio e URLs](common/preintegrated.png)

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Informações de início de sessão de único N2F - relatórios de despesas, domínio e URLs](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://www.n2f.com/app/`

6. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

7. Sobre o **configurar myPolicies** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Configurar N2F - relatórios de despesas de início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu N2F - site de empresa de relatórios de despesas como administrador.

2. Clique em **configurações** e, em seguida, selecione **definições avançadas** na lista pendente.

    ![N2F - configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecione **definições da conta** separador.

    ![N2F - configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure2.png)

4. Selecione **autenticação** e, em seguida, selecione **+ adicionar um método de autenticação** separador.

    ![N2F - configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure3.png)

5. Selecione **SAML Microsoft Office 365** como método de autenticação.

    ![N2F - configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure4.png)

6. Sobre o **método de autenticação** secção, execute os seguintes passos:

    ![N2F - configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/configure5.png)

    a. Na **ID de entidade** caixa de texto, colar a **do Azure AD identificador** valor, que copiou do portal do Azure.

    b. Na **URL de metadados** caixa de texto, colar a **Url de metadados de Federação de aplicação** valor, que copiou do portal do Azure.

    c. Clique em **Guardar**.

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

Nesta secção, ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para N2F - relatórios de despesas.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **N2F - relatórios de despesas**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **N2F - relatórios de despesas**.

    ![Relatórios de despesas de N2F - link na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-n2f---expense-reports-test-user"></a>Criar N2F - utilizador de teste de relatórios de despesas

Para permitir que utilizadores do Azure AD iniciar sessão no N2F - relatórios de despesas, eles têm de ser aprovisionados em N2F - relatórios de despesas. No caso de N2F - relatórios de despesas, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão na sua N2F - site de empresa de relatórios de despesas como administrador.

2. Clique em **configurações** e, em seguida, selecione **definições avançadas** na lista pendente.

    ![N2F - despesas adicionar utilizador](./media/n2f-expensereports-tutorial/configure1.png)

3. Selecione **utilizadores** separador do painel de navegação à esquerda.

    ![N2F - configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/user1.png)

4. Selecione **+ novo utilizador** separador.

    ![N2F - configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/user2.png)

5. Sobre o **utilizador** secção, execute os seguintes passos:

    ![N2F - configuração de relatórios de despesas](./media/n2f-expensereports-tutorial/user3.png)

    a. Na **endereço de E-Mail** caixa de texto, introduza o endereço de e-mail do utilizador, como **brittasimon@contoso.com**.

    b. Na **nome próprio** caixa de texto, introduza o nome de utilizador, como **Eduarda**.

    c. Na **Name** caixa de texto, introduza o nome de utilizador, como **BrittaSimon**.

    d. Escolher **função de Gestor direto (N + 1)**, e **divisão** de acordo com seus requisitos de organização.

    e. Clique em **para validar e enviar convite**.

    > [!NOTE]
    > Se estiver com problemas ao adicionar o utilizador, entre em contato com [N2F - equipa de suporte de relatórios de despesas](mailto:support@n2f.com)

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar o N2F - mosaico de relatórios de despesas no painel de acesso, deve ser automaticamente sessão iniciada no N2F - relatórios de despesas para os quais configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

