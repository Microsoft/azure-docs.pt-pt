---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com ColorTokens ZTNA / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o ColorTokens ZTNA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 54386bd7431c2003dd29a905b3b696f69a39d2d9
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97914438"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-colortokens-ztna"></a>Tutorial: Azure Ative Directy integração única (SSO) com ColorTokens ZTNA

Neste tutorial, você vai aprender a integrar o ColorTokens ZTNA com O Azure Ative Directory (Azure AD). Quando integra o ColorTokens ZTNA com Azure AD, pode:

* Control em Azure AD que tem acesso ao ColorTokens ZTNA.
* Ative os seus utilizadores a serem automaticamente inscritos no ColorTokens ZTNA com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por ColorTokens ZTNA (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* ColorTokens ZTNA suporta **SSO** iniciado SP

## <a name="adding-colortokens-ztna-from-the-gallery"></a>Adicionar ColorTokens ZTNA da galeria

Para configurar a integração do ColorTokens ZTNA em AD Azure, precisa adicionar o ColorTokens ZTNA da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **escreva ColorTokens ZTNA** na caixa de pesquisa.
1. Selecione **ColorTokens ZTNA** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-colortokens-ztna"></a>Configurar e testar Azure AD SSO para ColorTokens ZTNA

Configure e teste Azure AD SSO com ColorTokens ZTNA usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no ColorTokens ZTNA.

Para configurar e testar O SSO Azure AD com o ColorTokens ZTNA, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o ColorTokens ZTNA SSO](#configure-colortokens-ztna-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create ColorTokens ZTNA test user](#create-colortokens-ztna-test-user)** - para ter uma contraparte de B.Simon in ColorTokens ZTNA que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **ColorTokens ZTNA,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<tenantname>.spectrum.colortokens.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identifier e URL de resposta. Contacte [a equipa de suporte do cliente ColorTokens ZTNA](mailto:support@colortokens.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação ZTNA ColorTokens espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação ColorTokens ZTNA espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ------------- | --------- |
    | departamento | user.userprincipalname |
    | Grupo | utilizador.grupos |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) para saber como criar papéis em Azure AD.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **set up ColorTokens ZTNA,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao ColorTokens ZTNA.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **ColorTokens ZTNA**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-colortokens-ztna-sso"></a>Configure ColorTokens ZTNA SSO

Para configurar um único sign-on no lado **ZTNA do ColorTokens,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte ColorTokens ZTNA](mailto:support@colortokens.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-colortokens-ztna-test-user"></a>Criar utilizador de teste ColorTokens ZTNA

Nesta secção, cria-se um utilizador chamado Britta Simon in ColorTokens ZTNA. Trabalhe com a [equipa de suporte do ColorTokens ZTNA](mailto:support@colortokens.com) para adicionar os utilizadores na plataforma ColorTokens ZTNA. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de sinal de ZTNA do ColorTokens, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de assinatura do ColorTokens ZTNA e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo ZTNA do ColorTokens nas Minhas Apps, este irá redirecionar para o URL de assinatura do ColorTokens ZTNA. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o ColorTokens ZTNA, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).