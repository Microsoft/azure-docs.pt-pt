---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com mongoDB Cloud [ Azure Ative Diretório) integração com a Nuvem MongoDB [ MongoDB Cloud] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MongoDB Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ef392044-235b-4d80-8a33-eeba9b142849
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef8094464b428f1f46017b0d4abaac5aeb1fa428
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024534"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com MongoDB Cloud

Neste tutorial, você vai aprender a integrar mongoDB Cloud com O Diretório Ativo Azure (Azure AD). Quando integrar a Nuvem MongoDB com o Azure AD, pode:

* Controle em Azure AD que tem acesso a MongoDB Cloud, MongoDB Atlas, a comunidade MongoDB, A Universidade mongoDB e Apoio mongoDB.
* Ative que os seus utilizadores sejam automaticamente inscritos na MongoDB Cloud com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma organização MongoDB Cloud que está ativada para um único sign-on (SSO), você pode se inscrever para um [cluster gratuito](https://www.mongodb.com/cloud)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* MongoDB Cloud suporta **SP** e **IDP** iniciado SSO.
* MongoDB Cloud suporta o fornecimento de utilizadores **Just In Time.**
* Depois de configurar a Nuvem MongoDB, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. Para mais informações, consulte Saiba como impor o controlo da [sessão com](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)o Microsoft Cloud App Security .

## <a name="add-mongodb-cloud-from-the-gallery"></a>Adicione mongoDB Cloud da galeria

Para configurar a integração da MongoDB Cloud em Azure AD, você precisa adicionar MongoDB Cloud da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Ative Directory**.
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **digite MongoDB Cloud** na caixa de pesquisa.
1. Selecione **MongoDB Cloud** a partir dos resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Configure e teste Azure AD único sign-on para MongoDB Cloud

Configure e teste Azure AD SSO com MongoDB Cloud, utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador da AD Azure e o utilizador relacionado na Nuvem MongoDB.

Para configurar e testar o Azure AD SSO com a Nuvem MongoDB, complete os seguintes blocos de construção:

1. [Configure O SSO AD Azure](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o único sinal de Azure AD com B.Simon.
    1. [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que b.Simon utilize um único sinal de AD Azure.
1. [Configure mongoDB Cloud SSO](#configure-mongodb-cloud-sso) para configurar as definições de inscrição únicas no lado da aplicação.
    1. [Crie um utilizador de teste MongoDB Cloud](#create-a-mongodb-cloud-test-user) para ter uma contrapartida de B.Simon em MongoDB Cloud, ligada à representação da AD Azure do utilizador.
1. [Teste sSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações da **MongoDB Cloud,** encontre a secção **Gerir.** Selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. Na configuração do single sign-on com a página **SAML,** selecione o ícone do lápis para **configuração Básica sAML** para editar as definições.

   ![Screenshot de configurar um único sign-on com página SAML, com ícone de lápis realçado](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** digite um URL que utilize o seguinte padrão:`https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. Na caixa de texto **URL de resposta,** digite um URL que utilize o seguinte padrão:`https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Selecione **Definir URLs adicionais**e executar o seguinte passo se pretender configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL que utilize o seguinte padrão:`https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta e URL de sinalização. Para obter estes valores, contacte a equipa de suporte ao [Cliente MongoDB Cloud](https://support.mongodb.com/). Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação MongoDB Cloud espera que as afirmações do SAML estejam num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot de atributos padrão](common/default-attributes.png)

1. Além dos atributos anteriores, a aplicação MongoDB Cloud espera que mais alguns atributos sejam retransmitidos na resposta SAML. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome | |  Atributo de origem|
    | ---------------| --------------- | --------- |
    | e-mail | | user.userprincipalname |
    | nomePróprio | | user.givenname |
    | apelido | | utilizador.sobrenome |

1. Na configuração de um único sessão com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML**. Selecione **Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![Screenshot da secção de certificado de assinatura SAML, com link de descarregamento em destaque](common/metadataxml.png)

1. Na secção Configurar a **Cloud MongoDB,** copie os URLs apropriados, com base na sua exigência.

    ![Screenshot da secção Mongo DB Cloud, com URLs em destaque](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure,**Users** > selecione **Utilizadores de Diretório** > Ativo Azure**Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva a palavra-passe para baixo.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que b.Simon utilize um único sign-on Azure, concedendo acesso à Nuvem MongoDB.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações**.
1. Na lista de aplicações, selecione **MongoDB Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com Utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **adicionar atribuição,** selecione **Utilizadores e grupos**.

    ![Screenshot de Utilizadores e página de grupos, com adicionar utilizador destacado](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecionar** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha **Selecionar** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

## <a name="configure-mongodb-cloud-sso"></a>Configure MongoDB Cloud SSO

Para configurar um único sinal no lado da Nuvem MongoDB, precisa dos URLs apropriados copiados do portal Azure. Também precisa de configurar a Candidatura da Federação para a sua Organização de Nuvem MongoDB. Siga as instruções na documentação da [Nuvem MongoDB](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Se tiver algum problema, contacte a equipa de apoio da [MongoDB Cloud.](https://support.mongodb.com/)

### <a name="create-a-mongodb-cloud-test-user"></a>Criar um utilizador de teste MongoDB Cloud

MongoDB Cloud suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhuma ação adicional a tomar. Se um utilizador já não existir na Nuvem MongoDB, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Ao selecionar o azulejo MongoDB Cloud no Painel de Acesso, está automaticamente inscrito na Nuvem MongoDB para a qual configura o SSO. Para mais informações, consulte [o Iniciar Sessão e inicie aplicações a partir do portal My Apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Inscreva-se no MongoDB Atlas no Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/mongodb.mongodb_atlas_azure_08082019?tab=Overview)

- [Experimente a Nuvem MongoDB com a AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Proteja a Nuvem MongoDB com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

