---
title: 'Tutorial: Integração do Active Directory do Azure com o SAML SSO para Jira pela resolução GmbH | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o SAML SSO para Jira pela resolução GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67be6251b1500ff85b833bbb7c7348a76045496f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092072"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Integração do Active Directory do Azure com o SAML SSO para Jira pela resolução GmbH

Neste tutorial, saiba como configurar o SSO SAML para Jira pela resolução GmbH com o Azure Active Directory (Azure AD).
Integrar o SSO SAML para Jira pela resolução GmbH com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que pode iniciar sessão no Jira com o plug-in do SAML SSO resolução GmbH.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada ao Jira com as suas contas do Azure AD utilizando SAML SSO para Jira pela resolução GmbH (início de sessão único).
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD e SAML SSO para Jira por resolução GmbH, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO para Jira pela resolução GmbH logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* SAML SSO para Jira pela resolução suporta GmbH **SP** e **IDP** iniciada SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Adicionar uma aplicação empresarial para início de sessão único

Para configurar o início de sessão único no Azure AD, terá de adicionar um novo aplicativo empresarial. Na galeria, existe uma aplicação pré-configurada configuração predefinida para isso, **SAML SSO para Jira pela resolução GmbH**.

**Adicionar SAML SSO para Jira através da resolução GmbH partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais**e, em seguida, clique em **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique a **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SAML SSO para Jira pela resolução GmbH**, selecione **SAML SSO para Jira pela resolução GmbH** no painel de resultados e, em seguida, clique o **Add** botão para adicionar o aplicação. Também pode alterar o nome da aplicação empresarial.

     ![SAML SSO para Jira pela resolução GmbH, na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Configurar e testar o início de sessão único com o plug-in do SAML SSO e o Azure AD

Nesta secção, irá testar e configurar o início de sessão único para Jira para um utilizador do Azure AD. Esta operação será efetuada para um utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SAML SSO para Jira pela resolução GmbH deve ser estabelecido.

Para configurar e testar o início de sessão único, terá de concluir os seguintes passos:

1. **[Configurar a aplicação de enterprise do Azure AD para início de sessão único](#configure-the-azure-ad-enterprise-application-for-single-sign-on)**  -configurar o aplicativo de empresarial do Azure AD para o início de sessão único
2. **[Configurar o plug-in do SAML SSO da sua instância de Jira](#configure-the-saml-sso-plugin-of-your-jira-instance)**  -configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  -criar um utilizador de teste no Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  -ativar o utilizador de teste utilizar o único início de sessão no lado do Azure.
1. **[Criar o utilizador de teste no Jira](#create-the-test-user-also-in-jira)**  -criar um utilizador de teste equivalente no Jira para o utilizador de teste do Azure AD.
1. **[Testar início de sessão único](#test-single-sign-on)**  -verificar se a configuração funciona.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Configurar a aplicação de enterprise do Azure AD para início de sessão único

Nesta secção, vai configurar o início de sessão único no portal do Azure.

Para configurar o início de sessão único com o SAML SSO para Jira pela resolução GmbH, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), acabou de criar na **SAML SSO para Jira pela resolução GmbH** aplicação empresarial, selecione **início de sessão único** no painel esquerdo.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Para **selecionar um método de início de sessão único**, selecione a **SAML** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Em seguida, clique nas **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, em seguida, execute os seguintes passos:

    ![Informações de início de sessão de único SAML SSO para Jira pela resolução GmbH domínio e URLs](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique em **definir URLs adicionais** e de executar o passo seguinte, se desejar configurar a aplicação no **SP** iniciada pelo modo:

    ![Informações de início de sessão de único SAML SSO para Jira pela resolução GmbH domínio e URLs](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Para o identificador, a URL de resposta e o URL de início de sessão, substitua  **\<server-base-url >** com o URL de base da sua instância do Jira. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure. Se tiver um problema, contacte-nos [equipa de suporte de SAML SSO para Jira pela resolução GmbH cliente](https://www.resolution.de/go/support).

4. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, transfira o **XML de metadados de Federação** e guarde-o para o seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Configurar o plug-in do SAML SSO da sua instância do Jira 

1. Numa janela do browser web diferente, inicie sessão para a sua instância do Jira como um administrador.

2. Paire o rato sobre o ícone de roda dentada no lado direito e clique em **gerir aplicações**.
    
    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon1.png)

3. Se será redirecionado à página de acesso de administrador, introduza o **palavra-passe** e clique nas **confirmar** botão.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon2.png)

4. Jira normalmente redireciona-o para o marketplace da Atlassian. Se não estiver, clique em **encontrar novas aplicações** no painel esquerdo. Procure **SAML único início de sessão (SSO) para JIRA** e clique nas **instalar** botão para instalar o plug-in SAML.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/store.png)

5. A instalação de plug-in será iniciado. Quando tiver terminado, clique nas **fechar** botão.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/store-2.png)

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/store-3.png)

6. Em seguida, clique em **gerir**.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/store-4.png)
    
8. Em seguida, clique em **configurar** para configurar o plug-in apenas instalado.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/store-5.png)

9. Na **configuração de plug-in SAML SingleSignOn** assistente, clique em **adicionar IdP novo** para configurar o Azure AD como um novo provedor de identidade.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon4.png) 

10. Sobre o **escolha o fornecedor de identidade de SAML** página, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon5a.png)
 
    a. Definir **do Azure AD** como o tipo de IdP.
    
    b. Adicionar a **nome** do fornecedor de identidade (por exemplo, do Azure AD).
    
    c. Adicionar um (opcional) **Descrição** do fornecedor de identidade (por exemplo, do Azure AD).
    
    d. Clique em **Seguinte**.
    
11. Sobre o **configuração do fornecedor de identidade** página, clique em **próxima**.
 
    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon5b.png)

12. No **importação SAML IdP metadados** página, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon5c.png)

    a. Clique nas **selecionar ficheiro de XML de metadados** botão e escolha o **XML de metadados de Federação** ficheiro transferido antes.

    b. Clique nas **importação** botão.
     
    c. Aguarde um momento até que a importação for concluída com êxito.  
     
    d. Clique nas **seguinte** botão.
    
13. No **atributo de ID de utilizador e a transformação** página, clique nas **próxima** botão.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon5d.png)
    
14. Na **criação do utilizador e de atualização** página, clique em **Save & seguinte** para guardar as definições.
    
    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon6a.png)
    
15. Sobre o **testar suas configurações** página, clique em **ignorar teste e configurar manualmente** para ignorar o teste de utilizador por agora. Este será executada na próxima seção e requer algumas definições no portal do Azure.
    
    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon6b.png)
    
16. Clique em **OK** para ignorar o aviso.
    
    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida. Com o usuário, irá testar início de sessão único.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Escolher **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Na **propriedades de utilizador**, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **Eduarda Almeida**.
  
    b. Na **nome de utilizador** , insira <b> BrittaSimon@contoso.com </b>.

    c. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, é possível adicionar Eduarda Almeida para o aplicativo empresarial, que permite-lhe utilizar o início de sessão único.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**. 

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, procure o aplicativo empresarial que criou no início deste tutorial. Se estiver a seguir os passos do tutorial, é chamado **SAML SSO para Jira pela resolução GmbH**. Se já deu para ele um nome de outro, procure por esse nome.

    ![O SSO SAML para Jira pela ligação de GmbH resolução na lista de aplicações](common/all-applications.png)

3. No painel esquerdo, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na asserção de SAML, em seguida, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã .

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-the-test-user-also-in-jira"></a>Criar o utilizador de teste também no Jira

Permitir que os utilizadores do Azure AD para iniciar sessão SAML SSO para Jira resolução GmbH, eles têm de ser aprovisionados no SAML SSO para Jira pela resolução GmbH. Para o caso deste tutorial, terá de fazer o aprovisionamento manualmente. No entanto, há também outros modelos de aprovisionamento disponíveis para o plug-in do SAML SSO por resolução, por exemplo **Just In Time** aprovisionamento. Consulte sua documentação em [SAML SSO pela resolução GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Se tiver uma pergunta sobre isso, contacte o suporte ao [suporte de resolução](https://www.resolution.de/go/support).

**Para aprovisionar manualmente uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Jira instância como um administrador.

2. Coloque o cursor sobre o ícone de roda dentada e selecione **gestão de utilizadores**.

   ![Adicionar o funcionário](./media/samlssojira-tutorial/user1.png)

3. Se será redirecionado para a página de acesso de administrador, em seguida, introduza o **palavra-passe** e clique nas **confirmar** botão.

    ![Adicionar o funcionário](./media/samlssojira-tutorial/user2.png) 

4. Sob o **gestão de utilizadores** secção, clique em **criar utilizador**.

    ![Adicionar o funcionário](./media/samlssojira-tutorial/user3-new.png) 

5. Sobre o **"Criar novo usuário"** caixa de diálogo página, execute os seguintes passos. Tem de criar o utilizador exatamente como no Azure AD:

    ![Adicionar o funcionário](./media/samlssojira-tutorial/user4-new.png) 

    a. Na **endereço de E-Mail** caixa de texto, escreva o endereço de e-mail do utilizador: <b> BrittaSimon@contoso.com </b>.

    b. Na **FullName** caixa de texto, nome completo do tipo do utilizador: **Britta Simon**.

    c. Na **nome de utilizador** caixa de texto, escreva o endereço de e-mail do utilizador: <b> BrittaSimon@contoso.com </b>. 

    d. Na **palavra-passe** caixa de texto, introduza a palavra-passe do utilizador.

    e. Clique em **criar utilizador** para concluir a criação do utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o SAML SSO para Jira por mosaico de GmbH resolução no painel de acesso, deve ser automaticamente sessão iniciada no SAML SSO para Jira por GmbH de resolução para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Também pode testar início de sessão único, se navegar até [https://\<server-base-url >/plug-ins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Substitute  **\<server-base-url >** com o URL de base da sua instância do Jira.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Ativar o redirecionamento de início de sessão único para Jira

Conforme observado na seção antes, isso significa que existem atualmente duas formas para acionar o início de sessão único. Utilizando o **portal do Azure** ou a utilizar **uma ligação à sua instância do Jira**. O plug-in do SAML SSO pela resolução GmbH também permite-lhe acionar o início de sessão único por simplesmente **aceder a qualquer URL que aponta para a sua instância do Jira**.

Em essência, todos os utilizadores a aceder ao Jira, serão redirecionados para o início de sessão único após a ativação de uma opção no plug-in.

Para ativar o redirecionamento SSO, efetue o seguinte procedimento **sua instância do Jira**:

1. Aceda à página de configuração do plug-in do SAML SSO no Jira.
1. Clique em **redirecionamento** no painel esquerdo.
![](./media/samlssojira-tutorial/ssore1.png)

1. Escala **ativar o redirecionamento SSO**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Prima a **guardar definições** botão no canto superior direito.

Depois de ativar a opção, ainda consegue contactá-se de linha de comandos de nome de utilizador/palavra-passe a **ativar o nosso** opção está selecionada, navegando até [https://\<server-base-url > /login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Como sempre, substitua  **\<server-base-url >** pelo seu URL de base.


## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

