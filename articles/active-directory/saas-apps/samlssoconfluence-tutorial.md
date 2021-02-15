---
title: 'Tutorial: Integração do Diretório Ativo Azure com a SAML SSO for Confluence por resolução GmbH | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAML SSO for Confluence por resolução GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 64e358ef6c20c72b1a6a406df1e49ca5a9763b1c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094403"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Tutorial: Integração do Azure Ative Directory com a SAML SSO for Confluence por resolução GmbH

Neste tutorial, você aprenderá a integrar SAML SSO para Confluência através da resolução GmbH com Azure Ative Directory (Azure AD). Quando integrar o SSO SAML para Confluência por resolução GmbH com Azure AD, pode:

* Control em Azure AD que tem acesso a SSO SAML para Confluência por resolução GmbH.
* Permitir que os seus utilizadores sejam automaticamente inscritos no SSO SAML para Confluência por resolução GmbH com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SAML SSO for Confluence por resolução A assinatura ativada pela GmbH (SSO) é ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* SAML SSO for Confluence por resolução GmbH apoia **SP e IDP** iniciado SSO

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Adicione SSO SAML para Confluência por resolução GmbH da galeria

Para configurar a integração do SSO SAML para Confluência através da resolução GmbH em Azure AD, você precisa adicionar SAML SSO para Confluence por resolução GmbH da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** escreva **SAML SSO para Confluência por resolução GmbH** na caixa de pesquisa.
1. Selecione **SAML SSO para Confluência por resolução GmbH** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Configure e teste Azure AD SSO para SAML SSO para confluência por resolução GmbH

Configure e teste Azure AD SSO com SAML SSO para Confluência por resolução GmbH usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no SSO SAML para Confluência por resolução GmbH.

Para configurar e testar a Azure AD SSO com SAML SSO para Confluência por resolução GmbH, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
2. **[Configure o SSO SAML para Confluência por resolução GmbH SSO](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Criar SAML SSO para Confluência por resolução Utilizador](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** de teste GmbH - para ter uma contraparte de Britta Simon em SAML SSO para Confluência por resolução GmbH que está ligada à representação AZure AD do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, no **SSO saml para Confluência por resolução página** de integração de aplicações GmbH, encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/samlsso`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a SAML SSO para a Confluence por resolução a equipa de suporte do cliente gmbH](https://www.resolution.de/go/support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a SAML SSO para Confluência por resolução GmbH.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **SAML SSO para Confluência por resolução GmbH**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>Configure SAML SSO para a Confluência por resolução GmbH SSO

1. Numa janela diferente do navegador web, inicie sessão no seu **SSO SAML para Confluence por resolução O portal de administração GmbH** como administrador.

2. Hover on cog e clique nos **Add-ons**.
    
    ![Screenshot que mostra o ícone "Cog" selecionado e "Add-ons" selecionados a partir do drop-down.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. É redirecionado para a página de Acesso ao Administrador. Introduza a palavra-passe e clique no botão **Confirmar.**

    ![Screenshot que mostra a página "Acesso ao administrador" com o botão "Confirmar" selecionado.](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. No **separador ATLASSIAN MARKETPLACE,** clique **em Localizar novos addons**. 

    ![Screenshot que mostra o separador "Attlassian Marketplace" com "Encontre novos addons" selecionados.](./media/saml-sso-confluence-tutorial/add-on.png)

5. Procure **o sinal único SAML On (SSO) para confluência** e clique no botão **Instalar** para instalar o novo plugin SAML.

    ![Screenshot que mostra a página "Encontrar novos add-ons" com "S A M L Single Sign On (S O) for Confluence" na caixa de pesquisa e no botão "Instalar" selecionado.](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. A instalação plugin irá arrancar. Clique em **Fechar**.

    ![Screenshot que mostra o diálogo "Instalar".](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Screenshot que mostra o "Instalado e pronto para ir!" diálogo com a ação "Fechar" selecionada.](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Clique em **Gerir**.

    ![Screenshot que mostra a página de aplicação "S A M L Single Sign On (S O) for Confluence" com o botão "Gerir" selecionado.](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Clique **em Configurar** para configurar o novo plugin.

    ![Screenshot que mostra a página "Gerir" com o botão "Configurar" selecionado.](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Este novo plugin também pode ser encontrado no separador & SEGURANÇA dos **UTILIZADORES.**

    ![Screenshot que mostra o separador "Utilizadores & Security" com "S A M L SingleSignOn" selecionado.](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. Na página **de configuração do plugin singleSignOn SAML,** clique em Adicionar novo botão **IdP** para configurar as definições do Fornecedor de Identidade.

    ![Screenshot que mostra a página "S A M L SingleSignOn Plugin Configuration", com o botão "Adicionar novo I d P" selecionado.](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. Na escolha da sua página **de Fornecedor de Identidade SAML,** execute os seguintes passos:

    ![Screenshot que mostra a página "Escolha o seu Fornecedor de Identidade S A M L" com as caixas de texto "I d P Type", "Name" e "Descrição" em destaque.](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. Desagrafe **o AD AD** como o tipo IdP.
    
    b. Adicionar **Nome** do Fornecedor de Identidade (por exemplo, Azure AD).
    
    c. Adicionar **Descrição** do Fornecedor de Identidade (por exemplo, Azure AD).
    
    d. Clique em **Seguinte**.
    
12. Na página **de configuração do fornecedor de identidade,** clique no botão **Seguinte.**

    ![Screenshot que mostra a página "Configuração do fornecedor de identidade" com o botão "Seguinte" selecionado.](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. Na página **de Metadados IdP de Importação SAML,** execute os seguintes passos:

    ![Screenshot que mostra a página "Import S A M L I d P Metadados" com os botões "Import", "Load File" e "Next" selecionados.](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Clique no botão **'Ficheiro de Carga'** e escolha o ficheiro XML dos metadados que descarregou no Passo 5.

    b. Clique **no botão Importar.**
    
    c. Aguarde brevemente até que a importação tenha sucesso.
    
    d. Clique **no próximo** botão.
    
14. Na página de id do utilizador e página **de transformação,** clique no botão **Seguinte.**

    ![Screenshot que mostra a página "Atributo e transformação do ID do utilizador" com o botão "Seguinte" selecionado.](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. Na **página de criação e atualização do Utilizador,** clique em **Guardar & Seguinte** para guardar as definições.   
    
    ![Screenshot que mostra a página "Criação e atualização do utilizador" com o botão "Save & Next" selecionado.](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. Na página **de Teste das definições,** clique em **fazer o teste de saltar & configurar manualmente** para saltar o teste do utilizador por enquanto. Isto será realizado na secção seguinte e requer algumas configurações no portal Azure. 
    
    ![Screenshot que mostra a página "Testar as suas definições" com o botão "Saltar teste & configurar manualmente" selecionado.](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. Na leitura do diálogo que **aparece, ignorando o teste significa...** clique **em OK**.
    
    ![Configurar Sign-On Individuais](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Criar SSO SAML para Confluência por resolução Utilizador de teste GmbH

Para permitir que os utilizadores da Azure AD iniciem sessão no SSO SAML para Confluência por resolução GmbH, devem ser alojadas na SAML SSO para Confluence por resolução GmbH.  
No SSO SAML for Confluence por resolução GmbH, o provisionamento é uma tarefa manual.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu SSO SAML para Confluence por resolução do site da empresa GmbH como administrador.

2. Hover on cog e clique na **gestão** do Utilizador .

    ![Screenshot que mostra o ícone "Cog" selecionado e "Gestão do utilizador" selecionado no menu.](./media/saml-sso-confluence-tutorial/user-1.png) 

3. Na secção Utilizadores, clique no **separador Adicionar utilizadores.** Na página de diálogo **"Adicionar um Utilizador",** execute os seguintes passos:

    ![Adicionar Empregado](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. Na caixa de texto **username,** escreva o e-mail de um utilizador como Britta Simon.

    b. Na caixa de texto **'Nome Completo',** digite o nome completo do utilizador como Britta Simon.

    c. Na caixa de texto **por e-mail,** digite o endereço de e-mail do utilizador como Brittasimon@contoso.com .

    d. Na caixa de texto **da palavra-passe,** digite a palavra-passe para Britta Simon.

    e. Clique **em Confirmar A Palavra-passe** reentre na palavra-passe.
    
    f. Clique no botão **Adicionar.**

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para SAML SSO para Confluência por resolução Sinal gmbH no URL onde pode iniciar o fluxo de login.  

* Vá ao SSO SAML para Confluência por resolução GMBH Sign-on URL diretamente e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no SSO SAML para Confluência por resolução GmbH para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no SSO SAML para Confluência por resolução, o azulejo gmbH nas Minhas Apps, se configurado no modo SP, será redirecionado para a página de inscrição para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no SSO saml para confluência por resolução GmbH para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado SAML SSO para Confluência por resolução GmbH, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
