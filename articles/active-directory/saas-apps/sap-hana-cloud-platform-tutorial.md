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
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: c2738e1a6168440adee79ebaa599a313600153a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546768"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: Integração do Azure Ative Directory com a Plataforma NUVEM SAP

Neste tutorial, aprende-se a integrar a Plataforma Nuvem SAP com o Azure Ative Directory (Azure AD).
A integração da Plataforma nuvem SAP com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Plataforma Cloud SAP.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Plataforma CLOUD SAP (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Plataforma CLOUD SAP, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição ativada pela Plataforma CLOUD SAP Cloud

Após completar este tutorial, os utilizadores AD AZure que atribuíram à Plataforma cloud SAP poderão assinar a aplicação através da [Introdução ao Painel de Acesso.](../user-help/active-directory-saas-access-panel-introduction.md)

>[!IMPORTANT]
>Tem de implementar a sua própria aplicação ou subscrever uma aplicação na sua conta da Plataforma CLOUD SAP para testar um único sinal. Neste tutorial, é implementada uma aplicação na conta.
> 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Plataforma NUVEM SAP suporta **SSO** iniciado sp

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Adicionando plataforma de nuvem SAP da galeria

Para configurar a integração da Plataforma CLOUD SAP em Azure AD, é necessário adicionar a Plataforma NUVEM SAP da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar a Plataforma nuvem SAP da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **escreva Plataforma nuvem SAP**, selecione **Plataforma nuvem SAP** a partir do painel de resultados e clique em Adicionar o botão **Adicionar** a aplicação.

     ![Plataforma nuvem SAP na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com uma única placa de sinalização com a Plataforma NUVEM SAP com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Plataforma Cloud SAP.

Para configurar e testar o Azure AD com a Plataforma nuvem SAP, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure a plataforma de nuvem SAP Single Sign-On](#configure-sap-cloud-platform-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Crie o utilizador](#create-sap-cloud-platform-test-user)** de teste da Plataforma Cloud SAP - para ter uma contrapartida de Britta Simon na Plataforma nuvem SAP que está ligada à representação AD AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com a Plataforma nuvem SAP, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações da **Plataforma CLOUD SAP,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

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

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Configurar plataforma nuvem SAP Sign-On

1. Numa janela diferente do navegador, inscreva-se no Cockpit da Plataforma cloud SAP `https://account.<landscape host>.ondemand.com/cockpit` (por exemplo: https://account.hanatrial.ondemand.com/cockpit) .

2. Clique no **separador Trust.**
   
    ![Confiança](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Confiança")

3. Na secção De Gestão Fiduciu de Confiança, no **Âmbito do Prestador de Serviços Local,** execute os seguintes passos:

    ![Gestão de Confiança](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Gestão de Confiança")
   
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
   
    ![Gestão de Confiança](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Gestão de Confiança")
   
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

    a. Clique **em Adicionar Assertion-Based Atribua**e, em seguida, adicione os seguintes atributos baseados em afirmação:
       
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
 
Utilize grupos baseados em afirmações quando pretende atribuir simultaneamente muitos utilizadores a uma ou mais funções de aplicações na sua conta da Plataforma cloud SAP. Se pretender atribuir apenas um ou pequeno número de utilizadores a funções**específicas,** recomendamos a sua atribuição diretamente no separador " Autorizações " do cockpit da Plataforma cloud SAP.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso à Plataforma nuvem SAP.

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações**e, em seguida, selecione **a Plataforma cloud SAP**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **a Plataforma nuvem SAP**.

    ![O link da Plataforma Nuvem SAP na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

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

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Plataforma nuvem SAP no Painel de Acesso, deverá ser automaticamente inscrito na Plataforma cloud SAP para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

