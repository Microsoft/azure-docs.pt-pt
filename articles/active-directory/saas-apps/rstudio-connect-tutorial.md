---
title: 'Tutorial: Integração do Active Directory do Azure com o r Studio Connect | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o RStudio ligar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9bc78022-6d38-4476-8f03-e3ca2551e72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32aa7a531de2e236e3941bbe8afd84d845f80f99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726022"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect"></a>Tutorial: Integração do Active Directory do Azure com o r Studio Connect

Neste tutorial, saiba como integrar o r Studio ligar com o Azure Active Directory (Azure AD).
Integrar o r Studio ligar com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao ligar-se do RStudio.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para RStudio ligar (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o r Studio Connect, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Ligar o RStudio. Existe um [45 dias gratuitos de avaliação.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a ligar-se do RStudio **SP e IDP** iniciada SSO

* Oferece suporte a ligar-se do RStudio **Just In Time** aprovisionamento de utilizadores

## <a name="adding-rstudio-connect-from-the-gallery"></a>Adicionando o RStudio ligar da Galeria

Para configurar a integração do r Studio ligar ao Azure AD, terá de adicionar RStudio ligar partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o RStudio ligar partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **RStudio ligar**, selecione **RStudio ligar** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![RStudio ligar-se na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o r Studio ligar com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no RStudio ligar deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o r Studio Connect, terá de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o r Studio ligar início de sessão único](#configure-rstudio-connect-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de ligar o RStudio](#create-rstudio-connect-test-user)**  - para ter um equivalente da Eduarda Almeida no RStudio ligar-se que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o r Studio Connect, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **RStudio ligar** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os passos seguintes, substituindo `<example.com>` com o seu servidor de ligação do RStudio O endereço e porta:

    ![Domínio de ligar RStudio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<example.com>/__login__/saml`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<example.com>/__login__/saml/acs`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Domínio de ligar RStudio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<example.com>/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. São determinados do endereço de servidor do RStudio ligar (`https://example.com` nos exemplos acima). Contacte os [RStudio ligar-se a equipa de suporte](mailto:support@rstudio.com) se estiver com problemas. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. A aplicação ligar RStudio espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. RStudio ligar aplicação espera **nameidentifier** seja mapeado com **user.mail**, por isso, precisa editar o mapeamento do atributo, clicando em **editar** ícone e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

7. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-rstudio-connect-single-sign-on"></a>Configurar o r Studio ligar início de sessão único

Para configurar o início de sessão único em para **RStudio ligar**, tem de utilizar o **Url de metadados de Federação de aplicação** e **endereço do servidor** utilizado acima. Isso é feito no arquivo de configuração RStudio ligar em `/etc/rstudio-connect.rstudio-connect.gcfg`.

Este é um ficheiro de configuração de exemplo:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Store sua **endereço do servidor** no `Server.Address` valor e o **Url de metadados de Federação de aplicação** no `SAML.IdPMetaData` valor.

Se tiver problemas com a configuração, pode ler a [Guia do administrador ligar RStudio](https://docs.rstudio.com/connect/admin/authentication.html#authentication-saml) ou envie um e-mail a [equipa de suporte do RStudio](mailto:support@rstudio.com) para obter ajuda.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para ligar do RStudio.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **RStudio ligar**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **RStudio ligar**.

    ![A ligação RStudio ligar-se na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-rstudio-connect-test-user"></a>Criar utilizador de teste de ligar o RStudio

Nesta secção, um usuário chamado Eduarda Almeida é criado no RStudio ligar. Ligar do RStudio suporta o aprovisionamento just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no RStudio ligar, uma nova é criada quando tentar acessar o RStudio ligar.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico RStudio ligar-se no painel de acesso, deve ser automaticamente conectado para o r Studio ligar para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

