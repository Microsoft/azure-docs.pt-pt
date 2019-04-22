---
title: 'Tutorial: Integração do Active Directory do Azure com a revisão clara | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e clara de revisão.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2576aa112d58e499f0c4a16bf8e9261114974b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698984"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Tutorial: Integração do Active Directory do Azure com a revisão clara

Neste tutorial, saiba como integrar a revisão clara com o Azure Active Directory (Azure AD).
Integração de revisão clara com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso à revisão clara.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para revisão claro (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a revisão clara, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Limpar revisão início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Limpar suporta a revisão **SP e IDP** iniciada SSO

## <a name="adding-clear-review-from-the-gallery"></a>Adicionar a revisão clara da Galeria

Para configurar a integração de revisão claro para o Azure AD, terá de adicionar revisão claro a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar revisão claro a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **clara de revisão**, selecione **revisão clara** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Limpar revisão na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com revisão clara com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado revisão clara deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com a revisão claro, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar clara revisão de início de sessão único](#configure-clear-review-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de revisão clara](#create-clear-review-test-user)**  - para ter um equivalente da Eduarda Almeida na revisão clara que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com a revisão clara, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **revisão clara** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Limpar o domínio de revisão e URLs único informações de início de sessão](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<customer name>.clearreview.com/sso/metadata/`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<customer name>.clearreview.com/sso/acs/`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Limpar o domínio de revisão e URLs único informações de início de sessão](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<customer name>.clearreview.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de cliente de revisão clara](https://clearreview.com/contact/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. Rever clara da aplicação espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Rever clara da aplicação espera **nameidentifier** seja mapeado com **user.mail**, por isso terá de editar o mapeamento do atributo clicando no **editar** ícone e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

7. Sobre o **atributos de utilizador e afirmações** caixa de diálogo, execute os seguintes passos:

    a. Clique em **ícone de edição** à direita da **o valor do identificador de nome**.

    ![image](./media/clearreview-tutorial/attribute02.png)

    ![image](./media/clearreview-tutorial/attribute01.png)

    b. Partir do **atributo de origem** lista, selecione o **user.mail** valor para essa linha de atributo.

    c. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Sobre o **configurar revisão clara** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-clear-review-single-sign-on"></a>Configurar revisão clara início de sessão único

1. Para configurar o início de sessão único num **clara de revisão** lado, abra o **revisão clara** portal com credenciais de administrador.

2. Selecione **administrador** no painel de navegação esquerda.

    ![Configurar o botão único início de sessão em Guardar](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

3. No **integrações** secção na parte inferior da página clique a **alteração** botão à direita do **definições de início de sessão único**.

    ![Configurar o botão único início de sessão em Guardar](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

4. Execute os seguintes passos **definições de início de sessão único** página

    ![Configurar o botão único início de sessão em Guardar](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. No **URL de emissor** caixa de texto, cole o valor de **do Azure AD identificador** que copiou do portal do Azure.

    b. Na **ponto final de SAML** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.  

    c. Na **ponto final de SLO** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.  

    d. Abra o certificado transferido no bloco de notas e cole o conteúdo do **certificado X.509** caixa de texto.   

    e. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à revisão clara.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **revisão clara**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **revisão clara**.

    ![A ligação de revisão limpar na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-clear-review-test-user"></a>Criar utilizador de teste de revisão clara

Nesta secção, vai criar um usuário chamado Eduarda Almeida na revisão clara. Trabalhe em conjunto com [equipa de suporte de revisão clara](https://clearreview.com/contact/) para adicionar os utilizadores na plataforma da revisão clara.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de revisão limpar no painel de acesso, deve ser automaticamente conectado à revisão clara para os quais configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

