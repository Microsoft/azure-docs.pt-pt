---
title: 'Tutorial: Integração do Active Directory do Azure ao Freshservice | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ao Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c87b23c338788804be22639f73acfb61ce8d6973
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989394"
---
# <a name="tutorial-integrate-freshservice-with-azure-active-directory"></a>Tutorial: Integrar ao Freshservice com o Azure Active Directory

Neste tutorial, irá aprender como integrar ao Freshservice com o Azure Active Directory (Azure AD). Quando integrar ao Freshservice com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Freshservice.
* Permita que os utilizadores ser automaticamente sessão iniciada ao Freshservice com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Ao Freshservice início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta ao Freshservice **SP** iniciada SSO

## <a name="adding-freshservice-from-the-gallery"></a>Adicionando ao Freshservice da Galeria

Para configurar a integração ao Freshservice com o Azure AD, terá de adicionar ao Freshservice a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **ao Freshservice** na caixa de pesquisa.
1. Selecione **ao Freshservice** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD ao Freshservice com um utilizador de teste **Eduarda Almeida**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado ao Freshservice.

Para configurar e testar o SSO do Azure AD ao Freshservice, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO ao Freshservice](#configure-freshservice-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste ao Freshservice](#create-freshservice-test-user)**  - para ter um equivalente da Eduarda Almeida ao Freshservice que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **ao Freshservice** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<democompany>.freshservice.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente ao Freshservice](https://support.freshservice.com/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. Na **certificado de assinatura SAML** secção, clique em **editar** botão para abrir **certificado de assinatura SAML** caixa de diálogo.

    ![Editar o certificado de assinatura de SAML](common/edit-certificate.png)

1. Na **certificado de assinatura SAML** secção, copie a **Thumbprint** e guarde-o no seu computador.

    ![Copie o valor do Thumbprint](common/copy-thumbprint.png)

1. Sobre o **configurar ao Freshservice** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-freshservice-sso"></a>Configurar o SSO ao Freshservice

1. Para automatizar a configuração ao Freshservice, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **ao Freshservice de configuração** irá direcioná-lo para a aplicação ao Freshservice. A partir daí, forneça as credenciais de administrador a iniciar sessão em ao Freshservice. A extensão do browser irá configurar o aplicativo para e automatizar passos 3 a 6 automaticamente.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se pretender configurar ao Freshservice manualmente, abra uma nova janela de browser e inicie sessão no site da sua empresa ao Freshservice como administrador e execute os seguintes passos:

4. No menu na parte superior, clique em **administrador**.

    ![Admin](./media/freshservice-tutorial/ic790814.png "Admin")

5. Na **Portal do cliente**, clique em **segurança**.

    ![Segurança](./media/freshservice-tutorial/ic790815.png "segurança")

6. Na **segurança** secção, execute os seguintes passos:

    ![Início de sessão único](./media/freshservice-tutorial/ic790816.png "início de sessão único")

    a. Comutador **início de sessão único**.

    b. Selecione **SAML SSO**.

    c. Na **URL de início de sessão de SAML** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    d. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    e. Na **impressão digital do certificado de segurança** caixa de texto, colar a **THUMBPRINT** valor do certificado que copiou do portal do Azure.

    f. Clique em **Guardar**

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

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao Freshservice.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **ao Freshservice**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-freshservice-test-user"></a>Criar utilizador de teste ao Freshservice

Permitir que os utilizadores do Azure AD entrar para ao FreshService, tem de ser aprovisionados ao FreshService. No caso de ao FreshService, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **ao FreshService** site da empresa como administrador.

2. No menu na parte superior, clique em **administrador**.

    ![Admin](./media/freshservice-tutorial/ic790814.png "Admin")

3. Na **gestão de utilizadores** secção, clique em **os solicitantes**.

    ![Os solicitantes](./media/freshservice-tutorial/ic790818.png "os solicitantes")

4. Clique em **autor do pedido novo**.

    ![Os solicitantes novos](./media/freshservice-tutorial/ic790819.png "os solicitantes de novo")

5. Na **autor do pedido novo** secção, execute os seguintes passos:

    ![Novo autor do pedido](./media/freshservice-tutorial/ic790820.png "novo autor do pedido")  

    a. Introduza o **nome próprio** e **E-Mail** atributos de uma conta válida do Azure Active Directory que pretende aprovisionar em caixas de texto relacionadas.

    b. Clique em **Guardar**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory obtém um e-mail, incluindo uma ligação para confirmar a conta até se tornar Active Directory
    >  

> [!NOTE]
> Pode utilizar quaisquer outras ao FreshService utilizador conta criação ferramentas ou APIs fornecidas ao FreshService para aprovisionar contas de utilizador do AAD.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico ao Freshservice no painel de acesso, deve ser automaticamente sessão iniciada no ao Freshservice para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
