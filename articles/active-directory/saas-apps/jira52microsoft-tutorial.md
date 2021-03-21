---
title: 'Tutorial: Integração do Azure Ative Directory com a JIRA SAML SSO pela Microsoft (V5.2) | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o JIRA SAML SSO pela Microsoft (V5.2).
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
ms.openlocfilehash: 7b85cc064babf44b14e80abc02669573b4730da2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736905"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Tutorial: Integração do Azure Ative Directory com a JIRA SAML SSO pela Microsoft (V5.2)

Neste tutorial, você aprenderá a integrar JIRA SAML SSO pela Microsoft (V5.2) com Azure Ative Directory (Azure AD). Quando integrar o JIRA SAML SSO pela Microsoft (V5.2) com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao JIRA SAML SSO pela Microsoft (V5.2).
* Ative os seus utilizadores a serem automaticamente inscritos no JIRA SAML SSO pela Microsoft (V5.2) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="description"></a>Description

Utilize a sua conta Microsoft Azure Ative Directory com o servidor Atlassian JIRA para ativar um único s-on. Desta forma, todos os utilizadores da sua organização podem usar as credenciais AZURE AD para iniciar súm na aplicação JIRA. Este plugin usa SAML 2.0 para federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com o JIRA SAML SSO pela Microsoft (V5.2), precisa dos seguintes itens:

- Uma subscrição do Azure
- JIRA Core e Software 5.2 devem ser instalados e configurados na versão do Windows 64-bit
- O servidor JIRA está com ATIVAR HTTPS
- Note que as versões suportadas para O Plugin JIRA são mencionadas na secção seguinte.
- O servidor JIRA é acessível na internet particularmente à página de Login AZure para autenticação e deve ser capaz de receber o token da Azure AD
- Credenciais de administração são criadas no JIRA
- WebSudo está desativado no JIRA
- Utilizador de teste criado na aplicação do servidor JIRA

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção do JIRA. Teste a integração em primeiro lugar no ambiente de desenvolvimento ou encenação da aplicação e, em seguida, utilize o ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a não ser que seja necessário.
- Se não tiver um ambiente experimental da AD Azure, pode obter um julgamento de um mês aqui: [Oferta de teste](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Versões apoiadas do JIRA

* JIRA Core e Software: 5.2
* JIRA também suporta 6.0 a 7.12. Para mais detalhes, clique em [JIRA SAML SSO pela Microsoft](jiramicrosoft-tutorial.md)

> [!NOTE]
> Por favor, note que o nosso JIRA Plugin também funciona na versão Ubuntu 16.04.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* JIRA SAML SSO by Microsoft (V5.2) suporta **SSO** iniciado SP

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Adicionar JIRA SAML SSO pela Microsoft (V5.2) da galeria

Para configurar a integração do JIRA SAML SSO pela Microsoft (V5.2) no Azure AD, é necessário adicionar O SSO JIRA SAML pela Microsoft (V5.2) da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** digite **JIRA SAML SSO by Microsoft (V5.2)** na caixa de pesquisa.
1. Selecione **JIRA SAML SSO pela Microsoft (V5.2)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft-v52"></a>Configure e teste Azure AD SSO para JIRA SAML SSO pela Microsoft (V5.2)

Nesta secção, configura e testa o Azure AD com um único sign-on com jira SAML SSO pela Microsoft (V5.2) com base num utilizador de teste chamado **Britta Simon**. Para um único sinal de sação a funcionar, deve estabelecer uma relação ligada entre um utilizador AD Azure e o utilizador relacionado no JIRA SAML SSO pela Microsoft (V5.2).

Para configurar e testar o Azure AD com um único sign-on com jira SAML SSO pela Microsoft (V5.2), execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
2. **[Configure O SSO JIRA SAML da Microsoft (V5.2) SSO](#configure-jira-saml-sso-by-microsoft-v52-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
    1. **[Crie o JIRA SAML SSO by Microsoft (V5.2) utilizador](#create-jira-saml-sso-by-microsoft-v52-test-user)** de teste - para ter uma contraparte de Britta Simon em JIRA SAML SSO pela Microsoft (V5.2) que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

1. No portal Azure, na página de integração de aplicações **JIRA SAML da Microsoft (V5.2),** encontre a secção **Gestão** e selecione **single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa **identifier,** digite um URL utilizando o seguinte padrão: `https://<domain:port>/`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL Sign-On. A porta é opcional no caso de ser uma URL chamada. Estes valores são recebidos durante a configuração do plugin Jira, que é explicado mais tarde no tutorial.

5. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** clique no botão de cópia para copiar o **Url de Metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo acesso ao JIRA SAML SSO pela Microsoft (V5.2).

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **JIRA SAML SSO pela Microsoft (V5.2)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-jira-saml-sso-by-microsoft-v52-sso"></a>Configure JIRA SAML SSO pela Microsoft (V5.2) SSO

1. Numa janela diferente do navegador web, inscreva-se na sua instância JIRA como administrador.

2. Hover on cog e clique nos **Add-ons**.

    ![A screenshot mostra add-ons selecionados a partir do menu Definições.](./media/jira52microsoft-tutorial/addon1.png)

3. Na secção de separadores de add-ons, clique **em Gerir suplementos**.

    ![Screenshot mostra Gerir os addons selecionados no separador Add-ons.](./media/jira52microsoft-tutorial/addon7.png)

4. Descarregue o plugin do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56521). Faça o upload manual do plugin fornecido pela Microsoft utilizando o menu **add-on do Upload.** O download do plugin está coberto pelo [Microsoft Service Agreement](https://www.microsoft.com/servicesagreement/).

    ![Screenshot mostra Gerir add-ons com o link add-on upload chamado.](./media/jira52microsoft-tutorial/addon12.png)

5. Uma vez instalado o plugin, aparece na secção de add-ons **instalados pelo utilizador.** Clique **em Configurar** para configurar o novo plugin.

    ![A screenshot mostra o Azure A D SAML Single Sign-on para a secção Jira com configuração selecionada.](./media/jira52microsoft-tutorial/addon13.png)

6. Execute os seguintes passos na página de configuração:

    ![A screenshot mostra a página de configuração do Conector Microsoft Jira S S O.](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Certifique-se de que existe apenas um certificado mapeado contra a app para que não haja erro na resolução dos metadados. Se houver vários certificados, ao resolver os metadados, a administração recebe um erro.

    a. Na caixa de texto **URL dos metadados,** cole o valor **do url da Federação de Aplicações** que copiou a partir do portal Azure e clique no botão **Resolver.** Lê o URL de metadados IdP e povoa todas as informações dos campos.

    b. Copie o **Identificador, URS de Resposta e Assine nos** valores de URL e cole-os em **Identifier, URL de resposta e assinar em** caixas de texto URL, respectivamente, na secção de **Configuração SAML Básica** no portal Azure.

    c. No **Botão de Início O nome** do botão o nome do botão que a sua organização quer que os utilizadores vejam no ecrã de login.

    d. Nas **localizações de ID do utilizador SAML,** selecione que **o ID do utilizador está no elemento NameIdentifier da declaração do Assunto** ou o **ID do utilizador está num elemento Atributo**.  Este ID tem de ser o ID do utilizador JIRA. Se o ID do utilizador não for igualado, então o sistema não permitirá que os utilizadores entrem.

    > [!Note]
    > A localização padrão do ID do utilizador SAML é o identificador de nome. Pode alterá-lo para uma opção de atributo e introduzir o nome de atributo apropriado.

    e. Se selecionar **o ID do utilizador está numa** opção de elemento Atributo, então na caixa de texto do nome Atributo **digite** o nome do atributo onde o ID do utilizador é esperado. 

    f. Se estiver a utilizar o domínio federado (como ADFS, etc.) com Azure AD, clique na opção **Enable Home Realm Discovery** e configuure o Nome de **Domínio**.

    exemplo, Em **Nome de Domínio** digite o nome de domínio aqui no caso do login baseado em ADFS.

    h. Verifique **ativar a assinatura única** se pretender assinar a partir do Azure AD quando um utilizador assinar fora do JIRA. 

    i. Clique **em Guardar** o botão para guardar as definições.

    > [!NOTE]
    > Para mais informações sobre a instalação e resolução de problemas, visite [o MS JIRA SSO Connector Admin Guide](./ms-confluence-jira-plugin-adminguide.md) e também há [FAQ](./ms-confluence-jira-plugin-adminguide.md) para a sua assistência.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Criar Jira SAML SSO pela Microsoft (V5.2) utilizador de teste

Para permitir que os utilizadores de Azure AD inscrevam-se no servidor JIRA no local, devem ser ateados no servidor JIRA no local.

**Para obter uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu servidor JIRA no local como administrador.

2. Hover on cog e clique na **gestão** do Utilizador .

    ![A screenshot mostra a gestão do utilizador selecionada a partir do menu Definições.](./media/jira52microsoft-tutorial/user1.png)

3. É redirecionado para a página de Acesso do Administrador para introduzir **palavra-passe** e clicar no botão **Confirmar.**

    ![A screenshot mostra a página de Acesso do Administrador onde introduz as suas credenciais.](./media/jira52microsoft-tutorial/user2.png)

4. Na secção de separador **de gestão do utilizador,** clique em **criar utilizador.**

    ![A screenshot mostra o separador de gestão do Utilizador onde pode criar o utilizador.](./media/jira52microsoft-tutorial/user3.png) 

5. Na página de diálogo **"Criar novo utilizador",** execute os seguintes passos:

    ![A screenshot mostra a nova caixa de diálogo do utilizador criar onde pode introduzir as informações neste passo.](./media/jira52microsoft-tutorial/user4.png)

    a. Na caixa de texto **do endereço de e-mail,** digite o endereço de e-mail do utilizador como Brittasimon@contoso.com .

    b. Na caixa de texto **'Nome Completo',** escreva o nome completo do utilizador como Britta Simon.

    c. Na caixa de texto **username,** escreva o e-mail do utilizador como Brittasimon@contoso.com .

    d. Na caixa de texto **da palavra-passe,** digite a palavra-passe do utilizador.

    e. Clique **em Criar utilizador.**

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para JIRA SAML SSO pela Microsoft (V5.2) URL de entrada de dados onde pode iniciar o fluxo de login. 

* Vá diretamente ao JIRA SAML SSO pela Microsoft (V5.2) URL de entrada diretamente e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo JIRA SAML SSO da Microsoft (V5.2) nas Minhas Apps, este será redirecionado para JIRA SAML SSO pela Microsoft (V5.2) URL de inscrição. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado O SSO JIRA SAML pela Microsoft (V5.2) pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).