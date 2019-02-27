---
title: 'Tutorial: Integração do Active Directory do Azure com o NetSuite | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9c1b7b60069b4ea59828bc552c4dc1e6da6b001
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869059"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Tutorial: Integração do Active Directory do Azure com o NetSuite

Neste tutorial, saiba como integrar o NetSuite com o Azure Active Directory (Azure AD).
Integrar o NetSuite no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao NetSuite.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o NetSuite (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o NetSuite, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* O NetSuite logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* O NetSuite suporta **IDP** iniciada SSO
* O NetSuite suporta **Just In Time** aprovisionamento de utilizadores
* O NetSuite suporta [automatizada de aprovisionamento de utilizador](NetSuite-provisioning-tutorial.md)

## <a name="adding-netsuite-from-the-gallery"></a>Adicionando o NetSuite da Galeria

Para configurar a integração entre o NetSuite com o Azure AD, terá de adicionar o NetSuite a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o NetSuite a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **NetSuite**, selecione **NetSuite** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![NetSuite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o NetSuite com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no NetSuite deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o NetSuite, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o NetSuite Single Sign-On](#configure-netsuite-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste NetSuite](#create-netsuite-test-user)**  - para ter um equivalente da Eduarda Almeida na NetSuite que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o NetSuite, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **NetSuite** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![O NetSuite domínio e URLs únicas início de sessão em informações](common/idp-reply.png)

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de resposta real. Contacte [equipa de suporte de cliente NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. O NetSuite aplicativo espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Name | Atributo de origem | 
    | ---------------| --------------- |
    | conta  | `account id` |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

    >[!NOTE]
    >O valor do atributo de conta não é real. Atualizar este valor, o que é explicado mais tarde no tutorial.

4. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **metadados XML**entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar o NetSuite** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-netsuite-single-sign-on"></a>Configurar o NetSuite Single Sign-On

1. Abra um novo separador no seu browser e inicie sessão no site da sua empresa NetSuite como administrador.

2. Na barra de ferramentas na parte superior da página, clique em **programa de configuração**, em seguida, navegue até à **empresa** e clique em **ativar funcionalidades**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na barra de ferramentas no meio da página, clique em **SuiteCloud**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Sob **Gerir autenticação** secção, selecione **-Diante de início de sessão único SAML** para ativar a opção de início de sessão único SAML-no NetSuite.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na barra de ferramentas na parte superior da página, clique em **configuração**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

6. Partir do **tarefas de configuração** , clique em **integração**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-integration.png)

7. Na **GERIR autenticação** secção, clique em **o início de sessão único SAML**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-saml.png)

8. Sobre o **configuração de SAML** página, em **NetSuite configuração** secção execute os seguintes passos:

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selecione **método de autenticação primária**.

    b. Para o campo rotulado **METADADOS de fornecedor de identidade SAMLV2**, selecione **carregar ficheiro de METADADOS de IDP**. Em seguida, clique em **procurar** para carregar o ficheiro de metadados que transferiu a partir do portal do Azure.

    c. Clique em **Submit** (Submeter).

9. No NetSuite, clique em **programa de configuração** , em seguida, navegue até à **empresa** e clique em **informações da empresa** no menu de navegação superior.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-com.png)

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-account-id.png)

    b. Na **informações da empresa** página sobre a cópia de coluna da direita a **ID de conta**.

    c. Colar o **ID da conta** que copiou da conta de NetSuite-lo para o **o valor do atributo** campo no Azure AD. 

10. Antes dos utilizadores podem efetuar o início de sessão único para o NetSuite, eles tem primeiro de atribuir as permissões adequadas no NetSuite. Siga as instruções abaixo para atribuir estas permissões.

    a. No menu de navegação superior, clique em **configuração**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

    b. No menu de navegação esquerdo, selecione **os utilizadores/funções**, em seguida, clique em **gerir funções**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Clique em **nova função**.

    d. Escreva um **nome** para a sua nova função.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-new-role.png)

    e. Clique em **Guardar**.

    f. No menu na parte superior, clique em **permissões**. Em seguida, clique em **configuração**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecione **o início de sessão único SAML**e, em seguida, clique em **Add**.

    h. Clique em **Guardar**.

    i. No menu de navegação superior, clique em **programa de configuração**, em seguida, clique em **Gestor de configuração**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

    j. No menu de navegação esquerdo, selecione **os utilizadores/funções**, em seguida, clique em **Manage Users**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecione um utilizador de teste. Em seguida, clique em **edite** e, em seguida, navegue até **acesso** separador.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Na caixa de diálogo Funções, atribua a função adequada que tenha criado.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-add-role.png)

    m. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para o NetSuite.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **NetSuite**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **NetSuite**.

    ![A ligação de NetSuite na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-netsuite-test-user"></a>Criar utilizador de teste NetSuite

Nesta secção, um usuário chamado Eduarda Almeida é criado na NetSuite. O NetSuite suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no NetSuite, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico NetSuite no painel de acesso, deve ser automaticamente sessão iniciada no NetSuite para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar o aprovisionamento do utilizador](NetSuite-provisioning-tutorial.md)

