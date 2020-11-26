---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com o TrendMiner Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TrendMiner.
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
ms.openlocfilehash: 7f7e7126c482038907e5e986d0779827957cb093
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182177"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trendminer"></a>Tutorial: Azure Ative Directory integração única (SSO) com o TrendMiner

Neste tutorial, você vai aprender a integrar o TrendMiner com o Azure Ative Directory (Azure AD). Quando integra o TrendMiner com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao TrendMiner.
* Ative os seus utilizadores a serem automaticamente inscritos no TrendMiner com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por TrendMiner (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* TrendMiner apoia **SP e IDP** iniciado SSO

## <a name="adding-trendminer-from-the-gallery"></a>Adicionar TrendMiner da galeria

Para configurar a integração do TrendMiner no AD Azure, é necessário adicionar o TrendMiner da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite TrendMiner** na caixa de pesquisa.
1. Selecione **TrendMiner** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-trendminer"></a>Configure e teste Azure AD SSO para TrendMiner

Configure e teste Azure AD SSO com TrendMiner usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no TrendMiner.

Para configurar e testar o Azure AD SSO com o TrendMiner, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o TrendMiner SSO](#configure-trendminer-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create TrendMiner test user](#create-trendminer-test-user)** - para ter uma contraparte de B.Simon em TrendMiner que está ligada à representação AD Ad Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **TrendMiner,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER>.trendminer.cloud/security/saml/metadata`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<CUSTOMER>.trendminer.cloud/security/saml/SSO`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<CUSTOMER>.trendminer.cloud/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente TrendMiner](mailto:support@trendminer.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção Configurar o **TrendMiner,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao TrendMiner.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **TrendMiner**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-trendminer-sso"></a>Configurar trendMiner SSO

1. Abra uma nova janela do navegador web e inscreva-se no seu site da empresa TrendMiner como administrador.

1. No menu esquerdo, selecione **SECURITY > Identity Provider**

1. Na página **'Fornecedor de Identidade',** clique em **SAML** e, em seguida, clique no **passo seguinte**.

    ![selecione SAML](./media/trendminer-tutorial/saml.png)

1. Clique no **próximo passo** na página de backup **DA SAML.**

    ![selecione BACKUP SAML](./media/trendminer-tutorial/saml-backup.png)

1. Clique no **próximo passo** para **obter certificados auto-assinados.**

    ![página de certificados de Self-Signed](./media/trendminer-tutorial/self-signed-certificate.png)

1. Podes **passar a** carregar uma chave de assinatura.

    ![carregar uma chave de assinatura](./media/trendminer-tutorial/signing-key.png)

1. No ecrã de **configuração SAML** em **URL base da Entidade,** insira URL de domínio como `https://trendminer.domain.com/`

1. Nos **metadados do fornecedor de identidade,** carrede o **ficheiro Azure Metadata** que copiou a partir do portal Azure e, em seguida, clique no **passo seguinte**.

    ![Configuração SAML](./media/trendminer-tutorial/saml-configuration.png)

1. Na secção **de mapeamento do utilizador SAML,** insira os nomes de utilizador que utilizará para iniciar sessão e clique em **Terminar**.

    ![Mapeamento do utilizador SAML](./media/trendminer-tutorial/user-mapping.png)

### <a name="create-trendminer-test-user"></a>Criar utilizador de teste TrendMiner

Nesta secção, cria-se um utilizador chamado Britta Simon in TrendMiner. Trabalhe com a [equipa de suporte TrendMiner](mailto:support@trendminer.com) para adicionar os utilizadores na plataforma TrendMiner. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o TrendMiner Sign on URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de login trendMiner e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no TrendMiner para o qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo TrendMiner no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no TrendMiner para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos Seguintes

Uma vez configurado o TrendMiner, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).