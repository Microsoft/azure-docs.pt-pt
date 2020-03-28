---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com boomi [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22a36d5e6c36008c3a574cbcf9be8ec4f52b82b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77086444"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com boomi

Neste tutorial, aprenderá a integrar boomi com o Azure Ative Directory (Azure AD). Quando integrar o Boomi com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Boomi.
* Permita que os seus utilizadores sejam automaticamente inscritos na Boomi com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada por um único sinal (SSO) da Boomi.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Boomi suporta **IDP** iniciado SSO
* Assim que configurar o Boomi, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-boomi-from-the-gallery"></a>Adicionando Boomi da galeria

Para configurar a integração do Boomi em Azure AD, precisa de adicionar boomi da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** **digite Boomi** na caixa de pesquisa.
1. Selecione **Boomi** no painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-boomi"></a>Configure e teste Azure AD single sign-on para Boomi

Configure e teste Azure AD SSO com Boomi utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Boomi.

Para configurar e testar o Azure AD SSO com boomi, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Boomi SSO](#configure-boomi-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Create Boomi test user](#create-boomi-test-user)** - para ter uma contrapartida de B.Simon em Boomi que está ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Boomi,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** se tiver **ficheirode metadados do Prestador** de Serviços e pretender configurar no modo iniciado do **IDP,** execute os seguintes passos:

    a. Clique no **ficheiro de metadados de upload**.

    ![Upload ficheiro de metadados](common/upload-metadata.png)

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    ![escolher ficheiro de metadados](common/browse-upload-metadata.png)

    c. Após o ficheiro de metadados ser carregado com sucesso, os valores de URL do **Identificador** e **da Resposta** ficam povoados automaticamente na secção de Configuração Básica do SAML.

    ![image](common/idp-intiated.png)

    > [!Note]
    > Obterá o ficheiro de **metadados** do Fornecedor de Serviços da secção **Configure Boomi SSO,** o que é explicado mais tarde no tutorial. Se os valores de URL do **Identificador** e **da Resposta** não forem automaticamente polerados, preencha os valores manualmente de acordo com a sua exigência.

1. A aplicação Boomi espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos saml. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Boomi espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome |  Atributo fonte|
    | ---------------|  --------- |
    | FEDERATION_ID | utilizador.mail |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **'set up Boomi',** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Boomi.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Boomi**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-boomi-sso"></a>Configure Boomi SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Boomi como administrador.

1. Navegue para **o nome da empresa** e vá para **configurar**.

1. Clique no separador **Opções SSO** e execute abaixo os passos.

    ![Configure um único sinal no lado da aplicação](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Verifique ativar a caixa **de verificação de sinal único SAML.**

    b. Clique **em Importar** para fazer o upload do certificado descarregado da Azure AD para o Certificado de Fornecedor de **Identidade**.

    c. Na caixa de texto URL do Fornecedor de **Identidade,** coloque o valor do URL de **Login** a partir da janela de configuração da aplicação Azure AD.

    d. Para **localização de id da Federação,** selecione o Id da Federação está em FEDERATION_ID botão de rádio do elemento **Atributo.**

    e. Copie o **URL AtomSphere MetaData,** vá ao **URL MetaData** através do browser à sua escolha e guarde a saída para um ficheiro. Faça upload do **URL MetaData** na secção **de Configuração Básica SAML** no portal Azure.

    f. Clique no botão **Guardar.**

### <a name="create-boomi-test-user"></a>Criar o utilizador de teste Boomi

Para permitir que os utilizadores da AD Azure inscrevam-se no Boomi, devem ser aprovisionados em Boomi. No caso do Boomi, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para fornecer uma conta de utilizador, execute os seguintes passos:

1. Inscreva-se no site da empresa Boomi como administrador.

1. Depois de iniciar sessão, navegue para **a Gestão do Utilizador** e vá até aos **Utilizadores.**

    ![Utilizadores](./media/boomi-tutorial/tutorial_boomi_001.png "Utilizadores")

1. Clique **+** no ícone e o diálogo de funções de **utilizador Adicionar/Manter** abre.

    ![Utilizadores](./media/boomi-tutorial/tutorial_boomi_002.png "Utilizadores")

    ![Utilizadores](./media/boomi-tutorial/tutorial_boomi_003.png "Utilizadores")

    a. Na caixa de texto de **endereço de e-mail do Utilizador,** digite o e-mail do utilizador como B.Simon@contoso.com.

    b. Na caixa de texto **de primeiro nome,** digite o primeiro nome do utilizador como B.

    c. Na caixa de texto **De apelido,** digite o último nome do utilizador como Simon.

    d. Introduza o **ID**da Federação do utilizador . Cada utilizador deve ter um ID da Federação que identifique exclusivamente o utilizador dentro da conta.

    e. Atribuir a função **de Utilizador Padrão** ao utilizador. Não designe a função de Administrador porque isso lhes daria acesso normal à Atmosfera, bem como acesso único ao sign-on.

    f. Clique em **OK**.

    > [!NOTE]
    > O utilizador não receberá um e-mail de notificação de boas-vindas contendo uma palavra-passe que possa ser usada para iniciar sessão na conta AtomSphere porque a sua palavra-passe é gerida através do fornecedor de identidade. Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Boomi ou APIs fornecidas pela Boomi para fornecer contas de utilizador AAD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Boomi no Painel de Acesso, deve ser automaticamente inscrito no Boomi para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Experimente Boomi com Azure AD](https://aad.portal.azure.com/)
