---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com mS Azure SSO Acesso para O Escritório de Conformidade Ethidex™ / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o MS Azure SSO Access for Ethidex Compliance Office™.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58b2e70f-d1dd-47b6-b91f-f77581df01c6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00cbb5ff4b9354d0d1702161460b2646669f34c2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "70844364"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com o MS Azure SSO Access for Ethidex Compliance Office™

Neste tutorial, você aprenderá a integrar o MS Azure SSO Acesso para o Ethidex Compliance Office™ com o Azure Ative Directory (Azure AD). Quando integrar o Acesso MS Azure SSO ao Gabinete de Conformidade com a Ethidex™ com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao MS Azure SSO Acesso para o Ethidex Compliance Office™.
* Ative que os seus utilizadores sejam automaticamente inscritos no MS Azure SSO Access for Ethidex Compliance Office™ com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* MS Azure SSO Acesso para ethidex Compliance Office™ assinatura de inscrição única (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* MS Azure SSO Acesso para ethidex Compliance Office™ suporta **IDP** iniciado SSO

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Adicionar acesso MS Azure SSO para o Ethidex Compliance Office™ da galeria

Para configurar a integração do MS Azure SSO Access for Ethidex Compliance Office™ em Azure AD, você precisa adicionar MS Azure SSO Access for Ethidex Compliance Office™ da galeria para a sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** **digite MS Azure SSO Acesso para ethidex Compliance Office™** na caixa de pesquisa.
1. Selecione **MS Azure SSO Access for Ethidex Compliance Office™** do painel de resultados e, em seguida, adicione a app. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Configure e teste o único sinal de Acesso Azure AD para o MS Azure SSO Access for Ethidex Compliance Office™

Configure e teste Azure AD SSO com MS Azure SSO Access for Ethidex Compliance Office™ utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no MS Azure SSO Access for Ethidex Compliance Office™.

Para configurar e testar o Azure AD SSO com o Acesso SSO MS Azure para o Gabinete de Conformidade da Ethidex™, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Acesso MS Azure SSO para o Ethidex Compliance Office SSO](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o Acesso MS Azure SSO para o utilizador](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** de teste do Ethidex Compliance Office - para ter uma contrapartida de B.Simon no MS Azure SSO Access for Ethidex Compliance Office™ que está ligado à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), no **MS Azure SSO Access for Ethidex Compliance Office™** página de integração de aplicações, encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`com.ethidex.prod.<CLIENTID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte a [MS Azure SSO Access for Ethidex Compliance Office™ equipa](mailto:support@ethidex.com) de apoio para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. MS Azure SSO Acesso para o Ethidex Compliance Office™ aplicação espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** MS Azure SSO Acesso para Ethidex Compliance Office™ aplicação espera que **identificador** de nomes seja mapeado com **user.mail**, por isso precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **certificado (Cru)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificateraw.png)

1. Na **configuração do MS Azure SSO Access for Ethidex Compliance Office™** secção, copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao MS Azure SSO Access for Ethidex Compliance Office™.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **MS Azure SSO Acesso para ethidex Compliance Office™**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Configure MS Azure SSO Acesso para Ethidex Compliance Office SSO

Para configurar um único sinal no **MS Azure SSO Acesso para o Gabinete de Conformidade ethidex™** lado, você precisa enviar o Certificado descarregado **(Raw)** e URLs copiados apropriados do portal Azure para [MS Azure SSO Acesso para Ethidex Compliance Office™ equipa de suporte](mailto:support@ethidex.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Criar acesso MS Azure SSO para o utilizador de teste do Ethidex Compliance Office

Nesta secção, cria-se um utilizador chamado B.Simon no MS Azure SSO Access for Ethidex Compliance Office™. Trabalhe com o [MS Azure SSO Access for Ethidex Compliance Office™ equipa](mailto:support@ethidex.com) de suporte para adicionar os utilizadores na plataforma MS Azure SSO Access for Ethidex Compliance Office™. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no MS Azure SSO Access for Ethidex Compliance Office™ azulejo no Painel de Acesso, deverá ser automaticamente inscrito no MS Azure SSO Access for Ethidex Compliance Office™ para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Acesso SSO MS Azure para o Gabinete de Conformidade da Ethidex™ com a AD Azure](https://aad.portal.azure.com/)

