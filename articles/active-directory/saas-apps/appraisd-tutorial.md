---
title: 'Tutorial: Integração do Active Directory do Azure com Appraisd | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561205"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Tutorial: Integrar Appraisd com o Azure Active Directory

Neste tutorial, irá aprender como integrar Appraisd com o Azure Active Directory (Azure AD). Quando integrar Appraisd com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Appraisd.
* Permita que os utilizadores ser automaticamente sessão iniciada para Appraisd com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Appraisd início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta Appraisd **SP e IDP** iniciada SSO.

## <a name="adding-appraisd-from-the-gallery"></a>Adicionando Appraisd da Galeria

Para configurar a integração do Appraisd com o Azure AD, terá de adicionar Appraisd a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Appraisd** na caixa de pesquisa.
1. Selecione **Appraisd** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com Appraisd com um utilizador de teste **B. Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Appraisd.

Para configurar e testar o SSO do Azure AD com Appraisd, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar Appraisd](#configure-appraisd)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Simon B.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Simon B. utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Appraisd](#create-appraisd-test-user)**  para ter um equivalente de Simon B. no Appraisd que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Appraisd** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, a aplicação está pré-configurada e os URLs necessários já estão previamente preenchidos com o Azure. O utilizador tem de guardar a configuração ao clicar no botão Save e execute os seguintes passos:

    a. Clique em **definir URLs adicionais**.

    b. Na **estado de reencaminhamento** caixa de texto, escreva um URL: `<TENANTCODE>`

    c. Se desejar configurar a aplicação no **SP** iniciadas pelo modo, na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Obter o valor de URL de início de sessão e estado de reencaminhamento real na página de configuração de SSO Appraisd do que é explicada mais tarde no tutorial.

1. Aplicação de Appraisd espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Aplicação Appraisd espera **nameidentifier** seja mapeado com **user.mail**, por isso terá de editar o mapeamento do atributo clicando no **editar** ícone e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar Appraisd** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Configurar Appraisd

1. Para automatizar a configuração no Appraisd, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **Appraisd configuração** irá direcioná-lo para o aplicativo Appraisd. A partir daí, forneça as credenciais de administrador a iniciar sessão em Appraisd. A extensão de navegador automaticamente irá configurar o aplicativo para e automatizar passos 3 a 7.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se desejar configurar manualmente o Appraisd, abra uma nova janela de browser e inicie sessão no site da sua empresa Appraisd como administrador e execute os seguintes passos:

4. No canto superior direito da página, clique em **definições** ícone, em seguida, navegue até à **configuração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Do lado esquerdo do menu, clique em **SAML início de sessão único**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Sobre o **SAML 2.0 Single Sign-On configuração** página, execute os seguintes passos:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Cópia a **estado de reencaminhamento do padrão** valor e cole-o na **estado de reencaminhamento** caixa de texto no **configuração básica de SAML** no portal do Azure.

    b. Copiar o **URL de início de sessão iniciadas pelo serviço** valor e cole-o na **URL de início de sessão** caixa de texto no **configuração básica de SAML** no portal do Azure.

7. Desloque para baixo na mesma página sob **identificar utilizadores**, execute os seguintes passos:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Na **URL fornecedor de identidade única Sign-On** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure e clique em **guardar**.

    b. Na **URL de emissor do fornecedor de identidade** caixa de texto, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure e clique em **guardar**.

    c. No bloco de notas, abra o certificado com codificação base 64 que transferiu a partir do portal do Azure, copie o seu conteúdo e, em seguida, cole-o para o **certificado X.509** caixa e clique em **guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Simon B.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `B. Simon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Simon B. utilizar o Azure início de sessão único ao conceder acesso para Appraisd.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Appraisd**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B. Simon** a partir da lista de utilizadores, em seguida, clique no **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-appraisd-test-user"></a>Criar utilizador de teste Appraisd

Para ativar o Azure AD os utilizadores iniciam sessão Appraisd, eles têm de ser aprovisionados em Appraisd. Appraisd, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Appraisd como um administrador de segurança.

2. No canto superior direito da página, clique em **definições** ícone, em seguida, navegue até à **Centro de administração**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na barra de ferramentas na parte superior da página, clique em **pessoas**, em seguida, navegue até à **adicionar um novo utilizador**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Sobre o **adicionar um novo utilizador** página, execute os seguintes passos:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **Apelido** texto, digite o apelido do utilizador, como **simon**.

    c. Na **E-Mail** texto, introduza o e-mail do utilizador, como `B. Simon@contoso.com`.

    d. Clique em **Adicionar utilizador**.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico Appraisd no painel de acesso, deve ser automaticamente sessão iniciada no Appraisd para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
