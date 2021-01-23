---
title: 'Tutorial: Integração do Azure Ative Directory com a Salesforce Sandbox | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Salesforce Sandbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: c82b00b1f107dc147cbef6a0ca406f2054321216
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734884"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Tutorial: Azure Ative Directory integração única (SSO) com Salesforce Sandbox

Neste tutorial, você vai aprender a integrar Salesforce Sandbox com Azure Ative Directory (Azure AD). Quando integrar a Salesforce Sandbox com a Azure AD, pode:

* Controlo em Azure AD que tem acesso à Salesforce Sandbox.
* Permita que os seus utilizadores sejam automaticamente inscritos na Salesforce Sandbox com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pela Salesforce Sandbox (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Salesforce Sandbox suporta **SP e IDP** iniciado SSO
* Salesforce Sandbox suporta **provisão de** utilizadores just in time
* Salesforce Sandbox suporta fornecimento [ **automatizado** de utilizadores](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Adicionar Salesforce Sandbox da galeria

Para configurar a integração da Salesforce Sandbox no AZure AD, é necessário adicionar a Salesforce Sandbox da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Salesforce Sandbox** na caixa de pesquisa.
1. Selecione **Salesforce Sandbox** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-salesforce-sandbox"></a>Configure e teste Azure AD SSO para Salesforce Sandbox

Configure e teste Azure AD SSO com Salesforce Sandbox usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Salesforce Sandbox.

Para configurar e testar a Azure AD SSO com a Salesforce Sandbox, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a Salesforce Sandbox SSO](#configure-salesforce-sandbox-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste Salesforce Sandbox](#create-salesforce-sandbox-test-user)** - para ter uma contrapartida de B.Simon na Salesforce Sandbox que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Salesforce Sandbox,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços** e desejar configurar no modo iniciado pelo **IDP,** execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    > [!NOTE]
    > Receberá o ficheiro de metadados do fornecedor de serviços do portal de administração Salesforce Sandbox, que é explicado mais tarde no tutorial.

    c. Após o carregamento com sucesso do ficheiro de metadados, o valor **URL de resposta** será preenchido automaticamente na caixa de texto URL de **resposta.**

    ![image](common/both-replyurl.png)

    > [!Note]
    > Se o valor **URL de resposta** não for automaticamente poluído, preencha o valor manualmente de acordo com o seu requisito.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na secção **Configuração Salesforce Sandbox,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Salesforce Sandbox.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Salesforce Sandbox**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-salesforce-sandbox-sso"></a>Configurar salesforce sandbox SSO

1. Abra um novo separador no seu navegador e inscreva-se na sua conta de administrador da Salesforce Sandbox.

2. Clique na **Configuração** no **ícone de definições** no canto superior direito da página.

    ![Screenshot que mostra o ícone "Definições" no superior direito selecionado e "Configuração" selecionado a partir do drop-down.](./media/salesforce-sandbox-tutorial/configure1.png)

3. Desloque-se para baixo para as **DEFINIÇÕES** no painel de navegação esquerdo, clique em **Identidade** para expandir a secção relacionada. Em seguida, clique em **Definições Sign-On únicas**.

    ![Screenshot que mostra o menu "Definições" no painel esquerdo, com "Definições de Sign-On únicas" selecionadas no menu "Identidade".](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Na página **'Definições Sign-On única',** clique no botão **Editar.**

    ![Screenshot que mostra a página "Configurações Sign-On única" com o botão "Editar" selecionado.](./media/salesforce-sandbox-tutorial/configure3.png)

5. Selecione **SAML Ativado** e, em seguida, clique em **Guardar**.

    ![Screenshot que mostra a página "Configurações Sign-On únicas" com a caixa de verificação "S A M L Enabled" selecionada e o botão "Guardar" selecionado.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Para configurar as definições de inscrição única do SAML, clique em **Novo A partir do Ficheiro de Metadados**.

    ![Screenshot que mostra a página "Configurações Sign-On única" com o botão "Novo do Ficheiro de Metadados" selecionado.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Clique **em Escolher Ficheiro** para carregar o ficheiro XML dos metadados que descarregou a partir do portal Azure e clique em **Criar**.

    ![Screenshot que mostra a página "Configurações de Sign-On única" com os botões "Escolha o Ficheiro" e "Criar" selecionados.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Na página **'Definições Sign-On Único SAML',** os campos povoam-se automaticamente e clicam em guardar.

    ![Screenshot que mostra a página "Configurações Sign-On única" com campos povoados e o botão "Guardar" selecionado.](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Na página **'Definições Sign-On única',** clique no botão **Desembaratos de Transferência** para descarregar o ficheiro de metadados do fornecedor de serviços. Utilize este ficheiro na secção **de Configuração BÁSICA SAML** no portal Azure para configurar os URLs necessários, conforme explicado acima.

    ![Screenshot que mostra a página "Configurações Sign-On única" com o botão "Transferir metadados" selecionado.](./media/salesforce-sandbox-tutorial/configure4.png)

10. Se desejar configurar a aplicação no modo iniciado pela **SP,** seguem-se os pré-requisitos para tal:

    a. Deve ter um domínio verificado.

    b. Você precisa configurar e ativar o seu domínio na Salesforce Sandbox, passos para isso são explicados mais tarde neste tutorial.

    c. No portal Azure, na secção **De Configuração Básica SAML,** clique em **Definir URLs adicionais** e execute o seguinte passo:
  
    ![Salesforce Sandbox Domain e URLs informações únicas de entrada](common/both-signonurl.png)

    Na caixa de texto **URL de entrada de sinais,** digite o valor utilizando o seguinte padrão: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Este valor deve ser copiado do portal Salesforce Sandbox uma vez que tenha ativado o domínio.

11. Na secção **Certificado de Assinatura SAML,** clique em **MetdataS da Federação XML** e, em seguida, guarde o ficheiro xml no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

12. Abra um novo separador no seu navegador e inscreva-se na sua conta de administrador da Salesforce Sandbox.

13. Clique na **Configuração** no **ícone de definições** no canto superior direito da página.

    ![Screenshot que mostra o ícone "Definições" no superior direito selecionado e "Configuração" selecionado a partir do menu suspenso.](./media/salesforce-sandbox-tutorial/configure1.png)

14. Desloque-se para baixo para as **DEFINIÇÕES** no painel de navegação esquerdo, clique em **Identidade** para expandir a secção relacionada. Em seguida, clique em **Definições Sign-On únicas**.

    ![Screenshot que mostra o menu "Definições" no painel de navegação esquerdo, com "Definições de Sign-On únicas" selecionadas no menu "Identidade".](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Na página **'Definições Sign-On única',** clique no botão **Editar.**

    ![Screenshot que mostra a página "Configurações Sign-On única" com o botão "Editar" selecionado.](./media/salesforce-sandbox-tutorial/configure3.png)

16. Selecione **SAML Ativado** e, em seguida, clique em **Guardar**.

    ![Screenshot que mostra a página "Configurações Sign-On únicas" com a caixa "S A M L Enabled" verificada e o botão "Guardar" selecionado.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Para configurar as definições de inscrição única do SAML, clique em **Novo A partir do Ficheiro de Metadados**.

    ![Screenshot que mostra a página "Configurações Sign-On única" e o botão "Novo do Ficheiro de Metadados" selecionado.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Clique **em Escolher O Ficheiro** para carregar o ficheiro XML dos metadados e clique em **Criar**.

    ![Screenshot que mostra a página "Configurações Sign-On única" com o botão "Escolha Ficheiro" e o botão "Criar" selecionado.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Na página **de Definições de Sign-On Única SAML,** os campos povoam-se automaticamente, digita o nome da configuração (por exemplo: *SPSSOWAAD_Test),* na caixa de texto **'Nome'** e clique em guardar.

    ![Screenshot que mostra a página "Configurações Sign-On única" com campos povoados, um nome de exemplo na caixa de texto "Nome" e o botão "Guardar" selecionado.](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Para ativar o seu domínio na Salesforce Sandbox, execute os seguintes passos:

    > [!NOTE]
    > Antes de ativar o domínio, precisa de criar o mesmo na Salesforce Sandbox. Para obter mais informações, consulte [definindo o nome do seu domínio.](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) Uma vez criado o domínio, certifique-se de que está configurado corretamente.

21. No painel de navegação à esquerda na Salesforce Sandbox, clique em **Definições da Empresa** para expandir a secção relacionada e, em seguida, clique em **My Domain**.

    ![Screenshot que mostra as "Configurações da Empresa" e "Meu Domínio" selecionadas a partir do painel de navegação esquerdo.](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Na secção Configuração de **Autenticação,** clique em **Editar**.

    ![Screenshot que mostra a secção "Configuração de autenticação", com o botão "Editar" selecionado.](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Na secção Configuração de **Autenticação,** como **Serviço de Autenticação**, selecione o nome da Definição de Sign-On Única SAML que definiu durante a configuração SSO na Caixa de Areia Salesforce e clique em **Guardar**.

    ![Configurar Sign-On Individuais](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Criar utilizador de teste salesforce Sandbox

Nesta secção, um utilizador chamado Britta Simon é criado na Salesforce Sandbox. A Salesforce Sandbox suporta o provisionamento just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na Salesforce Sandbox, é criado um novo quando tenta aceder à Salesforce Sandbox. Salesforce Sandbox também suporta o fornecimento automático de utilizadores, podendo encontrar mais detalhes [aqui](salesforce-sandbox-provisioning-tutorial.md) sobre como configurar o fornecimento automático do utilizador.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Salesforce Sandbox Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição da Salesforce Sandbox e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Salesforce Sandbox para a qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo salesforce Sandbox nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Caixa de Areia Salesforce para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Próximos passos

Uma vez configurado o Salesforce Sandbox, pode impor controlos de sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)