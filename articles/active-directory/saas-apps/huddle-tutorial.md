---
title: 'Tutorial: Integração do Active Directory do Azure com Huddle | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Huddle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c9d91aaf9f5ffff82ed9191e0b5deaa393571e0
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587706"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Tutorial: Integração do Active Directory do Azure com Huddle

Neste tutorial, saiba como integrar Huddle com o Azure Active Directory (Azure AD).
Integrar Huddle no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Huddle.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Huddle (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Huddle, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Huddle única início de sessão ativada subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Huddle suporta **SP e IDP** iniciada SSO

## <a name="adding-huddle-from-the-gallery"></a>Adicionando Huddle da Galeria

Para configurar a integração do Huddle com o Azure AD, terá de adicionar Huddle a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Huddle a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Huddle**, selecione **Huddle** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Huddle na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Huddle com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Huddle deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Huddle, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Huddle início de sessão único](#configure-huddle-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Huddle](#create-huddle-test-user)**  - para ter um equivalente da Eduarda Almeida na Huddle que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Huddle, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Huddle** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    > [!NOTE]
    > A instância de huddle será automaticamente detetada do domínio, que introduza abaixo.

    ![Huddle domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL:

    | | |
    |--|--|
    | `https://login.huddle.net`|
    | `https://login.huddle.com`|
    | |

    b. Na **URL de resposta** caixa de texto, escreva um URL:

    | | |
    |--|--|
    | `https://login.huddle.net/saml/browser-sso`|
    | `https://login.huddle.com/saml/browser-sso`|
    | `https://login.huddle.com/saml/idp-initiated-sso`|
    | |

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Huddle domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.com`|
    | `https://us.huddle.com`|
    | |

    > [!NOTE]
    > O valor de URL de início de sessão não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Huddle](https://huddle.zendesk.com) para obter este valor.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar Huddle** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-huddle-single-sign-on"></a>Configurar Huddle início de sessão único

Para configurar o início de sessão único num **Huddle** lado, terá de enviar o transferido **certificado (Base64)** e adequadas copiados URLs a partir do portal do Azure para [equipa de suporte de Huddle](https://huddle.zendesk.com/). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

> [!NOTE]
> Início de sessão único tem de ser ativado pela equipe de suporte do Huddle. Receba uma notificação quando a configuração foi concluída.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Huddle.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Huddle**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Huddle**.

    ![A ligação de Huddle na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-huddle-test-user"></a>Criar utilizador de teste Huddle

Para ativar a utilizadores do Azure AD iniciar sessão no Huddle, tem de ser aprovisionados em Huddle. No caso de Huddle, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Huddle** site da empresa como administrador.

2. Clique em **área de trabalho**.

3. Clique em **pessoas \> convidar pessoas**.

    ![As pessoas](./media/huddle-tutorial/ic787838.png "pessoas")

4. Na **criar um novo convite** secção, execute os seguintes passos:
  
    ![Novo convite](./media/huddle-tutorial/ic787839.png "novo convite")
  
    a. Na **escolha uma equipe para convidar pessoas para ingressar** lista, selecione **equipe**.

    b. Tipo de **endereço de E-Mail** de um Azure válido conta AD que pretende aprovisionar para **introduza o endereço de e-mail para pessoas que gostaria de convidar** caixa de texto.

    c. Clique em **convidar**.

    > [!NOTE]
    > O titular da conta do Azure AD irá receber um e-mail, incluindo uma ligação para confirmar a conta até se tornar Active Directory.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação da conta de utilizador de Huddle ou APIs fornecidas pelos Huddle para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Huddle no painel de acesso, deve ser automaticamente sessão iniciada no Huddle para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

