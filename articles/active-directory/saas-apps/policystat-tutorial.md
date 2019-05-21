---
title: 'Tutorial: Integração do Active Directory do Azure com PolicyStat | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 98ab9904f8afc5c74d9f043b0964bc56b49670cc
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65904570"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Integração do Active Directory do Azure com PolicyStat

Neste tutorial, saiba como integrar PolicyStat com o Azure Active Directory (Azure AD).
Integrar PolicyStat no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao PolicyStat.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para PolicyStat (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com PolicyStat, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* PolicyStat logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta PolicyStat **SP** iniciada SSO

* Suporta PolicyStat **Just In Time** aprovisionamento de utilizadores

## <a name="adding-policystat-from-the-gallery"></a>Adicionando PolicyStat da Galeria

Para configurar a integração do PolicyStat com o Azure AD, terá de adicionar PolicyStat a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar PolicyStat a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **PolicyStat**, selecione **PolicyStat** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![PolicyStat na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com PolicyStat com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no PolicyStat deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com PolicyStat, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar PolicyStat Single Sign-On](#configure-policystat-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste PolicyStat](#create-policystat-test-user)**  - para ter um equivalente da Eduarda Almeida na PolicyStat que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com PolicyStat, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **PolicyStat** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![PolicyStat domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.policystat.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente PolicyStat](http://www.policystat.com/support/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

5. Seu aplicativo PolicyStat espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. Além dos acima, o aplicativo PolicyStat espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.
    
    ![image](common/new-save-attribute.png)

    ![image](./media/policystat-tutorial/attribute01.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **transformação**.

    e. Partir do **transformação** lista, digite o valor de atributo apresentado para essa linha.
    
    f. Partir do **parâmetro 1** lista, digite o valor de atributo apresentado para essa linha.

    g. Clique em **Guardar**.

7. Sobre o **configurar PolicyStat** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-policystat-single-sign-on"></a>Configurar PolicyStat Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa PolicyStat como um administrador.

2. Clique nas **administrador** separador e, em seguida, clique em **configuração de início de sessão único** no painel de navegação à esquerda.
   
    ![Menu de administrador](./media/policystat-tutorial/ic808633.png "Menu de administrador")

3. Na **programa de configuração** secção, selecione **integração de início de sessão único ativar**.
   
    ![A única configuração de início de sessão](./media/policystat-tutorial/ic808634.png "única configuração de início de sessão")

4. Clique em **configurar atributos**e, em seguida, no **configurar atributos** secção, execute os seguintes passos:
   
    ![A única configuração de início de sessão](./media/policystat-tutorial/ic808635.png "única configuração de início de sessão")
   
    a. Na **atributo de nome de utilizador** caixa de texto, tipo **uid**.

    b. Na **atributo de nome próprio** caixa de texto, tipo **firstname** do utilizador **Eduarda**.

    c. Na **último nome do atributo** caixa de texto, tipo **lastname** do utilizador **Simon**.

    d. Na **atributo de correio eletrónico** caixa de texto, tipo **emailaddress** do utilizador `BrittaSimon@contoso.com`.

    e. Clique em **guardar alterações**.

5. Clique em **Your IDP metadados**e, em seguida, no **Your IDP metadados** secção, execute os seguintes passos:
   
    ![A única configuração de início de sessão](./media/policystat-tutorial/ic808636.png "única configuração de início de sessão")
   
    a. A abrir o ficheiro de metadados baixado, copie o conteúdo e, em seguida, cole-o para o **seus metadados do fornecedor de identidade** caixa de texto.

    b. Clique em **guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para PolicyStat.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **PolicyStat**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **PolicyStat**.

    ![A ligação de PolicyStat na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-policystat-test-user"></a>Criar utilizador de teste PolicyStat

Nesta secção, um usuário chamado Eduarda Almeida é criado na PolicyStat. PolicyStat suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no PolicyStat, é criado um novo após a autenticação.

>[!NOTE]
>Pode utilizar quaisquer outras PolicyStat utilizador conta criação ferramentas ou APIs fornecidas pelo PolicyStat para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico PolicyStat no painel de acesso, deve ser automaticamente sessão iniciada no PolicyStat para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

