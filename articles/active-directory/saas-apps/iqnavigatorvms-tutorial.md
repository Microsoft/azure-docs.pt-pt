---
title: 'Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com IQNavigator VMS [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o IQNavigator VMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f61681fbe3ec93467db67290fde6548ce62425fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75560531"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iqnavigator-vms"></a>Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com IQNavigator VMS

Neste tutorial, você vai aprender a integrar IQNavigator VMS com O Diretório Ativo Azure (Azure AD). Quando integrar o IQNavigator VMS com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a IQNavigator VMS.
* Ative que os seus utilizadores sejam automaticamente inscritos no IQNavigator VMS com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal (SSO) do IQNavigator VMS (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.


* IQNavigator VMS suporta **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.



## <a name="adding-iqnavigator-vms-from-the-gallery"></a>Adicionar IQNavigator VMS da galeria

Para configurar a integração do IQNavigator VMS em Azure AD, precisa adicionar IQNavigator VMS da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** **digite IQNavigator VMS** na caixa de pesquisa.
1. Selecione **IQNavigator VMS** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-iqnavigator-vms"></a>Configure e teste Azure AD único sinal para IQNavigator VMS

Configure e teste Azure AD SSO com IQNavigator VMS utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no IQNavigator VMS.

Para configurar e testar o Azure AD SSO com o IQNavigator VMS, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure IQNavigator VMS SSO](#configure-iqnavigator-vms-sso)** - para configurar as definições de sinal única no lado da aplicação.
    1. **[Crie o utilizador de teste IQNavigator VMS](#create-iqnavigator-vms-test-user)** - para ter uma contraparte de B.Simon no IQNavigator VMS que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **IQNavigator VMS,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** digite um URL:`iqn.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **do Estado relé,** digite um URL utilizando o seguinte padrão:`https://<subdomain>.iqnavigator.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Resposta real e o Estado de Retransmissão. Contacte a equipa de [suporte ao cliente IQNavigator VMS](https://www.beeline.com/support-iqn/) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação IQNavigator espera o valor exclusivo do identificador de utilizador na alegação do identificador de nome. O cliente pode mapear o valor correto para a reclamação do Identificador de Nome. Neste caso, mapeamos o utilizador. UserPrincipalName para o propósito de demonstração. Mas de acordo com as definições da sua organização, deve mapear o valor correto para isso.

    ![image](common/edit-attribute.png)

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao IQNavigator VMS.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **IQNavigator VMS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-iqnavigator-vms-sso"></a>Configure IQNavigator VMS SSO

Para configurar um único sinal no lado **IQNavigator VMS,** precisa enviar o Url de **Metadados da Federação de Aplicações** para a equipa de [suporte IQNavigator VMS](https://www.beeline.com/support-iqn/). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-iqnavigator-vms-test-user"></a>Criar o utilizador de teste IQNavigator VMS

Nesta secção, cria-se um utilizador chamado Britta Simon no IQNavigator VMS. Trabalhe com a equipa de [suporte IQNavigator VMS](https://www.beeline.com/support-iqn/) para adicionar os utilizadores na plataforma IQNavigator VMS. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo IQNavigator VMS no Painel de Acesso, deve ser automaticamente inscrito no IQNavigator VMS para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente IQNavigator VMS com Azure AD](https://aad.portal.azure.com/)

