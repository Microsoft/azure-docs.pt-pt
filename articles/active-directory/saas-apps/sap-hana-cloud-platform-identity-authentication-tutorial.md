---
title: 'Tutorial: Integração do Active Directory do Azure com a autenticação de identidade de plataforma de Cloud SAP | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e autenticação de identidade da plataforma de Cloud de SAP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69ca6edb9dd125350e1e557633cdfd6f07217c06
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867648"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Integração do Active Directory do Azure com a autenticação de identidade da plataforma de Cloud de SAP

Neste tutorial, saiba como integrar a autenticação de identidade da plataforma de Cloud de SAP no Azure Active Directory (Azure AD).
Integrar a autenticação de identidade da plataforma de Cloud de SAP no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD, quem tem acesso a autenticação de identidade da plataforma de Cloud de SAP.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para autenticação de identidade do SAP Cloud Platform (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com autenticação de identidade da plataforma de Cloud de SAP, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SAP autenticação de identidade de plataforma de Cloud logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a autenticação de identidade da plataforma de Cloud SAP **SP** e **IDP** iniciada SSO

Antes de aprofundar os detalhes técnicos, é fundamental para compreender os conceitos que pretende examinar. A autenticação de identidade da plataforma de Cloud de SAP e serviços de Federação do Active Directory permitem implementar SSO em aplicações ou serviços que estão protegidos pelo Azure AD (como um IdP) com aplicações SAP e serviços que estão protegidos pelo SAP Cloud Autenticação de identidade da plataforma.

Atualmente, a autenticação de identidade da plataforma de Cloud de SAP atua como um fornecedor de identidade do Proxy de aplicações SAP. O Azure Active Directory funciona, por sua vez, como líder no fornecimento de identidade nessa configuração. 

O diagrama seguinte ilustra essa relação:

![Criar um utilizador de teste do Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Com esta configuração, o seu inquilino de autenticação de identidade da plataforma de Cloud de SAP está configurado como um aplicativo confiável no Azure Active Directory.

Todas as aplicações SAP e serviços que pretende proteger desta forma, em seguida, são configurados na consola de gestão de autenticação de identidade da plataforma de Cloud de SAP.

Por conseguinte, a autorização para conceder acesso a serviços e aplicações SAP precisa ser feita na autenticação de identidade do SAP Cloud Platform (em oposição ao Azure Active Directory).

Ao configurar a autenticação de identidade da plataforma de Cloud de SAP como uma aplicação através do Azure Active Directory Marketplace, não terá de configurar afirmações individuais ou asserções SAML.

> [!NOTE]
> Atualmente, apenas SSO de Web foi testado por ambas as partes. Os fluxos que são necessários para a API de aplicações ou a comunicação de API para API devem funcionar, mas ainda não foram testados. Serão testados durante as atividades subsequentes.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionando autenticação de identidade da plataforma de Cloud de SAP da Galeria

Para configurar a integração de autenticação de identidade da plataforma de Cloud de SAP no Azure AD, terá de adicionar autenticação de identidade da plataforma de Cloud de SAP a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar a autenticação de identidade da plataforma de Cloud de SAP da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **autenticação de identidade da plataforma de Cloud de SAP**, selecione **autenticação de identidade da plataforma de Cloud de SAP** no painel de resultados, em seguida, clique em **Add** botão para Adicione a aplicação.

     ![Autenticação de identidade do SAP Cloud Platform na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com [nome da aplicação] com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em [nome da aplicação] deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com [nome da aplicação], tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SAP Cloud Platform identidade autenticação de início de sessão único](#configure-sap-cloud-platform-identity-authentication-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de autenticação de identidade da plataforma de Cloud de SAP](#create-sap-cloud-platform-identity-authentication-test-user)**  - para ter um equivalente da Eduarda Almeida na SAP Cloud plataforma de identidade de autenticação que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com [nome da aplicação], execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **autenticação de identidade da plataforma de Cloud de SAP** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se pretender configurar no **IDP** intiated modo execute os seguintes passos:

    ![URLs de domínio de autenticação de identidade de plataforma na Cloud SAP e únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Contacte os [equipa de suporte de cliente de autenticação de identidade de plataforma de Cloud de SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) obter esses valores. Se não sabe o valor do identificador, leia a documentação de autenticação de identidade da plataforma de Cloud de SAP sobre [configuração do inquilino SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![URLs de domínio de autenticação de identidade de plataforma na Cloud SAP e únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Utilize o seu negócio específico início de sessão URL da aplicação. Contacte os [equipa de suporte de cliente de autenticação de identidade de plataforma de Cloud de SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) se tiver qualquer dúvida.

6. Aplicação de autenticação de identidade de plataforma em nuvem SAP espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. Se a sua aplicação SAP espera que um atributo, tal como **firstName**, adicionar o **firstName** atributo o **afirmações de utilizador** secção no **atributos de utilizador**  caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o firstName de nome de atributo.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** , selecione o user.givenname de valor de atributo.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **metadados XML**entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Sobre o **configurar a autenticação de identidade da plataforma de Cloud de SAP** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Configurar o SAP Cloud Platform identidade autenticação início de sessão único

1. Para obter SSO configurado para a sua aplicação, vá para a consola de administração de autenticação de identidade da plataforma de Cloud de SAP. O URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin`. Em seguida, leia a documentação sobre a autenticação de identidade do SAP Cloud Platform em [integração com o Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. No portal do Azure, selecione o **guardar** botão.

3. Continue com o seguinte procedimento apenas se pretende adicionar e ativar o SSO para outra aplicação SAP. Repita os passos na secção **adicionar SAP Cloud Platform autenticação de identidade na galeria do**.

4. No portal do Azure, sobre o **autenticação de identidade da plataforma de Cloud de SAP** página de integração de aplicações, selecione **início de sessão ligado em**.

    ![Configurar o início de sessão ligado](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Guarde a configuração.

> [!NOTE]
> A nova aplicação tira partido da configuração única início de sessão do aplicativo SAP anterior. Certifique-se de que utilizar os mesmos fornecedores de identidade empresarial na consola de administração de autenticação de identidade da plataforma de Cloud de SAP.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para autenticação de identidade da plataforma de Cloud de SAP.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **autenticação de identidade da plataforma de Cloud de SAP**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **autenticação de identidade da plataforma de Cloud de SAP**.

    ![A ligação de autenticação de identidade da plataforma de Cloud de SAP na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Criar utilizador de teste de autenticação de identidade da plataforma de Cloud de SAP

Não precisa de criar um utilizador na autenticação de identidade da plataforma de Cloud de SAP. Os utilizadores que estão no arquivo de utilizador do Azure AD podem utilizar a funcionalidade SSO.

Autenticação de identidade da plataforma de Cloud SAP suporta a opção de Federação de identidades. Esta opção permite que o aplicativo verificar se os utilizadores que são autenticados pelo fornecedor de identidade empresarial existem no arquivo de SAP Cloud plataforma de identidade autenticação de utilizador.

A opção de Federação de identidades está desativada por predefinição. Se estiver ativada a Federação de identidades, apenas os utilizadores que são importados na autenticação de identidade da plataforma de Cloud de SAP podem aceder à aplicação.

Para obter mais informações sobre como ativar ou desativar o Federação de identidades com autenticação de identidade da plataforma de Cloud de SAP, consulte "Ativar a Federação com o SAP Cloud plataforma identidade autenticação de identidade" no [configurar a Federação de identidades com o Autenticação de identidade do utilizador Store de SAP Cloud Platform](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de autenticação de identidade da plataforma de Cloud de SAP no painel de acesso, deve ser automaticamente conectado para a autenticação de plataforma na Cloud do SAP identidade para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
