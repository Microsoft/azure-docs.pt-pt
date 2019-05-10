---
title: 'Tutorial: Integração do Active Directory do Azure com o Zscaler Beta | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Zscaler Beta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f31361dc3d7e24092677f1a78b2c405ae84578ed
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230068"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: Integração do Active Directory do Azure com a versão Beta do Zscaler

Neste tutorial, saiba como integrar o Zscaler Beta com o Azure Active Directory (Azure AD).
Quando integrar o Zscaler Beta com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Zscaler Beta.
* Permitir que os utilizadores ter automaticamente sessão iniciada no Zscaler Beta com as suas contas do Azure AD. Este controlo de acesso é chamado de início de sessão único (SSO).
* Gira as suas contas num local central, utilizando o portal do Azure.

Para obter mais informações sobre o software como uma integração de aplicação de serviço (SaaS) com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler Beta, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Uma subscrição do Zscaler Beta que utiliza o início de sessão único.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Versão Beta do Zscaler suporta SSO iniciado por SP.
* Versão Beta do Zscaler suporta o aprovisionamento de utilizador de just-in-time.

## <a name="add-zscaler-beta-from-the-azure-marketplace"></a>Adicionar Zscaler Beta no Azure Marketplace

Para configurar a integração do Zscaler Beta para o Azure AD, adicione Zscaler Beta do Azure Marketplace à sua lista de aplicações de SaaS geridas.

Para adicionar o Zscaler Beta do Azure Marketplace, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![Botão de Active Directory do Azure](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **nova aplicação** na parte superior da caixa de diálogo.

    ![Botão novo da aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Zscaler Beta**. Selecione **Zscaler Beta** no painel de resultados e, em seguida, selecione **Add**.

     ![Versão Beta do Zscaler na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Zscaler Beta com base no utilizador de teste Eduarda Almeida.
Para o início de sessão único trabalhar, estabelece uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na versão Beta do Zscaler.

Para configurar e testar o Azure AD início de sessão único com o Zscaler Beta, conclua os seguintes blocos de construção:

- [Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on) para permitir aos utilizadores utilizar esta funcionalidade.
- [Configurar Zscaler Beta início de sessão único](#configure-zscaler-beta-single-sign-on) para configurar as definições de início de sessão únicas no lado do aplicativo.
- [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user) para testar o Azure AD início de sessão único com Eduarda Almeida.
- [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user) para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
- [Criar um utilizador de teste Zscaler Beta](#create-a-zscaler-beta-test-user) ter um equivalente da Eduarda Almeida na versão Beta do Zscaler que está ligado a representação do Azure AD do utilizador.
- [Testar início de sessão único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Zscaler Beta, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com/), na **Zscaler Beta** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione a **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definido no início de sessão único com o SAML** página, selecione **editar** para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, siga este passo:

    ![Informações de início de sessão de único Zscaler Beta domínio e URLs](common/sp-intiated.png)

    - Na **iniciar sessão no URL** , introduza o URL utilizado pelos seus utilizadores para iniciar sessão na sua aplicação Zscaler Beta.

    > [!NOTE]
    > O valor não é real. Atualize o valor com o real valor de URL de início de sessão. Para obter o valor, entre em contato com o [equipa de suporte de cliente do Zscaler Beta](https://www.zscaler.com/company/contact).

5. O aplicativo Zscaler Beta espera que as asserções SAML num formato específico. Tem de adicionar mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Selecione **edite** para abrir o **atributos de utilizador** caixa de diálogo.

    ![Caixa de diálogo de atributos de utilizador](common/edit-attribute.png)

6. O aplicativo Zscaler Beta espera mais alguns atributos a serem passados na resposta SAML. Na **afirmações de utilizador** secção a **atributos de utilizador** caixa de diálogo, siga estes passos para adicionar o atributo de token SAML, conforme mostrado na seguinte tabela.
    
    | Name | Atributo de origem | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Selecione **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![Caixa de diálogo de afirmações de utilizador](common/new-save-attribute.png)

    ![Gerir a caixa de diálogo de afirmações de utilizador](common/new-attribute-details.png)

    b. Na **nome** , introduza o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** caixa em branco.

    d. Para **origem**, selecione **atributo**.

    e. Partir do **atributo de origem** lista, introduza o valor do atributo apresentado para essa linha.

    f. Selecione **OK**.

    g. Selecione **Guardar**.

    > [!NOTE]
    > Para saber como configurar funções no Azure AD, veja [configurar a declaração de função](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management).

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, selecione **transferir** para transferir o **certificado (Base64)** . Guarde-o no seu computador.

    ![Ligação de transferência do certificado](common/certificatebase64.png)

8. Na **configurar a versão Beta do Zscaler** secção, copie os URLs que precisa para os seus requisitos:

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    - URL de início de sessão
    - Identificador do Azure AD
    - URL de fim de sessão

### <a name="configure-zscaler-beta-single-sign-on"></a>Configurar Zscaler Beta início de sessão único

1. Para automatizar a configuração no Zscaler Beta, instale **segura de aplicações meu início de sessão da extensão de browser** ao selecionar **instalar a extensão**.

    ![Extensão My Apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, selecionando **configurar a versão Beta do Zscaler** direciona-o para o aplicativo Zscaler Beta. A partir daí, forneça as credenciais de administrador para iniciar sessão no Zscaler Beta. A extensão de navegador automaticamente configura o aplicativo para e automatiza os passos 3 a 6.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Para configurar manualmente a versão Beta do Zscaler, abra uma nova janela de browser. Inicie sessão no site da sua empresa Zscaler Beta como administrador e siga estes passos.

4. Aceda a **Administration** > **autenticação** > **definições de autenticação**e siga estes passos.
   
    ![Administração](./media/zscaler-beta-tutorial/ic800206.png "administração")

    a. Sob **tipo de autenticação**, selecione **SAML**.

    b. Selecione **configurar SAML**.

5. Na **SAML editar** janela, siga estes passos: 
            
    ![Gerir utilizadores e de autenticação](./media/zscaler-beta-tutorial/ic800208.png "gerir utilizadores e de autenticação")
    
    a. Na **URL do Portal de SAML** caixa, cole a **URL de início de sessão** que copiou do portal do Azure.

    b. Na **atributo de nome de início de sessão** , introduza **NameID**.

    c. Na **certificado de SSL público** caixa, selecione **carregar** para carregar o certificado de assinatura de SAML do Azure que transferiu a partir do portal do Azure.

    d. Botão de alternar **ativar o aprovisionamento automático do SAML**.

    e. Na **atributo de nome de exibição do usuário** , introduza **displayName** se pretender ativar SAML autoprovisioning para atributos de displayName.

    f. Na **atributo de nome de grupo** , introduza **memberOf** se pretender ativar SAML autoprovisioning para memberOf atributos.

    g. Na **atributo de nome de departamento** , introduza **departamento** se pretender ativar SAML autoprovisioning para atributos de departamento.

    h. Selecione **Guardar**.

6. Sobre o **configurar a autenticação de utilizador** caixa de diálogo página, siga estes passos:

    ![Menu de ativação e o botão Ativar](./media/zscaler-beta-tutorial/ic800207.png)

    a. Paire o rato sobre o **ativação** menu na parte inferior esquerda.

    b. Selecione **ativar**.

## <a name="configure-proxy-settings"></a>Configurar definições de proxy
Para configurar as definições de proxy no Internet Explorer, siga estes passos.

1. Inicie **do Internet Explorer**.

2. Selecione **opções da Internet** partir a **ferramentas** menu para abrir o **opções da Internet** caixa de diálogo. 
    
     ![Caixa de diálogo Opções da Internet](./media/zscaler-beta-tutorial/ic769492.png "opções da Internet")

3. Selecione o **ligações** separador. 
  
     ![Separador ligações](./media/zscaler-beta-tutorial/ic769493.png "ligações")

4. Selecione **definições de LAN** para abrir o **definições de rede Local (LAN)** caixa de diálogo.

5. Na **servidor Proxy** secção, siga estes passos: 
   
    ![Secção de servidor proxy](./media/zscaler-beta-tutorial/ic769494.png "servidor Proxy")

    a. Selecione o **utilizar um servidor proxy para a rede local** caixa de verificação.

    b. Na **endereço** , introduza **gateway. Zscaler Beta.net**.

    c. Na **porta** , introduza **80**.

    d. Selecione o **Ignorar servidor proxy para endereços locais** caixa de verificação.

    e. Selecione **OK** para fechar a **definições de rede Local (LAN)** caixa de diálogo.

6. Selecione **OK** para fechar a **opções da Internet** caixa de diálogo.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

Crie um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory** > **utilizadores** > **todos os utilizadores**.

    ![Os utilizadores e todas as ligações de utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão de novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga estes passos:

    ![Caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** , introduza `brittasimon@yourcompanydomain.extension`. Um exemplo é BrittaSimon@contoso.com.

    c. Selecione o **palavra-passe de Show** caixa de verificação. Anote o valor que é apresentado na **palavra-passe** caixa.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Ative a Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso para o Zscaler Beta.

1. No portal do Azure, selecione **aplicações empresariais** > **todas as aplicações** > **Zscaler Beta**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, introduza e selecione **Zscaler Beta**.

    ![Ligação de Zscaler Beta na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![Ligação de utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**. Na **adicionar atribuição** caixa de diálogo, selecione **utilizadores e grupos**.

    ![Botão Adicionar utilizador](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione o utilizador, como **Eduarda Almeida** da lista. Em seguida, escolha **selecione** na parte inferior do ecrã.

    ![Caixa de diálogo de utilizadores e grupos](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_users.png)

6. Na **selecionar função** diálogo caixa, selecione a função de utilizador adequada na lista. Em seguida, escolha **selecione** na parte inferior do ecrã.

    ![Selecione a caixa de diálogo de função](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_roles.png)

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

    ![Adicionar a caixa de diálogo de atribuição](./media/zscaler-beta-tutorial/tutorial_zscalerbeta_assign.png)

### <a name="create-a-zscaler-beta-test-user"></a>Criar um utilizador de teste Zscaler Beta

Nesta secção, o utilizador Eduarda Almeida é criado na versão Beta do Zscaler. Suporta a versão Beta do Zscaler **aprovisionamento de utilizadores de just-in-time**, que está ativado por predefinição. Não há nada para que possa fazer nesta secção. Se um utilizador já não existe na versão Beta do Zscaler, é criado um novo após a autenticação.

>[!Note]
>Para criar manualmente um utilizador, entre em contato com o [equipa de suporte da versão Beta do Zscaler](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Teste a configuração do Azure AD única início de sessão com o painel de acesso.

Ao selecionar o mosaico do Zscaler Beta no painel de acesso, deve ser automaticamente conectado para a versão Beta de Zscaler para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

