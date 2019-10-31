---
title: 'Tutorial: integração do Azure Active Directory com o Ceridian Dayforce HCM | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Ceridian Dayforce HCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f488f22535c290b5fecbd0ffa9f8867f0b715bac
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73158681"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: integração do Azure Active Directory com o Ceridian Dayforce HCM

Neste tutorial, você aprenderá a integrar o Ceridian Dayforce HCM ao Azure Active Directory (Azure AD).
A integração do Ceridian Dayforce HCM ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Ceridian Dayforce HCM.
* Você pode permitir que seus usuários sejam conectados automaticamente ao Ceridian Dayforce HCM (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um local central-a portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Ceridian Dayforce HCM, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Ceridian Dayforce HCM

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Ceridian Dayforce HCM dá suporte ao SSO iniciado por **SP**

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Adicionando Ceridian Dayforce HCM da Galeria

Para configurar a integração do Ceridian Dayforce HCM ao Azure AD, você precisará adicionar o Ceridian Dayforce HCM da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Ceridian Dayforce HCM da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Ceridian DAYFORCE HCM**, selecione **Ceridian Dayforce HCM** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Ceridian Dayforce HCM na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Ceridian Dayforce HCM com base em um usuário de teste chamado **Brenda Simon**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Ceridian Dayforce HCM.

Para configurar e testar o logon único do Azure AD com o Ceridian Dayforce HCM, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Ceridian DAYFORCE HCM](#configure-ceridian-dayforce-hcm-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure ad](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com o Brenda Simon.
4. **[Atribuir o usuário de teste do Azure ad](#assign-the-azure-ad-test-user)** – para permitir que Brenda Simon use o logon único do Azure AD.
5. **[Criar usuário de teste do Ceridian DAYFORCE HCM](#create-ceridian-dayforce-hcm-test-user)** – para ter um equivalente de Brenda Simon no CERIDIAN Dayforce HCM que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Ceridian Dayforce HCM, execute as seguintes etapas:

1. Na [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Ceridian Dayforce HCM** , selecione **logon único**.

    ![Link configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , execute as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Ceridian Dayforce HCM](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de logon** , digite a URL usada pelos usuários para fazer logon no aplicativo CERIDIAN Dayforce HCM.

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Para teste | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Na caixa de texto **identificador** , digite uma URL usando o seguinte padrão:

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Para teste | `https://fs-test.dayforcehcm.com/sp` |

    c. Na caixa de texto **URL de resposta** , digite a URL usada pelo Azure ad para postar a resposta.

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Para teste | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de logon, o identificador e a URL de resposta reais. Contate a [equipe de suporte ao cliente do Ceridian DAYFORCE HCM](https://www.ceridian.com/support) para obter esses valores. Você também pode consultar os padrões mostrados na seção **configuração básica do SAML** no portal do Azure.

5. O aplicativo Ceridian Dayforce HCM espera que as asserções SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir a caixa de diálogo **atributos de usuário** .

    ![imagem](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:
    
    | Nome | Atributo de origem|
    | ---------| --------- |
    | nome  | User. extensionAttribute2 |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **namespace** em branco.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , selecione o atributo de usuário que você deseja usar para sua implementação. Por exemplo, se você quiser usar o EmployeeID como um identificador de usuário exclusivo e tiver armazenado o valor do atributo em ExtensionAttribute2, selecione User. ExtensionAttribute2.

    f. Clique em **OK**

    g. Clique em **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o **XML de metadados** das opções especificadas de acordo com seu requisito e salve-o em seu computador.

    ![O link de download do certificado](common/metadataxml.png)

8. Na seção **Configurar Ceridian DAYFORCE HCM** , copie as URLs apropriadas de acordo com seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logout

### <a name="configure-ceridian-dayforce-hcm-single-sign-on"></a>Configurar o logon único do Ceridian Dayforce HCM

Para configurar o logon único no lado do **Ceridian DAYFORCE HCM** , é necessário enviar o XML de **metadados** baixado e as URLs copiadas apropriadas de portal do Azure para a [equipe de suporte do Ceridian Dayforce HCM](https://www.ceridian.com/support). Eles definem essa configuração para que a conexão de SSO do SAML seja definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no portal do Azure chamado Brenda Simon.

1. No portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **usuários**e, em seguida, selecione **todos os usuários**.

    ![Os links "usuários e grupos" e "todos os usuários"](common/users.png)

2. Selecione **novo usuário** na parte superior da tela.

    ![Botão novo usuário](common/new-user.png)

3. Nas propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo usuário](common/user-properties.png)

    a. No campo **nome** , insira **brendafernandes**.
  
    b. No campo **nome de usuário** , digite **brendafernandes\@yourcompanydomain. Extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e anote o valor exibido na caixa senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Simon use o logon único do Azure concedendo acesso ao Ceridian Dayforce HCM.

1. Na portal do Azure, selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Ceridian Dayforce HCM**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Ceridian DAYFORCE HCM**.

    ![O link do Ceridian Dayforce HCM na lista de aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **usuários e grupos**.

    ![O link "usuários e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar usuário** e selecione **usuários e grupos** na caixa de diálogo **Adicionar atribuição** .

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **usuários e grupos** , selecione **Brenda Simon** na lista usuários e, em seguida, clique no botão **selecionar** na parte inferior da tela.

6. Se você estiver esperando qualquer valor de função na declaração SAML, na caixa de diálogo **selecionar função** , selecione a função apropriada para o usuário na lista e, em seguida, clique no botão **selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição** , clique no botão **atribuir** .

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Criar usuário de teste do Ceridian Dayforce HCM

Nesta seção, você criará um usuário chamado Brenda Simon no Ceridian Dayforce HCM. Trabalhe com a [equipe de suporte do Ceridian DAYFORCE HCM](https://www.ceridian.com/support) para adicionar os usuários à plataforma CERIDIAN Dayforce HCM. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o painel de acesso.

Ao clicar no bloco Ceridian Dayforce HCM no painel de acesso, você deverá entrar automaticamente no Ceridian Dayforce HCM para o qual você configurou o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

