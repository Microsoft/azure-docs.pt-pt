---
title: 'Tutorial: Integração do Active Directory do Azure com EBSCO | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2753daf225016d3bd8e07383193a1260b40a36d5
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564980"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Tutorial: Integração do Active Directory do Azure com EBSCO

Neste tutorial, saiba como integrar EBSCO com o Azure Active Directory (Azure AD).
Integrar EBSCO no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao EBSCO.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para EBSCO (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com EBSCO, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* EBSCO logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta EBSCO **SP** e **IDP** iniciada SSO

* Suporta EBSCO **Just In Time** aprovisionamento de utilizadores

## <a name="adding-ebsco-from-the-gallery"></a>Adicionando EBSCO da Galeria

Para configurar a integração do EBSCO com o Azure AD, terá de adicionar EBSCO a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar EBSCO a partir da galeria, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **EBSCO**, selecione **EBSCO** a partir do painel de resultados, em seguida, clique nas **Add** botão para adicionar a aplicação.

     ![EBSCO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com EBSCO com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no EBSCO deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com EBSCO, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar EBSCO Single Sign-On](#configure-ebsco-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste EBSCO](#create-ebsco-test-user)**  - para ter um equivalente da Eduarda Almeida na EBSCO que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com EBSCO, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **EBSCO** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, clique no **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar o passo seguinte:

    ![EBSCO domínio e URLs únicas início de sessão em informações](common/idp-identifier.png)

    Na **identificador** caixa de texto, escreva um URL:  `pingsso.ebscohost.com`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![image](common/both-preintegrated-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > O valor de URL de início de sessão não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente EBSCO](mailto:sso@ebsco.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

    o **elementos exclusivos:**  

    s **Custid** = o ID de cliente EBSCO Enter exclusivo 

    s **perfil** = clientes podem personalizar a ligação para direcionar os utilizadores a um perfil específico (consoante o que eles comprar de EBSCO). Eles podem digitar um ID de perfil específico. Os IDs principais são eds (serviço de deteção de EBSCO) e ehost (EBSOCOhost bases de dados). Instruções para a mesma recebem [aqui](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

6. Aplicação de EBSCO espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

     > [!Note]
    > O **name** atributo é obrigatório e está mapeado com **o valor do identificador de nome** no aplicativo EBSCO. Este valor é adicionado por predefinição pelo que não precisa adicionar isso manualmente.

7. Além dos acima, o aplicativo EBSCO espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir: 

    | Name | Atributo de origem|
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Sobre o **configurar EBSCO** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-ebsco-single-sign-on"></a>Configurar EBSCO início de sessão único

Para configurar o início de sessão único num **EBSCO** lado, terá de enviar o transferido **XML de metadados** e adequadas copiados URLs a partir do portal do Azure para [equipa de suporte de EBSCO](mailto:sso@ebsco.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para EBSCO.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **EBSCO**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **EBSCO**.

    ![A ligação EBSCO na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-ebsco-test-user"></a>Criar utilizador de teste EBSCO

No caso de EBSCO, aprovisionamento de utilizadores é automática.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

Do Azure AD passa os dados necessários para a aplicação de EBSCO. Aprovisionamento de utilizadores do EBSCO pode ser automática ou adquirir uma forma de uso individual. Depende se o cliente possui muita pré-existente EBSCOhost contas com configurações pessoais guardadas. O mesmo podem ser discutido com o [equipa de suporte de EBSCO](mailto:sso@ebsco.com) durante a implementação. De qualquer forma, o cliente não tem que criar as contas de EBSCOhost antes de testar.

   >[!Note]
   >Pode automatizar EBSCOhost aprovisionamento/personalização de utilizador. Contacte [equipa de suporte de EBSCO](mailto:sso@ebsco.com) sobre Just-In-Time aprovisionamento de utilizadores. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

1. Quando clica no mosaico EBSCO no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo EBSCO.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

2. Assim que faça logon para a aplicação, clique nas **iniciar sessão** botão no canto superior direito.

    ![O EBSCO início de sessão na lista de aplicações](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Receberá uma única linha de comandos para emparelhar o início de sessão institucional/SAML com uma **ligar a sua conta existente do MyEBSCOhost à sua conta da instituição agora** ou **criar uma nova conta de MyEBSCOhost e ligá-lo para seu conta da instituição**. A conta é utilizada para personalização no aplicativo EBSCOhost. Selecione a opção **criar uma nova conta** e verá que o formulário para personalização é previamente foi concluído com os valores da resposta saml, conforme mostrado na captura de ecrã abaixo. Clique em **'Continuar'** para guardar esta seleção.
    
     ![O utilizador EBSCO na lista de aplicações](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Depois de concluir a configuração acima, limpe a cache de cookies/e início de sessão novamente. Não precisará manualmente iniciar sessão novamente e as definições de personalização podem ser memorizadas

## <a name="additional-sesources"></a>Sesources adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

