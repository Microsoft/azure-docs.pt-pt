---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Nuclino Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Nuclino.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 4b84cbe57c40b12ae01952963b324b8bcfdcfd2e
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945486"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nuclino"></a>Tutorial: Azure Ative Directory integração única (SSO) com Nuclino

Neste tutorial, você vai aprender a integrar Nuclino com Azure Ative Directory (Azure AD). Quando integrar Nuclino com AZure AD, pode:

* Controlo em Azure AD que tem acesso a Nuclino.
* Ative os seus utilizadores a serem automaticamente inscritos no Nuclino com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por Nuclino single-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Nuclino apoia **SP e IDP** iniciaM SSO
* Nuclino suporta **provisão do** utilizador Just In Time

## <a name="adding-nuclino-from-the-gallery"></a>Adicionar Nuclino da galeria

Para configurar a integração de Nuclino em Azure AD, você precisa adicionar Nuclino da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** **digite Nuclino** na caixa de pesquisa.
1. Selecione **Nuclino** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nuclino"></a>Configurar e testar Azure AD único sinal para Nuclino

Configure e teste Azure AD SSO com Nuclino usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em Nuclino.

Para configurar e testar o Azure AD SSO com Nuclino, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Nuclino SSO](#configure-nuclino-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Nuclino test user](#create-nuclino-test-user)** - para ter uma contraparte de B.Simon em Nuclino que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Nuclino,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real da secção **de Autenticação,** que é explicado mais tarde neste tutorial.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente Nuclino](mailto:contact@nuclino.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. A aplicação Nuclino espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

7. Além de acima, a aplicação Nuclino espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome |  Atributo de origem|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | utilizador.sobrenome |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Nuclino,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Nuclino.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Nuclino**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-nuclino-sso"></a>Configurar Nuclino SSO

1. Para automatizar a configuração dentro do Nuclino, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Nuclino** irá direcioná-lo para a aplicação Nuclino. A partir daí, forneça as credenciais de administração para assinar em Nuclino. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Nuclino manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Nuclino como administrador e execute os seguintes passos:

4. Clique no **ICON**.

    ![Screenshot que mostra o ícone "Menu" selecionado ao lado de "Azure A D S S O".](./media/nuclino-tutorial/configure1.png)

5. Clique no **Azure AD SSO** e selecione **as definições** de Equipa a partir do dropdown.

    ![Screenshot que mostra o drop-down "Azure A D S S O" com "Definições de equipa" selecionadas.](./media/nuclino-tutorial/configure2.png)

6. Selecione **autenticação** do painel de navegação à esquerda.

    ![Screenshot que mostra "Autenticação" selecionada.](./media/nuclino-tutorial/configure3.png)

7. Na secção **autenticação,** execute os seguintes passos:

    ![Configuração nuclino](./media/nuclino-tutorial/configure4.png)

    a. Selecione **um único sinal de SSO baseado em SAML (SSO)**.

    b. Copie o **URL ACS (É necessário copiar e colar isto ao seu fornecedor SSO)** e cole-o na caixa de texto URL de **resposta** da secção **de Configuração SAML Básica** no portal Azure.

    c. Copiar **o ID da Entidade (É necessário copiar e colar isto ao seu fornecedor SSO)** valor e colá-lo na caixa de texto **identifier** da secção **de Configuração SAML Básica** no portal Azure.

    d. Na caixa de texto **SSO URL,** cole o valor URL de **login** que copiou a partir do portal Azure.

    e. Na caixa de texto ID da **Entidade,** cole o valor **identificador Azure AD** que copiou do portal Azure.

    f. Abra o seu ficheiro **Certificado (Base64)** descarregado no Bloco de Notas. Copie o conteúdo da sua pasta e, em seguida, cole-o na caixa de texto **do certificado público.**

    exemplo, CLIQUE **EM GUARDAR ALTERAÇÕES.**

### <a name="create-nuclino-test-user"></a>Criar utilizador de teste Nuclino

Nesta secção, um utilizador chamado B.Simon é criado em Nuclino. Nuclino suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Nuclino, um novo é criado após a autenticação.

> [!Note]
> Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte da Nuclino](mailto:contact@nuclino.com).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Nuclino no Painel de Acesso, deverá ser automaticamente inscrito no Nuclino para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Nuclino com Azure AD](https://aad.portal.azure.com/)

