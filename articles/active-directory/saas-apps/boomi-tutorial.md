---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Boomi Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Boomi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.openlocfilehash: e14ef0c039fdf07d50c09fe57dc3cac222be524d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456879"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Tutorial: Azure Ative Directory integração única (SSO) com boomi

Neste tutorial, você vai aprender a integrar Boomi com Azure Ative Directory (Azure AD). Quando integrar o Boomi com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Boomi.
* Ative os seus utilizadores a serem automaticamente inscritos no Boomi com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Boomi (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Boomi apoia **IDP** iniciado SSO
* Assim que configurar o Boomi, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-boomi-from-the-gallery"></a>Adicionando Boomi da galeria

Para configurar a integração do Boomi no Azure AD, precisa adicionar Boomi da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **da galeria,** **escreva Boomi** na caixa de pesquisa.
1. Selecione **Boomi** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Configurar e testar Azure AD único sinal para Boomi

Configure e teste Azure AD SSO com Boomi usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Boomi.

Para configurar e testar a Azure AD SSO com a Boomi, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Boomi SSO](#configure-boomi-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Boomi test user](#create-boomi-test-user)** - para ter uma contraparte de B.Simon em Boomi que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Boomi,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços** e desejar configurar no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Após o carregamento com sucesso do ficheiro de metadados, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção de Configuração BÁSICA SAML.

    ![A screenshot mostra a Configuração BÁSICA SAML, onde aparecem os valores do Identifier e da Resposta U R L.](common/idp-intiated.png)

    d. Introduza o **URL de inscrição,** tal como `https://platform.boomi.com/AtomSphere.html#build;accountId={your-accountId}` .

    > [!Note]
    > Você receberá o **ficheiro de metadados do Fornecedor** de Serviços na secção **Configure Boomi SSO,** que é explicado mais tarde no tutorial. Se os valores de URL **de identificação** e **resposta** não forem automaticamente poluídos, preencha os valores manualmente de acordo com o seu requisito.

1. A aplicação Boomi espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![A screenshot mostra atributos do utilizador & Reclamações com valores predefinidos, tais como givenname user.givenname e Emailaddress User.mail.](common/default-attributes.png)

1. Além de acima, a aplicação Boomi espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name |  Atributo de origem|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar Boomi,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Boomi.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Boomi**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-boomi-sso"></a>Configure Boomi SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Boomi como administrador.

1. Navegue para **o nome da empresa** e vá para **configurar.**

1. Clique no separador **Opções SSO** e execute abaixo dos passos.

    ![Configurar Sign-On única no lado da aplicação](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Verifique ativar a caixa de verificação **DE Inscrição Única SAML.**

    b. Clique **em Importar** para fazer o upload do certificado descarregado de Azure AD para Certificado de Fornecedor de **Identidade.**

    c. Na caixa de texto **URL do Fornecedor de Identidade,** coloque o valor do URL de **login** a partir da janela de configuração da aplicação AD Azure.

    d. Para **localização de identificação da Federação,** selecione o **ID da Federação está no botão de** rádio do elemento atributo FEDERATION_ID.

    e. Copie o **URL AtomSphere MetaData,** vá ao **URL MetaData** através do navegador à sua escolha e guarde a saída para um ficheiro. Faça o upload do **URL MetaData** na secção **de Configuração BÁSICA SAML** no portal Azure.

    f. Clique no botão **Guardar.**

### <a name="create-boomi-test-user"></a>Criar utilizador de teste Boomi

Para permitir que os utilizadores da Azure AD inscrevam-se no Boomi, devem ser a provisionados no Boomi. No caso da Boomi, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para obter uma conta de utilizador, execute os seguintes passos:

1. Inscreva-se no site da empresa Boomi como administrador.

1. Depois de iniciar sessão, navegue para a **Gestão do Utilizador** e vá aos **Utilizadores**.

    ![A screenshot mostra a página de Gestão do Utilizador com os Utilizadores selecionados.](./media/boomi-tutorial/tutorial_boomi_001.png "Utilizadores")

1. Clique **+**  no ícone e o diálogo **'Adicionar/Manter os papéis do utilizador'** abre.'

    ![A screenshot mostra o ícone + selecionado.](./media/boomi-tutorial/tutorial_boomi_002.png "Utilizadores")

    ![A screenshot mostra as funções de utilizador add / keep onde configura um utilizador.](./media/boomi-tutorial/tutorial_boomi_003.png "Utilizadores")

    a. Na caixa de texto **do endereço de e-mail** do Utilizador, digite o e-mail do utilizador como B.Simon@contoso.com .

    b. Na caixa de texto do **primeiro nome,** digite o primeiro nome do utilizador como B.

    c. Na caixa de texto **do último nome,** digite o último nome de utilizador como Simon.

    d. Introduza o **ID**da Federação do Utilizador . Cada utilizador deve ter um ID da Federação que identifique exclusivamente o utilizador dentro da conta.

    e. Atribua a função **utilizador padrão** ao utilizador. Não atribua a função de Administrador porque isso lhes daria acesso normal à Atmosfera, bem como um único acesso de sessão de acesso.

    f. Clique em **OK**.

    > [!NOTE]
    > O utilizador não receberá um e-mail de notificação bem-vindo contendo uma senha que pode ser usada para iniciar sessão na conta AtomSphere porque a sua palavra-passe é gerida através do fornecedor de identidade. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Boomi ou APIs fornecidas pela Boomi para fornecer contas de utilizador AAD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Boomi no Painel de Acesso, deverá ser automaticamente inscrito no Boomi para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Experimente Boomi com Azure AD](https://aad.portal.azure.com/)