---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Velpic SAML [ Integração de assinaturas únicas do Diretório Ativo azure) com velpic SAML [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72241567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Velpic SAML

Neste tutorial, você vai aprender a integrar velpic SAML com O Diretório Ativo Azure (Azure AD). Quando integrar a Velpic SAML com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Velpic SAML.
* Ative que os seus utilizadores sejam automaticamente inscritos na Velpic SAML com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Velpic SAML single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Velpic SAML suporta **SP** iniciado SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>Adicionando Velpic SAML da galeria

Para configurar a integração do Velpic SAML em Azure AD, você precisa adicionar Velpic SAML da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Velpic SAML** na caixa de pesquisa.
1. Selecione **Velpic SAML** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Configure e teste azure AD único sinal para Velpic SAML

Configure e teste Azure AD SSO com Velpic SAML utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Velpic SAML.

Para configurar e testar o Azure AD SSO com velpic SAML, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure velpic SAML SSO](#configure-velpic-saml-sso)** - para configurar as definições de sinal única no lado da aplicação.
    1. **[Crie o utilizador de teste Velpic SAML](#create-velpic-saml-test-user)** - para ter uma contraparte de B.Simon em Velpic SAML que esteja ligada à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Velpic SAML,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<sub-domain>.velpicsaml.net`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Por favor, note que o Sign on URL será fornecido pela equipa Velpic SAML e o valor do identificador estará disponível quando configurar o Plugin SSO no lado Velpic SAML. Você precisa copiar esse valor a partir da página de aplicação Velpic SAML e cole-lo aqui.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Velpic SAML,** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Velpic SAML.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Velpic SAML**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-velpic-saml-sso"></a>Configure Velpic SAML SSO

1. Para automatizar a configuração dentro do Velpic SAML, é necessário instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Velpic SAML** irá direcioná-lo para a aplicação Velpic SAML. A partir daí, forneça as credenciais de administração para assinar na Velpic SAML. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Velpic SAML manualmente, abra uma nova janela do navegador web e inscreva-se no seu site da empresa Velpic SAML como administrador e execute os seguintes passos:

4. Clique no separador **'Gerir'** e vá para a secção **integração** onde precisa de clicar no botão **Plugins** para criar um novo plugin para iniciar sessão.

    ![Plugin](./media/velpicsaml-tutorial/velpic_1.png)

5. Clique no botão **'Adicionar plugin'.**
    
    ![Plugin](./media/velpicsaml-tutorial/velpic_2.png)

6. Clique no azulejo **SAML** na página Add Plugin.
    
    ![Plugin](./media/velpicsaml-tutorial/velpic_3.png)

7. Introduza o nome do novo plugin SAML e clique no botão **'Adicionar'.**

    ![Plugin](./media/velpicsaml-tutorial/velpic_4.png)

8. Introduza os detalhes da seguinte forma:

    ![Plugin](./media/velpicsaml-tutorial/velpic_5.png)

    a. Na caixa de texto **Name,** digite o nome de plugin SAML.

    b. Na caixa de texto **URL emitente,** cola o **identificador Azure AD** que copiou da janela de **sinalização Configure** do portal Azure.

    c. No **Fornecedor Metadata Config** carregue o ficheiro Metadata XML que descarregou do portal Azure.

    d. Também pode optar por ativar o SAML a tempo de oprovisionamento, permitindo que a caixa de verificação **"Auto crie novos utilizadores".** Se um utilizador não existir em Velpic e esta bandeira não estiver ativada, o login do Azure falhará. Se a bandeira estiver ativada, o utilizador será automaticamente colocado em Velpic no momento do login. 

    e. Copie o **sinal único no URL** da caixa de texto e cole-o no portal Azure.
    
    f. Clique em **Guardar**.

### <a name="create-velpic-saml-test-user"></a>Criar o utilizador de teste Velpic SAML

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
    
    f. Clique em **SAVE**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

1. Quando clicar no azulejo Velpic SAML no Painel de Acesso, deverá obter uma página de login da aplicação Velpic SAML. Deve ver o botão **'Entrar com a AD Azure'** na página da placa.

    ![Plugin](./media/velpicsaml-tutorial/velpic_6.png)

1. Clique no botão **'Iniciar sessão com a AD Azure'** para iniciar sessão na Velpic utilizando a sua conta AD Azure.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente velpic SAML com Azure AD](https://aad.portal.azure.com/)

