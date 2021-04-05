---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com workday | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: af001ddd8dc468d06706e63eaf092d1179fe3fdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181378"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Tutorial: Azure Ative Directory integração única (SSO) com Workday

Neste tutorial, você vai aprender a integrar o Workday com o Azure Ative Directory (Azure AD). Quando integrar o Workday com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Workday.
* Ative os seus utilizadores a serem automaticamente inscritos no Workday com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por um único sign-on (SSO) do dia de trabalho.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Workday suporta **SP** iniciado SSO.

* A aplicação Workday Mobile pode agora ser configurada com Azure AD para ativar sSO. Para mais detalhes sobre como configurar, siga [este](workday-mobile-tutorial.md) link.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-workday-from-the-gallery"></a>Adicionar dia de trabalho da galeria

Para configurar a integração do Workday em Azure AD, precisa adicionar Workday da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Workday** na caixa de pesquisa.
1. Selecione **Workday** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-workday"></a>Configure e teste Azure AD SSO para o dia de trabalho

Configure e teste Azure AD SSO com Workday usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Workday.

Para configurar e testar a Azure AD SSO com workday, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
2. **[Configure o Workday](#configure-workday)** para configurar as definições SSO no lado da aplicação.
    1. Crie o utilizador de **[teste Workday](#create-workday-test-user)** para ter uma contraparte de B.Simon em Workday que esteja ligada à representação AD AZure do utilizador.
3. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Workday,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml.htmld`

    c. Na caixa de texto **URL logout,** digite um URL utilizando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de inscrição real, URL de resposta e URL de logo. A url de resposta deve ter um subdomínio, por exemplo: www. wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Usar algo como `http://www.myworkday.com` obras, mas `http://myworkday.com` não funciona. Contacte [a equipa de suporte do Cliente workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A sua aplicação Workday espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação Workday espera que **o identificador** de nomes seja mapeado com **user.mail**, **UPN,** etc., pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![A screenshot mostra atributos do utilizador com o ícone editar selecionado.](common/edit-attribute.png)

    > [!NOTE]
    > Aqui mapeamos o ID de nome com UPN (user.userprincipalname) como padrão. Tem de mapear o ID do nome com o ID do utilizador real na sua conta workday (o seu email, UPN, etc.) para um trabalho bem sucedido de SSO.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Para modificar as opções **de Assinatura** de acordo com o seu requisito, clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Certificado](common/edit-certificate.png) 

    ![Certificado de assinatura SAML](./media/workday-tutorial/signing-option.png)

    a. Selecione **Sign SAML resposta e afirmação** para **a opção de assinatura**.

    b. Clique em **Guardar**

1. Na secção Configurar o **Workday,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Workday.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Workday**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-workday"></a>Configure dia de trabalho

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Workday como administrador.

1. Na **caixa de pesquisa** procure com o nome **EditAr Configuração do Inquilino – Segurança** no lado superior esquerdo da página inicial.

    ![Editar Segurança do Inquilino](./media/workday-tutorial/IC782925.png "Editar Segurança do Inquilino")


1. Na secção **configuração SAML,** execute os seguintes passos:

    ![Configuração SAML](./media/workday-tutorial/IC782926.png "Configuração SAML")

    a.  Selecione **Ativar a autenticação SAML**.

    b.  Clique **em Adicionar Linha**.

1. Na secção **SamL Identity Providers,** execute as seguintes ações para a linha recém-criada.

    a. Execute as seguintes ações para os campos, que são mostradas abaixo.

    ![Fornecedores de Identidade SAML 1](./media/workday-tutorial/IC7829271.png "Fornecedores de Identidade SAML")

    * Na caixa de texto **Name Provider Identity,** escreva um nome de fornecedor (por exemplo: *SPInitiatedSSO).*

    * No portal Azure, na secção Configurar o **Workday,** copie o valor do **identificador Azure AD** e, em seguida, cole-o na caixa de texto do **Emitente.**

    * Abra o **Certificado** descarregado do portal Azure para o Bloco de Notas e cole o conteúdo na caixa de texto **do Certificado x.509.**

    b. Execute as seguintes ações para os campos, que são mostradas abaixo.

    ![Fornecedores de Identidade SAML 2](./media/workday-tutorial/saml-identity-provider-2.png "Fornecedores de Identidade SAML")

    * Clique em **Enable IDP Iniciado logout** checkbox.

    * Na caixa de texto **URL de resposta logout,** escreva **http://www.workday.com** .

    * Na caixa de texto **URL do pedido de logout,** cole o valor URL **logout,** que copiou do portal Azure.

    * Clique na caixa **de verificação SP Iniciado.**

    * Na caixa de texto **iD do fornecedor de serviços,** escreva **http://www.workday.com** .


    * **Selecione Não Esvazie o Pedido de Autenticação Iniciado pelo SP**.

    c. Execute as seguintes ações para os campos, que são mostradas abaixo.

    ![Fornecedores de Identidade SAML 3](./media/workday-tutorial/saml-identity-provider-3.png "Fornecedores de Identidade SAML")

    * No portal Azure, na secção Configurar o **Workday,** copie o valor URL do **Login** e, em seguida, cole-o na caixa de texto **URL do Serviço IdP SSO.**

    * Na caixa de texto **Usada para Ambientes,** selecione os nomes ambientais apropriados a partir do dropdown.

1. Execute os seguintes passos na imagem abaixo.

    ![Workday](./media/workday-tutorial/service-provider.png "Fornecedores de Identidade SAML")

    a. Na caixa de texto **do Fornecedor de Serviços (será depregatada),** escreva **http://www.workday.com** .

    b. Na caixa de texto **do Serviço IDP SSO (será depregatada), escreva** o valor **URL do login.**

    c. **Selecione Não Esvazie o Pedido de Autenticação Iniciado pelo SP (será depretado)**.

    d. Para **autenticação Solicite Método de assinatura**, selecione **SHA256**.

    e. Clique em **OK**.

    > [!NOTE]
    > Certifique-se de que configura corretamente uma única inscrição. Caso ative um único sinal de entrada com configuração incorreta, poderá não conseguir introduzir a aplicação com as suas credenciais e ficar bloqueado. Nesta situação, o Workday fornece um url de login de backup onde os utilizadores podem iniciar sessão usando o seu nome de utilizador normal e palavra-passe no seguinte formato:[O URL do dia de trabalho]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Criar utilizador de teste workday

1. Inscreva-se no site da empresa Workday como administrador.

1. Clique no **Perfil** no canto superior direito, selecione **Home** e Clique no **Diretório** no separador **Aplicações.** 

1. Na página do **Diretório,** selecione **Localizar trabalhadores** no separador visualização.

    ![Encontrar trabalhadores](./media/workday-tutorial/user-directory.png)

1.  Na página **Find Workers,** selecione o utilizador entre os resultados.

1. Na página seguinte,selecione **Job > Segurança do Trabalhador** e a conta **Workday** tem de corresponder com o diretório ativo Azure como o valor **de Identificação** do Nome.

    ![Segurança dos Trabalhadores](./media/workday-tutorial/worker-security.png)

> [!NOTE]
> Para obter mais informações sobre como criar um utilizador de teste de dia de trabalho, contacte a [equipa de suporte do Cliente Workday](https://www.workday.com/en-us/partners-services/services/support.html).

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de início de trabalho, onde pode iniciar o fluxo de login. 

2. Vá diretamente para o URL de início de trabalho e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo workday no Painel de Acesso, deverá ser automaticamente inscrito no Dia de Trabalho para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Workday, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)