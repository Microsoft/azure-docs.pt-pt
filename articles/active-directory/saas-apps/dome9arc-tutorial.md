---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com Check Point CloudGuard Dome9 Arc [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e o Check Point CloudGuard Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b46ac34db21576c7e2de2271a468e3e782ff6aa9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73885358"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-cloudguard-dome9-arc"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com check point CloudGuard Dome9 Arc

Neste tutorial, você aprenderá a integrar check Point CloudGuard Dome9 Arc com Azure Ative Directory (Azure AD). Quando integrar o Check Point CloudGuard Dome9 Arc com AD Azure, pode:

* Controle em Azure AD que tem acesso a Check Point CloudGuard Dome9 Arc.
* Ative que os seus utilizadores sejam automaticamente inscritos no Check Point CloudGuard Dome9 Arc com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Verifique a subscrição de um único sinal (SSO) ativado pelo Point CloudGuard Dome9 Arc.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Check Point CloudGuard Dome9 Arc suporta **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Adicionar Check Point CloudGuard Dome9 Arc da galeria

Para configurar a integração do Check Point CloudGuard Dome9 Arc em Azure AD, você precisa adicionar Check Point CloudGuard Dome9 Arc da galeria à sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Check Point CloudGuard Dome9 Arc** na caixa de pesquisa.
1. Selecione **Check Point CloudGuard Dome9 Arc** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-check-point-cloudguard-dome9-arc"></a>Configure e teste Azure AD único sinal para Check Point CloudGuard Dome9 Arc

Configure e teste Azure AD SSO com Check Point CloudGuard Dome9 Arc utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Check Point CloudGuard Dome9 Arc.

Para configurar e testar o Azure AD SSO com o Check Point CloudGuard Dome9 Arc, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Verifique o Ponto de Verificação CloudGuard Dome9 Arc SSO](#configure-check-point-cloudguard-dome9-arc-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. Crie o utilizador de **[teste Check Point CloudGuard Dome9 Arc](#create-check-point-cloudguard-dome9-arc-test-user)** - para ter uma contrapartida de B.Simon no Check Point CloudGuard Dome9 Arc que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Check Point CloudGuard Dome9 Arc,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** digite um URL:`https://secure.dome9.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://secure.dome9.com/sso/saml/<yourcompanyname>`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Resposta real e URL de Sign-on. Você receberá `<company name>` o valor da secção **Configure Check Point CloudGuard Dome9 Arc SSO,** que é explicado mais tarde no tutorial. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Check Point CloudGuard Dome9 Arc aplicação espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Check Point CloudGuard Dome9 Arc espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.
    
    | Nome |  Atributo fonte|
    | ---------------| --------------- |
    | membro de | user.atribuídos |

    >[!NOTE]
    >Clique [aqui](https://docs.microsoft.com/azure/active-directory/saas-apps/apptio-tutorial) para saber como criar papéis em Azure AD.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na **secção 'Check Point CloudGuard Dome9 Arc',** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sinal de Azure, concedendo acesso ao Check Point CloudGuard Dome9 Arc.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Check Point CloudGuard Dome9 Arc**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-check-point-cloudguard-dome9-arc-sso"></a>Configure Ponto de verificação CloudGuard Dome9 Arc SSO

1. Para automatizar a configuração dentro do Check Point CloudGuard Dome9 Arc, precisa de instalar a extensão de **entrada de sinais de entrada** de My Apps Secure, clicando em instalar a **extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Ponto de Verificação CloudGuard Dome9 Arc** irá direcioná-lo para a aplicação Check Point CloudGuard Dome9 Arc. A partir daí, forneça as credenciais de administração para assinar no Check Point CloudGuard Dome9 Arc. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar manualmente o Check Point CloudGuard Dome9 Arc, abra uma nova janela do navegador web e instique-se no site da empresa Check Point CloudGuard Dome9 Arc como administrador e execute os seguintes passos:

2. Clique nas **Definições** de Perfil no canto superior direito e, em seguida, clique em **Definições de Conta**. 

    ![Verifique a configuração do arco do CloudGuard Dome9](./media/dome9arc-tutorial/configure1.png)

3. Navegue para **SSO** e, em seguida, clique em **ENABLE**.

    ![Verifique a configuração do arco do CloudGuard Dome9](./media/dome9arc-tutorial/configure2.png)

4. Na secção de Configuração SSO, execute os seguintes passos:

    ![Verifique a configuração do arco do CloudGuard Dome9](./media/dome9arc-tutorial/configure3.png)

    a. Introduza o nome da empresa na caixa de texto ID da **conta.** Este valor deve ser utilizado no **"Answer** and **Sign on** URL" mencionado na secção básica de **configuração SAML** do portal Azure.

    b. Na caixa de texto **emitente,** cola o valor do **Identificador AD Azure,** que copiou forma o portal Azure.

    c. Na caixa de texto de **url de ponto final idp,** colá o valor do URL de **Login,** que copiou para formar o portal Azure.

    d. Abra o seu certificado codificado Base64 descarregado no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do **certificado X.509.**

    e. Clique em **Guardar**.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Criar check point CloudGuard Dome9 Arc utilizador de teste

Para permitir que os utilizadores de Anúncios Azure assinem o Check Point CloudGuard Dome9 Arc, devem ser aprovisionados na aplicação. Verifique o ponto cloudGuard Dome9 Arc suporta o fornecimento just-in-time, mas para que funcione corretamente, o utilizador tem que selecionar **papel** específico e atribuir o mesmo ao utilizador.

   >[!Note]
   >Para criação de **papéis** e outros detalhes contacte a equipa de [suporte ao cliente Check Point CloudGuard Dome9 Arc](mailto:Dome9@checkpoint.com).

**Para fornecer manualmente uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa Check Point CloudGuard Dome9 Arc como administrador.

2. Clique nos **Utilizadores & Funções** e, em seguida, clique em **Utilizadores**.

    ![Adicionar Empregado](./media/dome9arc-tutorial/user1.png)

3. Clique em **ADICIONAR USER**.

    ![Adicionar Empregado](./media/dome9arc-tutorial/user2.png)

4. Na secção **Criar utilizador,** execute os seguintes passos:

    ![Adicionar Empregado](./media/dome9arc-tutorial/user3.png)

    a. Na caixa de texto **e-mail,** digite o e-mail do utilizador como B.Simon@contoso.com.

    b. Na caixa de texto **First Name,** digite o primeiro nome do utilizador como B.

    c. Na caixa de texto **Last Name,** escreva o sobrenome do utilizador como Simon.

    d. Faça **o Utilizador SSO** como **ligado**.

    e. Clique em **CRIAR**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Check Point CloudGuard Dome9 Arc no Painel de Acesso, deve ser automaticamente inscrito no Arco Check Point CloudGuard Dome9 para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Tente verificar o ponto de verificação CloudGuard Dome9 Arc com Azure AD](https://aad.portal.azure.com/)