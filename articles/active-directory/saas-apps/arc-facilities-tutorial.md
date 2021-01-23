---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com instalações ARC | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a ARC Facilities.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 2ca26d9730c335023864c071657522270880f74a
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735451"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-arc-facilities"></a>Tutorial: Azure Ative Directy integração única (SSO) com instalações ARC

Neste tutorial, você vai aprender a integrar as Arc Facilities com O Azure Ative Directory (Azure AD). Quando integra as Instalações ARC com AZure AD, pode:

* Controlo em Azure AD que tem acesso às Instalações ARC.
* Permita que os seus utilizadores sejam automaticamente inscritos nas Arc Facilities com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Arc Facilities assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Arc Facilities suporta **IDP** iniciado SSO

* Arc Facilities suporta **provisão de** utilizadores just in time

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-arc-facilities-from-the-gallery"></a>Adicionar instalações ARC da galeria

Para configurar a integração das Arc Facilities em Azure AD, é necessário adicionar as Instalações ARC da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **ARC Facilities** na caixa de pesquisa.
1. Selecione **ARC Facilities** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-arc-facilities"></a>Configurar e testar Azure AD único sinal para instalações ARC

Configure e teste Azure AD SSO com instalações ARC utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado nas Instalações ARC.

Para configurar e testar o Azure AD SSO com instalações ARC, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a ARC Facilities SSO](#configure-arc-facilities-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create ARC Facilities test user](#create-arc-facilities-test-user)** - para ter uma contraparte de B.Simon em Arc Facilities que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **ARC Facilities,** encontre a secção **Gerir** e selecione **um único sinal de saúde.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. A aplicação ARC Facilities espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador.

    ![A screenshot mostra a caixa de diálogo atributos do utilizador com o ícone Edito chamado.](common/edit-attribute.png)

1. Além de acima, a aplicação ARC Facilities espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção **"Atributos & Reclamações** do Utilizador no diálogo **'Pré-visualização)** do Grupo, execute os seguintes passos:

    a. Clique na **caneta** ao lado **de Grupos devolvidos em reivindicação**.

    ![A Screenshot mostra atributos do utilizador & Reclamações com a caneta ao lado de Grupos devolvidos em reivindicação chamada.](./media/arc-facilities-tutorial/config01.png)

    ![O Screenshot mostra as reclamações do grupo com todos os grupos e o Grupo I D selecionados e o botão Guardar chamado.](./media/arc-facilities-tutorial/config02.png)

    b. Selecione **Todos os Grupos** da lista de rádio.

    c. Selecione **Atributo de Origem** do **ID do Grupo**.

    d. Clique em **Guardar**.

    > [!NOTE]
    > A ARC Facilities espera funções para os utilizadores destacados para a aplicação. Por favor, crie estas funções em Azure AD para que os utilizadores possam ser atribuídos as funções apropriadas. Para entender como configurar papéis em Azure AD, consulte [aqui.](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar instalações ARC,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso às Instalações ARC.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **ARC Facilities**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-arc-facilities-sso"></a>Configure instalações arc SSO

Para configurar um único sinal no lado **das Instalações ARC,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de suporte da [ARC Facilities](mailto:support@arcfacilities.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-arc-facilities-test-user"></a>Criar utilizador de teste de instalações ARC

Nesta secção, um utilizador chamado Britta Simon é criado nas Instalações ARC. A ARC Facilities suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir nas Instalações ARC, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito nas Instalações ARC para as quais configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo arc Facilities nas Minhas Apps, deverá ser automaticamente inscrito nas Instalações ARC para as quais configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Próximos passos

Uma vez configurar as Arc Facilities, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
