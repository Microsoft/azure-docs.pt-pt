---
title: 'Tutorial: Integração do Active Directory do Azure com a forma como fazemos | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e a forma como fazemos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310409"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Tutorial: Integrar a forma como Trabalhamos com o Azure Active Directory

Neste tutorial, irá aprender como integrar a forma como fazemos com o Azure Active Directory (Azure AD). Quando integrar a forma como fazemos com o Azure AD, pode:

* Controlar no Azure AD, quem tem acesso a forma como fazemos.
* Permita que os utilizadores ser automaticamente sessão iniciada de forma podemos fazer com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Forma podemos início de sessão único (SSO) ativar a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste.

* Oferece suporte a forma como fazemos **SP** iniciada SSO
* Oferece suporte a forma como fazemos **Just In Time** aprovisionamento de utilizadores

## <a name="adding-way-we-do-from-the-gallery"></a>Adicionando a forma como fazemos da Galeria

Para configurar a integração de forma podemos fazer com o Azure AD, terá de adicionar a maneira que fazemos em partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **forma fazemos** na caixa de pesquisa.
1. Selecione **forma fazemos** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com a forma como fazemos com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na forma como fazemos.

Para configurar e testar o SSO do Azure AD com a forma como fazemos, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar a forma como podemos fazer SSO](#configure-way-we-do-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar de forma podemos testar utilizador](#create-way-we-do-test-user)**  - para ter um equivalente da Eduarda Almeida na forma como fazemos isso está ligada à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), no **forma fazemos** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de forma podemos fazer cliente](mailto:support@waywedo.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (bruto)** e selecione **transferir**para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificateraw.png)

1. Sobre o **configurar a forma como fazemos** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Configurar a forma como Trabalhamos SSO

1. Para automatizar a configuração na forma como fazemos, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão para o navegador, clique em **configuração forma fazemos** irá direcioná-lo para a aplicação de forma que fazemos. A partir daí, forneça as credenciais de administrador para iniciar sessão na forma como fazemos. A extensão do browser irá configurar o aplicativo para e automatizar passos 3 a 6 automaticamente.

    ![Configuração do programa de configuração](common/setup-sso.png)

1. Se quiser configurar a forma como fazemos manualmente, abra uma nova janela de browser e inicie sessão no seu site de empresa de forma podemos fazer como administrador e execute os seguintes passos:

1. Clique nas **ícone de pessoa** no canto superior direito de qualquer página na forma como fazemos, em seguida, clique em **conta** no menu pendente.

    ![Conta de fazemos forma](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Clique nas **ícone de menu** para abrir o menu de navegação de push e clique em **início de sessão único**.

    ![Forma como fazemos único](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Sobre o **configuração do início de sessão única** página, execute os seguintes passos:

    ![Forma como fazemos guardar](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Clique nas **ativar o início de sessão único** alternar para **Sim** para ativar o início de sessão único.

    b. Na **nome de início de sessão único** caixa de texto, introduza o seu nome.

    c. Na **ID de entidade** caixa de texto, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    d. Na **URL de SSO SAML** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    e. Carregue o certificado ao clicar o **selecionar** junto a **certificado**.

    f. **Definições opcionais** -
    
    * Permitir palavras-passe - quando esta opção está desativada, a palavra-passe regular funciona de forma podemos fazer para que os utilizadores só podem utilizar o início de sessão único.

    * Ativar aprovisionamento automático - quando esta opção estiver ativada, o endereço de e-mail utilizado para início de sessão será automaticamente comparado com a lista de utilizadores na forma como fazemos. Se o endereço de e-mail não corresponder a um utilizador ativo na forma como fazemos, ele adiciona automaticamente uma nova conta de utilizador da pessoa a iniciar sessão, solicitando todas as informações em falta.

      > [!NOTE]
      > Os utilizadores adicionados através do início de sessão único são adicionados como usuários gerais e não estão atribuídos uma função do sistema. Um administrador pode entrar e modificar a sua função de segurança como um editor ou administrador e também pode atribuir um ou vários organograma funções.

    g. Clique em **guardar** para manter as suas definições.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado B.Simon.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar B.Simon a utilizar do Azure-início de sessão único, conceder acesso à forma como fazemos.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **forma fazemos**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-way-we-do-test-user"></a>Criar utilizador de teste de forma podemos fazer

Nesta secção, um usuário chamado Eduarda Almeida é criado na forma como fazemos. Forma como fazemos suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir na forma como fazemos, é criado um novo após a autenticação.

> [!Note]
> Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de forma podemos fazer cliente](mailto:support@waywedo.com).

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico de forma que fazemos no painel de acesso, deve ser automaticamente conectado para a forma como fazemos para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)