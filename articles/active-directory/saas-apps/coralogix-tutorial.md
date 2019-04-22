---
title: 'Tutorial: Integração do Active Directory do Azure com Coralogix | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Coralogix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0095a825f5582dc795f5bebdcf08be07a92946e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678297"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Tutorial: Integração do Active Directory do Azure com Coralogix

Neste tutorial, saiba como integrar Coralogix com o Azure Active Directory (Azure AD).
Integrar Coralogix no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Coralogix.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no Coralogix (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para obter mais informações sobre a integração de aplicações SaaS com o Azure AD, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Coralogix, terá dos seguintes itens:

- Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
- Um Coralogix single-sign-on ativar a subscrição. 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Coralogix suporta SSO iniciado por SP.

## <a name="add-coralogix-from-the-gallery"></a>Adicionar Coralogix a partir da Galeria

Para configurar a integração do Coralogix com o Azure AD, primeiro adicione Coralogix partir da Galeria à sua lista de aplicações de SaaS geridas.

Para adicionar Coralogix a partir da galeria, siga os passos seguintes:

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione a **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Coralogix**. Selecione **Coralogix** no painel de resultados e, em seguida, selecione a **Add** botão para adicionar a aplicação.

     ![Coralogix na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Coralogix com base num utilizador de teste chamado Eduarda Almeida.
Para início de sessão único funcione, tem de estabelecer uma ligação entre um utilizador do Azure AD e o utilizador relacionado no Coralogix.

Para configurar e testar o Azure AD início de sessão único com Coralogix, primeiro conclua os seguintes blocos de construção:

1. [Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on) para permitir aos utilizadores utilizar esta funcionalidade.
2. [Configurar Coralogix início de sessão único](#configure-coralogix-single-sign-on) para configurar as definições de início de sessão únicas no lado do aplicativo.
3. [Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user) para testar o Azure AD início de sessão único com Eduarda Almeida.
4. [Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user) para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. [Criar um utilizador de teste Coralogix](#create-a-coralogix-test-user) ter um equivalente da Eduarda Almeida na Coralogix que está ligado à representação de utilizador do Azure AD.
6. [Testar início de sessão único](#test-single-sign-on) para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Coralogix, siga os passos seguintes:

1. Na [portal do Azure](https://portal.azure.com/), na **Coralogix** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML** para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Na **configuração básica de SAML** diálogo caixa, siga os passos seguintes:

    ![Coralogix domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** , introduza um URL com o seguinte padrão: `https://<SUBDOMAIN>.coralogix.com`

    b. Na **identificador (ID de entidade)** texto, introduza um URL, tais como:
    
    `https://api.coralogix.com/saml/metadata.xml`

    ou

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > O valor do URL de início de sessão não é real. Atualize o valor com o URL de início de sessão real. Contacte os [equipa de suporte de cliente Coralogix](mailto:info@coralogix.com) para obter o valor. Também pode consultar os padrões da **configuração básica de SAML** secção no portal do Azure.

5. O aplicativo Coralogix espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicativo. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** botão para abrir o **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. Na **afirmações de utilizador** secção a **atributos de utilizador** caixa de diálogo caixa, edite as afirmações utilizando o **editar** ícone. Também pode adicionar as afirmações utilizando **Adicionar nova afirmação** para configurar o atributo de token SAML, conforme mostrado na imagem anterior. Em seguida, siga os passos seguintes:
    
    a. Selecione o **ícone de edição** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](./media/coralogix-tutorial/tutorial_usermail.png) ![image](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Partir do **formato de identificador de nome escolher** lista, selecione **endereço de E-Mail**.

    c. Partir do **atributo de origem** lista, selecione **user.mail**.

    d. Selecione **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, selecione **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado, de acordo com os seus requisitos. Em seguida, guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

8. Na **configurar Coralogix** secção, copie o URL adequado.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-coralogix-single-sign-on"></a>Configurar Coralogix início de sessão único

Para configurar o início de sessão único no **Coralogix** lado, envie o transferido **XML de metadados de Federação** e copiados URLs a partir do portal do Azure para o [equipa de suporte de Coralogix](mailto:info@coralogix.com). Eles garantem que a ligação de SAML SSO está definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Na parte superior do ecrã, selecione **novo utilizador**.

    ![Novo utilizador botão](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga os passos seguintes.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , insira "brittasimon@yourcompanydomain.extension." Por exemplo, neste caso, poderá introduzir "brittasimon@contoso.com."

    c. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado no **palavra-passe** caixa.

    d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Coralogix.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Coralogix**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Coralogix**.

    ![A ligação de Coralogix na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Selecione o **adicionar utilizador** botão. Em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores. Em seguida, clique nas **selecione** na parte inferior do ecrã.

6. Se estiver à espera de um valor de função na asserção de SAML, no **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Em seguida, clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, selecione a **atribuir** botão.

### <a name="create-a-coralogix-test-user"></a>Criar um utilizador de teste Coralogix

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Coralogix. Trabalhar com o [equipa de suporte de Coralogix](mailto:info@coralogix.com) para adicionar os utilizadores na plataforma Coralogix. Tem de criar e ativar os utilizadores antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar seu única início de sessão em configuração do Azure AD com o portal MyApps.

Ao selecionar o mosaico Coralogix no MyApps portal, deve ser automaticamente sessão iniciada no Coralogix. Para obter mais informações sobre o portal MyApps, consulte [o que é o portal MyApps?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

