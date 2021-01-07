---
title: 'Tutorial: Integração do Azure Ative Directory com a Plataforma NUVEM SAP Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a Plataforma NUVEM SAP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: b15c5a9f9f1e4e144caa2ddaa36d42a2a225b31b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964053"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: Integração do Azure Ative Directory com a Plataforma NUVEM SAP

Neste tutorial, você vai aprender a integrar a Plataforma nuvem SAP com o Azure Ative Directory (Azure AD). Quando integrar a Plataforma nuvem SAP com AZure AD, pode:

* Control em Azure AD que tem acesso à Plataforma Cloud SAP.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Plataforma CLOUD SAP com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Plataforma CLOUD SAP, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada pela Plataforma CLOUD SAP Cloud

Após completar este tutorial, os utilizadores AD AZure que atribuíram à Plataforma cloud SAP poderão assinar a aplicação através da [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

>[!IMPORTANT]
>Tem de implementar a sua própria aplicação ou subscrever uma aplicação na sua conta da Plataforma CLOUD SAP para testar um único sinal. Neste tutorial, é implementada uma aplicação na conta.
> 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Plataforma NUVEM SAP suporta **SSO** iniciado sp

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Adicionando plataforma de nuvem SAP da galeria

Para configurar a integração da Plataforma CLOUD SAP em Azure AD, é necessário adicionar a Plataforma NUVEM SAP da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva plataforma de nuvem SAP** na caixa de pesquisa.
1. Selecione **a Plataforma nuvem SAP** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform"></a>Configure e teste Azure AD SSO para a plataforma sap cloud

Configure e teste Azure AD SSO com plataforma de nuvem SAP usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Plataforma cloud SAP.

Para configurar e testar o Azure AD SSO com a Plataforma NUVEM SAP, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure a Plataforma nuvem SAP SSO](#configure-sap-cloud-platform-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Crie o utilizador](#create-sap-cloud-platform-test-user)** de teste da Plataforma Cloud SAP - para ter uma contrapartida de Britta Simon na Plataforma nuvem SAP que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações da **Plataforma CLOUD SAP,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    ![Sap Cloud Platform Domain e URLs informações únicas de acesso](common/sp-identifier-reply.png)

    a. Na caixa de texto **do Sign On URL,** digite o URL utilizado pelos seus utilizadores para iniciar sing à sua aplicação **SAP Cloud Platform.** Este é o URL específico da conta de um recurso protegido na sua aplicação SAP Cloud Platform. O URL baseia-se no seguinte padrão: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Este é o URL da sua aplicação SAP Cloud Platform que requer que o utilizador autentica.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. Na caixa de texto **identifier** irá fornecer o URL de tipo a URL da sua Plataforma nuvem SAP utilizando um dos seguintes padrões: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL Sign-On real, identifier e URL de resposta. Contacte [a equipa de suporte do cliente da Plataforma NUVEM SAP](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) para obter Sign-On URL e Identifier. URL de resposta pode obter da secção de gestão de confiança que é explicada mais tarde no tutorial.
    > 
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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Plataforma cloud SAP.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **PLATAFORMA NUVEM SAP**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-sap-cloud-platform-sso"></a>Configurar plataforma nuvem SAP SSO

1. Numa janela diferente do navegador, inscreva-se no Cockpit da Plataforma cloud SAP `https://account.<landscape host>.ondemand.com/cockpit` (por exemplo: https://account.hanatrial.ondemand.com/cockpit) .

2. Clique no **separador Trust.**
   
    ![Confiança](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Confiança")

3. Na secção De Gestão Fiduciu de Confiança, no **Âmbito do Prestador de Serviços Local,** execute os seguintes passos:

    ![Screenshot que mostra a secção "Trust Management" com o separador "Fornecedor de Serviços Locais" selecionado e todas as caixas de texto destacadas.](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Gestão de Confiança")
   
    a. Clique em **Editar**.

    b. Como **Tipo de Configuração**, selecione **Custom**.

    c. Como **Nome do Fornecedor Local,** deixe o valor predefinido. Copie este valor e cole-o no campo **Identifier** na configuração AD Azure para a Plataforma nuvem SAP.

    d. Para gerar uma **chave de assinatura** e um par de chaves de certificado de **assinatura,** clique em **Gerar Par de Teclas**.

    e. Como **Propagação Principal**, selecione **Disabled**.

    f. Como **Autenticação de Força**, selecione **Disabled**.

    exemplo, Clique em **Guardar**.

4. Depois de guardar as definições **do Fornecedor de Serviços Locais,** execute o seguinte para obter o URL de resposta:
   
    ![Obter Metadados](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Obter Metadados")

    a. Descarregue o ficheiro de metadados da Plataforma CLOUD SAP clicando em **Obter Metadados**.

    b. Abra o ficheiro XML da Plataforma de Nuvem SAP descarregada e, em seguida, localize a etiqueta **ns3:AssertionConsumerService.**
 
    c. Copie o valor do atributo **Localização** e, em seguida, cole-o no campo **URL de resposta** na configuração AD Azure para a Plataforma nuvem SAP.

5. Clique no **separador Fornecedor de Identidade Fidedigna** e, em seguida, clique em **Adicionar Fornecedor de Identidade Fidedigna.**
   
    ![Screenshot que mostra a página "Trust Management" com o separador "Fornecedor de Identidade Fidedigna" selecionado.](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Gestão de Confiança")
   
    >[!NOTE]
    >Para gerir a lista de fornecedores de identidade fidedignos, precisa de ter escolhido o tipo de configuração personalizada na secção Fornecedor de Serviços Locais. Para o tipo de configuração predefinido, tem uma confiança não editável e implícita no Serviço DE ID SAP. Para Ninguém, não tens nenhuma definição de confiança.
    > 
    > 

6. Clique no separador **Geral** e, em seguida, clique em **procurar** para carregar o ficheiro de metadados descarregado.
    
    ![Gestão de Confiança](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Gestão de Confiança")
    
    >[!NOTE]
    >Após o upload do ficheiro metadados, os valores **para URL único de início de sessão,** URL único de **logo,** e **certificado de assinatura** são preenchidos automaticamente.
    > 
     
7. Clique no separador **Atributos**.

8. No separador **Atributos,** execute o seguinte passo:
    
    ![Atributos](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Atributos") 

    a. Clique **em Adicionar Assertion-Based Atribua** e, em seguida, adicione os seguintes atributos baseados em afirmação:
       
    | Atributo de afirmação | Atributo Principal |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |nome de primeiro nome |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |último nome |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >A configuração dos Atributos depende da forma como as aplicações em SCP são desenvolvidas, ou seja, quais os atributos que esperam na resposta SAML e em que nome (Atributo Principal) acedem a este atributo no código.
     > 
    
    b. O **Atributo Predefinido** na imagem é apenas para fins de ilustração. Não é necessário fazer o cenário funcionar.  
 
    c. Os nomes e valores **do Atributo Principal** mostrados na imagem dependem da forma como a aplicação é desenvolvida. É possível que a sua aplicação exija mapeamentos diferentes.

### <a name="assertion-based-groups"></a>Grupos baseados em afirmação

Como um passo opcional, pode configurar grupos baseados em afirmações para o seu Fornecedor de Identidade Azure Ative.

A utilização de grupos na Plataforma Cloud SAP permite atribuir dinamicamente um ou mais utilizadores a uma ou mais funções nas suas aplicações sap Cloud Platform, determinadas por valores de atributos na afirmação SAML 2.0. 

Por exemplo, se a afirmação contiver o atributo "*contract=temporário*", pode querer que todos os utilizadores afetados sejam adicionados ao grupo "*TEMPORÁRIO*". O grupo "*TEMPORÁRIO*" pode conter uma ou mais funções de uma ou mais aplicações implantadas na sua conta sap cloud Platform.
 
Utilize grupos baseados em afirmações quando pretende atribuir simultaneamente muitos utilizadores a uma ou mais funções de aplicações na sua conta da Plataforma cloud SAP. Se pretender atribuir apenas um ou pequeno número de utilizadores a funções **específicas,** recomendamos a sua atribuição diretamente no separador " Autorizações " do cockpit da Plataforma cloud SAP.

### <a name="create-sap-cloud-platform-test-user"></a>Criar utilizador de teste de plataforma cloud SAP

Para permitir que os utilizadores de Azure AD iniciem sessão na Plataforma CLOUD SAP, tem de atribuir-lhes funções na Plataforma cloud SAP.

**Para atribuir uma função a um utilizador, execute os seguintes passos:**

1. Faça login no cockpit **da Plataforma CLOUD SAP.**

2. Executar o seguinte:
   
    ![Autorizações](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autorizações")
   
    a. Clique **em Autorização**.

    b. Clique no **separador Utilizadores.**

    c. Na caixa de texto do **Utilizador,** digite o endereço de e-mail do utilizador.

    d. Clique **em Atribuir** para atribuir o utilizador a uma função.

    e. Clique em **Guardar**.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada da plataforma de nuvem SAP, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição da plataforma cloud SAP e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo da Plataforma Nuvem SAP nas Minhas Apps, deverá ser automaticamente inscrito na Plataforma cloud SAP para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado a Plataforma nuvem SAP, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).