---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a | de Nuvem MongoDB Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a MongoDB Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2021
ms.author: jeedes
ms.openlocfilehash: 5904d3eeec3f5880213f8a8c6a41cefbe76801b3
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520093"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Tutorial: Azure Ative Directory integração única (SSO) com a MongoDB Cloud

Neste tutorial, você vai aprender a integrar a MongoDB Cloud com O Azure Ative Directory (Azure AD). Quando integrar a MongoDB Cloud com Azure AD, pode:

* Controle em Azure AD que tem acesso a MongoDB Cloud, MongoDB Atlas, a comunidade MongoDB, Universidade MongoDB, e Apoio mongoDB.
* Permita que os seus utilizadores sejam automaticamente inscritos na MongoDB Cloud com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* MongoDB Cloud única subscrição ativa (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* MongoDB Cloud suporta **SP** e **IDP** iniciado SSO.
* A Nuvem MongoDB suporta o fornecimento do utilizador **Just In Time.**

## <a name="add-mongodb-cloud-from-the-gallery"></a>Adicione a Nuvem MongoDB da galeria

Para configurar a integração da MongoDB Cloud em AD Azure, precisa adicionar a MongoDB Cloud da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite a Nuvem MongoDB** na caixa de pesquisa.
1. Selecione **MongoDB Cloud** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-mongodb-cloud"></a>Configure e teste Azure AD SSO para a nuvem mongoDB

Configure e teste Azure AD SSO com MongoDB Cloud, utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado na Nuvem MongoDB.

Para configurar e testar Azure AD SSO com a MongoDB Cloud, execute os seguintes passos:

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

1. Selecione **Definir URLs adicionais** e executar o seguinte passo se quiser configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL que utiliza o seguinte padrão:  `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Para obter estes valores, contacte a equipa de suporte do [Cliente nuvem MongoDB.](https://support.mongodb.com/) Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação MongoDB Cloud espera que as afirmações DOL estejam num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot de atributos predefinidos](common/default-attributes.png)

1. Além dos atributos anteriores, a aplicação MongoDB Cloud espera que mais alguns atributos sejam repercutidos na resposta SAML. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name | Atributo de origem|
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

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de senha show** e, em seguida, escreva a palavra-passe para baixo.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Nuvem MongoDB.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **MongoDB Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-mongodb-cloud-sso"></a>Configurar mongodb nuvem Sso

Para configurar um único sinal no lado da nuvem mongoDB, você precisa dos URLs apropriados copiados do portal Azure. Também precisa configurar a Aplicação da Federação para a sua MongoDB Cloud Organization. Siga as instruções na documentação da [Nuvem MongoDB](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Se tiver algum problema, contacte a equipa de suporte da [MongoDB Cloud.](https://support.mongodb.com/)

### <a name="create-a-mongodb-cloud-test-user"></a>Criar um utilizador de teste mongoDB cloud

A MongoDB Cloud suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhuma ação adicional para tomar. Se um utilizador já não existir na MongoDB Cloud, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Nuvem MongoDB no URL, onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição na nuvem mongoDB e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Nuvem MongoDB para a qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo da Nuvem MongoDB nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Nuvem MongoDB para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado MongoDB Cloud, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
