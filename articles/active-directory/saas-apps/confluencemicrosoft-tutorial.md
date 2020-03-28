---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com Confluence SAML SSO by Microsoft [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Confluence SAML SSO pela Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6ea52474f3b352fabf19050f20012438e9d0c19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76120613"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com confluence SAML SSO pela Microsoft

Neste tutorial, você aprenderá a integrar confluence SAML SSO pela Microsoft com o Azure Ative Directory (Azure AD). Quando integrar a Confluence SAML SSO pela Microsoft com o Azure AD, pode:

* Controle em Azure AD que tem acesso a Confluence SAML SSO pela Microsoft.
* Ative que os seus utilizadores sejam automaticamente inscritos na Confluence SAML SSO pela Microsoft com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="description"></a>Descrição:

Utilize a sua conta Microsoft Azure Ative Directory com o servidor Atlassian Confluence para permitir uma única inscrição. Desta forma, todos os utilizadores da sua organização podem usar as credenciais Azure AD para iniciar sessão na aplicação Confluence. Este plugin utiliza O SAML 2.0 para a federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a Confluence SAML SSO pela Microsoft, precisa dos seguintes itens:

- Uma subscrição do Azure
- Aplicação de servidor de confluência instalada num servidor windows 64-bits (no local ou na infraestrutura IaaS da nuvem)
- O servidor de confluência está ativado por HTTPS
- Note que as versões suportadas para Confluence Plugin são mencionadas na secção abaixo.
- O servidor de confluência é acessível na internet particularmente para a página de Login Azure AD para autenticação e deve receber o token da Azure AD
- Credenciais de administração são criadas em Confluence
- WebSudo é desativado em Confluência
- Utilizador de teste criado na aplicação do servidor Confluence

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção de Confluência. Teste a integração primeiro no ambiente de desenvolvimento ou encenação da aplicação e, em seguida, use o ambiente de produção.

Para começar, precisa dos seguintes itens:

* Não utilize o seu ambiente de produção, a menos que seja necessário.
* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Confluência SAML SSO by Microsoft single sign-on (SSO) enabled subscrição.

## <a name="supported-versions-of-confluence"></a>Versões suportadas da Confluência

A partir de agora, as seguintes versões da Confluência são suportadas:

- Confluência: 5.0 a 5.10
- Confluência: 6.0.1 a 6.15.9
- Confluência: 7.0.1 a 7.1.0

> [!NOTE]
> Por favor, note que o nosso Plugin Confluence também trabalha na versão Ubuntu 16.04

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Confluência SAML SSO by Microsoft suporta **SP** iniciado SSO

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Adicionar Confluence SAML SSO pela Microsoft a partir da galeria

Para configurar a integração do Confluence SAML SSO pela Microsoft no Azure AD, é necessário adicionar confluence SAML SSO pela Microsoft da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **de galeria,** **digite Confluence SAML SSO pela Microsoft** na caixa de pesquisa.
1. **Selecione Confluence SAML SSO pela Microsoft** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Configure e teste Azure AD single sign-on for Confluence SAML SSO by Microsoft

Configure e teste Azure AD SSO com Confluence SAML SSO pela Microsoft utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Confluence SAML SSO pela Microsoft.

Para configurar e testar o Azure AD SSO com confluence SAML SSO pela Microsoft, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure confluence SAML SSO by Microsoft SSO](#configure-confluence-saml-sso-by-microsoft-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Criar Confluence SAML SSO pelo utilizador](#create-confluence-saml-sso-by-microsoft-test-user)** de teste da Microsoft - para ter uma contraparte de B.Simon em Confluence SAML SSO pela Microsoft que está ligada à representação de AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **Da Microsoft,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`https://<domain:port>/`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta e URL de sinal. O Porto é opcional no caso de ser um URL chamado. Estes valores são recebidos durante a configuração do plugin confluence, o que é explicado mais tarde no tutorial.

1. No **set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on do Azure, concedendo acesso à Confluence SAML SSO pela Microsoft.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, **selecione Confluence SAML SSO pela Microsoft**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Configure Confluence SAML SSO by Microsoft SSO

1. Numa janela diferente do navegador web, inscreva-se na sua instância de Confluência como administrador.

1. Passe sobre a engrenagem e clique nos **Add-ons**.

    ![Configurar um único sinal](./media/confluencemicrosoft-tutorial/addon1.png)

1. Descarregue o plugin do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503). Faça o upload manual do plugin fornecido pela Microsoft utilizando o menu **de adição de upload.** O download do plugin está coberto pelo [Microsoft Service Agreement](https://www.microsoft.com/servicesagreement/).

    ![Configurar um único sinal](./media/confluencemicrosoft-tutorial/addon12.png)

1. Para executar o cenário de procuração inverso de Confluência ou o cenário do equilíbrio de carga executam os seguintes passos:

    > [!NOTE]
    > Deve configurar o servidor primeiro com as instruções abaixo e, em seguida, instalar o plugin.

    a. Adicione abaixo o atributo na porta **do conector** no **ficheiro server.xml** da aplicação do servidor JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Configurar um único sinal](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Alterar **o URL base** nas **definições** do sistema de acordo com o equilíbrio proxy/load.

    ![Configurar um único sinal](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Uma vez instalado o plugin, aparece na secção de addons instalado pelo **utilizador** da secção **'Gerir addon'.** Clique em **Configurar** para configurar o novo plugin.

    ![Configurar um único sinal](./media/confluencemicrosoft-tutorial/addon15.png)

1. Execute os seguintes passos na página de configuração:

    ![Configurar um único sinal](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Certifique-se de que existe apenas um certificado mapeado contra a app para que não haja nenhum erro na resolução dos metadados. Se houver vários certificados, a administração obtém um erro ao resolver os metadados.

    1. Na caixa de texto URL de **Metadados,** colar app **Federation Metadata Url** que copiou do portal Azure e clicar no botão **Resolver.** Lê o URL de metadados idp e povoa todas as informações dos campos.

    1. Copie o **Identificador, o URL de resposta e assine os** valores de URL e cole-os no **Identifier, no URL de resposta e no signo nas** caixas de texto URL, respectivamente, na secção básica de **configuração SAML** no portal Azure.

    1. No nome do **botão Login,** o nome do botão que a organização quer que os utilizadores vejam no ecrã de login.
    
    1. No **botão de login Descrição** escreva a descrição do botão que a sua organização quer que os utilizadores vejam no ecrã de login.

    1. Nas localizações de ID do **utilizador SAML,** selecione ou o **ID do utilizador está no elemento NameIdentifier da declaração de Assunto** ou id do utilizador está num elemento **Deatributo**.  Este ID tem de ser o ID do utilizador da Confluência. Se o ID do utilizador não estiver compatível, o sistema não permitirá que os utilizadores assinem. 

       > [!Note]
       > A localização padrão do ID do utilizador SAML é identificador de nome. Pode alterar isto para uma opção de atributo e introduzir o nome de atributo apropriado.

    1. Se selecionar id do utilizador está numa opção de **elemento Atributo,** em seguida, no **nome atributo** escrever o nome do atributo onde o ID do utilizador é esperado. 

    1. Se estiver a utilizar o domínio federado (como ADFS, etc.) com AD Azure, clique na opção **Enable Home Realm Discovery** e configure o Nome de **Domínio**.

    1. Em **Nome de Domínio** digite o nome de domínio aqui no caso do login baseado em ADFS.

    1. Verifique a saída de Um **Sinal Único** se pretender assinar a partir de Azure AD quando um utilizador assinar a partir de Confluence. 

    1. Ative a caixa de verificação **Force Azure Login,** caso deseje iniciar sessão apenas através das credenciais Azure AD.

       > [!Note]
       > Para ativar o formulário de login predefinido para iniciar sessão na página de login quando o login azure de força estiver ativado, adicione o parâmetro de consulta no URL do navegador.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. Clique no botão **Guardar** para guardar as definições.

       > [!NOTE]
       > Para mais informações sobre instalação e resolução de problemas, visite o Guia de Administrador do [Conector MS Confluence SSO](../ms-confluence-jira-plugin-adminguide.md). Há também um [FAQ](../ms-confluence-jira-plugin-faq.md) para a sua assistência.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Criar Confluence SAML SSO pelo utilizador de teste da Microsoft

Para permitir que os utilizadores de AD Azure insinuem no servidor confluence no local, devem ser aprovisionados em Confluence SAML SSO pela Microsoft. Para confluence SAML SSO pela Microsoft, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu servidor de Confluence no local como administrador.

1. Passe sobre a engrenagem e clique na gestão do **Utilizador.**

    ![Adicionar Empregado](./media/confluencemicrosoft-tutorial/user1.png)

1. Na secção Utilizadores, clique em adicionar o separador **de utilizadores.** Na página adicionar um diálogo **do Utilizador,** execute os seguintes passos:

    ![Adicionar Empregado](./media/confluencemicrosoft-tutorial/user2.png)

    a. Na caixa de texto **username,** digite o e-mail de utilizador como B.Simon.

    b. Na caixa de texto **Nome Completo,** digite o nome completo do utilizador como B.Simon.

    c. Na caixa de texto **e-mail,** B.Simon@contoso.comdigite o endereço de e-mail do utilizador como .

    d. Na caixa de texto **password,** digite a palavra-passe para B.Simon.

    e. Clique **em Confirmar A Palavra-passe** reintroduza a palavra-passe.

    f. Clique no botão **Adicionar.**

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no SSO Confluence SAML por telés microsoft no Painel de Acesso, deve ser automaticamente inscrito no SSO confluence pela Microsoft para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente confluence SAML SSO by Microsoft com Azure AD](https://aad.portal.azure.com/)
