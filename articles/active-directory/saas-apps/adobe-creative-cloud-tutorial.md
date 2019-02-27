---
title: 'Tutorial: Integração do Active Directory do Azure com o Adobe Creative Cloud | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 225945c3accb9a92cc4a04faa9ba029092feae57
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868702"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Tutorial: Integração do Active Directory do Azure com o Adobe Creative Cloud

Neste tutorial, saiba como integrar o Adobe Creative Cloud no Azure Active Directory (Azure AD).
Integrar o Adobe Creative Cloud no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso à Adobe Creative Cloud.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Adobe Creative Cloud (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Adobe Creative Cloud, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Adobe Creative Cloud logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Adobe Creative Cloud suporta **SP** iniciada SSO

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adicionar o Adobe Creative Cloud a partir da Galeria

Para configurar a integração do Adobe Creative Cloud no Azure AD, terá de adicionar Adobe Creative Cloud a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o Adobe Creative Cloud a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Adobe Creative Cloud**, selecione **Adobe Creative Cloud** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Adobe Creative Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Adobe Creative Cloud com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Adobe Creative Cloud tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o Adobe Creative Cloud, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Adobe Creative Cloud início de sessão único](#configure-adobe-creative-cloud-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)**  - para ter um equivalente da Eduarda Almeida na Adobe Creative Cloud que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Adobe Creative Cloud, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Adobe Creative Cloud** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Adobe Creative Cloud domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **URL de início de sessão** texto, escreva o valor como: `https://adobe.com`.

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > O valor do identificador não é real. Atualize este valor com o identificador real. Contacte [equipa de suporte de cliente do Adobe Creative Cloud](https://www.adobe.com/au/creativecloud/business/teams/plans.html) para obter este valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação do Adobe Creative Cloud espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name | Atributo de origem|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

    > [!NOTE]
    > Os utilizadores tem de ter uma licença válida do ExO do Office 365 para e-mail valor deve ser preenchida na resposta SAML da afirmação.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar o Adobe Creative Cloud** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Configurar o Adobe Creative Cloud início de sessão único

1. Numa janela do browser web diferente, inicie sessão no [consola de administração do Adobe](https://adminconsole.adobe.com) como administrador.

2. Aceda a **configurações** no painel de navegação superior da barra e, em seguida, escolha **identidade**. É aberta a lista de domínios. Clique em **configurar** link em relação a seu domínio. Em seguida, execute os seguintes passos no **início de sessão único na configuração necessária** secção. Para obter mais informações, consulte [um domínio de configuração](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![As definições](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "definições")

    a. Clique em **navegue** para carregar o certificado transferido do Azure AD para **IDP certificado**.

    b. Na **emissor IDP** caixa de texto, colocar o valor de **Id de entidade de SAML** que copiou do **configurar início de sessão** secção no portal do Azure.

    c. Na **URL de início de sessão do IDP** caixa de texto, colocar o valor de **URL do serviço SAML SSO** que copiou do **configurar início de sessão** secção no portal do Azure.

    d. Selecione **HTTP - redirecionamento** como **IDP enlace**.

    e. Selecione **endereço de E-Mail** como **definição de início de sessão do utilizador**.

    f. Clique em **guardar** botão.

3. O dashboard agora apresentará o XML **"Metadados transferir"** ficheiro. Ela contém a EntityDescriptor URL da Adobe e a AssertionConsumerService URL. Abra o ficheiro e configurá-los na aplicação do Azure AD.

    ![Configurar o início de sessão único no lado de aplicação](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Utilize o valor de EntityDescriptor Adobe fornecido por **identificador** sobre o **configurar as definições da aplicação** caixa de diálogo.

    b. Utilize o valor de AssertionConsumerService Adobe fornecido por **URL de resposta** sobre o **configurar as definições da aplicação** caixa de diálogo.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à Adobe Creative Cloud.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Adobe Creative Cloud**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Adobe Creative Cloud**.

    ![A ligação do Adobe Creative Cloud na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-adobe-creative-cloud-test-user"></a>Criar utilizador de teste do Adobe Creative Cloud

Para habilitar os utilizadores do Azure AD iniciar sessão no Adobe Creative Cloud, tem de ser aprovisionados no Adobe Creative Cloud. No caso do Adobe Creative Cloud, o aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar contas de utilizador, execute os seguintes passos:

1. Inicie sessão no [consola de administração do Adobe](https://adminconsole.adobe.com) site como um administrador.

2. Adicionar o utilizador da consola da Adobe como ID Federado e atribuí-las a um perfil de produto. Para obter informações detalhadas sobre como adicionar utilizadores, consulte [adiciona os utilizadores da consola de administração do Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Neste momento, digite seu endereço de e-mail/upn no formulário de início de sessão do Adobe, prima tab, e deve ser federado para o Azure AD:
    * Acesso Web: www.adobe.com > início de sessão
    * Dentro do utilitário de aplicativo de desktop > início de sessão
    * Dentro da aplicação > Ajuda > início de sessão

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do Adobe Creative Cloud no painel de acesso, deve ser automaticamente conectado para o Adobe Creative Cloud para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Configurar um domínio (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Configurar o Azure para utilização com o Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
