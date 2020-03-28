---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Plataforma SAP Cloud [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89ea2c45e16dfeb63801f70fa4480c0d865a890f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160081"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: Integração de Diretório Ativo Azure com plataforma SAP Cloud

Neste tutorial, aprende-se a integrar a Plataforma Cloud SAP com o Azure Ative Directory (Azure AD).
Integrar a Plataforma Cloud SAP com a AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso à Plataforma Cloud SAP.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Plataforma SAP Cloud (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Plataforma Cloud SAP, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura de sinal ativada pela Plataforma SAP Cloud

Depois de concluir este tutorial, os utilizadores de AD Azure que atribuiu à Plataforma Cloud SAP poderão assinar um único sinal na aplicação utilizando a [Introdução ao Painel](../user-help/active-directory-saas-access-panel-introduction.md)de Acesso .

>[!IMPORTANT]
>Tem de implementar a sua própria aplicação ou subscrever uma aplicação na sua conta da Plataforma Cloud SAP para testar um único sinal. Neste tutorial, uma aplicação é implementada na conta.
> 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Plataforma SAP Cloud suporta **SP** iniciado SSO

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Adicionar plataforma de nuvem SAP da galeria

Para configurar a integração da Plataforma SAP Cloud em Azure AD, precisa adicionar a Plataforma Cloud SAP da galeria à sua lista de aplicações saaS geridas.

**Para adicionar a Plataforma Cloud SAP a partir da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **a Plataforma Cloud SAP**, selecione **SAP Cloud Platform** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Plataforma SAP Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on do Azure AD com a Plataforma SAP Cloud com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Plataforma Cloud SAP.

Para configurar e testar o único sinal de Azure AD com a Plataforma SAP Cloud, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SAP Cloud Platform Single Sign-On](#configure-sap-cloud-platform-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-sap-cloud-platform-test-user)** de teste sap Cloud Platform - para ter uma contrapartida de Britta Simon na Plataforma Cloud SAP que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com a Plataforma Cloud SAP, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **Plataforma SAP Cloud,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![Domínio da plataforma de nuvem SAP e informações de inscrição única](common/sp-identifier-reply.png)

    a. Na caixa de texto **SAP** **Cloud.** Este é o URL específico da conta de um recurso protegido na sua aplicação SAP Cloud Platform. O URL baseia-se no seguinte padrão:`https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Este é o URL da aplicação SAP Cloud Platform que requer que o utilizador autentime.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Na caixa de texto **identifier** irá fornecer ao tipo de URL da sua Plataforma Cloud SAP um URL utilizando um dos seguintes padrões: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de Sinal, Identificador e Resposta real. Contacte a equipa de suporte do Cliente da [Plataforma SAP Cloud](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) para obter URL e Identificador de Acesso. Resposta URL que pode obter da secção de gestão de confiança que é explicada mais tarde no tutorial.
    > 
4. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Configure sap cloud plataforma único sign-on

1. Numa janela diferente do navegador web, inscreva-se `https://account.<landscape host>.ondemand.com/cockpit`no Cockpit https://account.hanatrial.ondemand.com/cockpit)da Plataforma Cloud SAP em (por exemplo: .

2. Clique no separador **Trust.**
   
    ![Confiança](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Confiança")

3. Na secção Trust Management, ao abrigo do **Prestador de Serviços Locais,** execute os seguintes passos:

    ![Gestão de Confiança](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Gestão de Confiança")
   
    a. Clique em **Editar**.

    b. Como Tipo de **Configuração,** selecione **Custom**.

    c. Como **Nome do Fornecedor Local,** deixe o valor predefinido. Copie este valor e cole-o no campo **Identificador** na configuração Azure AD para a Plataforma Cloud SAP.

    d. Para gerar uma **chave de assinatura** e um par de chaves de certificado de **assinatura,** clique em **Gerar Par de Chaves**.

    e. Como **Propagação principal,** selecione **Desativado**.

    f. Como **Autenticação forçada,** selecione **Desativado**.

    g. Clique em **Guardar**.

4. Depois de guardar as definições do Fornecedor de **Serviço Local,** execute o seguinte para obter o URL de resposta:
   
    ![Obter Metadados](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Obter Metadados")

    a. Descarregue o ficheiro de metadados da Plataforma SAP Cloud clicando em **Obter Metadados**.

    b. Abra o ficheiro XML de metadados XML da Plataforma SAP Cloud descarregado e, em seguida, localize a etiqueta **ns3:AfirmaçõesConsumerService.**
 
    c. Copie o valor do atributo de **localização** e, em seguida, cole-o no campo URL de **resposta** na configuração Azure AD para a Plataforma Cloud SAP.

5. Clique no separador Fornecedor de **Identidade Fidedigno** e, em seguida, clique em **Adicionar Fornecedor de Identidade Fidedigna**.
   
    ![Gestão de Confiança](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Gestão de Confiança")
   
    >[!NOTE]
    >Para gerir a lista de fornecedores de identidade fidedignos, precisa de ter escolhido o tipo de configuração Personalizada na secção Fornecedor de Serviçolocal. Para o tipo de configuração Predefinido, tem uma confiança não editável e implícita para o Serviço de ID SAP. Para ninguém, não tens nenhuma definição de confiança.
    > 
    > 

6. Clique no separador **Geral** e, em seguida, clique em **Navegar** para carregar o ficheiro de metadados descarregado.
    
    ![Gestão de Confiança](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Gestão de Confiança")
    
    >[!NOTE]
    >Depois de carregar o ficheiro de metadados, os valores para **URL de início de assinatura único,** URL de início **único**e certificado de **assinatura** são preenchidos automaticamente.
    > 
     
7. Clique no separador **Atributos**.

8. No separador **Atributos,** execute o seguinte passo:
    
    ![Atributos](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Atributos") 

    a. Clique em **adicionar atributo baseado em afirmação**e, em seguida, adicione os seguintes atributos baseados em afirmação:
       
    | Atributo de afirmação | Atributo Principal |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |primeiro nome |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |apelido |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >A configuração dos Atributos depende da forma como as aplicações no SCP são desenvolvidas, ou seja, que atributos esperam na resposta SAML e em que nome (Principal Atributo) acedem a este atributo no código.
     > 
    
    b. O **Atributo Padrão** na imagem é apenas para fins de ilustração. Não é necessário fazer o cenário funcionar.  
 
    c. Os nomes e valores do **Principal Atributo** mostrados na imagem dependem da forma como a aplicação é desenvolvida. É possível que a sua aplicação exija mapeamentos diferentes.

### <a name="assertion-based-groups"></a>Grupos baseados em afirmação

Como passo opcional, pode configurar grupos baseados em afirmação para o seu Fornecedor de Identidade de Diretório Ativo Azure.

A utilização de grupos na Plataforma Cloud SAP permite-lhe atribuir de forma dinâmica um ou mais utilizadores a um ou mais funções nas aplicações da Plataforma Cloud SAP, determinadas por valores de atributos na afirmação SAML 2.0. 

Por exemplo, se a afirmação contiver o atributo "*contract=temporário*", você pode querer que todos os utilizadores afetados sejam adicionados ao grupo "*TEMPORÁRIO*". O grupo "*TEMPORARY*" pode conter uma ou mais funções de uma ou mais aplicações implementadas na sua conta SAP Cloud Platform.
 
Utilize grupos baseados em afirmações quando pretender simultaneamente atribuir muitos utilizadores a uma ou mais funções de aplicações na sua conta SAP Cloud Platform. Se pretender atribuir apenas um ou pequeno número de utilizadores a funções específicas, recomendamos atribuí-los diretamente no separador "**Autorizações**" do cockpit da Plataforma Cloud SAP.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso à Plataforma Cloud SAP.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **SAP Cloud Platform**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **SAP Cloud Platform**.

    ![O link SAP Cloud Platform na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-sap-cloud-platform-test-user"></a>Criar o utilizador de teste da Plataforma SAP Cloud

Para permitir que os utilizadores de AD Azure entrem na Plataforma Cloud SAP, deve atribuir-lhes funções na Plataforma Cloud SAP.

**Para atribuir uma função a um utilizador, execute os seguintes passos:**

1. Inicie sessão no cockpit da **Plataforma SAP Cloud.**

2. Execute o seguinte:
   
    ![Autorizações](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Autorizações")
   
    a. Clique **em Autorização**.

    b. Clique no separador **Utilizadores.**

    c. Na caixa de texto **do Utilizador,** digite o endereço de e-mail do utilizador.

    d. Clique em **Atribuir** para atribuir ao utilizador uma função.

    e. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo da Plataforma Cloud SAP no Painel de Acesso, deverá ser automaticamente inscrito na Plataforma Cloud SAP para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

