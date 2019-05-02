---
title: 'Tutorial: Integração do Active Directory do Azure com o Zscaler ZSCloud | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Zscaler ZSCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 35886a65020d7cc5a77f9f413d2d65637cb18382
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64719317"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Tutorial: Integração do Active Directory do Azure com o Zscaler ZSCloud

Neste tutorial, saiba como integrar o Zscaler ZSCloud com o Azure Active Directory (Azure AD).
Integrar o Zscaler ZSCloud no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Zscaler ZSCloud.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Zscaler ZSCloud (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler ZSCloud, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Zscaler ZSCloud logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Zscaler ZSCloud **SP** iniciada SSO

* Suporta Zscaler ZSCloud **Just In Time** aprovisionamento de utilizadores

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Adicionando Zscaler ZSCloud da Galeria

Para configurar a integração do Zscaler ZSCloud com o Azure AD, terá de adicionar Zscaler ZSCloud a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Zscaler ZSCloud a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Zscaler ZSCloud**, selecione **Zscaler ZSCloud** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Zscaler ZSCloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Zscaler ZSCloud com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zscaler ZSCloud deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Zscaler ZSCloud, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Zscaler ZSCloud início de sessão único](#configure-zscaler-zscloud-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Zscaler ZSCloud](#create-zscaler-zscloud-test-user)**  - para ter um equivalente da Eduarda Almeida na Zscaler ZSCloud que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Zscaler ZSCloud, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Zscaler ZSCloud** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Zscaler ZSCloud domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para início de sessão na sua aplicação ZScaler ZSCloud.

    > [!NOTE]
    > Terá de atualizar o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente do Zscaler ZSCloud](https://help.zscaler.com/) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Seu aplicativo Zscaler ZSCloud espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. Além dos acima, o aplicativo Zscaler ZSCloud espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:
    
    | Name | Atributo de origem |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.
    
    f. Clique em **Guardar**.

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) saber como configurar a função no Azure AD

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar Zscaler ZSCloud** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-zscaler-zscloud-single-sign-on"></a>Configurar Zscaler ZSCloud início de sessão único

1. Para automatizar a configuração no Zscaler ZSCloud, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **configuração Zscaler ZSCloud** irá direcioná-lo para o aplicativo Zscaler ZSCloud. A partir daí, forneça as credenciais de administrador a iniciar sessão em Zscaler ZSCloud. A extensão do browser irá configurar o aplicativo para e automatizar passos 3 a 6 automaticamente.

    ![Configurar o sso](common/setup-sso.png)

3. Se desejar configurar manualmente o Zscaler ZSCloud, abra uma nova janela de browser e inicie sessão no site da sua empresa Zscaler ZSCloud como administrador e execute os seguintes passos:

4. Aceda a **administração > autenticação > definições de autenticação** e execute os seguintes passos:
   
    ![Administração](./media/zscaler-zscloud-tutorial/ic800206.png "administração")

    a. Em tipo de autenticação, selecione **SAML**.

    b. Clique em **configurar SAML**.

5. Sobre o **SAML editar** janela, execute os seguintes passos: e clique em Guardar.  
            
    ![Gerir utilizadores e de autenticação](./media/zscaler-zscloud-tutorial/ic800208.png "gerir utilizadores e de autenticação")
    
    a. Na **URL do Portal de SAML** caixa de texto, colar a **URL de início de sessão** que copiou do portal do Azure.

    b. Na **atributo de nome de início de sessão** caixa de texto, introduza **NameID**.

    c. Clique em **carregue**para carregar o certificado de assinatura de SAML do Azure que transferiu a partir do portal do Azure no **certificado de SSL público**.

    d. Ativar/desativar a **ativar o aprovisionamento automático do SAML**.

    e. Na **atributo de nome de exibição do usuário** caixa de texto, introduza **displayName** se pretender ativar SAML aprovisionamento automático para atributos de displayName.

    f. Na **atributo de nome de grupo** caixa de texto, introduza **memberOf** se pretender ativar SAML aprovisionamento automático para atributos de memberOf.

    g. Na **atributo de nome de departamento** Enter **departamento** se pretender ativar SAML aprovisionamento automático para atributos de departamento.

    h. Clique em **Guardar**.

6. Sobre o **configurar a autenticação de utilizador** caixa de diálogo página, execute os seguintes passos:

    ![Administração](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Paire o rato sobre o **ativação** menu junto à parte inferior esquerda.

    b. Clique em **ativar**.

## <a name="configuring-proxy-settings"></a>Configurar definições de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para configurar as definições de proxy no Internet Explorer

1. Inicie **do Internet Explorer**.

2. Selecione **opções da Internet** partir a **ferramentas** menu para abrir o **opções da Internet** caixa de diálogo.   
    
     ![Opções da Internet](./media/zscaler-zscloud-tutorial/ic769492.png "opções da Internet")

3. Clique nas **ligações** separador.   
  
     ![Ligações](./media/zscaler-zscloud-tutorial/ic769493.png "ligações")

4. Clique em **definições de LAN** para abrir o **definições de LAN** caixa de diálogo.

5. Na secção de servidor Proxy, execute os seguintes passos:   
   
    ![Servidor proxy](./media/zscaler-zscloud-tutorial/ic769494.png "servidor Proxy")

    a. Selecione **utilizar um servidor proxy para a rede local**.

    b. Na caixa de texto endereço, escreva **gateway. Zscaler ZSCloud.net**.

    c. Na caixa de texto de porta, escreva **80**.

    d. Selecione **Ignorar servidor proxy para endereços locais**.

    e. Clique em **OK** para fechar a **definições de rede Local (LAN)** caixa de diálogo.

6. Clique em **OK** para fechar a **opções da Internet** caixa de diálogo.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Zscaler ZSCloud.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Zscaler ZSCloud**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler ZSCloud**.

    ![A ligação de Zscaler ZSCloud na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione o utilizador, como **Eduarda Almeida** na lista, em seguida, clique o **selecionar** na parte inferior do ecrã.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. Do **selecionar função** caixa de diálogo selecione a função de utilizador adequada na lista, em seguida, clique no **selecione** na parte inferior do ecrã.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

    ![image](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >Função de acesso predefinida não é suportada porque tal irá interromper o aprovisionamento, por isso, a função predefinida não pode ser selecionada ao atribuir utilizador.

### <a name="create-zscaler-zscloud-test-user"></a>Criar utilizador de teste Zscaler ZSCloud

Nesta secção, um usuário chamado Eduarda Almeida é criado na Zscaler ZSCloud. Zscaler ZSCloud suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Zscaler ZSCloud, é criado um novo após a autenticação.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte do Zscaler ZSCloud](https://help.zscaler.com/).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do Zscaler ZSCloud no painel de acesso, deve ser automaticamente sessão iniciada no Zscaler ZSCloud para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

