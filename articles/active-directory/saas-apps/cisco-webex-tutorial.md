---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com cisco Webex Meetings [ Azure Ative Diretório) integração com Cisco Webex Meetings [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cisco Webex Meetings.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4e497c556bde1be4e498cd85a68282a0e3b2666
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026262"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com cisco Webex Meetings

Neste tutorial, você vai aprender a integrar cisco Webex Meetings com O Diretório Ativo Azure (Azure AD). Quando integrar as Reuniões Webex da Cisco com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Cisco Webex Meetings.
* Ative que os seus utilizadores sejam automaticamente inscritos nas Reuniões Webex da Cisco com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Cisco Webex Meetings único sign-on (SSO) ativado subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Cisco Webex Meetings suporta **SP e IDP** iniciadoS SSO

* Cisco Webex Meetings suporta o provisionamento de utilizadores **justos no tempo**

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Adicionar Reuniões Webex cisco da galeria

Para configurar a integração das Reuniões Webex da Cisco em Azure AD, precisa adicionar as Reuniões Webex da Cisco da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **Cisco Webex Meetings** na caixa de pesquisa.
1. Selecione **Cisco Webex Meetings** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Configure e teste Azure AD único sign-on para cisco Webex Meetings

Configure e teste Azure AD SSO com Cisco Webex Meetings utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado nas Reuniões Webex da Cisco.

Para configurar e testar o Azure AD SSO com as Reuniões Webex da Cisco, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
2. **[Configure cisco Webex Meetings SSO](#configure-cisco-webex-meetings-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador](#create-cisco-webex-meetings-test-user)** de teste Cisco Webex Meetings - para ter uma contrapartida de B.Simon em Cisco Webex Meetings que está ligada à representação do utilizador da AD Azure.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Cisco Webex Meetings,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. Na configuração do Registo Único com a página **SAML,** pode configurar a aplicação no modo iniciado **idp,** carregando o ficheiro de metadados do Fornecedor de **Serviços** da seguinte forma:

    a. Clique no **ficheiro de metadados de upload**.

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    c. Após a conclusão com sucesso do upload de metadados do Fornecedor de Serviços, os valores **de URL** do **Identificador** e da Resposta ficam automaticamente povoados na secção de **Configuração Básica do SAML.**

    >[!Note]
    >Você receberá o ficheiro de metadados do fornecedor de serviços da secção **Configure Cisco Webex Meetings SSO,** o que é explicado mais tarde no tutorial. 

1. Se desejar configurar a aplicação no modo iniciado **sp,** execute os seguintes passos:  

    a. Na secção **Basic SAML Configuração,** clique no ícone de edição/caneta.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)
    
    b. No **Sign on URL** textbox, digite o URL utilizando o seguinte padrão:` https://<customername>.my.webex.com`

5. A aplicação Cisco Webex Meetings espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo de Atributos do Utilizador.

    ![image](common/edit-attribute.png)

6. Além de acima, a aplicação Cisco Webex Meetings espera que poucos atributos sejam retransmitidos na resposta SAML. Na secção Reivindicações do Utilizador no diálogo de Atributos do Utilizador, execute os seguintes passos para adicionar atributo token SAML, conforme indicado no quadro abaixo: 

    | Nome | Atributo fonte|
    | ---------------|  --------- |
    |   primeiro nome    | user.givenname |
    |   apelido    | utilizador.sobrenome |
    |   e-mail       | utilizador.mail |
    |   uid    | utilizador.mail |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Deixe o espaço de **nome em** branco.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** selecione o valor do atributo mostrado para essa linha a partir da lista de abandono.

    f. Clique em **Guardar**.

4. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

6. Na secção Configurar as **Reuniões Webex** da Cisco, copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize o único sign-on do Azure, concedendo acesso às Reuniões Webex da Cisco.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Cisco Webex Meetings**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-cisco-webex-meetings-sso"></a>Configure Cisco Webex Meetings SSO

1. Vá `https://<customername>.webex.com/admin` ao URL com as suas credenciais de administração.

2. Vá a **Configurações do Site Comum** e navegue para a **configuração SSO**.
 
    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Na página **da Administração Webex,** execute os seguintes passos:

    ![Configurar o início de sessão único](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. selecione **SAML 2.0** como **Protocolo da Federação**.

    b. Clique no link **Import SAML Metadata** para fazer o upload do ficheiro de metadados, que descarregou do portal Azure.

    c. Clique no botão **Export** para descarregar o ficheiro metadados do fornecedor de serviços e carregá-lo na secção **de Configuração SAML Básica** no portal Azure.

    d. Na caixa de texto **AuthContextClassRef,** escreva `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` e se quiser ativar o MFA utilizando o tipo Azure AD os dois valores como`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Selecione **Criação de Conta Automática**.

    >[!NOTE]
    >Para permitir o fornecimento de utilizadores **just-in-time,** você precisa verificar a **Criação**de Conta Automática . Além disso, os atributos simbólicos da SAML têm de ser passados na resposta SAML.

    f. Clique em **Guardar**.

    >[!NOTE]
    >Esta configuração é apenas para os clientes que utilizam o Webex UserID em formato de e-mail.

### <a name="create-cisco-webex-meetings-test-user"></a>Criar o utilizador de teste Cisco Webex Meetings

O objetivo desta secção é criar um utilizador chamado B.Simon em Cisco Webex Meetings. A Cisco Webex Meetings suporta o fornecimento **just-in-time,** que é por padrão ativado. Não há nenhum item de ação para si nesta secção. Se um utilizador ainda não existir nas Cisco Webex Meetings, um novo é criado quando tenta aceder às Reuniões Webex da Cisco.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Cisco Webex Meetings no Painel de Acesso, deverá ser automaticamente inscrito nas Reuniões Webex da Cisco para as quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente serviceNow com Azure AD](https://aad.portal.azure.com)
