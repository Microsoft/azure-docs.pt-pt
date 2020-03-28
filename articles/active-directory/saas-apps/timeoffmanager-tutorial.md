---
title: 'Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com timeOffManager [ TimeOffManager] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06aa2ddf3e7168147ec091ef6fb9826025f23364
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561818"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com timeOffManager

Neste tutorial, você vai aprender a integrar timeOffManager com O Diretório Ativo Azure (Azure AD). Quando integrar o TimeOffManager com o Azure AD, pode:

* Controle em Azure AD que tem acesso ao TimeOffManager.
* Ative que os seus utilizadores sejam automaticamente inscritos no TimeOffManager com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pelo TimeOffManager (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.


* TimeOffManager suporta **IDP** iniciado SSO

* TimeOffManager suporta o fornecimento de utilizadores **justo no tempo**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.


## <a name="adding-timeoffmanager-from-the-gallery"></a>Adicionar TimeOffManager da galeria

Para configurar a integração do TimeOffManager em Azure AD, precisa de adicionar timeOffManager da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **TimeOffManager** na caixa de pesquisa.
1. Selecione **TimeOffManager** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Configure e teste Azure AD single sign-on para TimeOffManager

Configure e teste Azure AD SSO com TimeOffManager utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no TimeOffManager.

Para configurar e testar o Azure AD SSO com o TimeOffManager, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure timeoffManager SSO](#configure-timeoffmanager-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Create TimeOffManager test user](#create-timeoffmanager-test-user)** - para ter uma contraparte de B.Simon no TimeOffManager que está ligado à representação do Utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **TimeOffManager,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL de Resposta real. Pode obter este valor a partir de **Single Sign na página de definições** que é explicado mais tarde no tutorial ou na equipa de suporte do [TimeOffManager](https://www.purelyhr.com/contact-us)de contacto . Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação TimeOffManager espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação TimeOffManager espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Nome | Atributo fonte|
    | --- | --- |
    | Primeiro nome |User.givenname |
    | Apelido |Utilizador.sobrenome |
    | Email |Utilizador.mail |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar timeoffManager,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on do Azure, concedendo acesso ao TimeOffManager.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **TimeOffManager**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-timeoffmanager-sso"></a>Configure TimeOffManager SSO

1. Numa janela de navegador web diferente, inscreva-se no site da sua empresa TimeOffManager como administrador.

2. Vá para as **opções \> \> de conta Definições de inscrição únicas**.
   
    ![Definições únicas de inscrição](./media/timeoffmanager-tutorial/ic795917.png "Definições únicas de inscrição")

3. Na secção Definições de **Sinal único,** execute os seguintes passos:
   
    ![Definições únicas de inscrição](./media/timeoffmanager-tutorial/ic795918.png "Definições únicas de inscrição")
   
    a. Abra o seu certificado codificado base-64 no bloco de notas, copie o conteúdo do mesmo na sua área de recção e, em seguida, cole todo o Certificado na caixa de texto **X.509 Certificate.**
   
    b. Na caixa de texto **IDP Emitente,** colá o valor do **Identificador Azure AD** que copiou do portal Azure.
   
    c. Na caixa de texto **URL idP Endpoint,** colá o valor do URL de **Login** que copiou do portal Azure.
   
    d. As **Enforce SAML,** selecione **No**.
   
    e. Como **Utilizadores de Criação Automática,** selecione **Sim**.
   
    f. Na caixa de texto **logout URL,** colá o valor do URL de **Logout** que copiou do portal Azure.
   
    g. clique em **Guardar Alterações**.

4. Na página de Definições de **Sinal Único,** copie o valor do URL do **Serviço de Consumidor de Afirmação** e cole-o na caixa de texto URL de **resposta** sob a secção **de configuração Básica SAML** no portal Azure. 

      ![Definições únicas de inscrição](./media/timeoffmanager-tutorial/ic795915.png "Definições únicas de inscrição")

### <a name="create-timeoffmanager-test-user"></a>Criar o utilizador de teste TimeOffManager

Nesta secção, um utilizador chamado Britta Simon é criado no TimeOffManager. TimeOffManager suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no TimeOffManager, um novo é criado após a autenticação.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador TimeOffManager ou APIs fornecidas pelo TimeOffManager para fornecer contas de utilizador da AD Azure.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo TimeOffManager no Painel de Acesso, deve ser automaticamente inscrito no TimeOffManager para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente timeoffmanager com Azure AD](https://aad.portal.azure.com/)

