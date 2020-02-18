---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com Single Sign-on para Skytap  Azure Ative Diretório) integração com single sign-on para Skytap  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Single Sign-on para a Skytap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 847534ee28880cd2206c6abef605e695cc968f80
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com single sign-on para Skytap

Neste tutorial, você vai aprender a integrar single sign-on para Skytap com Azure Ative Directory (Azure AD). Quando integrar o Single Sign-on para skytap com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Um Único Sign-on para skytap.
* Ative que os seus utilizadores sejam automaticamente inscritos no Single Sign-on para a Skytap com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* O único sinal para a assinatura ativada pela Skytap (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Single Sign-on para Skytap suporta **SP e IDP** iniciado SSO
* Assim que configurar o Single Sign-on para a Skytap, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-single-sign-on-for-skytap-from-the-gallery"></a>Adicionar um único sign-on para skytap da galeria

Para configurar a integração do Single Sign-on para a Skytap no Azure AD, precisa de adicionar um único sign-on para a Skytap da galeria à sua lista de aplicações saaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Um Único Sign-on para skytap** na caixa de pesquisa.
1. Selecione **Single Sign-on para Skytap** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Configure e teste Azure AD single sign-on para single sign-on para Skytap

Configure e teste Azure AD SSO com um único signo para skytap utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Single Sign-on para a Skytap.

Para configurar e testar o Azure AD SSO com um único sinal para a Skytap, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o sign-on single para Skytap SSO](#configure-single-sign-on-for-skytap-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie um único sign-on para o utilizador](#create-single-sign-on-for-skytap-test-user)** do teste Skytap - para ter uma contraparte de B.Simon em Single Sign-on para skytap que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Skytap,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se pretender configurar a aplicação no modo iniciado **idp,** execute os seguintes passos:

    a. Na caixa de texto **identificador,** digite um URL utilizando o seguinte padrão: `http://pingone.com/<custom EntityID>`

    b. Na caixa de texto **URL de resposta,** escreva um URL utilizando o seguinte padrão: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Clique em **Definir URLs adicionais** e execute os seguintes passos se pretender configurar a aplicação no modo iniciado por **SP:**

    d. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    e. Na caixa de texto **do Estado relé,** escreva um URL utilizando o seguinte padrão: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta, URL de inscrição e estado de retransmissão. Contacte a [Single Sign-on para](mailto:support@skytap.com) obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único registo com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Download** para descarregar o ficheiro de metadados e guardá-lo no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na **secção set up Single Sign-on para skytap,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso a Um Único Sign-on para a Skytap.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Single Sign-on para Skytap**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-single-sign-on-for-skytap-sso"></a>Configure o único sinal para Skytap SSO

Para configurar um único **sign-on no single sign-on para** o lado skytap, você precisa enviar os metadados da Federação **xML** descarregados e URLs copiados apropriados do portal Azure para [Single Sign-on para a equipa de suporte](mailto:support@skytap.com)ao Cliente Skytap . Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Criar um único sinal para o utilizador do teste Skytap

Nesta secção, cria-se uma utilizadora chamada Britta Simon em Single Sign-on para skytap. Trabalhe com a equipa de suporte ao [Cliente Skytap para](mailto:support@skytap.com) adicionar os utilizadores no Single Sign-on para a plataforma Skytap. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar no único sinal de entrada para o azulejo Skytap no Painel de Acesso, deve ser automaticamente inscrito no Único Sign-on para skytap para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente slack com Azure AD](https://aad.portal.azure.com/)

