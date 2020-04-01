---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com Zscaler Three [ Integração de um único sign-on do Azure Ative Diretório) com zscaler Três [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Zscaler Three.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b23abcf9a39ce7f6d77bc40e7143505bc68e8b72
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "72554981"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-three"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Zscaler Three

Neste tutorial, você vai aprender a integrar zscaler 3 com o Azure Ative Directory (Azure AD). Quando integrar o Zscaler 3 com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Zscaler 3.
* Ative que os seus utilizadores sejam automaticamente inscritos no Zscaler Three com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Zscaler Três assinaturas únicas ativadas (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Zscaler Três suporta **SP** iniciado SSO

* Zscaler Três suporta o fornecimento de utilizadores **justo no tempo**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-zscaler-three-from-the-gallery"></a>Adicionando Zscaler Três da galeria

Para configurar a integração do Zscaler Three em Azure AD, você precisa adicionar Zscaler Three da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite Zscaler 3** na caixa de pesquisa.
1. **Selecione Zscaler Three** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-three"></a>Configure e teste Azure AD único sign-on para Zscaler Três

Configure e teste Azure AD SSO com Zscaler Three utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Zscaler Three.

Para configurar e testar o Azure AD SSO com o Zscaler Three, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Zscaler Three SSO](#configure-zscaler-three-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Crie o utilizador de **[teste Zscaler Three](#create-zscaler-three-test-user)** - para ter uma contrapartida de B.Simon em Zscaler Three que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Zscaler Three,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://login.zscalerthree.net/sfc_sso`

1. A sua aplicação Zscaler Three espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

6. Além de acima, a aplicação Zscaler Three espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.
    
    | Nome | Atributo fonte |
    | ---------| ------------ |
    | membroOf     | user.atribuídos |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) para saber como configurar o papel em Azure AD

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar Zscaler Três,** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon use o único sign-on Azure, concedendo acesso ao Zscaler 3.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione Zscaler Three**.
1. No diálogo **Utilizadores e grupos,** selecione o utilizador como **Britta Simon** da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_users.png)

1. A partir do diálogo **Select Role,** escolha a função de utilizador apropriada na lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_roles.png)

1. No diálogo **adicionar atribuição,** selecione o botão **Atribuir.**

    ![image](./media/zscaler-three-tutorial/tutorial_zscalerthree_assign.png)

## <a name="configure-zscaler-three-sso"></a>Configure Zscaler Três SSO

1. Para automatizar a configuração dentro do Zscaler Three, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Zscaler 3** irá direcioná-lo para a aplicação Zscaler Three. A partir daí, forneça as credenciais de administração para assinar no Zscaler 3. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração](common/setup-sso.png)

3. Se quiser configurar o Zscaler Three manualmente, abra uma nova janela do navegador web e inscreva-se no seu site da empresa Zscaler Three como administrador e execute os seguintes passos:

4. Vá à **Administração > Autenticação > Definições** de Autenticação e execute os seguintes passos:
   
    ![Administration](./media/zscaler-three-tutorial/ic800206.png "Administração")

    a. No tipo de autenticação, escolha **SAML**.

    b. Clique **em Configurar SAML**.

5. Na janela **Edit SAML,** execute os seguintes passos: e clique em Guardar.  
            
    ![Gerir utilizadores & autenticação](./media/zscaler-three-tutorial/ic800208.png "Gerir utilizadores & autenticação")
    
    a. Na caixa de texto URL do **Portal SAML,** colá o URL de **login** que copiou do portal Azure.

    b. Na caixa de texto 'Atribuição de **Nome de login',** introduza **o NameID**.

    c. Clique no **Upload**, para fazer upload do certificado de assinatura Azure SAML que descarregou do portal Azure no **Certificado SSL Público**.

    d. Alternar o **fornecimento automático Enable SAML**.

    e. Na caixa de texto do nome do **utilizador,** introduza o nome do **ecrã** se pretender ativar o fornecimento automático de SAML para atributos displayName.

    f. Na caixa de texto de atribuição de nome de **grupo,** introduza **o membroSe** se pretender ativar o fornecimento automático sAML para membros De atributos.

    g. No **departamento** de atribuição de nome de **departamento,** se pretender ativar o fornecimento automático de SAML para atributos do departamento.

    h. Clique em **Guardar**.

6. Na página de diálogo de autenticação do **utilizador Configurar,** execute os seguintes passos:

    ![Administração](./media/zscaler-three-tutorial/ic800207.png)

    a. Paire sobre o menu **de ativação** perto do canto inferior esquerdo.

    b. Clique em **Ativar**.

## <a name="configuring-proxy-settings"></a>Definir configurações de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para configurar as definições de procuração no Internet Explorer

1. Inicie **o Internet Explorer**.

2. Selecione **opções** de Internet a partir do menu **Ferramentas** para abrir o diálogo internet **Options.**   
    
     ![Opções de Internet](./media/zscaler-three-tutorial/ic769492.png "Opções de Internet")

3. Clique no separador **Ligações.**   
  
     ![Ligações](./media/zscaler-three-tutorial/ic769493.png "Ligações")

4. Clique nas **definições lan** para abrir o diálogo lan **Definições.**

5. Na secção proxy do servidor, execute os seguintes passos:   
   
    ![Servidor proxy](./media/zscaler-three-tutorial/ic769494.png "Servidor proxy")

    a. Selecione **Utilize um servidor proxy para o seu LAN**.

    b. Na caixa de texto 'Endereço', digite **o gateway. Zscaler Three.net.**

    c. Na caixa de texto portuário, tipo **80**.

    d. Selecione **servidor de procuração bypass para endereços locais**.

    e. Clique **em OK** para fechar o diálogo de **definições da Rede local (LAN).**

6. Clique **em OK** para fechar o diálogo das Opções de **Internet.**

### <a name="create-zscaler-three-test-user"></a>Criar zscaler três utilizador de teste

Nesta secção, um utilizador chamado B.Simon é criado em Zscaler Three. Zscaler Three suporta o fornecimento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Zscaler Three, um novo é criado quando se tenta aceder ao Zscaler Three.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte a equipa de [suporte Zscaler Three](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Zscaler Three no Painel de Acesso, deve ser automaticamente inscrito no Zscaler Three para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente zscaler três com Azure AD](https://aad.portal.azure.com/)

