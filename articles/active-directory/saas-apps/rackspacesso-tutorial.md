---
title: 'Tutorial: Integração de Diretório Ativo Azure com Rackspace SSO [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Rackspace SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 36b398be-2f7e-4ce8-9031-53587299bc4a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: 31826f5d4d88c977f859a009bface2fddf3a1c88
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093187"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Tutorial: Integração de Diretório Ativo Azure com Rackspace SSO

Neste tutorial, aprende-se a integrar o Rackspace SSO com o Azure Ative Directory (Azure AD).
Integrar rackspace SSO com AD Azure proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Rackspace SSO.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Rackspace SSO (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Rackspace SSO, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura de inscrição única Rackspace SSO

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Rackspace SSO suporta **SP** iniciado SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>Adicionar Rackspace SSO da galeria

Para configurar a integração do Rackspace SSO em Azure AD, precisa adicionar Rackspace SSO da galeria à sua lista de aplicações saaS geridas.

**Para adicionar Rackspace SSO da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Rackspace SSO,** selecione **Rackspace SSO** do painel de resultados e, em seguida, clique em **Adicionar** o botão para adicionar a aplicação.

     ![Rackspace SSO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on azure ad com rackspace SSO com base num utilizador de teste chamado **Britta Simon**.
Ao utilizar um único sinal com o Rackspace, os utilizadores do Rackspace serão automaticamente criados na primeira vez que iniciarem sessão no portal Rackspace. 

Para configurar e testar o único sinal de Azure AD com o Rackspace SSO, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Rackspace SSO Single Sign-On](#configure-rackspace-sso-single-sign-on)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
1. **[Configurar o Mapeamento](#set-up-attribute-mapping-in-the-rackspace-control-panel)** do Atributo no Painel de Controlo do Rackspace - para atribuir funções de Rackspace aos utilizadores da AD Azure.
1. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com rackspace SSO, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Rackspace SSO,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **basic SAML Configuration,** carregue o ficheiro de metadados do Fornecedor de **Serviços** que pode descarregar a partir do [URL](https://login.rackspace.com/federate/sp.xml) e executar os seguintes passos:

    a. Clique no **ficheiro de metadados de upload**.

    ![image](common/upload-metadata.png)

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    ![image](common/browse-upload-metadata.png)

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, os urls necessários são automaticamente povoados automaticamente.

    d. Na caixa de texto **de URL sign-on,** escreva um URL:`https://login.rackspace.com/federate/`

    ![Rackspace Domínio SSO e URLs informações únicas de inscrição](common/sp-signonurl.png)   

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

Este ficheiro será enviado para rackspace para preencher as definições de configuração necessárias da Federação de Identidade.

### <a name="configure-rackspace-sso-single-sign-on"></a>Configure Rackspace SSO Single Sign-On

Para configurar um único sinal no lado **Rackspace SSO:**

1. Consulte a documentação em [Adicionar um Fornecedor de Identidade ao Painel de Controlo](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Vai levá-lo através dos passos para:
    1. Criar um novo Fornecedor de Identidade
    1. Especifique um domínio de e-mail que os utilizadores utilizarão para identificar a sua empresa ao iniciar sessão.
    1. Faça upload dos **Metadados da Federação XML** anteriormente descarregados a partir do painel de controlo Azure.

Isto configurará corretamente as definições básicas de SSO necessárias para que o Azure e o Rackspace se conectem.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que Britta Simon utilize um único sign-on Azure, concedendo acesso ao Rackspace SSO.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Rackspace SSO**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Rackspace SSO**.

    ![O link Rackspace SSO na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Configurar o mapeamento do atributo no painel de controlo rackspace

Rackspace usa uma Política de Mapeamento de **Atributos** para atribuir funções e grupos rackspace aos seus utilizadores de entrada única. A Política de Mapeamento do **Atributo** traduz as alegações do Azure AD SAML nos campos de configuração do utilizador que o Rackspace necessita. Mais documentação pode ser encontrada na [documentação básica](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/)de mapeamento de atribuição de rackspace. Algumas considerações:

* Se pretender atribuir diferentes níveis de acesso ao Rackspace utilizando grupos AD Azure, terá de ativar a reclamação dos Grupos nas definições de sign-on single **SSO** do Rackspace Azure. A Política de Mapeamento do **Atributo** será então usada para combinar com esses grupos com as funções e grupos derackspace desejados:

    ![Os Grupos reivindicam definições](common/sso-groups-claim.png)

* Por predefinição, a Azure AD envia o UID dos Grupos AD Azure na reivindicação SAML, contra o nome do Grupo. No entanto, se estiver a sincronizar o seu Diretório Ativo no local para a AD Azure, tem a opção de enviar os nomes reais dos grupos:

    ![Os Grupos reclamam definições de nome](common/sso-groups-claims-names.png)

O exemplo seguinte A política de mapeamento de **atributos** demonstra:
1. Definir o nome do utilizador `user.name` Rackspace na alegação SAML. Qualquer reclamação pode ser usada, mas é mais comum defini-lo num campo que contenha o endereço de e-mail do utilizador.
1. Definição das `admin` funções Rackspace e `billing:admin` de um utilizador, combinando um Grupo AD Azure, pelo Nome do Grupo ou pelo UID do Grupo. É utilizada `"{0}"` uma `roles` *substituição* no campo e substituída pelos `remote` resultados das expressões de regras.
1. Utilizando `"{D}"` a *substituição predefinida* para permitir que o Rackspace recupere campos SAML adicionais, procurando reivindicações SAML padrão e bem conhecidas na troca de SAML.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Certifique-se de que utiliza um editor de texto que valida a sintaxe YAML ao editar o seu ficheiro de política.

Consulte a [documentação de Base de Mapeamento](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/) de Atribuição de Rackspace para mais exemplos.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Rackspace SSO no Painel de Acesso, deve ser automaticamente inscrito no Rackspace SSO para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

Também pode utilizar o botão **Validate** nas definições de sinal single **Rackspace SSO:**

   ![SSO Validate Botão](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

