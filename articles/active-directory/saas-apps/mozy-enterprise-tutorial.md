---
title: 'Tutorial: Integração do Active Directory do Azure com o Mozy Enterprise | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Mozy Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: 4e7570afc5d359877f677e692678b09036da9ad3
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240464"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Tutorial: Integração do Active Directory do Azure com o Mozy Enterprise

Neste tutorial, saiba como integrar Mozy Enterprise com o Azure Active Directory (Azure AD).
Integrar Mozy Enterprise no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Mozy Enterprise.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a empresa Mozy (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Mozy empresarial, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Mozy Enterprise logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Mozy Enterprise **SP** iniciada SSO

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Adicionando Mozy Enterprise da Galeria

Para configurar a integração do Mozy Enterprise com o Azure AD, terá de adicionar Mozy empresarial a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Mozy empresarial a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Mozy Enterprise**, selecione **Mozy Enterprise** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Mozy Enterprise na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Mozy Enterprise com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na empresa Mozy deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Mozy empresarial, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Mozy Enterprise Single Sign-On](#configure-mozy-enterprise-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de Mozy Enterprise](#create-mozy-enterprise-test-user)**  - para ter um equivalente da Eduarda Almeida na empresa Mozy que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Mozy Enterprise, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Mozy Enterprise** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de empresa Mozy e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de Mozy Enterprise Client](http://support.mozy.com/) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Mozy Enterprise** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-mozy-enterprise-single-sign-on"></a>Configurar Mozy Enterprise Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Mozy empresarial como um administrador.

2. Na **Configuration** secção, clique em **política de autenticação**.
   
    ![Política de autenticação](./media/mozy-enterprise-tutorial/ic777314.png "política de autenticação")

3. Sobre o **política de autenticação** secção, execute os seguintes passos:
   
    ![Política de autenticação](./media/mozy-enterprise-tutorial/ic777315.png "política de autenticação")
   
    a. Selecione **serviço de diretório** como **fornecedor**.
   
    b. Selecione **utilizar o Push LDAP**.
   
    c. Clique nas **autenticação SAML** separador.
   
    d. Colar **URL de início de sessão**, que copiou do portal do Azure para o **URL de autenticação** caixa de texto.
   
    e. Colar **do Azure AD identificador**, que copiou do portal do Azure para o **ponto final de SAML** caixa de texto.
   
    f. Abra o seu certificado codificado de base 64 transferido no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole o certificado completo para **certificado SAML** caixa de texto.
   
    g. Selecione **ativar o SSO para os administradores iniciar sessão com as credenciais de rede**.
   
    h. Clique em **guardar alterações**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao Mozy Enterprise.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Mozy Enterprise**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Mozy Enterprise**.

    ![A ligação de Mozy Enterprise na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-mozy-enterprise-test-user"></a>Criar utilizador de teste Mozy Enterprise

Para habilitar os utilizadores do Azure AD iniciar sessão no Mozy Enterprise, tem de ser aprovisionados em Mozy Enterprise. No caso de Mozy Enterprise, o aprovisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras Mozy Enterprise utilizador conta criação ferramentas ou APIs fornecidas pelo Mozy Enterprise para aprovisionar contas de utilizador do AAD.

**Para aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Mozy Enterprise** inquilino.

2. Clique em **usuários**e, em seguida, clique em **adicionar novo utilizador**.
   
    ![Os utilizadores](./media/mozy-enterprise-tutorial/ic777317.png "utilizadores")
   
    >[!NOTE]
    >O **adicionar novo utilizador** opção só é apresentada apenas se **Mozy** está selecionado como o fornecedor em **política de autenticação**. Se estiver configurada a autenticação SAML, em seguida, os utilizadores são adicionados automaticamente no seu primeiro início de sessão através do início de sessão único no.
    
3. Na caixa de diálogo novo utilizador, execute os seguintes passos:
   
    ![Adicionar utilizadores](./media/mozy-enterprise-tutorial/ic777318.png "adicionar utilizadores")
   
    a. Partir do **escolha um grupo de** , selecione um grupo.
   
    b. Partir do **que tipo de utilizador** , selecione um tipo.
   
    c. Na **nome de utilizador** caixa de texto, escreva o nome do utilizador do Azure AD.
   
    d. Na **E-Mail** caixa de texto, escreva o endereço de e-mail do utilizador do Azure AD.
   
    e. Selecione **enviar mensagem de e-mail do utilizador instrução**.
   
    f. Clique em **adicionar utilizador (es)**.

     >[!NOTE]
     > Depois de criar o utilizador, será enviado um e-mail ao utilizador do Azure AD que inclui uma ligação para confirmar a conta até se tornar Active Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Mozy Enterprise no painel de acesso, deve ser automaticamente a sessão iniciada empresa Mozy para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

