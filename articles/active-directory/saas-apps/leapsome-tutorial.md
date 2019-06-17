---
title: 'Tutorial: Integração do Active Directory do Azure com Leapsome | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 7dab6f6b9215ead8cdcf513e226fd2fb8e59d8fc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098294"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Tutorial: Integração do Active Directory do Azure com Leapsome

Neste tutorial, saiba como integrar Leapsome com o Azure Active Directory (Azure AD).
Integrar Leapsome no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Leapsome.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Leapsome (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Leapsome, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Leapsome logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Leapsome **SP e IDP** iniciada SSO

## <a name="adding-leapsome-from-the-gallery"></a>Adicionando Leapsome da Galeria

Para configurar a integração do Leapsome com o Azure AD, terá de adicionar Leapsome a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Leapsome a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Leapsome**, selecione **Leapsome** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Leapsome na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Leapsome com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Leapsome deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Leapsome, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Leapsome Single Sign-On](#configure-leapsome-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Leapsome](#create-leapsome-test-user)**  - para ter um equivalente da Eduarda Almeida na Leapsome que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Leapsome, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Leapsome** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![[Nome da aplicação] Informações de início de sessão de único domínio e URLs](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL: `https://www.leapsome.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![[Nome da aplicação] Informações de início de sessão de único domínio e URLs](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > O valor de URL de URL de resposta e início de sessão anterior não é o valor real. Irá atualizá-los com os valores reais, que é explicado mais tarde no tutorial.

6. Seu aplicativo Leapsome espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos: 

    | Name | Atributo de origem | Espaço de Nomes |
    | ---------------| --------------- | --------- |  
    | FirstName | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Apelido | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | title | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | imagem | URL para imagem o funcionário | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > O valor do atributo de imagem não é real. Atualize este valor com o URL da imagem real. Para obter este valor de contacto [equipa de suporte de cliente Leapsome](mailto:support@leapsome.com).

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Na **espaço de nomes** caixa de texto, digite o uri de espaço de nomes para essa linha.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Sobre o **configurar Leapsome** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-leapsome-single-sign-on"></a>Configurar Leapsome Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no Leapsome como um administrador de segurança.

1. No canto superior direito, clique no logótipo de definições e, em seguida, clique em **definições de administração**.

    ![Conjunto de Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Na barra de menu à esquerda, clique em **único início de sessão (SSO)** e, no **baseado em SAML início de sessão único (SSO)** página execute os seguintes passos:

    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Selecione **baseado em SAML ativar início de sessão único**.

    b. Copiar o **URL de início de sessão (indicar a seus usuários aqui para iniciar o início de sessão)** valor e cole-o no **URL de início de sessão** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    c. Copiar o **URL de resposta (recebe a resposta do seu fornecedor de identidade)** valor e cole-o no **URL de resposta** caixa de texto no **configuração básica de SAML** secção no portal do Azure .

    d. Na **URL de início de sessão de SSO (fornecido pelo fornecedor de identidade)** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    e. Copie o certificado que transferiu a partir do portal do Azure sem `--BEGIN CERTIFICATE and END CERTIFICATE--` comentários e cole-a no **certificado (fornecido pelo fornecedor de identidade)** caixa de texto.

    f. Clique em **definições de ATUALIZAÇÃO de SSO**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Leapsome.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Leapsome**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Leapsome**.

    ![A ligação de Leapsome na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-leapsome-test-user"></a>Criar utilizador de teste Leapsome

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Leapsome. Trabalhar com [equipa de suporte de cliente Leapsome](mailto:support@leapsome.com) para adicionar os utilizadores ou de domínio que tem de ser adicionado a uma lista de permissões para a plataforma de Leapsome. Se o domínio for adicionado pela equipe, os utilizadores serão automaticamente aprovisionados para a plataforma de Leapsome. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Leapsome no painel de acesso, deve ser automaticamente sessão iniciada no Leapsome para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)