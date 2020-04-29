---
title: 'Tutorial: Azure Ative Directory integração individual de inscrição (SSO) com Bright Pattern Omnichannel Contact Center [ Bright Pattern Omnichannel Contact Center ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Bright Pattern Omnichannel Contact Center.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: de1e0091-ca10-4e7e-8014-f4579d8eabf6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27cda1f1a797ca0cb8e1b9d1c4cd7498c22ddde5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74081925"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bright-pattern-omnichannel-contact-center"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com bright pattern Omnichannel Contact Center

Neste tutorial, você aprenderá a integrar o Bright Pattern Omnichannel Contact Center com o Azure Ative Directory (Azure AD). Quando integrar o Bright Pattern Omnichannel Contact Center com a Azure AD, pode:

* Controle em Azure AD que tem acesso ao Bright Pattern Omnichannel Contact Center.
* Ative que os seus utilizadores sejam automaticamente inscritos no Bright Pattern Omnichannel Contact Center com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada pelo Centro de Contacto Omnichannel De Padrão Brilhante (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.



* Bright Pattern Omnichannel Contact Center suporta **SP e IDP** iniciadoS SSO
* Bright Pattern Omnichannel Contact Center suporta o fornecimento de utilizadores **justo no tempo**


## <a name="adding-bright-pattern-omnichannel-contact-center-from-the-gallery"></a>Adicionar Bright Pattern Omnichannel Contact Center da galeria

Para configurar a integração do Bright Pattern Omnichannel Contact Center em Azure AD, precisa de adicionar o Bright Pattern Omnichannel Contact Center da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite O Centro de **Contacto Omnichannel de Padrão Brilhante** na caixa de pesquisa.
1. Selecione **Bright Pattern Omnichannel Contact Center** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bright-pattern-omnichannel-contact-center"></a>Configure e teste azure AD único sinal para Bright Pattern Omnichannel Contact Center

Configure e teste Azure AD SSO com Bright Pattern Omnichannel Contact Center utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Bright Pattern Omnichannel Contact Center.

Para configurar e testar o Azure AD SSO com o Bright Pattern Omnichannel Contact Center, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure](#configure-bright-pattern-omnichannel-contact-center-sso)** o Centro de Contacto Omnichannel de Padrão Brilhante - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Crie um utilizador](#create-bright-pattern-omnichannel-contact-center-test-user)** de teste do Bright Pattern Omnichannel Contact Center - para ter uma contrapartida de B.Simon no Bright Pattern Omnichannel Contact Center que está ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do Centro de **Contacto Omnichannel de Padrão Brilhante,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`<SUBDOMAIN>_sso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.brightpattern.com/agentdesktop/sso/redirect`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.brightpattern.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de suporte do Cliente do [Bright Pattern Omnichannel Contact Center](mailto:support@brightpattern.com) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação Bright Pattern Omnichannel Contact Center espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Bright Pattern Omnichannel Contact Center espera que poucos atributos sejam passados na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Nome | Espaço de nomes  |
    | ---------------| --------------- |
    | nomePróprio | user.givenname |
    | apelido | utilizador.sobrenome |
    | e-mail | utilizador.mail |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção Configurar o Centro de **Contacto Omnichannel de Padrão Brilhante,** copie os URL(s) adequados com base na sua exigência.

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

Nesta secção, permitirá que b.Simon utilize um único sinal de Azure, concedendo acesso ao Bright Pattern Omnichannel Contact Center.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Bright Pattern Omnichannel Contact Center**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-bright-pattern-omnichannel-contact-center-sso"></a>Configure o Centro de Contacto Omnicanal de Padrão Brilhante SSO

Para configurar um único sinal no lado do **Bright Pattern Omnichannel Contact Center,** precisa enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de suporte do [Bright Pattern Omnichannel Contact Center](mailto:support@brightpattern.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-bright-pattern-omnichannel-contact-center-test-user"></a>Criar o utilizador de teste do Centro de Contacto Omnichannel de Padrão Brilhante

Nesta secção, um utilizador chamado B.Simon é criado no Bright Pattern Omnichannel Contact Center. O Bright Pattern Omnichannel Contact Center suporta o fornecimento de utilizadores just-in-time, que está ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador ainda não existir no Bright Pattern Omnichannel Contact Center, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Centro de Contacto Omnicanal de Padrão Brilhante no Painel de Acesso, deve ser automaticamente inscrito no Centro de Contacto Omnicanal de Padrão Brilhante para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Centro de Contacto Omnicanal de Padrão Brilhante com a AD Azure](https://aad.portal.azure.com/)

