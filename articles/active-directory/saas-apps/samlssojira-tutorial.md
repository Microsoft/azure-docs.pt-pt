---
title: 'Tutorial: Integração do Diretório Ativo Azure com a SAML SSO para jira pela Resolução GmbH | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML SSO para a Jira por resolução GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: 450db7c01de4ea7db0133ceca962941ae1a927e5
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173273"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Integração do Azure Ative Directory com a SAML SSO para a Jira por resolução GmbH

Neste tutorial, você aprenderá a integrar SAML SSO para Jira através da resolução GmbH com Azure Ative Directory (Azure AD). Quando integrar o SAML SSO para jira por resolução GmbH com Azure AD, pode:

* Controlo em Azure AD que tem acesso a SAML SSO para Jira por resolução GmbH.
* Permita que os seus utilizadores sejam automaticamente inscritos na SAML SSO para jira através da resolução GmbH com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SAML SSO para Jira por resolução A assinatura ativada pela GmbH (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* SAML SSO para Jira por resolução A GmbH apoia **a SP** e **a IDP** iniciada sSO.

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Adicione SAML SSO para Jira por resolução GmbH da galeria

Para configurar a integração do SAML SSO para jira através da resolução GmbH em Azure AD, você precisa adicionar SAML SSO para Jira por resolução GmbH da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** escreva **SAML SSO para Jira por resolução GmbH** na caixa de pesquisa.
1. Selecione **SAML SSO para Jira por resolução GmbH** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Configure e teste Azure AD SSO para SAML SSO para Jira por resolução GmbH

Configure e teste Azure AD SSO com SAML SSO para Jira por resolução GmbH usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na SAML SSO para a Jira por resolução GmbH.

Para configurar e testar a Azure AD SSO com a SAML SSO para a Jira por resolução GmbH, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o SAML SSO para a Jira por resolução GmbH SSO](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar sSo SAML para Jira por resolução Utilizador](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** de teste GmbH - para ter uma contraparte de B.Simon in SAML SSO para Jira por resolução GmbH que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, no **SSO SAML para Jira por resolução página** de integração de aplicações da GmbH, encontre a secção **Gerir** e selecione **um único sinal de solução**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** efetue os seguintes passos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo, se desejar configurar a aplicação no modo **iniciado** SP:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Para o Identificador, URL de resposta e URL de inscrição, substitua **\<server-base-url>** o URL base da sua instância Jira. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure. Se tiver algum problema, contacte-nos na [SAML SSO para a Jira através da resolução da equipa de apoio ao cliente da GmbH.](https://www.resolution.de/go/support)

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** faça o download do **Metadados XML da Federação** e guarde-o para o seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a SAML SSO para Jira por resolução GmbH.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **SAML SSO para Jira por resolução GmbH**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>Configure SAML SSO para Jira por resolução GmbH SSO 

1. Numa janela diferente do navegador web, inscreva-se na sua instância Jira como administrador.

2. Passe por cima da engrenagem no lado direito e clique em **Gerir aplicações**.
    
    ![Screenshot que mostra uma seta apontando para o ícone "Cog" e "Gerir aplicações" selecionadas a partir do drop-down.](./media/samlssojira-tutorial/add-on-1.png)

3. Se for redirecionado para a página De Acesso ao Administrador, insira a **Palavra-passe** e clique no botão **Confirmar.**

    ![Screenshot que mostra a página "Acesso ao Administrador".](./media/samlssojira-tutorial/add-on-2.png)

4. Jira normalmente redireciona-o para o mercado atlassiano. Caso contrário, clique em **Localizar novas aplicações** no painel esquerdo. Procure por **SAML Single Sign On (SSO) para JIRA** e clique no botão **Instalar** para instalar o plugin SAML.

    ![Screenshot que mostra a página "Atlassian Marketplace for JIRA" com uma seta apontando para o botão "Instalar" para a aplicação "S A M L Single Sign On (S O) Jira, S A M L/S O".](./media/samlssojira-tutorial/store.png)

5. A instalação plugin irá arrancar. Quando estiver pronto, clique no botão **Fechar.**

    ![Screenshot que mostra o diálogo "Instalar".](./media/samlssojira-tutorial/store-2.png)

    ![Screenshot que mostra o "Instalado e pronto para ir!" diálogo com o botão "Fechar" selecionado.](./media/samlssojira-tutorial/store-3.png)

6. Em seguida, clique em **Gerir**.

    ![Screenshot que mostra a aplicação "S A M L Single Sign On (S O) Jira, S A M L/S S O" com o botão "Gerir" selecionado.](./media/samlssojira-tutorial/store-4.png)
    
7. Em seguida, clique em **Configurar** para configurar o plugin acabado de instalar.

    ![Screenshot que mostra a página "Gerir apps", com o botão "Configurar" selecionado para a aplicação "S A M L SingleSignOn for Jira".](./media/samlssojira-tutorial/store-5.png)

8. No assistente **de configuração do plugin singleSignOn SAML,** clique em **Adicionar novo IdP** para configurar a Azure AD como um novo Fornecedor de Identidade.

    ![A screenshot mostra a página "Welcome", com o botão "Adicionar novo I d P" selecionado.](./media/samlssojira-tutorial/add-on-4.png) 

9. Na página Escolha a sua página **de Fornecedor de Identidade SAML,** execute os seguintes passos:

    ![Screenshot que mostra a página "Escolha o seu Fornecedor de Identidade S A M L" com as caixas de texto "I d P Type" e "Name" destacadas, e o botão "Seguinte" selecionado.](./media/samlssojira-tutorial/identity-provider.png)
 
    a. Desagrafe **o AD AD** como o tipo IdP.
    
    b. Adicione o **nome** do Fornecedor de Identidade (por exemplo, Azure AD).
    
    c. Adicione uma **descrição** (opcional) do Fornecedor de Identidade (por exemplo, Azure AD).
    
    d. Clique em **Seguinte**.
    
10. Na página **de configuração do fornecedor de identidade,** clique em **Seguinte**.
 
    ![Screenshot que mostra a página "Configuração do fornecedor de identidade".](./media/samlssojira-tutorial/configuration.png)

11. Na página **de Metadados IdP de Importação SAML,** execute os seguintes passos:

    ![Screenshot que mostra a página "Import S A M L I d P Metadados" com a ação "Select Metadata X M L File" selecionada.](./media/samlssojira-tutorial/metadata.png)

    a. Clique no botão **De ficheiro Select Metadas XML** e escolha o ficheiro **Federação de Metadados XML** que descarregou anteriormente.

    b. Clique no botão **Importar.**
     
    c. Aguarde brevemente até que a importação tenha sucesso.  
     
    d. Clique no botão **Seguinte.**
    
12. Na página de id do utilizador e na página **de transformação,** clique no botão **Seguinte.**

    ![Screenshot que mostra a página "Atributo e transformação do utilizador I D" com o botão "Seguinte" selecionado.](./media/samlssojira-tutorial/transformation.png)
    
13. Na página de **criação e atualização** do Utilizador, clique em **Guardar & Seguinte** para guardar as definições.
    
    ![Screenshot que mostra a página "Criação e atualização do utilizador" com o botão "Save & Next" selecionado.](./media/samlssojira-tutorial/update.png)
    
14. Na página **Test your settings,** clique em **"Saltar & configurar manualmente** para saltar o teste do utilizador por enquanto. Isto será realizado na secção seguinte e requer algumas configurações no portal Azure.
    
    ![Screenshot que mostra a página "Testar as suas definições" com o botão "Saltar teste & configurar manualmente" selecionado.](./media/samlssojira-tutorial/test.png)
    
15. Clique **em OK** para saltar o aviso.
    
    ![Screenshot que mostra o diálogo de aviso com o botão "O K" selecionado.](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Criar SSO SAML para Jira por resolução Utilizador de teste GmbH

Para permitir que os utilizadores da Azure AD inscrevam-se na SAML SSO para a Jira por resolução GmbH, devem ser alojadas na SAML SSO para a Jira por resolução GmbH. Para o caso deste tutorial, você tem que fazer o provisionamento à mão. No entanto, existem também outros modelos de provisionamento disponíveis para o plugin SSO SAML por resolução, por exemplo, o provisionamento **just in time.** Consulte a sua documentação na [SAML SSO por resolução GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Se tiver alguma dúvida sobre o mesmo, contacte o suporte de [resolução](https://www.resolution.de/go/support).

**Para obter manualmente uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se na instância Jira como administradora.

2. Passe por cima da engrenagem e selecione **a gestão do utilizador.**

   ![Screenshot que mostra uma seta apontando para o ícone "Cog" com "Gestão do utilizador" selecionada a partir do drop-down.](./media/samlssojira-tutorial/user-1.png)

3. Se for redirecionado para a página 'Acesso ao Administrador', insira a **Palavra-passe** e clique no botão **Confirmar.**

    ![Screenshot que mostra a página "Acesso ao Administrador" com a caixa de texto "Password" realçada.](./media/samlssojira-tutorial/user-2.png) 

4. Na secção de **separador de gestão do Utilizador,** clique em **criar utilizador.**

    ![Screenshot que mostra o separador "Gestão do Utilizador" com o botão "Criar utilizador" selecionado.](./media/samlssojira-tutorial/user-3-new.png) 

5. Na página de diálogo **"Criar novo utilizador",** execute os seguintes passos. Tem de criar o utilizador exatamente como no Azure AD:

    ![Adicionar Empregado](./media/samlssojira-tutorial/user-4-new.png) 

    a. Na caixa de texto **do endereço de e-mail,** digite o endereço de e-mail do utilizador:  <b>BrittaSimon@contoso.com</b> .

    b. Na caixa de texto **'Nome Completo',** escreva o nome completo do utilizador: **Britta Simon**.

    c. Na caixa de texto do nome de **utilizador,** digite o endereço de e-mail do utilizador: <b>BrittaSimon@contoso.com</b> . 

    d. Na caixa de texto **da palavra-passe,** introduza a palavra-passe do utilizador.

    e. Clique **em Criar utilizador** para terminar a criação do utilizador.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para SAML SSO para Jira por resolução Sinal gmbH no URL onde você pode iniciar o fluxo de login.  

* Vá ao SSO SAML para Jira por resolução GMBH Url de acesso diretamente e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no SSO SAML para Jira por resolução GmbH para a qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no SSO SAML para Jira por resolução, o azulejo gmbH nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no SSO SAML para Jira por resolução GmbH para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="enable-sso-redirection-for-jira"></a>Ativar a reorientação do SSO para jira

Como indicado na secção anterior, existem atualmente duas formas de ativar a única inscrição. Quer utilizando o **portal Azure,** quer utilizando **um link especial para a sua instância Jira**. O plugin SSO SAML por resolução Também permite ativar um único sinal de acesso **simplesmente a qualquer URL que indique a sua instância Jira**.

No essencial, todos os utilizadores que acedam a Jira serão redirecionados para o único sinal após ativar uma opção no plugin.

Para ativar o redirecionamento SSO, faça o seguinte no **seu exemplo Jira**:

1. Aceda à página de configuração do plugin SSO SAML em Jira.
1. Clique em **Redirecionamento** no painel esquerdo.

   ![Screenshot parcial da página de configuração do plugin singleSignOn Jira SAML realçando a ligação de redirecionamento na navegação esquerda.](./media/samlssojira-tutorial/configure-1.png)

1. Córique **Ativar o redirecionamento SSO**.

   ![Screenshot parcial da página de configuração do plugin singleSignOn Jira SAML destacando a caixa de verificação selecionada "Enable SSO Redirect".](./media/samlssojira-tutorial/configure-2.png) 

1. Prima o botão **'Guardar definições'** no canto superior direito.

Depois de ativar a opção, ainda pode chegar ao nome de utilizador/senha se a opção **Enable nosso** for assinalada navegando para `https://<server-base-url>/login.jsp?nosso` . Como sempre, substitua **\<server-base-url>** o URL base.

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado SAML SSO para Jira por resolução GmbH, você pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).