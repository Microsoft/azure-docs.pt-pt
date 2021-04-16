---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Check Point Identity Awareness | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a Verificação de Identidade de Ponto.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 68705d4d1f870820e30563d2a197ac56349d2445
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520948"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>Tutorial: Azure Ative Directory integração única (SSO) com Verificação de Identidade de Ponto de Verificação

Neste tutorial, você vai aprender a integrar Check Point Identity Awareness com Azure Ative Directory (Azure AD). Quando integrar a Consciência de Identidade de Ponto de Verificação com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Check Point Identity Awareness.
* Capacitar os seus utilizadores a serem automaticamente inscritos para verificar a Consciência de Identidade de Ponto com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Verifique a assinatura ativada para a verificação da identidade de ponto de vista (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Check Point Identity Awareness suporta SSO iniciado **SP.**

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>Adicionar Check Point Identity Awareness da galeria

Para configurar a integração da Consciência de Identidade de Check Point em Azure AD, é necessário adicionar a Consciência de Identidade de Ponto de Verificação da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Check Point Identity Awareness** na caixa de pesquisa.
1. Selecione **Check Point Identity Awareness** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>Configure e teste Azure AD SSO para verificação da identidade de ponto de verificação

Configure e teste Azure AD SSO com Check Point Identity Awareness usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Verificação de Identidade de Ponto de Verificação.

Para configurar e testar a Azure AD SSO com a Verificação da Identidade do Ponto de Verificação, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure Check Point Identity Awareness SSO](#configure-check-point-identity-awareness-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Check Point Identity Awareness user](#create-check-point-identity-awareness-test-user)** - para ter uma contraparte de B.Simon em Check Point Identity Awareness que está ligada à representação Azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Check Point Identity Awareness,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<GATEWAY_IP>/connect`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e sinal no URL. Contacte [a equipa de suporte do Cliente de Sensibilização para](mailto:support@checkpoint.com) a Identidade do Ponto de Verificação para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Check Point IdentityAwareness,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Verificação de Identidade de Ponto.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Check Point Identity Awareness**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-check-point-identity-awareness-sso"></a>Configurar check point de consciência de identidade SSO

1. Inscreva-se no site da empresa Check Point Identity Awareness como administrador.

1. Na visualização de **> Gateways & Servers** da SmartConsole, clique em New > Mais > Fornecedor de Identidade > de Identidade do **utilizador/identidade**.

    ![screenshot para novo Fornecedor de Identidade.](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. Execute os seguintes passos na janela **New Identity Provider.**

    ![screenshot para a secção de Fornecedor de Identidade.](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. No campo **Gateway,** selecione o Gateway de Segurança, que precisa de realizar a autenticação SAML.

    b. No campo **Serviço,** selecione a Consciência de **Identidade** a partir do dropdown.

    c. Copiar o valor **do Identificador (ID da Entidade),** cole este valor na caixa de texto **do Identificador** na secção **Configuração Básica saml** no portal Azure.

    d. Copiar **Resposta valor URL,** colar este valor na caixa de texto **URL resposta** na secção **Configuração Básica SAML** no portal Azure.

    e. **Selecione Importar o Arquivo de Metadados** para carregar o Certificado descarregado **(Base64)** a partir do portal Azure.

    > [!NOTE]
    > Em alternativa, também pode **selecionar Inserir Manualmente** para colar manualmente os valores **de ID** e **URL de acesso** manual nos campos correspondentes e fazer o upload do Arquivo de **Certificados** a partir do portal Azure.

    f. Clique em **OK**.

### <a name="create-check-point-identity-awareness-test-user"></a>Criar utilizador de teste de consciência de identidade de ponto de verificação de pontos de verificação

Nesta secção, cria-se um utilizador chamado Britta Simon in Check Point Identity Awareness. Trabalhe com a equipa de apoio à [consciência de identidade do ponto de verificação](mailto:support@checkpoint.com) para adicionar os utilizadores na plataforma de Sensibilização para a Identidade do Ponto de Verificação. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Check Point Identity Awareness SSVD, onde pode iniciar o fluxo de login. 

* Vá ao URL de inscrição de identidade de ponto de verificação diretamente e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo de Verificação de Identidade nas Minhas Apps, este irá redirecionar para Check Point Identity Awareness Sign-on URL. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Check Point Identity Awareness pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


