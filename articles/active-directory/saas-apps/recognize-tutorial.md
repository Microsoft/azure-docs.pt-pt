---
title: 'Tutorial: Integração do Active Directory do Azure com Recognize | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Recognize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfad939e-c8f4-45a0-bd25-c4eb9701acaa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: bd772a10cd64b4198e994fdefa671444447c8a53
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849568"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: Integração do Active Directory do Azure com Recognize

Neste tutorial, saiba como integrar Recognize com o Azure Active Directory (Azure AD).
Reconhecer a integração com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao reconhecer.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para reconhecer (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Recognize, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Reconhecer o início de sessão ativada subscrição individual

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Reconhecer suporta **SP** iniciada SSO

## <a name="adding-recognize-from-the-gallery"></a>Adicionando Recognize da Galeria

Para configurar a integração de reconhecer no Azure AD, terá de adicionar reconhecer a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar reconhecer a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Recognize**, selecione **Recognize** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Reconhecer na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Recognize com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Recognize deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Recognize, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar reconhecer início de sessão único](#configure-recognize-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de reconhecer](#create-recognize-test-user)**  - para ter um equivalente da Eduarda Almeida na reconhecer que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Recognize, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Recognize** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    >[!NOTE]
    >Obterá o **ficheiro de metadados do fornecedor de serviços** da **configurar reconhecer Single Sign-On** secção do tutorial.

    a. Clique em **carregamento de ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![Escolha o ficheiro de metadados](common/browse-upload-metadata.png)

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** valor obter automaticamente preenchida na seção de configuração básica de SAML.

    ![Reconhecer o domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

     Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão:  `https://recognizeapp.com/<your-domain>/saml/sso`

    > [!Note]
    > Se o **identificador** valor não obtém automaticamente preenchida, obterá o valor do identificador ao abrir o URL de metadados do fornecedor de serviço da secção definições de SSO que é explicada posteriormente no **configurar reconhecer único Início de sessão** secção do tutorial. O valor de URL de início de sessão não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de reconhecer](mailto:support@recognizeapp.com) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Recognize** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-recognize-single-sign-on"></a>Configurar a reconhecer o início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu inquilino de reconhecer como administrador.

2. No canto superior direito, clique em **Menu**. Aceda a **da empresa administrador**.
   
    ![Configure o lado de início de sessão na aplicação única](./media/recognize-tutorial/tutorial_recognize_000.png)

3. No painel de navegação esquerdo, clique em **definições**.
   
    ![Configure o lado de início de sessão na aplicação única](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Execute os seguintes passos no **as definições de SSO** secção.
   
    ![Configure o lado de início de sessão na aplicação única](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Como **ativar o SSO**, selecione **ON**.

    b. Na **ID de entidade do IDP** caixa de texto, cole o valor de **do Azure AD identificador** que copiou do portal do Azure.
    
    c. Na **url de destino de Sso** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.
    
    d. Na **url de destino de Slo** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure. 
    
    e. Abra sua transferido **certificado (Base64)** do ficheiro no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado** caixa de texto.
    
    f. Clique nas **guardar as definições de** botão. 

5. Junto a **as definições de SSO** secção, copie o URL sob **url de metadados do fornecedor de serviço**.
   
    ![Configure o lado de início de sessão na aplicação única](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Abra o **ligação de URL de metadados** num navegador em branco para transferir o documento de metadados. Depois, copie o value(entityID) EntityDescriptor do ficheiro e cole-a no **identificador** na caixa de texto **configuração básica de SAML** no portal do Azure.
    
    ![Configure o lado de início de sessão na aplicação única](./media/recognize-tutorial/tutorial_recognize_004.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para reconhecer.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Recognize**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Recognize**.

    ![A ligação de reconhecer na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-recognize-test-user"></a>Criar utilizador de teste de reconhecer

Para habilitar logon Recognize de utilizadores do Azure AD, tem de ser aprovisionados em Recognize. No caso de reconhecer, o aprovisionamento é uma tarefa manual.

Esta aplicação não suporta o aprovisionamento SCIM, mas tem uma sincronização de utilizador alternativas que Aprovisiona utilizadores. 

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa reconhecer como administrador.

2. No canto superior direito, clique em **Menu**. Aceda a **da empresa administrador**.

3. No painel de navegação esquerdo, clique em **definições**.

4. Execute os seguintes passos no **sincronização de utilizador** secção.
   
    ![Novo utilizador](./media/recognize-tutorial/tutorial_recognize_005.png "novo utilizador")
   
    a. Como **sincronização ativada**, selecione **ON**.
   
    b. Como **fornecedor de sincronização de escolher**, selecione **Microsoft / Office 365**.
   
    c. Clique em **execute a sincronização de utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de reconhecer no painel de acesso, deve ser automaticamente conectado para reconhecer para que configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

