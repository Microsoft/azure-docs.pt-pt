---
title: 'Tutorial: Integração do Active Directory do Azure com o Trello | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Trello.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453827b42d12333fd4f27761e7f73484fb749532
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578429"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Tutorial: Integração do Active Directory do Azure com o Trello

Neste tutorial, saiba como integrar o Trello com o Azure Active Directory (Azure AD).
Integrar o Trello com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Trello.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Trello (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para obter mais informações sobre a integração de aplicações SaaS com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Trello, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Uma subscrição única com início de sessão no-ativado do Trello.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o Trello e IDP-iniciado por SP SSO

* Trello suporta o aprovisionamento de utilizador do Just In Time

## <a name="add-trello-from-the-gallery"></a>Adicionar Trello a partir da Galeria

Para configurar a integração do Trello para o Azure AD, adicionar primeiro Trello a partir da Galeria à sua lista de aplicações de SaaS geridas.

Para adicionar Trello a partir da galeria, siga os passos seguintes:

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione a **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Trello**e, em seguida, selecione **Trello** do painel de resultados.

5. Selecione o **adicionar** botão para adicionar a aplicação.

     ![Trello na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Trello com base num utilizador de teste **Eduarda Almeida**.

Para o início de sessão único funcione, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado no Trello.

Para configurar e testar o Azure AD início de sessão único com o Trello, tem de concluir os seguintes blocos de construção:

1. [Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on) para permitir aos utilizadores utilizar esta funcionalidade.
2. [Configurar o Trello início de sessão único](#configure-trello-single-sign-on) para configurar as definições de início de sessão únicas no lado do aplicativo.
3. [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user) para testar o Azure AD início de sessão único com Eduarda Almeida.
4. [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user) para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. [Criar um utilizador de teste do Trello](#create-a-trello-test-user) ter um equivalente da Eduarda Almeida no Trello que está ligado a representação do Azure AD do utilizador.
6. [Testar início de sessão único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

> [!NOTE]
> Deve obter o **\<enterprise\>** campo dinâmico do Trello. Se não tiver o valor do campo dinâmico, entre em contato com o [equipa de suporte do Trello](mailto:support@trello.com) para obter o campo dinâmico para a sua empresa.

Para configurar o Azure AD início de sessão único com o Trello, siga os passos seguintes:

1. Na [portal do Azure](https://portal.azure.com/), na **Trello** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML** para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **configurar início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, se quiser configurar a aplicação no modo de iniciado o IDP, siga os passos seguintes:

    ![Domínio de Trello e URLs único informações de início de sessão](common/idp-intiated.png)

    a. Na **identificador** , introduza um URL ao utilizar o seguinte padrão: `https://trello.com/auth/saml/metadata`

    b. Na **URL de resposta** , introduza um URL ao utilizar o seguinte padrão: `https://trello.com/auth/saml/consume/<enterprise>`

5. Selecione **definir URLs adicionais**e, em seguida, siga os passos seguintes para configurar a aplicação no modo iniciado por SP:

    ![Domínio de Trello e URLs único informações de início de sessão](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** , introduza um URL ao utilizar o seguinte padrão:  `https://trello.com/auth/saml/login/<enterprise>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e início de sessão no URL. Contacte os [equipa de suporte de cliente de Trello](mailto:support@trello.com) obter esses valores. Também pode consultar os padrões da **configuração básica de SAML** secção no portal do Azure.

6. O aplicativo de Trello espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicativo. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** botão para abrir o **atributos de utilizador** caixa de diálogo.

    ![Caixa de diálogo de atributos de utilizador](common/edit-attribute.png)

7. Na **afirmações de utilizador** secção a **atributos de utilizador** diálogo caixa, configure o atributo de token SAML, conforme mostrado na imagem anterior. Em seguida, siga os passos seguintes:

    | Name |  Atributo de origem|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Selecione **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![Caixa de diálogo de afirmações de utilizador](common/new-save-attribute.png)

    ![Gerir afirmações de utilizador](common/new-attribute-details.png)

    b. Na **nome** , introduza o nome de atributo que é mostrado para essa linha.

    c. Deixe **espaço de nomes** em branco.

    d. Para **origem**, selecione **atributo**.

    e. Na **atributo de origem** lista, introduza o valor do atributo que é mostrado para essa linha.

    f. Selecione **Ok**.

    g. Selecione **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, selecione **transferir** para transferir o **certificado (Base64)**  entre as opções de determinado de acordo com os seus requisitos. Em seguida, guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Sobre o **configurar o Trello** secção, copie os URLs apropriados, de acordo com os seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-trello-single-sign-on"></a>Configurar o Trello início de sessão único

Para configurar o início de sessão único no lado do Trello, primeiro enviar o transferido **certificado (Base64)** e copiados URLs a partir do portal do Azure para o [equipa de suporte do Trello](mailto:support@trello.com). Eles garantem que a ligação de SAML SSO está definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Botão de novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga os passos seguintes.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , insira "brittasimon@yourcompanydomain.extension". Por exemplo, neste caso, poderá introduzir "BrittaSimon@contoso.com".

    c. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à aplicação Trello.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Trello**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Trello**.

    ![A ligação de Trello na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione o **adicionar utilizador** botão. Em seguida, na **adicionar atribuição** caixa de diálogo, selecione **utilizadores e grupos**.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores. Em seguida, clique nas **selecione** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função na asserção de SAML, em seguida, no **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Em seguida, clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, selecione a **atribuir** botão.

### <a name="create-a-trello-test-user"></a>Criar um utilizador de teste do Trello

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Trello. Trello suporta Just-in de aprovisionamento de utilizadores de tempo, que está ativada por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Trello, é criado um novo após a autenticação.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, entre em contato com o [equipa de suporte do Trello](mailto:support@trello.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD com o portal MyApps.

Ao selecionar o mosaico do Trello no MyApps portal, deve ser automaticamente sessão iniciada no Trello. Para obter mais informações sobre o portal as minhas aplicações, consulte [o que é o portal MyApps?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

