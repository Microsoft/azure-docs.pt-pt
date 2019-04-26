---
title: 'Tutorial: Integração do Active Directory do Azure com o Velpic SAML | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc8431e2c45cc3bfdfa08dd0078220ab2d290309
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316089"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Tutorial: Integração do Active Directory do Azure com Velpic SAML

Neste tutorial, saiba como integrar Velpic SAML com o Azure Active Directory (Azure AD).
Integrar Velpic SAML com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Velpic SAML.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Velpic SAML (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Velpic SAML, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Velpic SAML logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Velpic SAML **SP** iniciada SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>Adicionando Velpic SAML da Galeria

Para configurar a integração de Velpic SAML para o Azure AD, terá de adicionar Velpic SAML a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Velpic SAML a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Velpic SAML**, selecione **Velpic SAML** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![SAML Velpic na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Velpic SAML com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Velpic SAML deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Velpic SAML, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Velpic SAML início de sessão único](#configure-velpic-saml-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de Velpic SAML](#create-velpic-saml-test-user)**  - para ter um equivalente da Eduarda Almeida na Velpic SAML, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Velpic SAML, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Velpic SAML** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Velpic SAML domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<sub-domain>.velpicsaml.net`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Tenha em atenção que o URL de início de sessão será fornecido pela equipe do Velpic SAML e o valor do identificador estará disponível quando configurar o plug-in do SSO no lado de Velpic SAML. Precisa copiar esse valor de página de aplicativo Velpic SAML e cole-o aqui.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar Velpic SAML** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-velpic-saml-single-sign-on"></a>Configurar Velpic SAML início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Velpic SAML como administrador.

2. Clique em **gerir** separador e aceda à **integração** secção onde tem de clicar em **plug-ins** botão para criar o novo plug-in para início de sessão.

    ![Plug-in](./media/velpicsaml-tutorial/velpic_1.png)

3. Clique nas **"Adicionar plug-in"** botão.
    
    ![Plug-in](./media/velpicsaml-tutorial/velpic_2.png)

4. Clique nas **SAML** mosaico na página Adicionar plug-in.
    
    ![Plug-in](./media/velpicsaml-tutorial/velpic_3.png)

5. Introduza o nome do novo plug-in do SAML e clique nas **'Add'** botão.

    ![Plug-in](./media/velpicsaml-tutorial/velpic_4.png)

6. Introduza os detalhes da seguinte forma:

    ![Plug-in](./media/velpicsaml-tutorial/velpic_5.png)

    a. Na **nome** caixa de texto, escreva o nome do plug-in SAML.

    b. No **URL de emissor** caixa de texto, colar a **identificador do Azure AD** copiou do **configurar início de sessão** janela do portal do Azure.

    c. Na **fornecedor de configuração de metadados** carregar o ficheiro de metadados XML que transferiu a partir do portal do Azure.

    d. Também pode optar por ativar SAML apenas em tempo de aprovisionamento, permitindo que o **'Automática criar novos utilizadores'** caixa de verificação. Se um utilizador não existe no Velpic e este sinalizador não estiver ativado, o início de sessão do Azure irá falhar. Se o sinalizador estiver ativado o usuário será automaticamente provisionado no Velpic no momento do início de sessão. 

    e. Copiar o **início de sessão no URL único** o texto de caixa e cole-a no portal do Azure.
    
    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Velpic SAML.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Velpic SAML**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Velpic SAML**.

    ![A ligação de Velpic SAML na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-velpic-saml-test-user"></a>Criar utilizador de teste Velpic SAML

Neste passo, normalmente, não é necessário que o aplicativo oferece suporte apenas no tempo de aprovisionamento de utilizador. Se o aprovisionamento automático de utilizadores não estiver ativado, em seguida, a criação manual de utilizador pode ser feita conforme descrito abaixo.

Inicie sessão no site da sua empresa Velpic SAML como administrador e execute os seguintes passos:
    
1. Clique no separador de gerir e aceda à secção de utilizadores e, em seguida, clique no botão novo para adicionar utilizadores.

    ![Adicionar utilizador](./media/velpicsaml-tutorial/velpic_7.png)

2. Sobre o **"Criar o novo usuário"** caixa de diálogo página, execute os seguintes passos.

    ![Utilizador](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Na **nome próprio** caixa de texto, nome do tipo o primeiro da Eduarda.

    b. Na **Apelido** caixa de texto, digite o apelido de Simon.

    c. Na **nome de utilizador** caixa de texto, escreva o nome de utilizador da Eduarda Almeida.

    d. Na **E-Mail** caixa de texto, escreva o endereço de e-mail do Brittasimon@contoso.com conta.

    e. REST das informações é opcional, pode preenchê-lo se for necessário.
    
    f. Clique em **GUARDAR**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

1. Quando clica no mosaico de Velpic SAML no painel de acesso, deve obter a página de início de sessão da aplicação de Velpic SAML. Deverá ver o **"Iniciar sessão no Azure AD"** botão na página de início de sessão.

    ![Plug-in](./media/velpicsaml-tutorial/velpic_6.png)

1. Clique nas **"Iniciar sessão no Azure AD"** botão para iniciar sessão no Velpic com a sua conta do Azure AD.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

