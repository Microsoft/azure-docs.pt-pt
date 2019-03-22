---
title: 'Tutorial: Integração do Active Directory do Azure com a tarefa no | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e TAS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 129b6e69-e3b4-41d7-9ab5-a2ddd0068f76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 5dcae137db04b604d7b0450c3bec18fc23b36991
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227453"
---
# <a name="tutorial-azure-active-directory-integration-with-tas"></a>Tutorial: Integração do Active Directory do Azure com a tarefa no

Neste tutorial, saiba como integrar a tarefa no Azure Active Directory (Azure AD).
Integração de tarefa com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao TAS.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a tarefa de no (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a tarefa no, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Tarefa no início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Tarefa no suporta **SP e IDP** iniciada SSO

## <a name="adding-tas-from-the-gallery"></a>Adicionando a tarefa da Galeria

Para configurar a integração de tarefa para o Azure AD, terá de adicionar a tarefa no partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar a tarefa no partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **tarefa no**, selecione **tarefa no** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Tarefa na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com tarefa com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na tarefa no deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com a tarefa no, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar tarefa no início de sessão único](#configure-tas-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de tarefa no](#create-tas-test-user)**  - para ter um equivalente da Eduarda Almeida na tarefa no que está ligada à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com a tarefa no, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **tarefa no** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Tarefa no domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://taseu.combtas.com/<DOMAIN>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://taseu.combtas.com/<ENVIRONMENTNAME>/AssertionService.aspx`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Tarefa no domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://taseu.combtas.com/<DOMAIN>`

    > [!NOTE]
    > Estes valores não são reais. Atualizar-os com o URL de identificador, o URL de resposta e início de sessão real que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar a tarefa no** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-tas-single-sign-on"></a>Configurar tarefa no início de sessão único

1. Numa janela do browser web diferente, início de sessão para a tarefa no como administrador.

2. No lado esquerdo do menu, clique em **configurações** e navegue até à **administrador** e, em seguida, clique em **início de sessão único gerir**.

    ![Configuração da tarefa no](./media/tas-tutorial/configure01.png)

3. Sobre o **início de sessão único gerir** página, execute os seguintes passos:

    ![Configuração da tarefa no](./media/tas-tutorial/configure02.png)

    a. Na **nome** caixa de texto, escreva o nome do ambiente.
    
    b. Selecione **SAML2** como **tipo de autenticação**.

    c. Na **introduza o URL** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    d. No bloco de notas, abra o certificado com codificação base 64 que transferiu a partir do portal do Azure, copie o seu conteúdo e, em seguida, cole-o para o **certificação introduza** caixa.

    e. Na **introduza o novo IP** caixa de texto, escreva o endereço IP.

    >[!NOTE]
    > Contacte [equipa de suporte de tarefa no](mailto:support@combtas.com) para obter o endereço IP.

    f. Copiar o **início de sessão único** url e cole-o no **identificador (ID de entidade)** e **iniciar sessão no URL** caixa de texto de **configuração básica de SAML** no Portal do Azure. Tenha em atenção que o url é sensível às maiúsculas e tem de terminar com uma barra (/).

    g. Cópia a **serviço de asserção** url na configuração da página e colá-lo no **URL de resposta** caixa de texto de **configuração básica de SAML** no portal do Azure.

    h. Clique em **linha de inserir SSO**.

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

Nesta secção, vai ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para TAS.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **tarefa no**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **tarefa no**.

    ![A ligação da tarefa na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-tas-test-user"></a>Criar utilizador de teste de tarefa no

Nesta secção, vai criar um usuário chamado Eduarda Almeida no TAS. Trabalhar com [equipa de suporte de tarefa no](mailto:support@combtas.com) para adicionar os utilizadores na plataforma da tarefa no. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico da tarefa no painel de acesso, deve ser automaticamente conectado para a tarefa para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

