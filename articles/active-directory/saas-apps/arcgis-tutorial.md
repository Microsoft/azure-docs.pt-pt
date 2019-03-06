---
title: 'Tutorial: Integração do Active Directory do Azure com o ArcGIS Online | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44075a8e605fb6cb28e546ee35fb79f1a6ac3389
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407447"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Tutorial: Integração do Active Directory do Azure com o ArcGIS Online

Neste tutorial, saiba como integrar o ArcGIS Online com o Azure Active Directory (Azure AD).
Integrar o ArcGIS Online com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao ArcGIS Online.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o ArcGIS Online (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ArcGIS Online, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* O ArcGIS Online logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* O ArcGIS Online suporta **SP** iniciada SSO

## <a name="adding-arcgis-online-from-the-gallery"></a>Adicionar ArcGIS Online a partir da Galeria

Para configurar a integração do ArcGIS Online com o Azure AD, terá de adicionar ArcGIS Online na Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o ArcGIS Online a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ArcGIS Online**, selecione **ArcGIS Online** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![O ArcGIS Online na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o ArcGIS Online com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ArcGIS Online tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o ArcGIS Online, terá de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o ArcGIS Online início de sessão único](#configure-arcgis-online-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste ArcGIS Online](#create-arcgis-online-test-user)**  - para ter um equivalente da Eduarda Almeida ArcGIS online que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o ArcGIS Online, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **ArcGIS Online** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de ArcGIS Online e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.maps.arcgis.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte do cliente Online do ArcGIS](https://support.esri.com/en/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Para automatizar a configuração de dentro **ArcGIS Online**, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** clicando **instalar a extensão**.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. Depois de adicionar a extensão para o navegador, clique em **ArcGIS Online de configuração** irá direcioná-lo para o aplicativo ArcGIS Online. A partir daí, forneça as credenciais de administrador a iniciar sessão em ArcGIS Online. A extensão de navegador automaticamente irá configurar o aplicativo para e automatizar passos na secção **configurar ArcGIS Online Single Sign-On**.

### <a name="configure-arcgis-online-single-sign-on"></a>Configurar o ArcGIS Online início de sessão único

1. Se desejar configurar manualmente o ArcGIS Online, abra uma nova janela de browser e inicie sessão no site da sua empresa ArcGIS como um administrador e execute os seguintes passos:

2. Clique em **editar definições**.

    ![Editar definições](./media/arcgis-tutorial/ic784742.png "editar definições")

3. Clique em **segurança**.

    ![Segurança](./media/arcgis-tutorial/ic784743.png "segurança")

4. Sob **inícios de sessão do Enterprise**, clique em **definir o fornecedor de identidade**.

    ![Inícios de sessão empresarial](./media/arcgis-tutorial/ic784744.png "inícios de sessão empresarial")

5. Sobre o **definir o fornecedor de identidade** configuração página, execute os seguintes passos:

    ![Definir o fornecedor de identidade](./media/arcgis-tutorial/ic784745.png "definir o fornecedor de identidade")

    a. Na **nome** caixa de texto, escreva o nome da sua organização.

    b. Para **serão fornecidos metadados para o fornecedor de identidade empresarial utilizando**, selecione **um ficheiro**.

    c. Para carregar o ficheiro de metadados baixado, clique em **Escolher ficheiro**.

    d. Clique em **fornecedor de identidade do conjunto**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao ArcGIS Online.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **ArcGIS Online**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **ArcGIS Online**.

    ![O ArcGIS Online link na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-arcgis-online-test-user"></a>Criar utilizador de teste ArcGIS Online

Para habilitar os utilizadores do Azure AD iniciar sessão no ArcGIS Online, tem de ser aprovisionados no ArcGIS Online.  
No caso do ArcGIS Online, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **ArcGIS** inquilino.

2. Clique em **membros de convite**.
   
    ![Convidar os membros](./media/arcgis-tutorial/ic784747.png "convidar os membros")

3. Selecione **adicionar membros automaticamente sem enviar um e-mail**e, em seguida, clique em **PRÓXIMA**.
   
    ![Adicionar membros automaticamente](./media/arcgis-tutorial/ic784748.png "automaticamente a adicionar membros")

4. Sobre o **membros** caixa de diálogo página, execute os seguintes passos:
   
     ![Adicionar e reveja](./media/arcgis-tutorial/ic784749.png "Add e revisão")
    
     a. Introduza o **E-Mail**, **FirstName**, e **Apelido** de uma conta do AAD válida que pretende aprovisionar.
  
     b. Clique em **adicionar e revisão**.
5. Reveja os dados que introduziu e, em seguida, clique em **adicionar membros**.
   
    ![Adicionar membro](./media/arcgis-tutorial/ic784750.png "Adicionar membro")
        
    > [!NOTE]
    > O titular da conta do Azure Active Directory irá receber um e-mail e seguir uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico ArcGIS Online no painel de acesso, deve ser automaticamente conectado para o ArcGIS Online para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

