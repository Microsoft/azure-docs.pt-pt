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
ms.openlocfilehash: d0875e271a34577ed694caae4281df99b6b63411
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901870"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Integração do Active Directory do Azure com o SAML SSO para Jira pela resolução GmbH

Neste tutorial, saiba como integrar o SSO SAML para Jira pela resolução GmbH com o Azure Active Directory (Azure AD).
Integrar o SSO SAML para Jira pela resolução GmbH com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SAML SSO para Jira pela resolução GmbH.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para SAML SSO para Jira pela resolução GmbH (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SAML SSO para Jira pela resolução GmbH, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO para Jira pela resolução GmbH logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* SAML SSO para Jira pela resolução suporta GmbH **SP** e **IDP**iniciada SSO

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Adicionando SAML SSO para Jira pela resolução GmbH da Galeria

Para configurar a integração de SAML SSO para Jira pela resolução GmbH com o Azure AD, terá de adicionar SAML SSO para Jira pela resolução GmbH a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Adicionar SAML SSO para Jira através da resolução GmbH partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SAML SSO para Jira pela resolução GmbH**, selecione **SAML SSO para Jira pela resolução GmbH** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação .

     ![SAML SSO para Jira pela resolução GmbH, na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o SAML SSO para Jira pela resolução GmbH com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SAML SSO para Jira pela resolução GmbH deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o SAML SSO para Jira pela resolução GmbH, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO SAML para Jira através da resolução GmbH Single Sign-On](#configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar SAML SSO para Jira por utilizador de teste de GmbH de resolução](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)**  - para ter um equivalente da Eduarda Almeida no SAML SSO para Jira pela resolução GmbH que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único, com o SAML SSO para Jira, por resolução GmbH, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SAML SSO para Jira pela resolução GmbH** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação na **IDP** modo iniciado execute os seguintes passos:

    ![Informações de início de sessão de único SAML SSO para Jira pela resolução GmbH domínio e URLs](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Informações de início de sessão de único SAML SSO para Jira pela resolução GmbH domínio e URLs](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de SAML SSO para Jira pela resolução GmbH cliente](https://www.resolution.de/go/support) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configure SAML SSO para Jira, resolução GmbH** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on"></a>Configurar o SSO SAML para Jira através da resolução GmbH Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no seu **SAML SSO para Jira pelo portal de administração do resolução GmbH** como administrador.

2. Paire o rato sobre o ícone de roda dentada e clique nas **suplementos**.
    
    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon1.png)

3. Será redirecionado à página de acesso de administrador. Introduza o **palavra-passe** e clique em **confirmar** botão.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon2.png)

4. Na secção do separador de suplementos, clique em **encontrar novos suplementos**. Pesquisa **SAML único início de sessão (SSO) para JIRA** e clique em **instalar** botão para instalar o novo plug-in SAML.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon7.png)

5. A instalação de plug-in será iniciado. Clique em **Fechar**.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon8.png)

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon9.png)

6.  Clique em **Gerir**.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon10.png)
    
8. Clique em **configurar** para configurar o plug-in de novo.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon11.png)

9. No **configuração de plug-in SAML SingleSignOn** página, clique em **adicionar IdP novo** botão para configurar as definições do fornecedor de identidade.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon4.png)

10. No **escolha o fornecedor de identidade de SAML** página, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon5a.png)
 
    a. Definir **do Azure AD** como o tipo de IdP.
    
    b. Adicione **nome** do fornecedor de identidade (por exemplo, do Azure AD).
    
    c. Adicione **Descrição** do fornecedor de identidade (por exemplo, do Azure AD).
    
    d. Clique em **Seguinte**.
    
11. No **configuração do fornecedor de identidade** página, clique em **próxima** botão.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon5b.png)

12. No **importação SAML IdP metadados** página, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon5c.png)

    a. Clique em **carregar ficheiro** botão e escolha o ficheiro de metadados XML que transferiu no passo 5.

    b. Clique em **importação** botão.
    
    c. Aguarde um momento até que a importação for concluída com êxito.
    
    d. Clique em **seguinte** botão.
    
13. No **atributo de ID de utilizador e a transformação** página, clique em **próxima** botão.

    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon5d.png)
    
14. No **criação do utilizador e de atualização** página, clique em **Save & seguinte** ao guardar as definições.   
    
    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon6a.png)
    
15. No **testar suas configurações** página, clique em **ignorar teste e configurar manualmente** para ignorar o teste de utilizador por agora. Este será executada na próxima seção e requer algumas definições no portal do Azure. 
    
    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon6b.png)
    
16. Na leitura de caixa de diálogo appearing **a ignorar os meios de teste...** , clique em **OK**.
    
    ![Configurar o início de sessão único](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para SAML SSO para Jira pela resolução GmbH.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SAML SSO para Jira pela resolução GmbH**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **SAML SSO para Jira pela resolução GmbH**.

    ![O SSO SAML para Jira pela ligação de GmbH resolução na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Criar SAML SSO para Jira por utilizador de teste de GmbH de resolução

Para ativar a utilizadores do Azure AD iniciar sessão no SAML SSO para Jira pela resolução GmbH, eles têm de ser aprovisionados no SAML SSO para Jira pela resolução GmbH.  
No SAML SSO para Jira pela resolução GmbH, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão na sua SAML SSO para Jira pelo site de empresa resolução GmbH como administrador.

2. Paire o rato sobre o ícone de roda dentada e clique nas **gestão de utilizadores**.

    ![Adicionar o funcionário](./media/samlssojira-tutorial/user1.png) 

3. Será redirecionado para a página de acesso de administrador para introduzir **palavra-passe** e clique em **confirmar** botão.

    ![Adicionar o funcionário](./media/samlssojira-tutorial/user2.png) 

4. Sob **gestão de utilizadores** secção, clique em **criar utilizador**.

    ![Adicionar o funcionário](./media/samlssojira-tutorial/user3.png) 

5. Sobre o **"Criar novo usuário"** caixa de diálogo página, execute os seguintes passos:

    ![Adicionar o funcionário](./media/samlssojira-tutorial/user4.png) 

    a. Na **endereço de E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador Brittasimon@contoso.com.

    b. Na **FullName** caixa de texto, nome completo do tipo do utilizador, como a Eduarda Almeida.

    c. Na **nome de utilizador** caixa de texto, como o tipo de e-mail do utilizador Brittasimon@contoso.com.

    d. Na **palavra-passe** caixa de texto, escreva a palavra-passe do utilizador.

    e. Clique em **criar utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o SAML SSO para Jira por mosaico de GmbH resolução no painel de acesso, deve ser automaticamente sessão iniciada no SAML SSO para Jira por GmbH de resolução para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

