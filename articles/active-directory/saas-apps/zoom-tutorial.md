---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Zoom Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Zoom.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.openlocfilehash: 6b4484b9bb235b24d2b733c329b52ab6b04be183
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006426"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Tutorial: Azure Ative Directory integração única (SSO) com Zoom

Neste tutorial, você vai aprender a integrar Zoom com Azure Ative Direy (Azure AD). Quando integrar o Zoom com AZure AD, pode:

* Controlo em Azure AD que tem acesso ao Zoom.
* Ative os seus utilizadores a serem automaticamente inscritos no Zoom com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Faça o zoom de uma única subscrição (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Zoom suporta **SP** iniciado SSO e 
* O Zoom suporta o fornecimento [ **automatizado** do utilizador.](./zoom-provisioning-tutorial.md)

## <a name="adding-zoom-from-the-gallery"></a>Adicionar Zoom da galeria

Para configurar a integração do Zoom em Azure AD, precisa adicionar Zoom da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Zoom** na caixa de pesquisa.
1. Selecione **Zoom** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Configurar e testar Azure AD único sinal para zoom

Configure e teste Azure AD SSO com Zoom usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Zoom.

Para configurar e testar o Azure AD SSO com Zoom, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure Zoom SSO](#configure-zoom-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Zoom test user](#create-zoom-test-user)** - para ter uma contraparte de B.Simon in Zoom que está ligada à representação AD AD do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Zoom,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<companyname>.zoom.us`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `<companyname>.zoom.us`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<companyname>.zoom.us`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do Cliente Zoom](https://support.zoom.us/hc/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **'Configurar' Zoom,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

> [!NOTE]
> Para aprender a configurar o papel na Ad Azure, consulte [Configure a alegação de papel emitida no token SAML para aplicações empresariais](../develop/active-directory-enterprise-app-role-management.md).

> [!NOTE]
> Zoom pode esperar uma reivindicação de grupo na carga útil DAL. Se criou algum grupo, contacte a [equipa de suporte](https://support.zoom.us/hc/) do Zoom Client com as informações do grupo para que possam configurar as informações do grupo no seu final. Também precisa de fornecer o ID do Objeto para [a equipa de suporte do Cliente Zoom](https://support.zoom.us/hc/) para que possam configurar o ID do objeto na sua extremidade. Para obter o ID do objeto, consulte [o Zoom Configurante com Azure](https://support.zoom.us/hc/articles/115005887566).

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Zoom.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Zoom**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-zoom-sso"></a>Configurar zoom SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Zoom como administrador.

2. Clique no **separador 'Sign-on' único.**

    ![Separador único de inscrição](./media/zoom-tutorial/zoom-sso1.png "Início de sessão único")

3. Clique no **separador Controlo de Segurança** e, em seguida, vá para as definições **de 'Sign-On' único.**

4. Na secção Single Sign-On, execute os seguintes passos:

    ![Secção de inscrição única](./media/zoom-tutorial/zoom-sso2.png "Início de sessão único")

    a. Na caixa de texto **URL de página de início de sessão,** cole o valor do URL de **login** que copiou do portal Azure.

    b. Para obter o valor **de URL da página de assinatura,** você precisa ir ao portal Azure e clicar no **Azure Ative Directory** à esquerda e depois navegar para **as inscrições da App.**

    ![O botão Azure Ative Directory](./media/zoom-tutorial/appreg.png)

    c. Clique em **Pontos finais**

    ![O botão ponto final](./media/zoom-tutorial/endpoint.png)

    d. Copie o **PONTO FINAL DE ASSINATURA SAML-P** e **cole-o** na caixa de texto URL da página de assinatura.

    ![O botão ponto de ponta de cópia](./media/zoom-tutorial/endpoint1.png)

    e. Abra o certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto do **certificado de fornecedor de identidade.**

    f. Na caixa de texto **emitente,** cole o valor do **Identificador AD AZure** que copiou do portal Azure. 

    exemplo, Clique em **Guardar Alterações**.

    > [!NOTE]
    > Para mais informações, visite a documentação do zoom [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Criar utilizador de teste de zoom

O objetivo desta secção é criar um utilizador chamado B.Simon in Zoom. O Zoom suporta o fornecimento automático do utilizador, que é por defeito ativado. Pode encontrar mais detalhes [aqui](./zoom-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de [suporte do Cliente Zoom](https://support.zoom.us/hc/)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Zoom no Painel de Acesso, deverá ser automaticamente inscrito no Zoom para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente zoom com Azure AD](https://aad.portal.azure.com/)