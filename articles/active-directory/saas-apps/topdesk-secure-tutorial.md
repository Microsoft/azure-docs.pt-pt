---
title: 'Tutorial: Integração do Diretório Ativo Azure com a TOPdesk - Secure | Microsoft Docs'
description: Saiba como configurar um único sinal de inscrição entre o Azure Ative Directory e o TOPdesk - Secure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 5ed23889d8648c65ea0887d2f0f3406b50291f12
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654307"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Tutorial: Integração do Azure Ative Directory com a TOPdesk - Secure

Neste tutorial, você vai aprender a integrar TOPdesk - Secure with Azure Ative Directory (Azure AD). Quando integra o TOPdesk - Secure with Azure AD, pode:

* Control em Azure AD que tem acesso a TOPdesk - Seguro.
* Ativar os seus utilizadores a iniciarem automaticamente o seu contrato com o TOPdesk - Secure (Single Sign-On) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:
 
 * Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Um TOPdesk - Fixe uma assinatura única (SSO)ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* TOPdesk - Suportes seguros **SP** iniciado SSO

## <a name="add-topdesk---secure-from-the-gallery"></a>Adicionar TOPdesk - Seguro a partir da galeria

Para configurar a integração do TOPdesk - Secure in Azure AD, precisa de adicionar TOPdesk - Proteja da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **TOPdesk - Fixe** na caixa de pesquisa.
1. Selecione **TOPdesk - Proteja-se** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>Configure e teste Azure AD SSO para TOPdesk - Seguro

Nesta secção, configura e testa o Azure AD com topdesk - Secure com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em TOPdesk - Secure.

Para configurar e testar a Azure AD um único sinal de inscrição com TOPdesk - Seguro, tem de executar os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    2. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure o TOPdesk - Secure SSO](#configure-topdesk---secure-sso)** - para configurar as definições de Sign-On únicas no lado da aplicação.
    1. **[Crie TOPdesk - Utilizador de teste seguro](#create-topdesk---secure-test-user)** - para ter uma contraparte de Britta Simon em TOPdesk - Secure que está ligada à representação AD Ad do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar a Azure AD um único sinal de inscrição com TOPdesk - Secure, execute os seguintes passos:

1. No portal Azure, na página **TOPdesk - Secure** application integration, selecione **Single sign-on**.

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

3. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<companyname>.topdesk.net`

    b. Na caixa URL do **Identificador,** preencha o URL de metadados TOPdesk que pode obter a partir da configuração TOPdesk. Deve utilizar o seguinte padrão: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a TOPdesk - Equipa de suporte ao Cliente Seguro](https://www.topdesk.com/us/support/) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na **secção Configuração TOPdesk - Secção Segura,** copie os URL(s) apropriados de acordo com o seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao TOPdesk - Secure.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **TOPdesk - Secure**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-topdesk---secure-sso"></a>Configurar TOPdesk - Secure SSO

1. Inscreva-se no seu **SITE TOPdesk - Secure** company como administrador.

2. No menu **TOPdesk,** clique em **Definições**.

    ![Definições](./media/topdesk-secure-tutorial/ic790598.png "Definições")

3. Clique em **Definições de Início** de Sessão .

    ![Definições de início de sessão](./media/topdesk-secure-tutorial/ic790599.png "Definições de início de sessão")

4. Expandir o menu **'Definições de início de sessão'** e, em seguida, clicar em **Geral**.

    ![Geral](./media/topdesk-secure-tutorial/ic790600.png "Geral")

5. Na secção **Secure** da secção de configuração de **login SAML,** execute os seguintes passos:

    ![Definições Técnicas](./media/topdesk-secure-tutorial/ic790855.png "Definições Técnicas")

    a. Clique **em Baixar** para descarregar o ficheiro de metadados públicos e guarde-o localmente no seu computador.

    b. Abra o ficheiro de metadados e, em seguida, localize o nó **'Serviço de Afirmação'.**

    ![Serviço de Apoio ao Consumidor de Afirmação](./media/topdesk-secure-tutorial/ic790856.png "Serviço de Apoio ao Consumidor de Afirmação")

    c. Copie o valor **AfirmaçãoConsumerService,** cole este valor na caixa de texto URL answer em **TOPdesk - Domínio Seguro e URLs.**

6. Para criar um ficheiro de certificado, execute os seguintes passos:

    ![Certificado](./media/topdesk-secure-tutorial/ic790606.png "Certificado")

    a. Abra o ficheiro de metadados descarregado do portal Azure.

    b. Expandir o nó **RoleDescriptor** que tem um **xsi:tipo** de **fed:ApplicationServiceType**.

    c. Copie o valor do nó **X509Certificado.**

    d. Guarde o valor **X509Certificado** copiado localmente no seu computador num ficheiro.

7. Na secção **Público,** clique **em Adicionar**.

    ![Adicionar](./media/topdesk-secure-tutorial/ic790607.png "Adicionar")

8. Na página de diálogo **do assistente de configuração SAML,** execute os seguintes passos:

    ![Assistente de Configuração SAML](./media/topdesk-secure-tutorial/ic790608.png "Assistente de Configuração SAML")

    a. Para fazer o upload do seu ficheiro de metadados descarregado a partir do portal Azure, no portal **Federation Metadas,** clique em **Procurar**.

    b. Para fazer o upload do seu arquivo de certificado, em **Certificado (RSA)**, clique em **Procurar**.

    c. Para **tecla privada (RSA, PKCS8, DER)**, pode carregar a sua própria chave privada ou pode contactar [a equipa de suporte do CLIENTE SEGURO TOPdesk - Secure Client](https://www.topdesk.com/us/support) para obter a chave privada.

    d. Para fazer o upload do ficheiro de logotipo que obteve da equipa de suporte TOPdesk, no **ícone do logotipo,** clique em **Procurar**.

    e. Na caixa de texto do **nome do utilizador,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    f. Na caixa de texto **do nome do Visor,** escreva um nome para a sua configuração.

    exemplo, Clique em **Guardar**.

### <a name="create-topdesk---secure-test-user"></a>Criar TOPdesk - Utilizador de teste seguro

Para permitir que os utilizadores de Azure AD entrem no TOPdesk - Secure, devem ser a provisionados em TOPdesk - Secure.  
No caso do TOPdesk - Secure, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento do utilizador, execute os seguintes passos:

1. Inscreva-se no seu **TOPdesk - Proteja** o site da empresa como administrador.

2. No menu em cima, clique no **TOPdesk \> New Support Files \> \> Operator**.

    ![Operador](./media/topdesk-secure-tutorial/ic790610.png "Operador")

3. No diálogo do **Novo Operador,** execute os seguintes passos:

    ![Novo Operador](./media/topdesk-secure-tutorial/ic790611.png "Novo Operador")

    a. Clique no separador **Geral**.

    b. Na caixa de texto **sobrenome,** escreva sobrenome do utilizador como **Simon**.

    c. Selecione um **Site** para a conta na secção **Localização.**

    d. Na caixa de texto 'Nome de Início de **Sessão'** da secção **TOPdesk,** digite um nome de login para o seu utilizador.

    e. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador seguras ou APIs fornecidas pela TOPdesk - Secure para fornecer contas de utilizador Azure AD.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para TOPdesk - URL secure Sign-on onde pode iniciar o fluxo de login. 

* Vá ao TOPdesk - Secure Sign-on URL diretamente e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no TOPdesk - Secure tile nas Minhas Apps, deverá ser automaticamente inscrito no TOPdesk - Secure para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado TOPdesk - Secure pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).