---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com hightail | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Hightail.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2020
ms.author: jeedes
ms.openlocfilehash: 29f2610c371ecd567a18dc70e51b3eb34754d8ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180868"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hightail"></a>Tutorial: Azure Ative Directory integração única (SSO) com Hightail

Neste tutorial, você vai aprender a integrar Hightail com Azure Ative Directory (Azure AD). Quando integra o Hightail com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Hightail.
* Permita que os seus utilizadores sejam automaticamente inscritos na Hightail com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por um único sign-on (SSO) de cauda.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Hightail suporta **SP e IDP** iniciado SSO
* Hightail suporta **provisão do** utilizador Just In Time

## <a name="adding-hightail-from-the-gallery"></a>Adicionando Hightail da galeria

Para configurar a integração da Hightail em Azure AD, precisa adicionar Hightail da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Hightail** na caixa de pesquisa.
1. Selecione **Hightail** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hightail"></a>Configurar e testar Azure AD único sign-on para Hightail

Configure e teste Azure AD SSO com Hightail usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Hightail.

Para configurar e testar a Azure AD SSO com Hightail, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Hightail SSO](#configure-hightail-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Hightail test user](#create-hightail-test-user)** - para ter uma contraparte de B.Simon em Hightail que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Hightail,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier (Entity ID),** digite o URL: `https://api.spaces.hightail.com/api/v1/saml/consumer`
    
    b. Na caixa de texto **URL de resposta,** digite o URL: `https://api.spaces.hightail.com/api/v1/saml/consumer`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://spaces.hightail.com/corp-login`

1. A aplicação Hightail espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Hightail espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | -------- |-------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | E-mail | user.mail |
    | UserIdentity | user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Hightail,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

> [!NOTE]
> Antes de configurar o Signo Único na aplicação Hightail, por favor adicione o seu domínio de e-mail à lista permitida com a equipa Hightail para que todos os utilizadores que estão a usar este domínio possam utilizar a funcionalidade Single Sign On.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Hightail.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Hightail**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-hightail-sso"></a>Configurar sso de cauda alta

1. Para automatizar a configuração dentro do Hightail, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar Hightail** irá direcioná-lo para a aplicação Hightail. A partir daí, forneça as credenciais de administração para assinar em Hightail. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

1. Se quiser configurar o Hightail manualmente, noutra janela do navegador, abra o portal de administração **Hightail.**

1. Clique no ícone do **utilizador** a partir do canto superior direito da página. 

    ![A imagem mostra o ícone do utilizador.](./media/hightail-tutorial/configure1.png)

1. Clique **no separador Consola de Admin.**

    ![A screenshot mostra o botão 'Ver Admin Console' para o Utilizador.](./media/hightail-tutorial/configure2.png)

1. No menu em cima, clique no separador **SAML** e execute os seguintes passos:

    ![A screenshot mostra o separador SAML onde pode introduzir o Certificado Login U R L e SAML.](./media/hightail-tutorial/configure3.png)

    a. Na caixa de texto **url de login,** cole o valor do URL de **login** copiado do portal Azure.

    b. Abra o certificado codificado base-64 no bloco de notas descarregado do portal Azure, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de texto **do Certificado SAML.**

    c. Clique em **COPY** para copiar o URL do consumidor SAML para o seu exemplo e cole-o na caixa de texto **URL de resposta** na secção de **configuração SAML básica** no portal Azure.

    d. Clique **em Guardar Configurações**.

### <a name="create-hightail-test-user"></a>Criar utilizador de teste hightail

Nesta secção, um utilizador chamado Britta Simon é criado em Hightail. Hightail suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em Hightail, um novo é criado após a autenticação.

> [!NOTE]
> Se precisar de criar um utilizador manualmente, tem de contactar a equipa de suporte da [Hightail](mailto:support@hightail.com).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Hightail Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de login hightail e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Hightail para o qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo hightail no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Hightail para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Hightail, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).