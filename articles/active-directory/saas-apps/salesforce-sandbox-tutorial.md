---
title: 'Tutorial: Integração do Active Directory do Azure com a Sandbox da Salesforce | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2940f3eee3112fe1c6d57cc92157c573ecad109
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722564"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Integração do Active Directory do Azure com a Sandbox de Salesforce

Neste tutorial, saiba como integrar o Sandbox de Salesforce com o Azure Active Directory (Azure AD).

Áreas de segurança dão-lhe a capacidade de criar várias cópias da sua organização em ambientes separados para diversas finalidades, como o desenvolvimento, teste e treinamento, sem comprometer os dados e aplicativos em sua produção de Salesforce organização.
Para obter mais detalhes, consulte [descrição geral de Sandbox](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Integrar o Salesforce Sandbox no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Sandbox do Salesforce.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a área de segurança do Salesforce (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a Sandbox da Salesforce, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Sandbox de Salesforce logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a proteção de segurança do Salesforce **SP e IDP** iniciada SSO
* Suporta a proteção de segurança do Salesforce **Just In Time** aprovisionamento de utilizadores
* Suporta a proteção de segurança do Salesforce [ **automatizada** aprovisionamento de utilizadores](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionar Sandbox do Salesforce a partir da Galeria

Para configurar a integração de Salesforce Sandbox para o Azure AD, terá de adicionar área de segurança do Salesforce a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar área de segurança do Salesforce a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Salesforce Sandbox**, selecione **Sandbox de Salesforce** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Sandbox de Salesforce na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com a Sandbox da Salesforce com base num utilizador de teste **Eduarda Almeida**.

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Salesforce Sandbox para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Sandbox do Salesforce tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com a Sandbox da Salesforce, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Salesforce Sandbox início de sessão único](#configure-salesforce-sandbox-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de área de segurança do Salesforce](#create-salesforce-sandbox-test-user)**  - para ter um equivalente da Eduarda Almeida na Sandbox do Salesforce que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com a Sandbox da Salesforce, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Sandbox de Salesforce** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, se tiver **ficheiro de metadados do fornecedor de serviços** e deseja configurar no **IDP** modo iniciado execute os seguintes passos:

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![Escolha o ficheiro de metadados](common/browse-upload-metadata.png)

    > [!NOTE]
    > Irá obter o fornecedor de serviços de ficheiro de metadados do portal de administração do Salesforce Sandbox, que é explicado mais tarde no tutorial.

    c. Depois do ficheiro de metadados é carregado com êxito, o **URL de resposta** valor irá obter automaticamente preenchido **URL de resposta** caixa de texto.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Se o **URL de resposta** valor não obtém automaticamente polulated, em seguida, preencha o valor manualmente de acordo com seus requisitos.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **metadados XML**entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar Sandbox de Salesforce** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-salesforce-sandbox-single-sign-on"></a>Configurar a proteção de segurança do Salesforce início de sessão único

1. Abra um novo separador no seu browser e inicie sessão na sua conta de administrador da área de segurança do Salesforce.

2. Clique nas **programa de configuração** sob **ícone de definições** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure1.png)

3. Desloque para baixo para o **configurações** no painel de navegação esquerdo, clique em **identidade** para expandir a secção relacionada. Em seguida, clique em **definições de início de sessão único**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Sobre o **definições de início de sessão único** página, clique no **editar** botão.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selecione **ativada SAML**e, em seguida, clique em **guardar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Para configurar o seu únicas início de sessão em definições de SAML, clique em **novo a partir do ficheiro de metadados**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Clique em **Escolher ficheiro** para carregar o ficheiro XML de metadados que transferiu a partir do portal do Azure e clique em **criar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Sobre o **SAML único configurações de logon** página, campos preencher automaticamente em clique em Guardar.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Sobre o **definições de início de sessão único** página, clique no **transferir metadados** botão para transferir o ficheiro de metadados do fornecedor de serviço. Utilize este ficheiro na **configuração básica de SAML** secção no portal do Azure para configurar os URLs necessários, tal como explicado anteriormente.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure4.png)

10. Se desejar configurar a aplicação no **SP** modo iniciado, a seguir são os pré-requisitos para isso:

    a. Deve ter um domínio verificado.

    b. Terá de configurar e ativar o seu domínio na Sandbox do Salesforce, passos para isso são explicados posteriormente neste tutorial.

    c. No portal do Azure, sobre o **configuração básica de SAML** secção, clique em **definir URLs adicionais** e executar o passo seguinte:
  
    ![Domínio de Sandbox do Salesforce e URLs únicas início de sessão em informações](common/both-signonurl.png)

    Na **URL de início de sessão** caixa de texto, digite o valor usando o seguinte padrão: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Este valor deve ser copiado a partir do portal do Salesforce Sandbox assim que tiver ativado o domínio.

11. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados de Federação** e, em seguida, guarde o ficheiro xml em seu computador.

    ![O link de download de certificado](common/metadataxml.png)

12. Abra um novo separador no seu browser e inicie sessão na sua conta de administrador da área de segurança do Salesforce.

13. Clique nas **programa de configuração** sob **ícone de definições** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure1.png)

14. Desloque para baixo para o **configurações** no painel de navegação esquerdo, clique em **identidade** para expandir a secção relacionada. Em seguida, clique em **definições de início de sessão único**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Sobre o **definições de início de sessão único** página, clique no **editar** botão.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selecione **ativada SAML**e, em seguida, clique em **guardar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Para configurar o seu únicas início de sessão em definições de SAML, clique em **novo a partir do ficheiro de metadados**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Clique em **Escolher ficheiro** para carregar o ficheiro XML de metadados e clique em **criar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Sobre o **SAML único configurações de logon** página, campos preencher automaticamente, escreva o nome da configuração (por exemplo: *SPSSOWAAD_Test*), além da **nome** caixa de texto e clique em Guardar.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Para ativar o seu domínio na Sandbox do Salesforce, execute os seguintes passos:

    > [!NOTE]
    > Antes de ativar o domínio tem de criar o mesmo na área de segurança do Salesforce. Para obter mais informações, consulte [definir seu nome de domínio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). Depois de criar o domínio, certifique-se de que está configurado corretamente.

21. No painel de navegação à esquerda na Sandbox do Salesforce, clique em **definições da empresa** para expandir a secção relacionada e, em seguida, clique em **meu domínio**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Na **configuração da autenticação** secção, clique em **editar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Na **configuração da autenticação** secção, como **serviço de autenticação**, selecione o nome SAML único início de sessão da definição de que definiu durante a configuração de SSO na Sandbox do Salesforce e Clique em **guardar**.

    ![Configurar o início de sessão único](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com.

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida a utilizar do Azure-início de sessão único, conceder acesso à área de segurança do Salesforce.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Salesforce Sandbox**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Salesforce Sandbox**.

    ![A ligação de área de segurança do Salesforce na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-salesforce-sandbox-test-user"></a>Criar utilizador de teste de Sandbox de Salesforce

Nesta secção, um usuário chamado Eduarda Almeida é criado na área de segurança do Salesforce. Sandbox de Salesforce suporta o aprovisionamento de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir na área de segurança do Salesforce, uma nova é criada quando tentar acessar a área de segurança do Salesforce. Sandbox de Salesforce também suporta o aprovisionamento automático de utilizadores, pode encontrar mais detalhes [aqui](salesforce-sandbox-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de área de segurança do Salesforce no painel de acesso, deve ser automaticamente conectado para a área de segurança do Salesforce para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Configurar o aprovisionamento do utilizador](salesforce-sandbox-provisioning-tutorial.md)
