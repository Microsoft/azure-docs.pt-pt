---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com A PureHR [ HSH) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o PureHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56049e1b1253cd749a8e16061957c6b5b8786e3c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72594539"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com A PureHR

Neste tutorial, você vai aprender a integrar o PureHR com o Azure Ative Directory (Azure AD). Quando integrar a PureHR com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a PureHR.
* Permita que os seus utilizadores sejam automaticamente inscritos na PureHR com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por assinatura exclusivamente HR (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* PuramenteHR apoia **SP e IDP** iniciadoS SSO
* PuramenteHR suporta o fornecimento de utilizadores **justo no tempo**

## <a name="adding-purelyhr-from-the-gallery"></a>Adicionando PureHR da galeria

Para configurar a integração da PureHR em Azure AD, precisa de adicionar o PureHR da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite PureHR** na caixa de pesquisa.
1. **Selecione PureHR** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Configure e teste Azure AD único signo para PureHR

Configure e teste Azure AD SSO com PureHR utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em PureHR.

Para configurar e testar o Azure AD SSO com a PureHR, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure PureHR SSO](#configure-purelyhr-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * Criar um utilizador de **[teste PureHR](#create-purelyhr-test-user)** - para ter uma contraparte de B.Simon em PureHR que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Puramenthr,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<companyID>.purelyhr.com/sso-consume`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Resposta real e URL de Sinal. Contacte a equipa de apoio ao [Cliente Da PureHR](https://support.purelyhr.com/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar PureHR,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso à PureHR.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione PureHR**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-purelyhr-sso"></a>Configure PuramenteHR SSO

1. Para automatizar a configuração dentro do PureHR, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar PureHR** irá direcioná-lo para a aplicação PureHR. A partir daí, forneça as credenciais de administração para assinar no PureHR. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-5.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o PureHR manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa PureHR como administrador e execute os seguintes passos:

1. Abra o **Dashboard** a partir das opções na barra de ferramentas e clique em **Definições SSO**.

1. Colar os valores nas caixas descritos abaixo...

    ![Configurar um único sinal](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Abra o **Certificado (Bas64)** descarregado do portal Azure em bloco de notas e copie o valor do certificado. Colhe o valor copiado na caixa de **certificadoS X.509.**

    b. Na caixa **DEURL IDP Emitidar,** cola o **identificador Azure AD** copiado do portal Azure.

    c. Na caixa **DEURL idp Endpoint,** colá o URL de **Login** copiado do portal Azure. 

    d. Verifique a caixa de verificação **De Utilizadores De Criação Automática** para ativar o fornecimento automático de utilizadores em PureHR.

    e. Clique em **Guardar Alterações** para guardar as definições.

### <a name="create-purelyhr-test-user"></a>Criar o utilizador de teste PureHR

Este passo geralmente não é necessário, uma vez que a aplicação suporta apenas no tempo de fornecimento do utilizador. Se o fornecimento automático do utilizador não estiver ativado, a criação manual do utilizador pode ser feita como descrito abaixo.

Inscreva-se no site da empresa Velpic SAML como administrador e execute os seguintes passos:

1. Clique no separador 'Gerir' e vá para a secção Utilizadores e, em seguida, clique no novo botão para adicionar utilizadores.

    ![adicionar utilizador](./media/velpicsaml-tutorial/velpic_7.png)

2. Na página de diálogo **"Criar novo utilizador",** execute os seguintes passos.

    ![utilizador](./media/velpicsaml-tutorial/velpic_8.png)

    a. Na caixa de texto **First Name,** digite o primeiro nome de B.

    b. Na caixa de texto **"Apelido",** escreva o último nome de Simon.

    c. Na caixa de texto nome do **utilizador,** digite o nome de utilizador de B.Simon.

    d. Na caixa de texto **por e-mail,** digite o endereço de e-mail da B.Simon@contoso.com conta.

    e. O resto da informação é opcional, pode preenchê-la se necessário.

    f. Clique em **GUARDAR**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo PureHR no Painel de Acesso, deve ser automaticamente inscrito no PureHR para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente PureHR com Azure AD](https://aad.portal.azure.com/)
