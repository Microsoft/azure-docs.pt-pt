---
title: 'Tutorial: Integração do Active Directory do Azure com a segurança TINFOIL | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a4e64bf6899d3384e0dadc9c2b4ce2fd5d092385
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849423"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: Integração do Active Directory do Azure com o TINFOIL SECURITY

Neste tutorial, saiba como integrar o TINFOIL SECURITY com o Azure Active Directory (Azure AD).
Integrar o TINFOIL SECURITY com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao TINFOIL SECURITY.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o TINFOIL SECURITY (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o TINFOIL SECURITY, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* TINFOIL SECURITY logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* TINFOIL SECURITY suporta **IDP** iniciada SSO

## <a name="adding-tinfoil-security-from-the-gallery"></a>Adicionando o TINFOIL SECURITY da Galeria

Para configurar a integração do TINFOIL SECURITY para o Azure AD, terá de adicionar TINFOIL SECURITY a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar TINFOIL SECURITY a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **TINFOIL SECURITY**, selecione **TINFOIL SECURITY** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![TINFOIL SECURITY na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o TINFOIL SECURITY com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TINFOIL SECURITY tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o TINFOIL SECURITY, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o TINFOIL SECURITY início de sessão único](#configure-tinfoil-security-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do TINFOIL SECURITY](#create-tinfoil-security-test-user)**  - para ter um equivalente da Eduarda Almeida na TINFOIL SECURITY, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com a segurança TINFOIL, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **TINFOIL SECURITY** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, o utilizador não tem de realizar qualquer passo como a aplicação já está pré-integrada com o Azure.

    ![Domínio de segurança TINFOIL e URLs únicas início de sessão em informações](common/preintegrated.png)

5. A aplicação do TINFOIL SECURITY espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

        ![image](common/edit-attribute.png)

6. Além dos acima, o aplicativo do TINFOIL SECURITY espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Na **atributo de origem** textbox, o valor de colar o ID de conta que obterá mais tarde no tutorial.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. Na **certificado de assinatura SAML** secção, clique em **editar** botão para abrir **certificado de assinatura SAML** caixa de diálogo.

    ![Editar o certificado de assinatura de SAML](common/edit-certificate.png)

8. Na **certificado de assinatura SAML** secção, copie a **Thumbprint** e guarde-o no seu computador.

    ![Copie o valor do Thumbprint](common/copy-thumbprint.png)

9. Sobre o **configurar a segurança TINFOIL** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-tinfoil-security-single-sign-on"></a>Configurar o TINFOIL SECURITY início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa TINFOIL SECURITY como um administrador.

2. Na barra de ferramentas na parte superior, clique em **minha conta**.
   
    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

3. Clique em **segurança**.
   
    ![Segurança](./media/tinfoil-security-tutorial/ic798972.png "segurança")

4. Sobre o **Single Sign-On** configuração página, execute os seguintes passos:
   
    ![Início de sessão único](./media/tinfoil-security-tutorial/ic798973.png "início de sessão único")
   
    a. Selecione **ativar SAML**.
   
    b. Clique em **configuração Manual**.
   
    c. Na **URL de publicação de SAML** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure
   
    d. Na **impressão digital do certificado de SAML** caixa de texto, cole o valor de **Thumbprint** que copiou do **certificado de assinatura SAML** secção.
  
    e. Cópia **sua ID de conta** valor e cole o valor na **o valor do atributo** caixa de texto em **adicionar atributo** secção no portal do Azure.
   
    f. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao TINFOIL SECURITY.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **TINFOIL SECURITY**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **TINFOIL SECURITY**.

    ![A ligação do TINFOIL SECURITY na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-tinfoil-security-test-user"></a>Criar utilizador de teste do TINFOIL SECURITY

Para habilitar os utilizadores do Azure AD iniciar sessão no TINFOIL SECURITY, tem de ser aprovisionados em TINFOIL SECURITY. No caso do TINFOIL SECURITY, o aprovisionamento é uma tarefa manual.

**Para obter um utilizador aprovisionado, execute os seguintes passos:**

1. Se o utilizador é uma parte de uma conta de empresa, terá [entre em contato com a equipa de suporte do TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) para obter a conta de utilizador que criou.

1. Se o utilizador for um utilizador normal do TINFOIL SECURITY SaaS, o utilizador pode adicionar um funcionário para qualquer um dos sites do utilizador. Isso dispara um processo para enviar um convite para o e-mail especificado para criar uma nova conta de utilizador do TINFOIL SECURITY.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação da conta de utilizador de TINFOIL SECURITY ou APIs fornecidas pelo TINFOIL SECURITY para aprovisionar contas de utilizador do Azure AD.
> 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do TINFOIL SECURITY no painel de acesso, deve ser automaticamente sessão iniciada no TINFOIL SECURITY para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

