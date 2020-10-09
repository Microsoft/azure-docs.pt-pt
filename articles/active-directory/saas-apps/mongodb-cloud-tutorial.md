---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a MongoDB Cloud Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a MongoDB Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 6a59df062adf9b5c2e511db29cb8601d4a4df70e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88554571"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Tutorial: Azure Ative Directory integração única (SSO) com a MongoDB Cloud

Neste tutorial, você vai aprender a integrar a MongoDB Cloud com O Azure Ative Directory (Azure AD). Quando integrar a MongoDB Cloud com Azure AD, pode:

* Controle em Azure AD que tem acesso a MongoDB Cloud, MongoDB Atlas, a comunidade MongoDB, Universidade MongoDB, e Apoio mongoDB.
* Permita que os seus utilizadores sejam automaticamente inscritos na MongoDB Cloud com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte O que é o acesso à [aplicação e um único acesso com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma organização mongoDB Cloud que está ativada para um único sign-on (SSO), você pode se inscrever para um [cluster gratuito](https://www.mongodb.com/cloud)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* MongoDB Cloud suporta **SP** e **IDP** iniciado SSO.
* A Nuvem MongoDB suporta o fornecimento do utilizador **Just In Time.**
* Depois de configurar a MongoDB Cloud, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. Para obter mais informações, consulte [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="add-mongodb-cloud-from-the-gallery"></a>Adicione a Nuvem MongoDB da galeria

Para configurar a integração da MongoDB Cloud em AD Azure, precisa adicionar a MongoDB Cloud da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Ative Directory**.
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite a Nuvem MongoDB** na caixa de pesquisa.
1. Selecione **MongoDB Cloud** a partir dos resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Configurar e testar Azure AD único sinal para MongoDB Cloud

Configure e teste Azure AD SSO com MongoDB Cloud, utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado na Nuvem MongoDB.

Para configurar e testar O Azure AD SSO com a Nuvem MongoDB, complete os seguintes blocos de construção:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar o Azure AD com B.Simon.
    1. [Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize um único sinal de Ad AD.
1. [Configure o MongoDB Cloud SSO](#configure-mongodb-cloud-sso) para configurar as definições de inscrição única no lado da aplicação.
    1. [Crie um utilizador de teste mongoDB Cloud](#create-a-mongodb-cloud-test-user) para ter uma contraparte de B.Simon na Nuvem MongoDB, ligada à representação AD AZure do utilizador.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **MongoDB Cloud,** encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, selecione o ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Screenshot de Configurar single Sign-On com página SAML, com ícone de lápis em destaque](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um URL que utiliza o seguinte padrão: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. Na caixa de texto **URL de resposta,** digite um URL que utiliza o seguinte padrão: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Selecione **Definir URLs adicionais**e executar o seguinte passo se quiser configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL que utiliza o seguinte padrão:  `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Para obter estes valores, contacte a equipa de suporte do [Cliente nuvem MongoDB.](https://support.mongodb.com/) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação MongoDB Cloud espera que as afirmações DOL estejam num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot de atributos predefinidos](common/default-attributes.png)

1. Além dos atributos anteriores, a aplicação MongoDB Cloud espera que mais alguns atributos sejam repercutidos na resposta SAML. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome | Atributo de origem|
    | ---------------| --------- |
    | e-mail | user.userprincipalname |
    | nomePróprio | user.givenname |
    | apelido | utilizador.sobrenome |

1. Na **configuração de um único sinal com** a página SAML, na secção certificado de assinatura **SAML,** encontre **o Metadados da Federação XML**. Selecione **Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![Screenshot da secção de Certificado de Assinatura SAML, com link de descarregamento em destaque](common/metadataxml.png)

1. Na secção Configurar a **nuvem MongoDB,** copie os URLs apropriados, com base na sua exigência.

    ![Screenshot da configuração da secção Mongo DB Cloud, com URLs em destaque](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de senha show** e, em seguida, escreva a palavra-passe para baixo.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon use a Azure single sign-on, concedendo acesso à Nuvem MongoDB.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **MongoDB Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **de atribuição de adicionar,** selecione **Utilizadores e grupos**.

    ![Screenshot da página de Utilizadores e grupos, com o utilizador Adicionar em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-mongodb-cloud-sso"></a>Configurar mongodb nuvem Sso

Para configurar um único sinal no lado da nuvem mongoDB, você precisa dos URLs apropriados copiados do portal Azure. Também precisa configurar a Aplicação da Federação para a sua MongoDB Cloud Organization. Siga as instruções na documentação da [Nuvem MongoDB](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Se tiver algum problema, contacte a equipa de suporte da [MongoDB Cloud.](https://support.mongodb.com/)

### <a name="create-a-mongodb-cloud-test-user"></a>Criar um utilizador de teste mongoDB cloud

A MongoDB Cloud suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhuma ação adicional para tomar. Se um utilizador já não existir na MongoDB Cloud, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de acesso Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo da nuvem MongoDB no Painel de Acesso, é automaticamente inscrito na Nuvem MongoDB para a qual configura sSO. Para obter mais informações, consulte [iniciar sômis e inicie aplicações a partir do portal My Apps.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Inscreva-se no MongoDB Atlas em Azure](https://azuremarketplace.microsoft.com/marketplace/apps/mongodb.mongodb_atlas_may_2020?tab=Overview)

- [Experimente a Nuvem MongoDB com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Proteja a Nuvem mongoDB com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

