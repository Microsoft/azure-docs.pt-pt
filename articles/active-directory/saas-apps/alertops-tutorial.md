---
title: 'Tutorial: Integração do Active Directory do Azure com AlertOps | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21b8cb06712e370972e0b8fec518c37d078262e0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107063"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Tutorial: Integrar AlertOps com o Azure Active Directory

Neste tutorial, irá aprender como integrar AlertOps com o Azure Active Directory (Azure AD). Quando integrar AlertOps com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao AlertOps.
* Permita que os utilizadores ser automaticamente sessão iniciada para AlertOps com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* AlertOps início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta AlertOps **SP e IDP** iniciada SSO.

## <a name="adding-alertops-from-the-gallery"></a>Adicionando AlertOps da Galeria

Para configurar a integração do AlertOps com o Azure AD, terá de adicionar AlertOps a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **AlertOps** na caixa de pesquisa.
1. Selecione **AlertOps** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com AlertOps com um utilizador de teste **Eduarda Almeida**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no AlertOps.

Para configurar e testar o SSO do Azure AD com AlertOps, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar AlertOps](#configure-alertops)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste AlertOps](#create-alertops-test-user)**  ter um equivalente da Eduarda Almeida na AlertOps que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **AlertOps** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    1. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.alertops.com`

    1. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de cliente AlertOps](mailto:support@alertops.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar AlertOps** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Configurar AlertOps

1. Para automatizar a configuração no AlertOps, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **AlertOps configuração** irá direcioná-lo para o aplicativo AlertOps. A partir daí, forneça as credenciais de administrador a iniciar sessão em AlertOps. A extensão de navegador automaticamente irá configurar o aplicativo para e automatizar passos 3 a 5.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se desejar configurar manualmente o AlertOps, abra uma nova janela de browser e inicie sessão no site da sua empresa AlertOps como administrador e execute os seguintes passos:

4. Clique nas **definições da conta** do painel de navegação à esquerda.

    ![Configuração de AlertOps](./media/alertops-tutorial/configure1.png)

5. Sobre o **definições de subscrição** página select **SSO** e execute os seguintes passos:

    ![Configuração de AlertOps](./media/alertops-tutorial/configure2.png)

    a. Selecione **Sign-On(SSO) de uso único** caixa de verificação.

    b. Selecione **do Azure Active Directory** como um **provedor SSO** na lista pendente.

    c. Na **URL de emissor** caixa de texto, utilize o valor de identificador, o que utilizou no **configuração básica de SAML** secção no portal do Azure.

    d. Na **URL de ponto final SAML** caixa de texto, colar a **URL de início de sessão** valor, que copiou do portal do Azure.

    e. Na **URL de ponto final SLO** caixa de texto, colar a **URL de início de sessão** valor, que copiou do portal do Azure.

    f. Selecione **SHA256** como um **o algoritmo de assinatura de SAML** na lista pendente.

    g. Abra o ficheiro de Certificate(Base64) transferido no bloco de notas. Copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-o à caixa de texto de certificado X.509.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para AlertOps.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **AlertOps**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-alertops-test-user"></a>Criar utilizador de teste AlertOps

1. Numa janela do browser diferente, inicie sessão no site da sua empresa AlertOps como administrador.

2. Clique nas **utilizadores** do painel de navegação à esquerda.

    ![Configuração de AlertOps](./media/alertops-tutorial/user1.png)

3. Selecione **adicionar utilizador**.

    ![Configuração de AlertOps](./media/alertops-tutorial/user2.png)

4. Sobre o **adicionar utilizador** caixa de diálogo, execute os seguintes passos:

    ![Configuração de AlertOps](./media/alertops-tutorial/user3.png)

    a. Na **nome de utilizador de início de sessão** caixa de texto, introduza o nome de utilizador do utilizador, como **Brittasimon**.

    b. Na **E-Mail oficial** caixa de texto, introduza o endereço de e-mail do utilizador, como **Brittasimon\@contoso.com**.

    c. Na **nome próprio** caixa de texto, introduza o nome de utilizador, como **Eduarda**.

    d. Na **sobrenome** caixa de texto, introduza o nome de utilizador, como **Simon**.

    e. Selecione o **tipo** valor na lista pendente de acordo com a sua organização.

    f. Selecione o **função** do utilizador na lista pendente de acordo com a sua organização.

    g. Selecione **Adicionar**.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico AlertOps no painel de acesso, deve ser automaticamente sessão iniciada no AlertOps para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)