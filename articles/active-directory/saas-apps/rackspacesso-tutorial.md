---
title: 'Tutorial: Integração do Azure Ative Directory com rackspace SSO | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Rackspace SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: eed0ea075c5e9eb39810259be2aa6781a59f2f22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92511680"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Tutorial: Integração do Azure Ative Directory com Rackspace SSO

Neste tutorial, você aprende a integrar Rackspace SSO com Azure Ative Directory (Azure AD).
A integração do Rackspace SSO com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso ao Rackspace SSO.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Rackspace SSO (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com o Rackspace SSO, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Rackspace SSO assinatura única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Rackspace SSO suporta **SSO** iniciado SP

## <a name="adding-rackspace-sso-from-the-gallery"></a>Adicionar Rackspace SSO da galeria

Para configurar a integração do Rackspace SSO em AD Azure, precisa adicionar Rackspace SSO da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar Rackspace SSO da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Rackspace SSO**, selecione **Rackspace SSO** do painel de resultados e, em seguida, clique em Adicionar o botão **Adicionar** a aplicação.

     ![Rackspace SSO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Rackspace SSO com base em um utilizador de teste chamado **Britta Simon**.
Ao utilizar um único sign-on com Rackspace, os utilizadores do Rackspace serão automaticamente criados na primeira vez que iniciarem sinsumento no portal Rackspace. 

Para configurar e testar o único sinal de Azure AD com Rackspace SSO, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure rackspace SSO Single Sign-On](#configure-rackspace-sso-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
1. **[Configurar o Mapping Do Atributo no Painel de Controlo do Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)** - para atribuir funções rackspace aos utilizadores AD do Azure.
1. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Rackspace SSO, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Rackspace SSO,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** carregue o **ficheiro de metadados do Fornecedor** de Serviços que pode descarregar a partir do [URL](https://login.rackspace.com/federate/sp.xml) e execute os seguintes passos:

    a. Clique **em Carregar o ficheiro de metadados**.

    ![A screenshot mostra a configuração básica do SAML com o link de ficheiro de metadados upload.](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![A screenshot mostra uma caixa de diálogo onde pode selecionar e carregar um ficheiro.](common/browse-upload-metadata.png)

    c. Uma vez que o ficheiro de metadados é carregado com sucesso, os urls necessários são automaticamente povoados automaticamente.

    d. Na caixa de texto **URL de entrada de inscrição,** digite um URL: `https://login.rackspace.com/federate/`

    ![Rackspace SSO Domain e URLs informações únicas de sinscrição](common/sp-signonurl.png)   

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

Este ficheiro será enviado para Rackspace para preencher as definições de configuração necessárias da Federação de Identidade.

### <a name="configure-rackspace-sso-single-sign-on"></a>Configurar rackspace SSO Single Sign-On

Para configurar um único sinal no lado **SSO rackspace:**

1. Consulte a [documentação](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/) no Add a Identity Provider ao Painel de Controlo
1. Vai levá-lo através dos passos para:
    1. Criar um novo Fornecedor de Identidade
    1. Especifique um domínio de e-mail que os utilizadores utilizarão para identificar a sua empresa ao iniciar sessão.
    1. Faça o upload do **Metdata XML** da Federação anteriormente descarregado do painel de controlo Azure.

Isto configurará corretamente as definições SSO básicas necessárias para que o Azure e o Rackspace se conectem.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **do nome do utilizador** `brittasimon@yourcompanydomain.extension` . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Rackspace SSO.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Rackspace SSO**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Rackspace SSO**.

    ![O link Rackspace SSO na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Configurar oApeamento do Atributo no painel de controlo rackspace

Rackspace usa uma **Política de Mapeamento de Atributos** para atribuir funções e grupos rackspace aos seus utilizadores de sessão única. A **Política de Mapeamento de Atributos** traduz as alegações do Azure AD SAML nos campos de configuração do utilizador que o Rackspace requer. Mais documentação pode ser encontrada na documentação de [Base básica de mapeamento do Rackspace Attribute.](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) Algumas considerações:

* Se pretender atribuir diferentes níveis de acesso ao Rackspace utilizando grupos AD Azure, terá de ativar a reivindicação dos Grupos nas definições de sign-on única **SSO do Rackspace** Azure. A **Política de Mapeamento de Atributos** será então usada para combinar esses grupos com as funções e grupos rackspace desejados:

    ![Os Grupos reivindicam definições](common/sso-groups-claim.png)

* Por defeito, a Azure AD envia o UID dos Grupos AD Azure na reivindicação SAML, contra o nome do Grupo. No entanto, se estiver a sincronizar o seu Ative Directory para Azure AD, tem a opção de enviar os nomes reais dos grupos:

    ![Os grupos reclamam definições de nome](common/sso-groups-claims-names.png)

O exemplo a **seguir, a Política de Mapeamento de Atributos** demonstra:
1. Definindo o nome do utilizador rackspace para a `user.name` alegação SAML. Qualquer reclamação pode ser usada, mas é mais comum defini-la num campo que contenha o endereço de e-mail do utilizador.
1. Definir as funções Rackspace `admin` e `billing:admin` um utilizador combinando um Grupo AD Azure, pelo Nome do Grupo ou pelo Grupo UID. É utilizada *uma substituição* `"{0}"` no campo e substituída pelos `roles` resultados das `remote` expressões de regras.
1. Usando a `"{D}"` *substituição padrão* para permitir que Rackspace recupere campos SAML adicionais procurando reivindicações SAML padrão e bem conhecidas na troca DE SAML.

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

Consulte a [documentação de base de mapeamento do rackspace para](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/) mais exemplos.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Rackspace SSO no Painel de Acesso, deverá ser automaticamente inscrito no Rackspace SSO para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

Também pode utilizar o botão **Validar** nas definições de **sso** único:

   ![SSO Validate Button](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)