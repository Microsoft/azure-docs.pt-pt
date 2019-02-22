---
title: 'Tutorial: Integração do Active Directory do Azure com Gigya | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Gigya.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/18/2019
ms.author: jeedes
ms.openlocfilehash: 0fffba4374f2eca8e9a57f8a698ca95b3f33c4e5
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588012"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Tutorial: Integração do Active Directory do Azure com Gigya

Neste tutorial, saiba como integrar Gigya com o Azure Active Directory (Azure AD).
Integrar Gigya no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Gigya.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Gigya (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Gigya, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Gigya logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Gigya **SP** iniciada SSO

## <a name="adding-gigya-from-the-gallery"></a>Adicionando Gigya da Galeria

Para configurar a integração do Gigya com o Azure AD, terá de adicionar Gigya a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Gigya a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Gigya**, selecione **Gigya** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Gigya na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Gigya com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Gigya deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Gigya, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Gigya Single Sign-On](#configure-gigya-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Gigya](#create-gigya-test-user)**  - para ter um equivalente da Eduarda Almeida na Gigya que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Gigya, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Gigya** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Gigya domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `http://<companyname>.gigya.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente Gigya](https://www.gigya.com/support-policy/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Gigya** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-gigya-single-sign-on"></a>Configurar Gigya Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Gigya como um administrador.

2. Aceda a **definições \> início de sessão de SAML**e, em seguida, clique nas **Add** botão.
   
    ![Início de sessão SAML](./media/gigya-tutorial/ic789532.png "início de sessão SAML")

3. Na **início de sessão de SAML** secção, execute os seguintes passos:
   
    ![Configuração de SAML](./media/gigya-tutorial/ic789533.png "configuração de SAML")
   
    a. Na **nome** caixa de texto, escreva um nome para a sua configuração.
   
    b. Na **emissor** caixa de texto, cole o valor de **Azure Ad identificador** que copiou do Portal do Azure. 
   
    c. Na **único URL de início de sessão no serviço** caixa de texto, cole o valor de **URL de início de sessão** que copiou do Portal do Azure.
   
    d. Na **formato do nome do ID** caixa de texto, cole o valor de **formato do nome do identificador** que copiou do Portal do Azure.
   
    e. Abra o certificado com codificação base 64 no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509** caixa de texto.
   
    f. Clique em **guardar as definições de**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Gigya.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Gigya**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Gigya**.

    ![A ligação de Gigya na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-gigya-test-user"></a>Criar utilizador de teste Gigya

Para habilitar logon Gigya de utilizadores do Azure AD, tem de ser aprovisionados em Gigya. No caso de Gigya, o aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para aprovisionar contas de utilizador, execute os seguintes passos:

1. Inicie sessão no seu **Gigya** site da empresa como administrador.

2. Aceda a **administrador \> gerir utilizadores**e, em seguida, clique em **convidar utilizadores**.
   
    ![Gerir utilizadores](./media/gigya-tutorial/ic789535.png "gerir utilizadores")

3. Na caixa de diálogo Convidar utilizadores, execute os seguintes passos:
   
    ![Convidar utilizadores](./media/gigya-tutorial/ic789536.png "convidar utilizadores")
   
    a. Na **E-Mail** caixa de texto, escreva o alias de e-mail de uma conta válida do Azure Active Directory que pretende aprovisionar.
    
    b. Clique em **convidar utilizador**.
      
    > [!NOTE]
    > O titular da conta do Azure Active Directory irá receber uma mensagem de e-mail que inclui uma ligação para confirmar a conta até se tornar Active Directory.
    > 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Gigya no painel de acesso, deve ser automaticamente sessão iniciada no Gigya para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

