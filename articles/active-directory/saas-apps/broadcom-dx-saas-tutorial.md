---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Broadcom DX SaaS | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Broadcom DX SaaS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: jeedes
ms.openlocfilehash: 35ab4d335560689a7e55bf1a8dff9fcb0bcc7d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601521"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Broadcom DX SaaS

Neste tutorial, você vai aprender a integrar Broadcom DX SaaS com Azure Ative Directory (Azure AD). Quando integrar o Broadcom DX SaaS com Azure AD, pode:

* Controlo em Azure AD que tem acesso à Broadcom DX SaaS.
* Permita que os seus utilizadores sejam automaticamente inscritos na Broadcom DX SaaS com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Broadcom DX SaaS (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A Broadcom DX SaaS apoia o **IDP** iniciado SSO.

* A Broadcom DX SaaS suporta o fornecimento do utilizador **Just In Time.**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>Adicione Broadcom DX SaaS da galeria

Para configurar a integração da Broadcom DX SaaS em Azure AD, é necessário adicionar o Broadcom DX SaaS da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** **digite Broadcom DX SaaS** na caixa de pesquisa.
1. Selecione **Broadcom DX SaaS** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Configurar e testar Azure AD SSO para Broadcom DX SaaS

Configure e teste Azure AD SSO com Broadcom DX SaaS usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Broadcom DX SaaS.

Para configurar e testar o Azure AD SSO com a Broadcom DX SaaS, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Broadcom DX SaaS SSO](#configure-broadcom-dx-saas-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste Broadcom DX SaaS](#create-broadcom-dx-saas-test-user)** - para ter uma contraparte de B.Simon na Broadcom DX SaaS que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Da Broadcom DX SaaS,** encontre a secção **Gerir** e selecione **um único sinal de solução.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `DXI_<TENANT_NAME>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte do Cliente Da Broadcom DX SaaS](mailto:dxi-na1@saas.broadcom.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Broadcom DX SaaS espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Da Broadcom DX SaaS espera que alguns mais atributos sejam repercutidos na resposta SAML, que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name |  Atributo de origem|
    | --------------- | --------- |
    | Group | utilizador.grupos |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Configurar o **SaaS DX da Broadcom,** copie os URL(s) apropriados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Broadcom DX SaaS.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Broadcom DX SaaS**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função &quot;Acesso Predefinido&quot; selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name=&quot;configure-broadcom-dx-saas-sso&quot;></a>Configurar Broadcom DX SaaS SSO

1. Faça login no seu site da empresa Broadcom DX SaaS como administrador.

2. Vá às **Definições** e clique no **separador Utilizadores.**

    ![Utilizadores](./media/broadcom-dx-saas-tutorial/settings.png &quot;Utilizadores")

3. Na secção **de gestão** do Utilizador, clique em elipses e selecione **SAML**.

    ![Gestão de utilizadores](./media/broadcom-dx-saas-tutorial/local.png "Gestão de utilizadores")

4. Na secção **de conta Identifique SAML,** execute os seguintes passos.
   
    ![Conta](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Conta") 

    a. Na caixa de texto **emitente,** cole o valor do **identificador Azure AD** que copiou do portal Azure.

    b. Na caixa de texto URL de login do **Fornecedor de Identidade (IDP),** cole o valor URL de **login** que copiou a partir do portal Azure.

    c. Na caixa de texto URL do **Fornecedor de Identidade (IDP),** cole o valor **URL logout** que copiou a partir do portal Azure.

    d. Abra o Certificado descarregado **(Base64)** do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto do **certificado de fornecedor de identidade.**

    e. Clique **em SEGUINTE**.

5. Na secção **de atributos do Mapa,** preencha os atributos SAML necessários na página seguinte e clique em **SEGUINTE**.

    ![Atributos](./media/broadcom-dx-saas-tutorial/broadcom-2.png "Atributos") 


6. Na secção **grupo de utilizadores Identificar,** introduza o ID do objeto desse grupo e clique em **SEGUINTE**.

    ![Adicionar grupo](./media/broadcom-dx-saas-tutorial/broadcom-3.png "Adicionar grupo") 

7. Na secção **Conta Populate SAML,** verifique os valores povoados e clique em **SAVE**.

    ![Conta SAML](./media/broadcom-dx-saas-tutorial/broadcom-4.png "Conta SAML") 

### <a name="create-broadcom-dx-saas-test-user"></a>Criar utilizador de teste Broadcom DX SaaS

Nesta secção, um utilizador chamado Britta Simon é criado na Broadcom DX SaaS. A Broadcom DX SaaS suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Broadcom DX SaaS, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no SaaS DX da Broadcom para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Da Broadcom DX SaaS nas Minhas Apps, deverá ser automaticamente inscrito no SaaS DX da Broadcom para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos Seguintes

Uma vez configurado o Broadcom DX SaaS, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).