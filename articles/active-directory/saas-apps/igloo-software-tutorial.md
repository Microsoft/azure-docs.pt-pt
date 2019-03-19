---
title: 'Tutorial: Integração do Active Directory do Azure com o Software de Iglu | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Software de Iglu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: d5c814fbf180ddd1cd3b447533a89cc577151d6c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58093791"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: Integração do Active Directory do Azure com o Software de Iglu

Neste tutorial, saiba como integrar o Software de Iglu com o Azure Active Directory (Azure AD).
Integrar o Software de Iglu no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Software Iglu.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Software de Iglu (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Software de Iglu, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Iglu Software logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a Software iglu **SP** iniciada SSO
* Oferece suporte a Software iglu **Just In Time** aprovisionamento de utilizadores

## <a name="adding-igloo-software-from-the-gallery"></a>Adicionar Iglu Software a partir da Galeria

Para configurar a integração de Software Iglu com o Azure AD, terá de adicionar Iglu Software a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Iglu Software a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Software Iglu**, selecione **Software Iglu** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Software de iglu na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Software de Iglu com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Iglu Software deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Software de Iglu, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Iglu Software início de sessão único](#configure-igloo-software-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de Software Iglu](#create-igloo-software-test-user)**  - para ter um equivalente da Eduarda Almeida na Iglu Software que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Software de Iglu, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Software Iglu** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Iglu Software domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.igloocommmunities.com`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão: `https://<company name>.igloocommmunities.com/saml.digest`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente de Software Iglu](https://www.igloosoftware.com/services/support) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Software Iglu** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-igloo-software-single-sign-on"></a>Configurar Iglu Software início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Iglu Software como um administrador.

2. Vá para o **painel de controlo**.

     ![Painel de controlo](./media/igloo-software-tutorial/ic799949.png "painel de controlo")

3. Na **associação** separador, clique em **definições de início de sessão**.

    ![Inscrever-se nas definições](./media/igloo-software-tutorial/ic783968.png "inscrever-se nas definições")

4. Na secção de configuração de SAML, clique em **configurar a autenticação SAML**.

    ![Configuração de SAML](./media/igloo-software-tutorial/ic783969.png "configuração de SAML")

5. Na **configuração geral** secção, execute os seguintes passos:

    ![Configuração geral](./media/igloo-software-tutorial/ic783970.png "configuração geral")

    a. Na **nome da ligação** caixa de texto, escreva um nome personalizado para a sua configuração.

    b. Na **URL de início de sessão do IdP** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Na **URL de fim de sessão do IdP** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    d. Selecione **resposta de fim de sessão e o tipo de pedido de HTTP** como **POST**.

    e. Abra sua **base 64** certificado codificado no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado público** caixa de texto.

6. Na **resposta e a configuração da autenticação**, execute os seguintes passos:

    ![Resposta e a configuração de autenticação](./media/igloo-software-tutorial/IC783971.png "resposta e a configuração de autenticação")
  
    a. Como **fornecedor de identidade**, selecione **Microsoft ADFS**.

    b. Como **tipo de identificador**, selecione **endereço de E-Mail**. 

    c. Na **atributo de correio eletrónico** caixa de texto, tipo **emailaddress**.

    d. Na **atributo de nome próprio** caixa de texto, tipo **givenname**.

    e. Na **último nome do atributo** caixa de texto, tipo **Apelido**.

7. Execute os seguintes passos para concluir a configuração:

    ![Criação do utilizador no início de sessão](./media/igloo-software-tutorial/IC783972.png "criação do utilizador no início de sessão") 

    a. Como **criação do utilizador no início de sessão**, selecione **criar um novo utilizador no seu site quando iniciam sessão**.

    b. Como **iniciar sessão nas definições**, selecione **botão de SAML de utilização no ecrã de "Iniciar sessão"**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Software Iglu.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Iglu Software**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Iglu Software**.

    ![A ligação de Iglu Software na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-igloo-software-test-user"></a>Criar utilizador de teste de Software Iglu

Não existe nenhum item de ação para configurar o aprovisionamento do utilizador Iglu Software.  

Quando um utilizador atribuído tenta iniciar sessão no Software Iglu usando o painel de acesso, o Software de Iglu verifica se o usuário existe.  Se nenhuma conta de utilizador disponível ainda existe, é criado automaticamente pelo Iglu Software.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Software Iglu no painel de acesso, deve ser automaticamente conectado para o Software de Iglu para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)