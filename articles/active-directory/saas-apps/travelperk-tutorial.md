---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a TravelPerk | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TravelPerk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: jeedes
ms.openlocfilehash: eefc47e1308dc5b59d8b57d7481a5250a7d2af93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99475747"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-travelperk"></a>Tutorial: Azure Ative Directory integração única (SSO) com TravelPerk

Neste tutorial, você vai aprender a integrar TravelPerk com Azure Ative Directory (Azure AD). Quando integrar o TravelPerk com a AD Azure, pode:

* Controle em Azure AD que tem acesso à TravelPerk.
* Permita que os seus utilizadores sejam automaticamente inscritos no TravelPerk com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma conta TravelPerk com subscrição Premium.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* TravelPerk suporta SSO iniciado **pela SP**

* TravelPerk suporta **provisão** de utilizadores just in time

## <a name="adding-travelperk-from-the-gallery"></a>Adicionando TravelPerk da galeria

Para configurar a integração do TravelPerk no AZure AD, precisa de adicionar o TravelPerk da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** escreva **TravelPerk** na caixa de pesquisa.
1. Selecione **TravelPerk** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-travelperk"></a>Configure e teste Azure AD SSO para TravelPerk

Configure e teste Azure AD SSO com a TravelPerk utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no TravelPerk.

Para configurar e testar a Azure AD SSO com a TravelPerk, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o TravelPerk SSO](#configure-travelperk-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create TravelPerk test user](#create-travelperk-test-user)** - para ter uma contraparte de B.Simon em TravelPerk que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **TravelPerk,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<COMPANY>.travelperk.com/`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<COMPANY>.travelperk.com/accounts/saml2/metadata/<APPLICATION_ID>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<COMPANY>.travelperk.com/accounts/saml2/callback/<APPLICATION_ID>/?acs`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, URL de resposta e identificador. Os valores podem ser encontrados dentro da sua conta TravelPerk: vá a **Configurações da Empresa**  >  **Integrações**  >  **Únicas Signo Único .** Para assistência, visite o [centro de ajuda TravelPerk.](https://support.travelperk.com/hc/en-us/articles/360052450271-How-can-I-setup-SSO-for-Azure-SAML-)

1. A sua aplicação TravelPerk espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. No mapeamento predefinido, o **emailaddress** é mapeado com **user.mail**. No entanto, a aplicação TravelPerk espera que o **emailaddress** seja mapeado com **o nome user.userprincipalname**. Para o TravelPerk, tem de editar o mapeamento do atributo: clique no ícone **Editar** e, em seguida, altere o mapeamento do atributo. Para editar um atributo, basta clicar no atributo para abrir o modo de edição.

    ![image](common/default-attributes.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar TravelPerk,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao TravelPerk.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **TravelPerk**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-travelperk-sso"></a>Configure TravelPerk SSO

Para configurar um único sign-on no lado **travelPerk,** você precisa enviar o **metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipe de [suporte TravelPerk](mailto:trex@travelperk.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-travelperk-test-user"></a>Criar utilizador de teste TravelPerk

Nesta secção, um utilizador chamado B.Simon é criado na TravelPerk. A TravelPerk suporta o provisionamento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no TravelPerk, um novo é criado quando tenta aceder ao TravelPerk.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição de TravelPerk, onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de inscrição travelPerk e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo TravelPerk no Painel de Acesso, este será redirecionado para URL de assinatura travelPerk. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar o TravelPerk, pode impor o controlo da sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
