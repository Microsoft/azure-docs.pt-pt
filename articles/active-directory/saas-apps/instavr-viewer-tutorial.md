---
title: 'Tutorial: Integração do Active Directory do Azure com o Visualizador de InstaVR | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Visualizador de InstaVR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85e773d74231b0b0847dad41a071be499724e2d4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872935"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Tutorial: Integração do Active Directory do Azure com o Visualizador de InstaVR

Neste tutorial, saiba como integrar o Visualizador de InstaVR com o Azure Active Directory (Azure AD).
Integrar o Visualizador de InstaVR no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Visualizador de InstaVR.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Visualizador de InstaVR (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Visualizador de InstaVR, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Visualizador de InstaVR logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Visualizador de InstaVR suporta **SP** iniciada SSO
* Visualizador de InstaVR suporta **Just In Time** aprovisionamento de utilizadores

## <a name="adding-instavr-viewer-from-the-gallery"></a>Adicionando InstaVR Visualizador da Galeria

Para configurar a integração do Visualizador de InstaVR com o Azure AD, terá de adicionar InstaVR Visualizador a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar InstaVR Visualizador da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **InstaVR Visualizador**, selecione **Visualizador de InstaVR** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Visualizador de InstaVR na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Visualizador de InstaVR com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Visualizador de InstaVR deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Visualizador de InstaVR, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar InstaVR Visualizador de início de sessão único](#configure-instavr-viewer-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Visualizador de InstaVR](#create-instavr-viewer-test-user)**  - para ter um equivalente da Eduarda Almeida no Visualizador de InstaVR que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Visualizador de InstaVR, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Visualizador de InstaVR** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![InstaVR Visualizador de domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Não existe nenhuma padrão fixo para o URL de início de sessão. Este é gerado quando o cliente do Visualizador de InstaVR web empacotamento. Ele é exclusivo para cada cliente e o pacote. Para obter o início de sessão exato na URL tem de iniciar sessão para o seu visualizador InstaVR de instância e a web empacotamento.

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > O valor do identificador não é real. Atualize este valor com o valor do identificador real que é explicado posteriormente neste tutorial.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** e **ficheiro de metadados de Federação** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadata-certificatebase64.png)

6. Sobre o **configurar InstaVR Visualizador** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-instavr-viewer-single-sign-on"></a>Configurar o Visualizador de InstaVR início de sessão único

1. Abra uma nova janela do browser web e de registo no site da sua empresa InstaVR Visualizador como administrador.

2. Clique em **ícone de utilizador** e selecione **conta**.

    ![Configuração do Visualizador de InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Desloque para baixo para o **autenticação SAML** e execute os seguintes passos:

    ![Configuração do Visualizador de InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. Na **URL de SSO** caixa de texto, colar a **URL de início de sessão** valor, que copiou do portal do Azure.

    b. Na **URL de fim de sessão** caixa de texto, colar a **URL de fim de sessão** valor, que copiou do portal do Azure.

    c. Na **ID de entidade** caixa de texto, colar a **do Azure Ad identificador** valor, que copiou do portal do Azure.

    d. Para carregar o ficheiro de certificado transferido, clique em **atualização**.

    e. Para carregar o ficheiro de metadados de Federação transferido, clique em **atualização**.

    f. Cópia a **ID de entidade** valor e cole o **identificador (ID de entidade)** caixa de texto a **configuração básica de SAML** secção no portal do Azure.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao Visualizador de InstaVR.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **InstaVR Visualizador**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **InstaVR Visualizador**.

    ![A ligação do Visualizador de InstaVR na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-instavr-viewer-test-user"></a>Criar utilizador de teste do Visualizador de InstaVR

Nesta secção, um usuário chamado Eduarda Almeida é criado no Visualizador de InstaVR. Visualizador de InstaVR suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Visualizador de InstaVR, é criado um novo após a autenticação. Se tiver quaisquer problemas, entre em contato com a [equipa de suporte do Visualizador de InstaVR](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

1. Abra uma nova janela do browser web e de registo no site da sua empresa InstaVR Visualizador como administrador.

2. Selecione **pacote** do painel de navegação à esquerda e selecione **pacote de marca para a Web**.

    ![Configuração do Visualizador de InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Selecione **Transferir**.

    ![Configuração do Visualizador de InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Selecione **aberto página alojado** depois que ele será redirecionado para o Azure AD para início de sessão.

    ![Configuração do Visualizador de InstaVR](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Introduza as credenciais do Azure AD para iniciar sessão com êxito para o Azure AD através do SSO.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
