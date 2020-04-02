---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com netvision Compas [ Integração de diretórios ativos de Azure) com a Netvision Compas [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Netvision Compas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a56f44c5-dc08-4c7c-ad20-b6e7127deb2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3015ea26d81505c4f058846dbcb3b7858f79267
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520132"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netvision-compas"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com netvision Compas

Neste tutorial, você vai aprender a integrar netvision Compas com O Diretório Ativo Azure (Azure AD). Quando integrar a Netvision Compas com o Azure AD, pode:

* Controlo em Azure AD que tem acesso à Netvision Compas.
* Ative que os seus utilizadores sejam automaticamente inscritos na Netvision Compas com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Netvision Compas compas única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Netvision Compas suporta **SP e IDP** iniciadoS SSO
* Assim que configurar o Netvision Compas, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)


## <a name="adding-netvision-compas-from-the-gallery"></a>Adicionando Netvision Compas da galeria

Para configurar a integração da Netvision Compas no Azure AD, é necessário adicionar o Netvision Compas da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Netvision Compas** na caixa de pesquisa.
1. Selecione **Netvision Compas** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netvision-compas"></a>Configure e teste Azure AD single sign-on para Netvision Compas

Configure e teste Azure AD SSO com Netvision Compas utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Netvision Compas.

Para configurar e testar o Azure AD SSO com a Netvision Compas, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Netvision Compas SSO](#configure-netvision-compas-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Configure o utilizador de teste Netvision Compas](#configure-netvision-compas-test-user)** - para ter uma contrapartida de B.Simon em Netvision Compas que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Netvision Compas,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<TENANT>.compas.cloud/Identity/Saml20`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<TENANT>.compas.cloud/Identity/Auth/AssertionConsumerService`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de suporte ao [Cliente Netvision Compas](mailto:contact@net.vision) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único registo com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Download** para descarregar o ficheiro de metadados e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)



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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Netvision Compas.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Netvision Compas**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-netvision-compas-sso"></a>Configure Netvision Compas SSO

Nesta secção, ativa o SAML SSO no **Netvision Compas**.
1. Inicie sessão no **Netvision Compas** utilizando uma conta administrativa e aceda à área administrativa.

    ![Área de administração](media/netvision-compas-tutorial/admin.png)

1. Localize a área **do Sistema** e selecione **Fornecedores de Identidade**.

    ![IdPs de administração](media/netvision-compas-tutorial/admin-idps.png)

1. Selecione a ação **Add** para registar o Azure AD como um novo IDP.

    ![Adicionar IDP](media/netvision-compas-tutorial/idps-add.png)

1. Selecione **SAML** para o **tipo Fornecedor**.
1. Introduza valores significativos para o **nome do display** e os campos **descrição.**
1. Atribuir os utilizadores **do Netvision Compas** ao IDP selecionando a partir da lista de **utilizadores disponíveis** e, em seguida, selecionando o botão **adicionado selecionado.** Os utilizadores também podem ser atribuídos ao IDP enquanto seguem o procedimento de provisionamento.
1. Para obter a opção **Metadata** SAML clique no botão **Escolha Ficheiro** e selecione o ficheiro de metadados previamente guardado no seu computador.
1. Clique em **Guardar**.

    ![Editar IDP](media/netvision-compas-tutorial/idp-edit.png)


### <a name="configure-netvision-compas-test-user"></a>Configure Netvision Compas test user

Nesta secção, configura um utilizador existente no **Netvision Compas** para utilizar o Azure AD para SSO.
1. Siga o procedimento de provisionamento de utilizadores **da Netvision Compas,** conforme definido pela sua empresa ou edite uma conta de utilizador existente.
1. Ao definir o perfil do utilizador, certifique-se de que o endereço **de e-mail (pessoal)** do utilizador corresponde ao nome de utilizador da AD Azure: username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.

    ![Editar utilizador](media/netvision-compas-tutorial/user-config.png)

Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de ad do Azure AD.

### <a name="using-the-access-panel-idp-initiated"></a>Utilizando o Painel de Acesso (IDP iniciado).

Quando clicar no azulejo Netvision Compas no Painel de Acesso, deve ser automaticamente inscrito no Netvision Compas para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

### <a name="directly-accessing-netvision-compas-sp-initiated"></a>Acesso direto à Netvision Compas (SP iniciado).

1. Aceda ao URL **Netvision Compas.** Por exemplo, `https://tenant.compas.cloud`.
1. Introduza o nome de utilizador **netvision Compas** e selecione **Next**.

    ![Utilizador de início de sessão](media/netvision-compas-tutorial/login-user.png)

1. **(opcional)** Se o utilizador for atribuído a vários IDPs dentro do **Netvision Compas,** é apresentada uma lista de IDPs disponíveis. Selecione o IDP Azure AD configurado anteriormente em **Netvision Compas**.

    ![Escolha de login](media/netvision-compas-tutorial/login-choose.png)

1. É redirecionado para a AD Azure para realizar a autenticação. Uma vez autenticado com sucesso, deverá ser automaticamente inscrito na **Netvision Compas** para a qual configura o SSO.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente netvision Compas com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
