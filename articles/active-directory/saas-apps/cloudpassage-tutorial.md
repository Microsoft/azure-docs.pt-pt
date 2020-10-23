---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com CloudPassage Microsoft Docs'
description: Saiba como configurar um único sinal de insuflação entre o Azure Ative Directory e o CloudPassage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: b720f7e49fc0679de5c3f430122bab05d5b706f8
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455775"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Tutorial: Azure Ative Directy integração única (SSO) com CloudPassage

Neste tutorial, você vai aprender a integrar CloudPassage com Azure Ative Directory (Azure AD). Quando integrar o CloudPassage com AD Azure, pode:

* Controle em Azure AD que tem acesso ao CloudPassage.
* Permita que os seus utilizadores sejam automaticamente inscritos no CloudPassage com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por CloudPassage (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* CloudPassage suporta **SSO** iniciado sp

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-cloudpassage-from-the-gallery"></a>Adicionar CloudPassage da galeria

Para configurar a integração do CloudPassage no AD Azure, precisa de adicionar CloudPassage da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite CloudPassage** na caixa de pesquisa.
1. Selecione **CloudPassage** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Configurar e testar a Azure AD um único sinal para cloudPassage

Configure e teste Azure AD SSO com CloudPassage usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no CloudPassage.

Para configurar e testar o Azure AD SSO com CloudPassage, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure cloudPassage SSO](#configure-cloudpassage-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create CloudPassage test user](#create-cloudpassage-test-user)** - para ter uma contraparte de B.Simon em CloudPassage que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **CloudPassage,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

     a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://portal.cloudpassage.com/saml/init/accountid`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://portal.cloudpassage.com/saml/consume/accountid` . Pode obter o seu valor para este atributo clicando na **documentação de configuração SSO** na secção **Definições de Inscrição única** do seu portal CloudPassage.

    ![A screenshot mostra o portal CloudPassage com o link de documentação de configuração S S O chamado.](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e URL de resposta Sign-On reais. Contacte [a equipa de suporte do Cliente CloudPassage](https://www.cloudpassage.com/company/contact/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação CloudPassage espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação CloudPassage espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | nome de primeiro nome |user.givenname |
    | último nome |utilizador.sobrenome |
    | e-mail |user.mail |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar cloudPassage,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao CloudPassage.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **CloudPassage**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cloudpassage-sso"></a>Configurar cloudPassage SSO

1. Numa janela de navegador diferente, inscreva-se no site da empresa CloudPassage como administrador.

1. No menu em cima, clique em **Definições**e, em seguida, clique em **Administração do Site**. 
   
    ![A screenshot mostra o site CloudPassage com a Administração do Site selecionada.][12]

1. Clique no **separador Definições de Autenticação.** 
   
    ![A screenshot mostra o site CloudPassage com o separador Definições de Autenticação selecionado.][13]

1. Na secção **Definições de Inscrição única,** execute os seguintes passos: 
   
    ![A screenshot mostra a secção 'Definições de início único' onde pode introduzir as informações neste passo.][14]

    a. Selecione Ative a caixa de verificação **SSO Setup.(SSO Setup Documentation).**
    
    b. Colar **O identificador AZure AD** na caixa de texto **URL do emitente SAML.**
  
    c. Cole **URL de login** na caixa de texto **URL do ponto final SAML.**
  
    d. Cole **URL logout** na caixa de texto da **página de aterragem logout.**
  
    e. Abra o seu certificado descarregado no bloco de notas, copie o conteúdo do certificado descarregado na sua área de transferência e, em seguida, cole-o na caixa de texto **do certificado x 509.**
  
    f. Clique em **Guardar**.

### <a name="create-cloudpassage-test-user"></a>Criar utilizador de teste CloudPassage

O objetivo desta secção é criar um utilizador chamado B.Simon in CloudPassage.

**Para criar um utilizador chamado B.Simon in CloudPassage, execute os seguintes passos:**

1. Iniciar s-on no site da empresa **CloudPassage** como administrador. 

1. Na barra de ferramentas na parte superior, clique em **Definições**e, em seguida, clique em **Administração do Site**. 
   
    ![A screenshot mostra cloudPassage com a Administração do Site selecionada.][22] 

1. Clique no **separador Utilizadores** e, em seguida, clique em **Adicionar Novo Utilizador**. 
   
    ![O Screenshot mostra a Administração do Site CloudPassage com o separador Utilizadores selecionado e a opção de adicionar novo utilizador.][23]

1. Na secção **Adicionar Novo Utilizador,** execute os seguintes passos: 
   
    ![A screenshot mostra a secção Adicionar Novo Utilizador onde pode especificar as informações do utilizador.][24]
    
    a. Na caixa de texto **Name First,** escreva Britta. 
  
    b. Na caixa de texto **do último nome,** escreva Simon.
  
    c. Na caixa de texto do **nome de utilizador,** na caixa de texto **de email** e na caixa de texto **retype Email,** digite o nome de utilizador da Britta em Azure AD.
  
    d. Como **Tipo de Acesso**, selecione Enable Halo Portal **Access**.
  
    e. Clique em **Adicionar**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo CloudPassage no Painel de Acesso, deverá ser automaticamente inscrito no CloudPassage para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente cloudPassage com Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png