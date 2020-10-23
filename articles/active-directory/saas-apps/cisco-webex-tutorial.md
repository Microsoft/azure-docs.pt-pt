---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Encontros Webex da Cisco Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cisco Webex Meetings.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.openlocfilehash: 1d53cfc874bca6529fdee821ce3173607d5f06b3
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456059"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Tutorial: Azure Ative Directory integração única (SSO) com reuniões webex da Cisco

Neste tutorial, você vai aprender a integrar Cisco Webex Meetings com Azure Ative Directory (Azure AD). Quando integrar as Reuniões Webex da Cisco com a Ad Azure, pode:

* Controle em Azure AD que tem acesso a Cisco Webex Meetings.
* Permitir que os seus utilizadores sejam automaticamente inscritos nas Reuniões Webex da Cisco com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Cisco Webex Meetings subscrição única (SSO) ativada.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Cisco Webex Meetings apoia **SP e IDP** iniciado SSO

* Cisco Webex Meetings suporta **provisão de** utilizadores just in time

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Adicionar Reuniões Webex da Cisco da galeria

Para configurar a integração das Reuniões Webex da Cisco em AZure AD, é necessário adicionar as Reuniões Webex da Cisco da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Cisco Webex Meetings** na caixa de pesquisa.
1. Selecione **Cisco Webex Meetings** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Configurar e testar a Azure AD um único sign-on para as Reuniões Webex da Cisco

Configure e teste Azure AD SSO com Cisco Webex Meetings usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado nas Reuniões Webex da Cisco.

Para configurar e testar o Azure AD SSO com as Reuniões Webex da Cisco, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure cisco Webex Meetings SSO](#configure-cisco-webex-meetings-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Cisco Webex Meetings test user](#create-cisco-webex-meetings-test-user)** - para ter uma contrapartida de B.Simon em Cisco Webex Meetings que está ligada à representação AD Ad Azure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Cisco Webex Meetings,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **Sign-On Único configurado com** a página SAML, pode configurar a aplicação no modo iniciado pelo **IDP,** carregando o ficheiro **de metadados do Fornecedor de Serviço** da seguinte forma:

    a. Clique **em Carregar o ficheiro de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    c. Após a conclusão com sucesso do ficheiro de metadados do Fornecedor de Serviços, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção **de Configuração BÁSICA SAML.**

    >[!Note]
    >Você receberá o ficheiro de metadados do Fornecedor de Serviços da secção **Configure Cisco Webex Meetings SSO,** que é explicado mais tarde no tutorial. 

1. Se desejar configurar a aplicação no modo iniciado pela **SP,** execute os seguintes passos:  

    a. Na secção **De Configuração Básica SAML,** clique no ícone edit/pen.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)
    
    b. Na placa de texto **URL,** digite o URL utilizando o seguinte padrão: ` https://<customername>.my.webex.com`

5. A aplicação Cisco Webex Meetings espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador.

    ![image](common/edit-attribute.png)

6. Além de acima, a aplicação Cisco Webex Meetings espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção 'Reclamações de Utilizador' no diálogo 'Atributos do Utilizador', execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo: 

    | Name | Atributo de origem|
    | ---------------|  --------- |
    |   nome de primeiro nome    | user.givenname |
    |   último nome    | utilizador.sobrenome |
    |   e-mail       | user.mail |
    |   uid    | user.mail |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** selecione o valor do atributo mostrado para esta linha a partir da lista de drop-down.

    f. Clique em **Guardar**.

4. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção Configurar as **Reuniões Webex da Cisco,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Cisco Webex Meetings.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Cisco Webex Meetings**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cisco-webex-meetings-sso"></a>Configurar cisco Webex Meetings SSO

1. Vá para a `https://<customername>.webex.com/admin` URL com as suas credenciais de administração.

2. Aceda às **definições do site comum** e navegue para a **Configuração SSO**.
 
    ![O Screenshot mostra a Administração Webex da Cisco com configurações de site comuns e configuração S S O selecionada.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Na página **da Administração Webex,** execute os seguintes passos:

    ![A screenshot mostra a página da Administração Webex com as informações descritas neste passo.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. selecione **SAML 2.0** como **Protocolo da Federação**.

    b. Clique na **ligação de metadados IMPORTADOR SAML** para carregar o ficheiro de metadados, que descarregou a partir do portal Azure.

    c. Clique no botão **Exportação** para descarregar o ficheiro de metadados do Fornecedor de Serviços e carregá-lo na secção **de Configuração Básica SAML** no portal Azure.

    d. Na caixa de texto **AuthContextClassRef,** `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` escreva e se quiser ativar o MFA utilizando o Azure AD tipo os dois valores como `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Selecione **Criação de Conta Automática**.

    >[!NOTE]
    >Para permitir o provisionamento do utilizador **just-in-time,** é necessário verificar a **Criação de Conta Automática**. Para além dos atributos simbólicos DA SAML, é necessário passar na resposta SAML.

    f. Clique em **Guardar**.

    >[!NOTE]
    >Esta configuração destina-se apenas aos clientes que utilizam o Webex UserID em formato de e-mail.

### <a name="create-cisco-webex-meetings-test-user"></a>Criar cisco Webex Meetings test utilizador

O objetivo desta secção é criar um utilizador chamado B.Simon em Cisco Webex Meetings. A Cisco Webex Meetings suporta o provisionamento **just-in-time,** que é por defeito ativado. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Cisco Webex Meetings, um novo é criado quando tenta aceder às Reuniões Webex da Cisco.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo cisco Webex Meetings no Painel de Acesso, deverá ser automaticamente inscrito nas Reuniões Webex da Cisco para as quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o ServiceNow com Azure AD](https://aad.portal.azure.com)