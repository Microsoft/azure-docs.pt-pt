---
title: 'Tutorial: Integração do Azure Ative Directory com a Dropbox Business | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Dropbox Business.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: d4c9d2e290f6a8f07878a019b755c8add2ab69ea
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102488481"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>Tutorial: Integrar o Negócio dropbox com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar o Dropbox Business com o Azure Ative Directory (Azure AD). Quando integra o Dropbox Business com a AD Azure, pode:

* Controle em Azure AD que tem acesso ao Dropbox Business.
* Permita que os seus utilizadores sejam automaticamente inscritos no Dropbox Business com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Dropbox Business assinatura única (SSO) ativada.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

* Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. Dropbox Business suporta SSO iniciado **SP.**

* O Dropbox Business suporta [o fornecimento e desprovisionamento automatizados dos utilizadores.](dropboxforbusiness-tutorial.md)

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-dropbox-business-from-the-gallery"></a>Adicionar Dropbox Business da galeria

Para configurar a integração do Dropbox Business no Azure AD, é necessário adicionar o Dropbox Business da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Dropbox Business** na caixa de pesquisa.
1. Selecione **Dropbox Business** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-dropbox-business"></a>Configure e teste Azure AD SSO para o Negócio dropbox

Configure e teste Azure AD SSO com Dropbox Business usando um utilizador de teste chamado **Britta Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Dropbox Business.

Para configurar e testar a Azure AD SSO com o Dropbox Business, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.    
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Configure o Dropbox Business SSO](#configure-dropbox-business-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Create Dropbox Business test user](#create-dropbox-business-test-user)** - para ter uma contrapartida de Britta Simon no Dropbox Business que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **Dropbox Business,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://www.dropbox.com/sso/<id>`
    
     b. Na caixa de texto **identifier (Entity ID),** digite o valor: `Dropbox`
    
    > [!NOTE]
    > O **Dropbox Sign SSO ID** pode ser encontrado no site Dropbox no Dropbox > > configurações de > de assinatura > URL > de sso de sso único.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **De Negócios set-up,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado Britta Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Dropbox Business.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Dropbox Business**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-dropbox-business-sso"></a>Configurar dropbox negócio SSO

1. Para automatizar a configuração dentro do Dropbox Business, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Dropbox Business** irá direcioná-lo para a aplicação Dropbox Business. A partir daí, forneça as credenciais de administração para assinar no Dropbox Business. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Dropbox Business manualmente, abra uma nova janela do navegador web e aceda ao seu inquilino dropbox business e inscreva-se no seu inquilino do Dropbox Business. e executar os seguintes passos:

    ![Screenshot que mostra a página "Dropbox Business Sign in".](./media/dropboxforbusiness-tutorial/account.png "Configurar o início de sessão único")

4. Clique no **separador Ícone** do Utilizador e selecione **O separador Definições.**

    ![Screenshot que mostra a ação "USER ICON" e "Definições" selecionadas.](./media/dropboxforbusiness-tutorial/configure-1.png "Configurar o início de sessão único")

5. No painel de navegação do lado esquerdo, clique na **consola Admin**.

    ![Screenshot que mostra "Consola Admin" selecionada.](./media/dropboxforbusiness-tutorial/configure-2.png "Configurar o início de sessão único")

6. Na **consola Admin,** clique em **Definições** no painel de navegação esquerdo.

    ![Screenshot que mostra "Definições" selecionadas.](./media/dropboxforbusiness-tutorial/configure-3.png "Configurar o início de sessão único")

7. Selecione a opção **de inscrição única** na secção **Autenticação.**

    ![Screenshot que mostra a secção "Autenticação" com "Single sign-on" selecionado.](./media/dropboxforbusiness-tutorial/configure-4.png "Configurar o início de sessão único")

8. Na secção **de inscrição única,** execute os seguintes passos:  

    ![Screenshot que mostra as definições de configuração "Single sign-on".](./media/dropboxforbusiness-tutorial/configure-5.png "Configurar o início de sessão único")

    a. Selecione **Requerido** como opção a partir do dropdown para o **sign-on único**.

    b. Clique em Adicionar URL de entrada de **inscrição** e na caixa de texto **URL de acesso ao fornecedor de identidade,** cole o valor URL de **login** que copiou a partir do portal Azure e, em seguida, selecione 'Fazer' ( **Sessão).**

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/sso.png "Configurar o início de sessão único")

    c. Clique **no certificado de upload** e, em seguida, navegue no seu ficheiro de certificado **codificado Base64** que descarregou a partir do portal Azure.

    d. Clique no **link Copy** e cole o valor copiado na caixa de texto URL **de Sign-on** do Domínio de **Negócios dropbox e na secção URLs** no portal Azure.

    e. Clique em **Guardar**.

### <a name="create-dropbox-business-test-user"></a>Criar utilizador de teste de negócio dropbox

Nesta secção, um utilizador chamado B.Simon é criado no Dropbox Business. O Dropbox Business suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Dropbox Business, um novo é criado após a autenticação.

>[!Note]
>Se precisar de criar um utilizador manualmente, contacte a [equipa de suporte do Cliente Comercial Dropbox](https://www.dropbox.com/business/contact)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de início de sessão do Dropbox Business, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição de negócios dropbox e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo do Dropbox Business nas Minhas Apps, este será redirecionado para o URL de sign-on do Negócio dropbox. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Dropbox Business, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).