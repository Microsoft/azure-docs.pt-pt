---
title: 'Tutorial: Integração do Diretório Ativo Azure com a | Marketo Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 433303bf0d51eff3bd3ab37726c9e98e8a766d25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686959"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: Integração do Diretório Ativo Azure com o Marketo

Neste tutorial, aprende-se a integrar o Marketo com o Azure Ative Directory (Azure AD).
A integração do Marketo com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Marketo.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Marketo (Sign-on Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Marketo, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura única habilitada para o marketo

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Marketo apoia **IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-marketo-from-the-gallery"></a>Adicionar Marketo da galeria

Para configurar a integração do Marketo no AD Azure, é necessário adicionar o Marketo da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** **digite Marketo** na caixa de pesquisa.
1. **Selecione Marketo** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>Configurar e testar Azure AD SSO para o Marketo

Nesta secção, você configura e testa Azure AD single sign-on com Marketo com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Marketo.

Para configurar e testar o Azure AD com o Marketo, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar Azure AD SSO com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize O SSO AZure.
2. **[Configure o Marketo SSO](#configure-marketo-sso)** - para configurar as definições SSO no lado da aplicação.
    1. **[Create Marketo test user](#create-marketo-test-user)** - ter uma contraparte de Britta Simon em Marketo que está ligada à representação AD AZure do utilizador.
3. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **do Marketo,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite o URL: `https://saml.marketo.com/sp`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://login.marketo.com/saml/assertion/<munchkinid>`

    c. Na caixa de texto **do Estado de retransmissão,** digite um URL utilizando o seguinte padrão: `https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e estado de retransmissão. Contacte [a equipa de suporte do Cliente Marketo](https://investors.marketo.com/contactus.cfm) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **Configurar Marketo, copie** os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Marketo.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, **selecione Marketo.**
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-marketo-sso"></a>Configurar Marketo SSO

1. Para automatizar a configuração dentro do Marketo, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **set up Marketo** irá direcioná-lo para a aplicação Marketo. A partir daí, forneça as credenciais de administração para assinar no Marketo. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-6.

    ![Configuração de configuração](common/setup-sso.png)

3. Se quiser configurar o Marketo manualmente, numa janela diferente do navegador web, inscreva-se no site da empresa Marketo como administrador.

1. Para obter a ID munchkin da sua aplicação, execute as seguintes ações:
   
    a. Faça login na app Marketo usando credenciais de administração.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar Sign-On1 Individuais](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue no menu Integração e clique no **link Munchkin**.
   
    ![Configurar Sign-On2 Individuais](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copie o ID Munchkin mostrado no ecrã e complete o URL de resposta no assistente de configuração AD Azure.
   
    ![Configurar Sign-On3 Individuais](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Para configurar o SSO na aplicação, siga os passos abaixo:
   
    a. Faça login na app Marketo usando credenciais de administração.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar Sign-On4 Individuais](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue no menu Integração e clique **em 'Signo Único'.**
   
    ![Configurar Sign-On5 Individuais](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Para ativar as Definições SAML, clique em **Editar.**
   
    ![Configurar Sign-On6 Individuais](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Habilidoso** Configurações Sign-On únicas.
   
    f. Cole o **identificador Azure AD,** na caixa de texto **do emitente.**
   
    exemplo, Na caixa de texto ID da **entidade,** insira o URL como `http://saml.marketo.com/sp` .
   
    h. Selecione a localização do ID do utilizador como **elemento identificador de nomes**.
   
    ![Configurar Sign-On7 Individuais](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se o seu Identificador de Utilizador não for o valor UPN, altere o valor no separador Atributo.
   
    i. Faça o upload do certificado, que descarregou a partir do assistente de configuração AD Azure. **Guarde** as definições.
   
    j. Editar as definições de Páginas de Redirecionamento.
   
    k. Cole o **URL de login** na caixa de texto do URL de **login.**
   
    l. Cole o **URL logout** na caixa de texto **URL logout.**
   
    m. No **URL de erro,** copie o **URL de instância de Marketo** e clique em **Guardar** o botão para guardar as definições.
   
    ![Configurar Sign-On8 Individuais](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Para ativar o SSO para os utilizadores, complete as seguintes ações:
   
    a. Faça login na app Marketo usando credenciais de administração.
   
    b. Clique no botão **Admin** no painel de navegação superior.
   
    ![Configurar Sign-On9 Individuais](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue no menu **Segurança** e clique em **Definições de Início de Sessão**.
   
    ![Configurar Sign-On10 Individuais](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Verifique a opção **SSO requere** e **guarde** as definições.
   
    ![Configurar Sign-On11 Individuais](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Criar utilizador de teste marketo

Nesta secção, cria-se um utilizador chamado Britta Simon em Marketo. siga estes passos para criar um utilizador na plataforma Marketo.

1. Faça login na app Marketo usando credenciais de administração.

2. Clique no botão **Admin** no painel de navegação superior.
   
    ![utilizador de teste1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navegue no menu **Segurança** e clique em **Utilizadores & Funções**
   
    ![utilizador de teste2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Clique no link **'Convidar Novo Utilizador'** no separador Utilizadores
   
    ![utilizador de teste3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. No novo assistente do utilizador convidar preencha as seguintes informações
   
    a. Insira o endereço **de e-mail** do utilizador na caixa de texto
   
    ![utilizador de teste4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Insira o **Primeiro Nome** na caixa de texto
   
    c. Insira o **Último Nome**  na caixa de texto
   
    d. Clique **em seguida**

6. No separador **Permissões,** selecione o **userRoles** e clique em **Seguinte**
   
    ![utilizador de teste5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Clique no botão **Enviar** para enviar o convite do utilizador
   
    ![utilizador de teste6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. O utilizador recebe a notificação de e-mail e tem de clicar no link e alterar a palavra-passe para ativar a conta. 

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Marketo para o qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo do Marketo nas Minhas Apps, deverá ser automaticamente inscrito no Marketo para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Marketo, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).