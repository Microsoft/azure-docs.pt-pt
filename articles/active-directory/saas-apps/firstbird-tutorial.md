---
title: 'Tutorial: Integração do Active Directory do Azure com Firstbird | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Firstbird.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: acab1200-32d3-4f4b-953f-f2a7e812b6a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3afbbfdcacb932deb688c31d513c134f52137d7d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771723"
---
# <a name="tutorial-azure-active-directory-integration-with-firstbird"></a>Tutorial: Integração do Active Directory do Azure com Firstbird

Neste tutorial, saiba como integrar Firstbird com o Azure Active Directory (Azure AD).
Integrar Firstbird no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Firstbird.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Firstbird (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Firstbird, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Firstbird logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Firstbird **SP e IDP** iniciada SSO
* Suporta Firstbird **Just In Time** aprovisionamento de utilizadores

## <a name="adding-firstbird-from-the-gallery"></a>Adicionando Firstbird da Galeria

Para configurar a integração do Firstbird com o Azure AD, terá de adicionar Firstbird a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Firstbird a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select_azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise_applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add_new_app.png)

4. Na caixa de pesquisa, escreva **Firstbird**, selecione **Firstbird** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Firstbird na lista de resultados](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com [nome da aplicação] com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em [nome da aplicação] deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com [nome da aplicação], tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Firstbird Single Sign-On](#configure-firstbird-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Firstbird](#create-firstbird-test-user)**  - para ter um equivalente da Eduarda Almeida na Firstbird que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com [nome da aplicação], execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Firstbird** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select_sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select_saml_option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit_urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Firstbird domínio e URLs únicas início de sessão em informações](common/idp_intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<company-domain>.auth.1brd.com/saml/sp`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<company-domain>.auth.1brd.com/saml/callback`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Firstbird domínio e URLs únicas início de sessão em informações](common/metadata_upload_additional_signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<company-domain>.1brd.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de cliente Firstbird](mailto:support@firstbird.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. Aplicação de Firstbird espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit_attribute.png)

7. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name | Atributo de origem|
    | ---------------| --------- |
    | first_name | `user.givenname` |
    | last_name | `user.surname` |
    | e-mail | `user.mail` |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new_save_attribute.png)

    ![image](common/new_attribute_details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir **XML de metadados de Federação** e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-firstbird-single-sign-on"></a>Configurar Firstbird Single Sign-On

Depois de concluir estes passos, envie Firstbird o XML de metadados de Federação num pedido de suporte por e-mail de e para [ support@firstbird.com ](mailto:support@firstbird.com) com o assunto: "Configuração de SSO".

Firstbird, em seguida, irá armazenar a configuração no sistema em conformidade e ativar o SSO para a sua conta. Depois disso, o membro da equipe de suporte irá contactá-lo para verificar a configuração.

> [!NOTE]
> Tem de ter a opção SSO incluída no seu contrato.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new_user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user_properties.png)

    a. Na **Name** , insira **BrittaSimon**.

    b. Na **nome de utilizador** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Firstbird.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Firstbird**.

    ![Painel de aplicações empresariais](common/enterprise_applications.png)

2. Na lista de aplicativos, escreva e selecione **Firstbird**.

    ![A ligação de Firstbird na lista de aplicações](common/all_applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users_groups_blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add_assign_user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-firstbird-test-user"></a>Criar utilizador de teste Firstbird

Nesta secção, um usuário chamado Eduarda Almeida é criado na Firstbird. Firstbird suporta o aprovisionamento de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Firstbird, uma nova é criada quando tentar acessar Firstbird.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Firstbird no painel de acesso, deve ser automaticamente sessão iniciada no Firstbird para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
