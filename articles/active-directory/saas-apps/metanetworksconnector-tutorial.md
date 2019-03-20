---
title: 'Tutorial: Integração do Active Directory do Azure com o conector de redes de Meta | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o conector de redes de metadados.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: aa0d7828534c96d9bda85e80fb297c848cec6845
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887034"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Tutorial: Integração do Active Directory do Azure com o conector de redes de metadados

Neste tutorial, saiba como integrar o conector de redes de Meta no Azure Active Directory (Azure AD).
Integrar o conector de redes de Meta no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao conector de redes de metadados.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o conector de redes de Meta (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o conector de redes de Meta, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Conector de redes de meta-início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Conector de redes de meta suporta **SP** e **IDP** iniciada SSO
 
* Conector de redes de meta suporta **Just In Time** aprovisionamento de utilizadores

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Adicionar o conector de redes de Meta a partir da Galeria

Para configurar a integração do conector de redes de Meta no Azure AD, terá de adicionar Meta redes conector a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Meta redes conector a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **conector de redes de Meta**, selecione **Meta redes conector** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Meta redes conector na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o conector de redes de metadados com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no conector de redes de Meta deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Meta redes conector, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Meta redes conector início de sessão único](#configure-meta-networks-connector-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do conector de redes de Meta](#create-meta-networks-connector-test-user)**  - para ter um equivalente da Eduarda Almeida no conector de redes de metadados que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o conector de redes de Meta, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Meta redes conector** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Meta redes conector do domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Meta redes conector do domínio e URLs únicas início de sessão em informações](common/both-advanced-urls.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Na **estado de reencaminhamento** caixa de texto, escreva um URL com o seguinte padrão: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, o URL de resposta, e o URL de início de sessão são explicadas mais tarde no tutorial.

6. Conector de redes de meta espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)
    
7. Além disso, anteriormente, a Meta de conector de redes espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:
    
    | Name | Atributo de origem | Espaço de nomes|
    | ---------------| --------------- | -------- |
    | FirstName | user.givenname | |
    | Apelido | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | nome | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefone | user.telephonenumber | |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Sobre o **configurar o conector de redes de Meta** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-meta-networks-connector-single-sign-on"></a>Configurar Meta redes conector início de sessão único

1. Abra um novo separador no seu browser e inicie sessão sua conta de administrador do conector de redes de Meta.
    
    > [!NOTE]
    > Conector de redes de meta é um sistema seguro. Portanto, antes de aceder ao seu portal tem de obter a lista de permissões de endereço IP pública no seu lado. Para obter seu endereço IP público, siga a ligação especificada abaixo [aqui](https://whatismyipaddress.com/). Enviar o seu endereço IP para o [equipa de suporte de cliente de conector de redes de Meta](mailto:support@metanetworks.com) para obter a lista de permissões de endereço IP.
    
2. Aceda a **administrador** e selecione **definições**.
    
    ![Configurar o início de sessão único](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Certifique-se **Registar tráfego de Internet** e **força VPN MFA** estão definidos como desativado.
    
    ![Configurar o início de sessão único](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Aceda a **administrador** e selecione **SAML**.
    
    ![Configurar o início de sessão único](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Execute os seguintes passos no **detalhes** página:
    
    ![Configurar o início de sessão único](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Cópia **URL de SSO** valor e cole-o no **URL de início de sessão** caixa de texto no **Meta redes conector do domínio e URLs** secção.
    
    b. Cópia **URL de destinatário** valor e cole-o no **URL de resposta** caixa de texto no **Meta redes conector do domínio e URLs** secção.
    
    c. Cópia **URI de audiência (ID de entidade de SP)** valor e cole-o no **identificador (ID de entidade)** caixa de texto no **Meta redes conector do domínio e URLs** secção.
    
    d. Ativar o SAML
    
6. Sobre o **gerais** separador. Execute os seguintes passos:

    ![Configurar o início de sessão único](./media/metanetworksconnector-tutorial/configure5.png)

    a. Na **URL fornecedor de identidade única Sign-On**, cole a **URL de início de sessão** valor que copiou do portal do Azure.

    b. Na **emissor do fornecedor de identidade**, cole a **do Azure AD identificador** valor que copiou do portal do Azure.

    c. Abra o certificado transferido a partir do portal do Azure no bloco de notas, cole-o no **certificado X.509** caixa de texto.

    d. Ativar a **Just-in-Time aprovisionamento**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao conector de redes de Meta.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Meta redes conector**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Meta redes conector**.

    ![A ligação de conector de redes de metadados na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-meta-networks-connector-test-user"></a>Criar utilizador de teste do conector de redes de metadados

Nesta secção, um usuário chamado Eduarda Almeida é criado no conector de redes de metadados. Meta redes conector suporta o aprovisionamento de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no conector de redes de Meta, uma nova é criada quando tentar acessar Meta redes conector.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de cliente de conector de redes de Meta](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de conector de redes de Meta no painel de acesso, deve ser automaticamente conectado para o conector de redes de Meta para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

