---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a PureHR Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PureHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 48db08f856407b2ceba32798ed0e39eab967b72d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88553317"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Tutorial: Azure Ative Directory integração única (SSO) com PureHR

Neste tutorial, você vai aprender a integrar PureHR com Azure Ative Directory (Azure AD). Quando integras o PureHR com a AD Azure, podes:

* Controlo em Azure AD que tem acesso a PureHR.
* Permitir que os seus utilizadores sejam automaticamente inscritos na PureHR com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura única (SSO) ativada pela PureHR.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* PureHR apoia **SP e IDP** iniciado SSO
* A PureHR suporta o fornecimento do utilizador **Just In Time**

## <a name="adding-purelyhr-from-the-gallery"></a>Adicionando PureHR da galeria

Para configurar a integração da PureHR em Azure AD, precisa adicionar PureHR da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite PureHR** na caixa de pesquisa.
1. Selecione **PureHR** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Configurar e testar Azure AD único sinal para PureHR

Configure e teste Azure AD SSO com PureHR usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em PureHR.

Para configurar e testar a Azure AD SSO com a PureHR, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o PureHR SSO](#configure-purelyhr-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create PureHR test user](#create-purelyhr-test-user)** - ter uma contraparte de B.Simon em PureHR que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **PureHR,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://<companyID>.purelyhr.com/sso-consume`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e Sign-On URL. Contacte [a equipa de suporte do Cliente DaMoscr para](https://support.purelyhr.com/) obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar PureHR,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a PureHR.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **PureHR**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-purelyhr-sso"></a>Configurar Puramente SHR SSO

1. Para automatizar a configuração dentro do PureHR, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar O PureHR** irá direcioná-lo para a aplicação PureHR. A partir daí, forneça as credenciais de administração para assinar em PureHR. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar a PureHR manualmente, abra uma nova janela do navegador web e inscreva-se no seu site da empresa PureHR como administrador e execute os seguintes passos:

1. Abra o **painel de instrumentos** a partir das opções na barra de ferramentas e clique em **Definições SSO**.

1. Cole os valores nas caixas como descrito abaixo...

    ![Configurar Sign-On Individuais](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Abra o **Certificado (Bas64)** descarregado do portal Azure em bloco de notas e copie o valor do certificado. Cole o valor copiado na caixa **de certificado X.509.**

    b. Na caixa URL do **emitente Idp,** cole o **identificador Azure AD** copiado do portal Azure.

    c. Na caixa **DE URL do Idp Endpoint,** cole o URL de **login** copiado do portal Azure. 

    d. Verifique a caixa de verificação **do Utilizador De Criação Automática** para permitir o provisionamento automático do utilizador em PureHR.

    e. Clique **em Guardar Alterações** para guardar as definições.

### <a name="create-purelyhr-test-user"></a>Criar utilizador de testes PureHR

Este passo não é geralmente necessário, uma vez que a aplicação suporta apenas a tempo de fornecimento do utilizador. Se o fornecimento automático do utilizador não estiver ativado, a criação manual do utilizador pode ser feita conforme descrito abaixo.

Inscreva-se no site da sua empresa Velpic SAML como administrador e execute os seguintes passos:

1. Clique no separador Gerir e ir à secção dos Utilizadores e, em seguida, clique em Novo botão para adicionar utilizadores.

    ![adicionar utilizador](./media/velpicsaml-tutorial/velpic_7.png)

2. Na página de diálogo **"Criar Novo Utilizador",** execute os seguintes passos.

    ![utilizador](./media/velpicsaml-tutorial/velpic_8.png)

    a. Na caixa de texto **Name First,** digite o primeiro nome de B.

    b. Na caixa de texto **do Último Nome,** escreva o último nome de Simon.

    c. Na caixa de texto **do Nome do Utilizador,** digite o nome de utilizador de B.Simon.

    d. Na caixa de sms **do Email,** digite o endereço de e-mail da B.Simon@contoso.com conta.

    e. O resto da informação é opcional, pode preenchê-la se necessário.

    f. Clique **em SAVE**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo PureHR no Painel de Acesso, deverá ser automaticamente inscrito no PureHR para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente PureHR com Azure AD](https://aad.portal.azure.com/)
