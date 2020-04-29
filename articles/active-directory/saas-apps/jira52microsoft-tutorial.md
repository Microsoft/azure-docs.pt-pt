---
title: 'Tutorial: Integração do Diretório Ativo Azure com JIRA SAML SSO pela Microsoft (V5.2) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o JIRA SAML SSO pela Microsoft (V5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b304bb35ea69906fc9576f45733134387be1b9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67099521"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Tutorial: Integração de Diretório Ativo Azure com JIRA SAML SSO pela Microsoft (V5.2)

Neste tutorial, aprende-se a integrar o JIRA SAML SSO pela Microsoft (V5.2) com o Azure Ative Directory (Azure AD).
Integrar o JIRA SAML SSO pela Microsoft (V5.2) com o Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso a JIRA SAML SSO pela Microsoft (V5.2).
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no JIRA SAML SSO pela Microsoft (V5.2) (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="description"></a>Descrição

Utilize a sua conta Microsoft Azure Ative Directory com o servidor ATLASSIAN JIRA para permitir uma única inscrição. Desta forma, todos os utilizadores da sua organização podem usar as credenciais Azure AD para iniciar sessão na aplicação JIRA. Este plugin utiliza O SAML 2.0 para a federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o JIRA SAML SSO pela Microsoft (V5.2), precisa dos seguintes itens:

- Uma subscrição do Azure
- JIRA Core e Software 5.2 devem ser instalados e configurados na versão do Windows 64-bit
- O servidor JIRA está ativado por HTTPS
- Note que as versões suportadas para JIRA Plugin são mencionadas na secção abaixo.
- O servidor JIRA é acessível na internet particularmente para a página de Login Azure AD para autenticação e deve receber o token da Azure AD
- Credenciais de administrador são criadas na JIRA
- WebSudo é desativado em JIRA
- Utilizador de teste criado na aplicação do servidor JIRA

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção da JIRA. Teste a integração primeiro no ambiente de desenvolvimento ou encenação da aplicação e, em seguida, use o ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de julgamento da AD Azure, pode obter um julgamento de um mês aqui: oferta de [julgamento.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="supported-versions-of-jira"></a>Versões suportadas da JIRA

* JIRA Core e Software: 5.2
* A JIRA também suporta 6,0 a 7.12. Para mais detalhes, clique em [JIRA SAML SSO pela Microsoft](jiramicrosoft-tutorial.md)

> [!NOTE]
> Por favor, note que o nosso PLUGIn JIRA também trabalha na versão Ubuntu 16.04

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* JIRA SAML SSO by Microsoft (V5.2) suporta **SP** iniciado SSO

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Adicionar JIRA SAML SSO pela Microsoft (V5.2) da galeria

Para configurar a integração do JIRA SAML SSO pela Microsoft (V5.2) no Azure AD, é necessário adicionar jira SAML SSO pela Microsoft (V5.2) da galeria à sua lista de aplicações saaS geridas.

**Para adicionar JIRA SAML SSO pela Microsoft (V5.2) da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **JIRA SAML SSO pela Microsoft (V5.2)**, selecione **JIRA SAML SSO por Microsoft (V5.2)** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** para adicionar a aplicação.

    ![JIRA SAML SSO by Microsoft (V5.2) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on do Azure AD com o JIRA SAML SSO pela Microsoft (V5.2) com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no JIRA SAML SSO pela Microsoft (V5.2).

Para configurar e testar o único sign-on do Azure AD com o JIRA SAML SSO pela Microsoft (V5.2), precisa de completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o JIRA SAML SSO pela Microsoft (V5.2) Single Sign-On](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de **[teste JIRA SAML SSO pela Microsoft (V5.2)](#create-jira-saml-sso-by-microsoft-v52-test-user)** - para ter uma contrapartida da Britta Simon no JIRA SAML SSO pela Microsoft (V5.2) que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on do Azure AD com o JIRA SAML SSO pela Microsoft (V5.2), execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **jira SAML sO by Microsoft (V5.2),** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![JIRA SAML SSO by Microsoft (V5.2) Domain and URLs single sign-on](common/sp-identifier-reply.png)

    a. Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<domain:port>/plugins/servlet/saml/auth`

    b. Na caixa **de identificador,** digite um URL utilizando o seguinte padrão:`https://<domain:port>/`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta e URL de sinal. O Porto é opcional no caso de ser um URL chamado. Estes valores são recebidos durante a configuração do plugin Jira, o que é explicado mais tarde no tutorial.

5. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>Configure JIRA SAML SSO by Microsoft (V5.2) Single Sign-On

1. Numa janela diferente do navegador web, inscreva-se na sua instância JIRA como administrador.

2. Passe sobre a engrenagem e clique nos **Add-ons**.

    ![Configurar um único sinal](./media/jira52microsoft-tutorial/addon1.png)

3. Na secção de separadores Add-ons, clique em **Gerir add-ons**.

    ![Configurar um único sinal](./media/jira52microsoft-tutorial/addon7.png)

4. Descarregue o plugin do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56521). Faça o upload manual do plugin fornecido pela Microsoft utilizando o menu **de adição de upload.** O download do plugin está coberto pelo [Microsoft Service Agreement](https://www.microsoft.com/servicesagreement/).

    ![Configurar um único sinal](./media/jira52microsoft-tutorial/addon12.png)

5. Uma vez instalado o plugin, aparece na secção de suplementos instalado pelo **utilizador.** Clique em **Configurar** para configurar o novo plugin.

    ![Configurar um único sinal](./media/jira52microsoft-tutorial/addon13.png)

6. Execute os seguintes passos na página de configuração:

    ![Configurar um único sinal](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Certifique-se de que existe apenas um certificado mapeado contra a app para que não haja nenhum erro na resolução dos metadados. Se houver vários certificados, ao resolver os metadados, a administração obtém um erro.

    a. Na caixa de texto URL de **Metadados,** colar app **Palavras de metadados Valor de url** que copiou do portal Azure e clicar no botão **Resolver.** Lê o URL de metadados idp e povoa todas as informações dos campos.

    b. Copie o **Identificador, o URL de resposta e assine os** valores de URL e cole-os no **Identifier, no URL de resposta e no signo nas** caixas de texto URL, respectivamente, na secção básica de **configuração SAML** no portal Azure.

    c. No nome do **botão Login,** o nome do botão que a organização quer que os utilizadores vejam no ecrã de login.

    d. No ID do **utilizador SAML,** as localizações selecionam o **ID do utilizador no elemento NameIdentifier da declaração** de Assunto ou id do utilizador está num elemento **Deatributo**.  Este ID tem de ser o ID do utilizador jira. Se o ID do utilizador não estiver compatível, o sistema não permitirá que os utilizadores assinem.

    > [!Note]
    > A localização padrão do ID do utilizador SAML é identificador de nome. Pode alterar isto para uma opção de atributo e introduzir o nome de atributo apropriado.

    e. Se selecionar id do utilizador está numa opção de **elemento Atributo,** em seguida, no **nome atributo** escrever o nome do atributo onde o ID do utilizador é esperado. 

    f. Se estiver a utilizar o domínio federado (como ADFS, etc.) com AD Azure, clique na opção **Enable Home Realm Discovery** e configure o Nome de **Domínio**.

    g. Em **Nome de Domínio** digite o nome de domínio aqui no caso do login baseado em ADFS.

    h. Verifique a saída de Um **Sinal Único** se pretender assinar a partir do Azure AD quando um utilizador assinar a partir de JIRA. 

    i. Clique no botão **Guardar** para guardar as definições.

    > [!NOTE]
    > Para mais informações sobre instalação e resolução de problemas, visite o Guia de Administrador do [Conector MS JIRA SSO](../ms-confluence-jira-plugin-adminguide.md) e também existem [FAQ](../ms-confluence-jira-plugin-faq.md) para a sua assistência

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon\@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com.

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que a Britta Simon utilize um único sign-on Azure, concedendo acesso ao JIRA SAML SSO pela Microsoft (V5.2).

1. No portal Azure, selecione **Aplicações Empresariais**, selecione **Todas as aplicações,** em seguida, selecione **JIRA SAML SSO pela Microsoft (V5.2)**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **JIRA SAML SSO by Microsoft (V5.2)**.

    ![O link JIRA SAML SSO by Microsoft (V5.2) na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Crie o utilizador de teste JIRA SAML SSO pela Microsoft (V5.2)

Para permitir que os utilizadores de Anúncios Azure insinuem no servidor jira no local, devem ser aprovisionados no servidor jira no local.

**Para fornecer uma conta de utilizador, execute os seguintes passos:**

1. Inscreva-se no seu servidor jira no local como administrador.

2. Passe sobre a engrenagem e clique na gestão do **Utilizador.**

    ![Adicionar Empregado](./media/jira52microsoft-tutorial/user1.png)

3. É redirecionado para a página de Acesso ao Administrador para introduzir **palavra-passe** e clicar no botão **Confirmar.**

    ![Adicionar Empregado](./media/jira52microsoft-tutorial/user2.png)

4. Na secção do separador de **gestão do utilizador,** clique em **criar o utilizador**.

    ![Adicionar Empregado](./media/jira52microsoft-tutorial/user3.png) 

5. Na página de diálogo **"Criar um novo utilizador",** execute os seguintes passos:

    ![Adicionar Empregado](./media/jira52microsoft-tutorial/user4.png)

    a. Na caixa de texto de endereço de Brittasimon@contoso.com **e-mail,** digite o endereço de e-mail do utilizador como .

    b. Na caixa de texto **Full Name,** digite o nome completo do utilizador como Britta Simon.

    c. Na caixa de texto **username,** digite o e-mail do utilizador como Brittasimon@contoso.com.

    d. Na caixa de texto **Password,** digite a palavra-passe do utilizador.

    e. Clique em **Criar o utilizador**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo JIRA SAML SSO da Microsoft (V5.2) no Painel de Acesso, deve ser automaticamente inscrito no JIRA SAML SSO pela Microsoft (V5.2) para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
