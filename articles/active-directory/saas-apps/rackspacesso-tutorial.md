---
title: 'Tutorial: Integração do Active Directory do Azure com o Rackspace SSO | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Rackspace SSO.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093187"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Tutorial: Integração do Active Directory do Azure com o Rackspace SSO

Neste tutorial, saiba como integrar o Rackspace SSO com o Azure Active Directory (Azure AD).
Integrar o Rackspace SSO no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Rackspace SSO.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Rackspace SSO (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Rackspace SSO, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Rackspace SSO logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a rackspace SSO **SP** iniciada SSO

## <a name="adding-rackspace-sso-from-the-gallery"></a>Adicionando Rackspace SSO da Galeria

Para configurar a integração do Rackspace SSO para o Azure AD, terá de adicionar Rackspace SSO partir da galeria, à sua lista de aplicações de SaaS geridas.

**Para adicionar Rackspace SSO a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Rackspace SSO**, selecione **Rackspace SSO** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![SSO rackspace na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Rackspace SSO com base num utilizador de teste **Eduarda Almeida**.
Ao utilizar o início de sessão único com Rackspace, os utilizadores de Rackspace serão automaticamente criados pela primeira vez que iniciar sessão portal do Rackspace. 

Para configurar e testar o Azure AD início de sessão único com o Rackspace SSO, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Rackspace SSO início de sessão único](#configure-rackspace-sso-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Configurar o mapeamento do atributo no painel de controlo de Rackspace](#set-up-attribute-mapping-in-the-rackspace-control-panel)**  - para atribuir funções de Rackspace para utilizadores do Azure AD.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Rackspace SSO, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Rackspace SSO** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. No **configuração básica de SAML** secção, carregamento a **ficheiro de metadados do fornecedor de serviços** que pode transferir a partir do [URL](https://login.rackspace.com/federate/sp.xml) e execute os seguintes passos:

    a. Clique em **carregamento de ficheiro de metadados**.

    ![image](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![image](common/browse-upload-metadata.png)

    c. Depois do ficheiro de metadados é carregado com êxito, os urls necessários obter automaticamente preenchido automaticamente.

    d. Na **URL de início de sessão** caixa de texto, escreva um URL: `https://login.rackspace.com/federate/`

    ![Domínio de SSO rackspace e URLs únicas início de sessão em informações](common/sp-signonurl.png)   

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

Esse arquivo será carregado para Rackspace para preencher as definições de configuração de Federação de identidades necessárias.

### <a name="configure-rackspace-sso-single-sign-on"></a>Configurar o Rackspace SSO início de sessão único

Para configurar o início de sessão único num **Rackspace SSO** lado:

1. Consulte a documentação em [adicionar um fornecedor de identidade para o painel de controle](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/)
1. Ele irá levá-lo pelos passos para:
    1. Criar um novo provedor de identidade
    1. Especifique um domínio de e-mail que os utilizadores irão utilizar para identificar a sua empresa ao iniciar sessão.
    1. Carregar o **XML de metadados de Federação** anteriormente transferidas a partir do painel de controlo do Azure.

Isto irá configurar corretamente as definições de SSO básicas necessárias para o Azure e Rackspace para se ligar.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Rackspace SSO.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Rackspace SSO**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Rackspace SSO**.

    ![A ligação de Rackspace SSO na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Configurar o mapeamento do atributo no painel de controlo de Rackspace

Rackspace utiliza um **política de mapeamento do atributo** para atribuir funções de Rackspace e de grupos aos seus utilizadores de início de sessão únicos. O **política de mapeamento do atributo** traduz as afirmações de SAML do Azure AD para os campos de configuração de utilizador requer Rackspace. Mais documentação pode ser encontrada na Rackspace [documentação de noções básicas de mapeamento do atributo](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/). Algumas considerações:

* Se pretender atribuir diferentes níveis de acesso de Rackspace com grupos do Azure AD, terá de permitir que a declaração de grupos do Azure **Rackspace SSO** as definições de início de sessão único. O **política de mapeamento do atributo** , em seguida, será utilizado para corresponder a esses grupos para pretendido Rackspace funções e grupos:

    ![Os grupos de definições de afirmações](common/sso-groups-claim.png)

* Por predefinição, o Azure AD envia os grupos de UID do Azure AD no afirmações de SAML, em comparação com o nome do grupo. No entanto, se estiver a sincronizar o seu diretório de Active Directory no local para o Azure AD, terá a opção para enviar os nomes reais dos grupos:

    ![Os grupos de definições de nome de afirmação](common/sso-groups-claims-names.png)

O exemplo a seguir **política de mapeamento do atributo** demonstra:
1. Definir o nome do utilizador Rackspace como o `user.name` afirmações SAML. Qualquer declaração pode ser usada, mas é mais comuns para defini-lo como um campo que contém o endereço de e-mail do utilizador.
1. Definir as funções de Rackspace `admin` e `billing:admin` num utilizador ao corresponder um grupo do AD do Azure, pelo nome do grupo ou UID de grupo. A *substituição* dos `"{0}"` no `roles` campo é utilizado e será substituído com os resultados da `remote` expressões de regra.
1. Utilizar o `"{D}"` *predefinido substituição* para permitir que Rackspace obter campos adicionais de SAML, procurando as afirmações SAML padrão e bem conhecidas na troca de SAML.

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
> Certifique-se de que utilize um editor de texto que valida a sintaxe YAML, ao editar o ficheiro de política.

Consulte a Rackspace [documentação de noções básicas de mapeamento do atributo](https://developer.rackspace.com/docs/rackspace-federation/attribmapping-basics/) para obter mais exemplos.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Rackspace SSO no painel de acesso, deve ser automaticamente sessão iniciada no SSO Rackspace para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Também pode utilizar o **Validate** botão no **Rackspace SSO** único configurações de logon:

   ![Botão Validar de SSO](common/sso-validate-sign-on.png)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

