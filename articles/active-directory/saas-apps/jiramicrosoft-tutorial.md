---
title: 'Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com jira SAML SSO pela Microsoft [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o JIRA SAML SSO pela Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cd922d0e65da627f11e6aab3827cb848c3dd635
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75560530"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jira-saml-sso-by-microsoft"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com jira SAML SSO pela Microsoft

Neste tutorial, você aprenderá a integrar jira SAML SSO pela Microsoft com o Azure Ative Directory (Azure AD). Quando integrar o JIRA SAML SSO pela Microsoft com o Azure AD, pode:

* Controle em Azure AD que tem acesso a JIRA SAML SSO pela Microsoft.
* Ative que os seus utilizadores sejam automaticamente inscritos no JIRA SAML SSO pela Microsoft com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="description"></a>Descrição

Utilize a sua conta Microsoft Azure Ative Directory com o servidor ATLASSIAN JIRA para permitir uma única inscrição. Desta forma, todos os utilizadores da sua organização podem usar as credenciais Azure AD para iniciar sessão na aplicação JIRA. Este plugin utiliza O SAML 2.0 para a federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Anúncio Azure com o JIRA SAML SSO pela Microsoft, precisa dos seguintes itens:

- Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
- JIRA Core and Software 6.4 a 8.5.1 ou MESA de Serviço JIRA 3.0 a 4.6.0 deve ser instalada e configurada na versão do Windows 64 bits
- O servidor JIRA está ativado por HTTPS
- Note que as versões suportadas para JIRA Plugin são mencionadas na secção abaixo.
- O servidor JIRA é acessível na internet particularmente para a página de Login Azure AD para autenticação e deve receber o token da Azure AD
- Credenciais de administrador são criadas na JIRA
- WebSudo é desativado em JIRA
- Utilizador de teste criado na aplicação do servidor JIRA

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção da JIRA. Teste a integração primeiro no ambiente de desenvolvimento ou encenação da aplicação e, em seguida, use o ambiente de produção.

Para começar, precisa dos seguintes itens:

* Não utilize o seu ambiente de produção, a menos que seja necessário.
* Jira SAML SSO by Microsoft single sign-on (SSO) enabled subscrição.

## <a name="supported-versions-of-jira"></a>Versões suportadas da JIRA

* JIRA Core e Software: 6.4 a 8.5.1
* Mesa de Serviço JIRA 3.0.0 a 4.6.0
* Jira também suporta 5.2. Para mais detalhes, clique no [Microsoft Azure Ative Directory single sign-on para JIRA 5.2](jira52microsoft-tutorial.md)

> [!NOTE]
> Por favor, note que o nosso JIRA Plugin também trabalha na Versão Ubuntu 16.04 e Linux.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* JIRA SAML SSO by Microsoft suporta **SP** iniciado SSO

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>Adicionar JIRA SAML SSO pela Microsoft a partir da galeria

Para configurar a integração do JIRA SAML SSO pela Microsoft no Azure AD, é necessário adicionar jira SAML SSO pela Microsoft da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** escreva **JIRA SAML SSO pela Microsoft** na caixa de pesquisa.
1. Selecione **JIRA SAML SSO pela Microsoft** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jira-saml-sso-by-microsoft"></a>Configure e teste Azure AD single sign-on for JIRA SAML SSO by Microsoft

Configure e teste Azure AD SSO com JIRA SAML SSO pela Microsoft utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no JIRA SAML SSO pela Microsoft.

Para configurar e testar o Azure AD SSO com o JIRA SAML SSO pela Microsoft, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o JiRA SAML SSO pelo Microsoft SSO](#configure-jira-saml-sso-by-microsoft-sso)** - para configurar as definições de início de sessão individuais no lado da aplicação.
    1. **[Crie o JIRA SAML SSO pelo utilizador](#create-jira-saml-sso-by-microsoft-test-user)** de teste da Microsoft - para ter uma contraparte de B.Simon no JIRA SAML SSO pela Microsoft que esteja ligado à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **jira SAML pela Microsoft,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`https://<domain:port>/`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta e URL de sinal. O Porto é opcional no caso de ser um URL chamado. Estes valores são recebidos durante a configuração do plugin Jira, o que é explicado mais tarde no tutorial.

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

Nesta secção, permitirá que a B.Simon utilize um único sign-on do Azure, concedendo acesso ao JIRA SAML SSO pela Microsoft.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **JIRA SAML SSO pela Microsoft**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-jira-saml-sso-by-microsoft-sso"></a>Configure JIRA SAML SSO por Microsoft SSO

1. Numa janela diferente do navegador web, inscreva-se na sua instância JIRA como administrador.

2. Passe sobre a engrenagem e clique nos **Add-ons**.

    ![Configurar um único sinal](./media/jiramicrosoft-tutorial/addon1.png)

3. Descarregue o plugin do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56506). Faça o upload manual do plugin fornecido pela Microsoft utilizando o menu **de adição de upload.** O download do plugin está coberto pelo [Microsoft Service Agreement](https://www.microsoft.com/servicesagreement/).

    ![Configurar um único sinal](./media/jiramicrosoft-tutorial/addon12.png)

4. Para executar o cenário de procuração reverso JIRA ou o cenário do equilíbrio de carga executar os seguintes passos:

    > [!NOTE]
    > Deve configurar o servidor primeiro com as instruções abaixo e, em seguida, instalar o plugin.

    a. Adicione abaixo o atributo na porta **do conector** no **ficheiro server.xml** da aplicação do servidor JIRA.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Configurar um único sinal](./media/jiramicrosoft-tutorial/reverseproxy1.png)

    b. Alterar **o URL base** nas **definições** do sistema de acordo com o equilíbrio proxy/load.

    ![Configurar um único sinal](./media/jiramicrosoft-tutorial/reverseproxy2.png)

5. Uma vez instalado o plugin, aparece na secção de addons instalado pelo **utilizador** da secção **'Gerir addon'.** Clique em **Configurar** para configurar o novo plugin.

    ![Configurar um único sinal](./media/jiramicrosoft-tutorial/addon14.png)

6. Execute os seguintes passos na página de configuração:

    ![Configurar um único sinal](./media/jiramicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Certifique-se de que existe apenas um certificado mapeado contra a app para que não haja nenhum erro na resolução dos metadados. Se houver vários certificados, ao resolver os metadados, a administração obtém um erro.

    1. Na caixa de texto URL de **Metadados,** colar app **Federation Metadata Url** que copiou do portal Azure e clicar no botão **Resolver.** Lê o URL de metadados idp e povoa todas as informações dos campos.

    1. Copie o **Identificador, o URL de resposta e assine os** valores de URL e cole-os no **identifier, no URL de resposta e no signo nas** caixas de texto URL, respectivamente, no **JIRA SAML SSO pela Secção de Domínio da Microsoft e URLs** no portal Azure.

    1. No nome do **botão Login,** o nome do botão que a organização quer que os utilizadores vejam no ecrã de login.
    
    1. No **botão de login Descrição** escreva a descrição do botão que a sua organização quer que os utilizadores vejam no ecrã de login.

    1. No ID do **utilizador SAML,** as localizações selecionam o **ID do utilizador no elemento NameIdentifier da declaração** de Assunto ou id do utilizador está num elemento **Deatributo**.  Este ID tem de ser o ID do utilizador jira. Se o ID do utilizador não estiver compatível, o sistema não permitirá que os utilizadores assinem.

       > [!Note]
       > A localização padrão do ID do utilizador SAML é identificador de nome. Pode alterar isto para uma opção de atributo e introduzir o nome de atributo apropriado.

    1. Se selecionar id do utilizador está numa opção de **elemento Atributo,** em seguida, no **nome atributo** escrever o nome do atributo onde o ID do utilizador é esperado.

    1. Se estiver a utilizar o domínio federado (como ADFS, etc.) com AD Azure, clique na opção **Enable Home Realm Discovery** e configure o Nome de **Domínio**.

    1. Em **Nome de Domínio** digite o nome de domínio aqui no caso do login baseado em ADFS.

    1. Verifique a assinatura individual de **ativação** se pretender assinar a partir do Azure AD quando um utilizador assinar a partir do JIRA.
    
    1. Ative a caixa de verificação **Force Azure Login,** caso deseje iniciar sessão apenas através das credenciais Azure AD.
    
       > [!Note]
       > Para ativar o formulário de login predefinido para o login de administração na página de login quando o login do Force Azure estiver ativado, adicione o parâmetro de consulta no URL do navegador.
       > `https://<domain:port>/login.jsp?force_azure_login=false`

    1. Clique no botão **Guardar** para guardar as definições.

       > [!NOTE]
       > Para mais informações sobre instalação e resolução de problemas, visite o Guia de Administrador do [Conector MS JIRA SSO](../ms-confluence-jira-plugin-adminguide.md). Há também um [FAQ](../ms-confluence-jira-plugin-faq.md) para a sua assistência.

### <a name="create-jira-saml-sso-by-microsoft-test-user"></a>Criar jira SAML SSO por utilizador de teste da Microsoft

Para permitir que os utilizadores de Anúncios Azure assinem no servidor jira no local, devem ser aprovisionados no JIRA SAML SSO pela Microsoft. Para o JIRA SAML SSO pela Microsoft, o provisionamento é uma tarefa manual.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu servidor jira no local como administrador.

2. Passe sobre a engrenagem e clique na gestão do **Utilizador.**

    ![Adicionar Empregado](./media/jiramicrosoft-tutorial/user1.png)

3. É redirecionado para a página de Acesso ao Administrador para introduzir **palavra-passe** e clicar no botão **Confirmar.**

    ![Adicionar Empregado](./media/jiramicrosoft-tutorial/user2.png)

4. Na secção do separador de **gestão do utilizador,** clique em **criar o utilizador**.

    ![Adicionar Empregado](./media/jiramicrosoft-tutorial/user3.png) 

5. Na página de diálogo **"Criar um novo utilizador",** execute os seguintes passos:

    ![Adicionar Empregado](./media/jiramicrosoft-tutorial/user4.png) 

    a. Na caixa de texto de endereço de B.simon@contoso.com **e-mail,** digite o endereço de e-mail do utilizador como .

    b. Na caixa de texto **Nome Completo,** digite o nome completo do utilizador como B.Simon.

    c. Na caixa de texto **username,** digite o e-mail do utilizador como B.simon@contoso.com.

    d. Na caixa de texto **Password,** digite a palavra-passe do utilizador.

    e. Clique em **Criar o utilizador**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no JIRA SAML SSO por telés microsoft no Painel de Acesso, deve ser automaticamente inscrito no Jira SAML SSO pela Microsoft para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente jira SAML SSO by Microsoft com Azure AD](https://aad.portal.azure.com/)
