---
title: 'Tutorial: Integração do Active Directory do Azure com SumoLogic | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 07442d9636ec488da6eb3cdf9000b7f3cc24b61f
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223517"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Tutorial: Integração do Active Directory do Azure com SumoLogic

Neste tutorial, saiba como integrar SumoLogic com o Azure Active Directory (Azure AD).
Integrar SumoLogic no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SumoLogic.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para SumoLogic (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SumoLogic, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SumoLogic logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta SumoLogic **SP** iniciada SSO

## <a name="adding-sumologic-from-the-gallery"></a>Adicionando SumoLogic da Galeria

Para configurar a integração do SumoLogic com o Azure AD, terá de adicionar SumoLogic a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SumoLogic a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SumoLogic**, selecione **SumoLogic** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![SumoLogic na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com SumoLogic com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SumoLogic deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com SumoLogic, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar SumoLogic Single Sign-On](#configure-sumologic-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste SumoLogic](#create-sumologic-test-user)**  - para ter um equivalente da Eduarda Almeida na SumoLogic que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com SumoLogic, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SumoLogic** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![SumoLogic domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenantname>.SumoLogic.com`

   b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente SumoLogic](https://www.sumologic.com/contact-us/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar SumoLogic** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-sumologic-single-sign-on"></a>Configurar SumoLogic Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa SumoLogic como um administrador.

1. Aceda a **gerenciar \> segurança**.

    ![Gerir](./media/sumologic-tutorial/ic778556.png "gerir")

1. Clique em **SAML**.

    ![Configurações de segurança globais](./media/sumologic-tutorial/ic778557.png "configurações de segurança globais")

1. Partir do **selecionar uma configuração ou criar um novo** , selecione **do Azure AD**e, em seguida, clique em **configurar**.

    ![Configurar SAML 2.0](./media/sumologic-tutorial/ic778558.png "configurar SAML 2.0")

1. Sobre o **configurar SAML 2.0** caixa de diálogo, execute os seguintes passos:

    ![Configurar SAML 2.0](./media/sumologic-tutorial/ic778559.png "configurar SAML 2.0")

    a. Na **nome de configuração** caixa de texto, tipo **do Azure AD**.

    b. Selecione **modo de depuração**.

    c. Na **emissor** caixa de texto, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    d. Na **URL do pedido Authn** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    e. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole o certificado completo para **certificado X.509** caixa de texto.

    f. Como **atributo de correio eletrónico**, selecione **assunto de utilização SAML**.  

    g. Selecione **SP iniciou a configuração de início de sessão**.

    h. Na **caminho de início de sessão** caixa de texto, tipo **Azure** e clique em **guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para SumoLogic.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SumoLogic**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SumoLogic**.

    ![A ligação de SumoLogic na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sumologic-test-user"></a>Criar utilizador de teste SumoLogic

Para habilitar os utilizadores do Azure AD iniciar sessão no SumoLogic, tem de ser aprovisionados para SumoLogic. No caso de SumoLogic, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **SumoLogic** inquilino.

1. Aceda a **gerenciar \> utilizadores**.

    ![Os utilizadores](./media/sumologic-tutorial/ic778561.png "utilizadores")

1. Clique em **Adicionar**.

    ![Os utilizadores](./media/sumologic-tutorial/ic778562.png "utilizadores")

1. Sobre o **novo utilizador** caixa de diálogo, execute os seguintes passos:

    ![Novo utilizador](./media/sumologic-tutorial/ic778563.png "novo utilizador") 

    a. Escreva as informações relacionadas da conta do Azure AD que pretende aprovisionar para o **nome próprio**, **Apelido**, e **E-Mail** caixas de texto.
  
    b. Selecione uma função.
  
    c. Como **Status**, selecione **Active Directory**.
  
    d. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras SumoLogic utilizador conta criação ferramentas ou APIs fornecidas pelo SumoLogic para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico SumoLogic no painel de acesso, deve ser automaticamente sessão iniciada no SumoLogic para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

