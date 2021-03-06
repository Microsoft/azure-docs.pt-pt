---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com Catchpoint'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Catchpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: a8515920985c569df74b1e328d6bfe1c4ec97195
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735318"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Tutorial: Azure Ative Directory integração única com Catchpoint

Neste tutorial, aprende-se a integrar o Catchpoint com o Azure Ative Directory (Azure AD). Quando integrar o Catchpoint com a Azure AD, pode:

* Controle o acesso do utilizador ao Catchpoint a partir do Azure AD.
* Ativar o registo automático do Catchpoint para utilizadores com contas AZure AD.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição de Catchpoint com um único sinal de sso ativado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* O Catchpoint suporta sSO iniciado pelo SP e iniciado pelo IDP.
* O Catchpoint suporta o fornecimento do utilizador just-in-time (JIT).

## <a name="add-catchpoint-from-the-gallery"></a>Adicione Catchpoint da galeria

Para configurar a integração do Catchpoint no Azure AD, adicione o Catchpoint à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure com uma conta de trabalho, escola ou Microsoft pessoal.
1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Catchpoint** na caixa de pesquisa.
1. Selecione **Catchpoint** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-catchpoint"></a>Configure e teste Azure AD SSO para catchpoint

Para que o SSO funcione, é necessário ligar um utilizador Azure AD a um utilizador em Catchpoint. Para este tutorial, vamos configurar um utilizador de teste chamado **B.Simon.** 

Complete as seguintes secções:

1. [Configure Azure AD SSO](#configure-azure-ad-sso), para ativar esta funcionalidade para os seus utilizadores.
    * [Crie um utilizador de teste AZure AD,](#create-an-azure-ad-test-user)para testar o Azure AD com B.Simon.
    * [Atribua o utilizador de teste Azure AD,](#assign-the-azure-ad-test-user)para permitir que a B.Simon utilize um único sinal de Adure.
1. [Configure o Catchpoint SSO,](#configure-catchpoint-sso)para configurar as definições de inscrição única no lado da aplicação.
    * [Crie o utilizador de teste Catchpoint](#create-a-catchpoint-test-user), para permitir a ligação da conta de teste B.Simon Azure AD a uma conta de utilizador semelhante em Catchpoint.
1. [Teste SSO,](#test-sso)para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos no portal Azure para ativar o Azure AD SSO:

1. Inicie sessão no portal do Azure.
1. Na página de integração da aplicação **Catchpoint,** encontre a secção **'Gerir'** e selecione **um único sinal de sação.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** selecione o ícone de lápis para editar as definições básicas de **configuração SAML.**

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Configure o modo iniciado para o Catchpoint:
   - Para o modo iniciado pelo **IDP,** insira os valores para os seguintes campos:
     - Para **identificador:**`https://portal.catchpoint.com/SAML2`
     - Url **de resposta:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - Para o modo iniciado pelo **SP,** selecione **Definir URLs adicionais** e introduzir o seguinte valor:
     - Para **URL de inscrição:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. A aplicação Catchpoint espera as afirmações DOL num formato específico. Adicione mapeamentos de atributos personalizados à sua configuração de atributos de token SAML. A tabela a seguir contém a lista de atributos predefinidos:

    | Name | Atributo de origem|
    | ------------ | --------- |
    | Nome dado | user.givenneame |
    | Apelido | utilizador.sobrenome |
    | Emailaddress | user.mail |
    | Name | user.userprincipalname |
    | Identificador de utilizador único | user.userprincipalname |

    ![Imagem da lista de atributos & de atributos do utilizador](common/default-attributes.png)

1. Além disso, a aplicação Catchpoint espera que outro atributo seja aprovado numa resposta SAML. Consulte a tabela seguinte. Este atributo também é pré-povoado, mas pode revê-lo e atualizá-lo de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ------------ | --------- |
    | espaço de nomes | user.assignedrole |

    > [!NOTE]
    > A `namespace` reclamação precisa de ser mapeada com o nome da conta. Este nome de conta deve ser criado com um papel na Azure AD a ser repercutido na resposta SAML. Para obter mais informações sobre papéis em Azure AD, consulte [Configure a alegação de papel emitida no token SAML para aplicações empresariais](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Vá ao set Up Single Sign-On com a página **SAML.** Na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)**. Selecione **Baixar** para guardar o certificado para o seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **set up Catchpoint,** copie os URLs de que necessita num passo posterior.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, utiliza-se o portal Azure para criar um utilizador de teste AD Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, introduza `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de senha show.** Note o valor da palavra-passe apresentada.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon utilize o Azure single sign-on, concedendo acesso ao Catchpoint.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Catchpoint**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **'Adicionar Atribuição'.**
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Clique em **Selecionar** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-catchpoint-sso"></a>Configurar catchpoint SSO

1. Numa janela diferente do navegador web, inscreva-se na aplicação Catchpoint como administrador.

1. Selecione o ícone **Definições** e, em seguida, **O Provedor de Identidade SSO**.

    ![Catchpoint configura screenshot com SSO Identity Provider selecionado](./media/catchpoint-tutorial/configuration1.png)

1. Na página **Single Sign On,** insira os seguintes campos:

   ![Sinal único de ponto de captura na imagem da página](./media/catchpoint-tutorial/configuration2.png)

   Campo | Valor
   ----- | ----- 
   **Espaço de Nomes** | Um valor de espaço de nome válido.
   **Emitente de fornecedor de identidade** | O `Azure AD Identifier` valor do portal Azure.
   **Sinal único no url** | O `Login URL` valor do portal Azure.
   **Certificado** | O conteúdo do ficheiro descarregado `Certificate (Base64)` a partir do portal Azure. Utilize o Bloco de Notas para visualizar e copiar.

   Também pode carregar o **XML de metadados da Federação** selecionando a opção **Upload Metadata.**

1. Selecione **Guardar**.

### <a name="create-a-catchpoint-test-user"></a>Criar um utilizador de teste catchpoint

O Catchpoint suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não tem artigos de ação nesta secção. Se b.Simon já não existe como utilizador no Catchpoint, é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Catchpoint Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição do Catchpoint e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Catchpoint para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo do Catchpoint nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Catchpoint para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


> [!NOTE]
> Quando tiver assinado a aplicação Catchpoint através da página de login, depois de fornecer **Credenciais de Ponto de Acesso,** insira o valor de **Espaço Nome** válido no campo **Credenciais da Empresa (SSO)** e selecione **Login**.
> 
> ![Configuração do ponto de captura](./media/catchpoint-tutorial/loginimage.png)

## <a name="next-steps"></a>Passos seguintes

Depois de configurar o Catchpoint, pode impor o controlo da sessão. Esta precaução protege contra a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão é uma extensão do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
