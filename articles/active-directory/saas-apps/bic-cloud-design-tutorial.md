---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com bic Cloud Design [ Azure Ative Diretório) integração com bic Cloud Design [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o BIC Cloud Design.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68709502-480d-4f47-add2-05e0046be8ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13f6de35400280d68227af1dd7e3a981494d9e61
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77088215"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bic-cloud-design"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com bic Cloud Design

Neste tutorial, você vai aprender a integrar o BIC Cloud Design com o Azure Ative Directory (Azure AD). Quando integrar o BIC Cloud Design com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao BIC Cloud Design.
* Ative que os seus utilizadores sejam automaticamente inscritos no BIC Cloud Design com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por subscrição do BIC Cloud Design (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* BIC Cloud Design suporta **SP** iniciado SSO
* Assim que configurar o BIC Cloud Design, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-bic-cloud-design-from-the-gallery"></a>Adicionar design de nuvem bic da galeria

Para configurar a integração do BIC Cloud Design em Azure AD, você precisa adicionar o BIC Cloud Design da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **o BIC Cloud Design** na caixa de pesquisa.
1. Selecione **BIC Cloud Design** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bic-cloud-design"></a>Configure e teste Azure AD único sign-on para BIC Cloud Design

Configure e teste Azure AD SSO com BIC Cloud Design utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no BIC Cloud Design.

Para configurar e testar o Azure AD SSO com o BIC Cloud Design, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o BIC Cloud Design SSO](#configure-bic-cloud-design-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie](#create-bic-cloud-design-test-user)** o utilizador de teste BIC Cloud Design - para ter uma contrapartida de B.Simon no BIC Cloud Design que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações bic **Cloud Design,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se tiver ficheiro de **metadados do Prestador**de Serviços, execute os seguintes passos:

    a. Clique no **ficheiro de metadados de upload**.

    ![Upload ficheiro de metadados](common/upload-metadata.png)

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    ![escolher ficheiro de metadados](common/browse-upload-metadata.png)

    c. Após o ficheiro de metadados ser carregado com sucesso, o valor **do Identificador** é preenchido automaticamente na secção de Configuração SAML Básica.

    ![Bic Cloud Design Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:

    |||
    |-|-|
    | `https://<customer-specific-name/tenant>.biccloud.com`|
    | `https://<customer-specific-name/tenant>.biccloud.de` |

    > [!Note]
    > Se o valor do **Identificador** não for automaticamente pollulado, por favor preencha o valor manualmente de acordo com o seu requisito. O valor do URL de inscrição não é real. Atualize este valor com o URL de início de sinal real. Contacte a equipa de suporte do [Cliente BIC Cloud Design](mailto:bicsupport@gbtec.de) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação BIC Cloud Design espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação BIC Cloud Design espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo fonte|
    | ------------ | --------- |
    | Nome | user.name |
    | Endereço de e-mail | utilizador.mail |
    | ID de nome | user.userprincipalname |
    | e-mail | utilizador.mail |
    | nametest | user.displayname |

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on Azure, concedendo acesso ao BIC Cloud Design.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **BIC Cloud Design**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-bic-cloud-design-sso"></a>Configure BIC Cloud Design SSO

Para configurar um único sinal no lado do **BIC Cloud Design,** você precisa enviar o Url de **Metadados da Federação de Aplicações** para a equipa de suporte do [BIC Cloud Design](mailto:bicsupport@gbtec.de). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-bic-cloud-design-test-user"></a>Criar o utilizador de teste bic Cloud Design

Nesta secção, cria-se um utilizador chamado B.Simon no BIC Cloud Design. Trabalhe com a equipa de suporte do [BIC Cloud Design](mailto:bicsupport@gbtec.de) para adicionar os utilizadores na plataforma BIC Cloud Design. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo BIC Cloud Design no Painel de Acesso, deverá ser automaticamente inscrito no Bic Cloud Design para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o design de nuvem bic com a AD Azure](https://aad.portal.azure.com/)