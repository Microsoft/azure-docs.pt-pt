---
title: 'Tutorial: Integração do Active Directory do Azure com etouches | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e etouches.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72854ab0de93b101f2462894f2ca07be4c56b9a5
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731436"
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Tutorial: Integração do Active Directory do Azure com etouches

Neste tutorial, saiba como integrar etouches com o Azure Active Directory (Azure AD).
Integrar etouches no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao etouches.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para etouches (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com etouches, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* etouches único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* suporta etouches **SP** iniciada SSO

## <a name="adding-etouches-from-the-gallery"></a>Adicionando etouches da Galeria

Para configurar a integração do etouches com o Azure AD, terá de adicionar etouches a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar etouches a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **etouches**, selecione **etouches** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![etouches na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com etouches com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no etouches deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com etouches, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar etouches início de sessão único](#configure-etouches-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste etouches](#create-etouches-test-user)**  - para ter um equivalente da Eduarda Almeida na etouches que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com etouches, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **etouches** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **configuração de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![etouches URLs de domínio e única informações de início de sessão](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Estes valores não são reais. Atualize o valor com o início de sessão real URL e o identificador, o que é explicado mais tarde no tutorial.
    > 

5. Seu aplicativo etouches espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **editar** ícone para adicionar os atributos.

    ![imagem](common/edit-attribute.png)

6. Além dos acima, o aplicativo etouches espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem|
    | ------------------- | -------------------- |
    | E-mail | user.mail | 

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **configuração de início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

8. Sobre o **configurar etouches** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-etouches-single-sign-on"></a>Configurar etouches início de sessão único

1. Para obter SSO configurado para a sua aplicação, execute os seguintes passos no aplicativo etouches: 

    ![configuração de etouches](./media/etouches-tutorial/tutorial_etouches_06.png) 

    a. Inicie sessão no **etouches** aplicativo usando os direitos de administrador.
   
    b. Vá para o **SAML** configuração.

    c. Na **definições gerais** secção, abra o certificado transferido a partir do portal do Azure no bloco de notas, copie o conteúdo e, em seguida, cole-o na caixa de texto de metadados do IDP. 

    d. Clique nas **guardar e mantenha-se** botão.
  
    e. Clique nas **metadados de atualização** botão na secção de metadados de SAML. 

    f. Isso abre a página e executa o SSO. Depois do SSO está a funcionar, em seguida, pode configurar o nome de utilizador.

    g. No campo de nome de utilizador, selecione o **emailaddress** conforme mostrado na imagem abaixo. 

    h. Cópia a **ID de entidade de SP** valor e cole-o no **identificador** caixa de texto, que está a ser **configuração básica de SAML** secção no portal do Azure.

    i. Cópia a **URL de SSO / ACS** valor e cole-o no **URL de início de sessão** caixa de texto, que está a ser **configuração básica de SAML** secção no portal do Azure.
   
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a etouches.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **etouches**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **etouches**.

    ![A ligação de etouches na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador na lista, em seguida, clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-etouches-test-user"></a>Criar utilizador de teste etouches

Nesta secção, vai criar um usuário chamado Eduarda Almeida no etouches. Trabalhar com [equipa de suporte de etouches cliente](https://www.etouches.com/event-software/support/customer-support/) para adicionar os utilizadores na plataforma etouches.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico etouches no painel de acesso, deve ser automaticamente sessão iniciada no etouches para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

