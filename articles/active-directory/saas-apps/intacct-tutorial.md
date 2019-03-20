---
title: 'Tutorial: Integração do Active Directory do Azure com Intacct | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Intacct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: cd10adbc509812a360ae59fdac3f16bd2af0324c
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188157"
---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>Tutorial: Integração do Active Directory do Azure com Intacct

Neste tutorial, saiba como integrar Intacct com o Azure Active Directory (Azure AD).
Integrar Intacct no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Intacct.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Intacct (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Intacct, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Intacct logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Intacct **IDP** iniciada SSO

## <a name="adding-intacct-from-the-gallery"></a>Adicionando Intacct da Galeria

Para configurar a integração do Intacct com o Azure AD, terá de adicionar Intacct a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Intacct a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Intacct**, selecione **Intacct** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Intacct na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Intacct com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Intacct deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Intacct, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Intacct Single Sign-On](#configure-intacct-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Intacct](#create-intacct-test-user)**  - para ter um equivalente da Eduarda Almeida na Intacct que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Intacct, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Intacct** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Intacct domínio e URLs únicas início de sessão em informações](common/idp-reply.png)

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.intacct.com/ia/acct/sso_response.phtml`|
    | `https://www.intacct.com/ia/acct/sso_response.phtml` |

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de resposta real. Contacte [equipa de suporte de cliente Intacct](https://us.intacct.com/support) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Intacct** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-intacct-single-sign-on"></a>Configurar Intacct Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Intacct como um administrador.

1. Clique nas **empresa** separador e, em seguida, clique em **informações da empresa**.

    ![Empresa](./media/intacct-tutorial/ic790037.png "empresa")

1. Clique nas **Security** separador e, em seguida, clique em **editar**.

    ![Segurança](./media/intacct-tutorial/ic790038.png "segurança")

1. Na **início de sessão (SSO) único** secção, execute os seguintes passos:

    ![Início de sessão único](./media/intacct-tutorial/ic790039.png "início de sessão único")

    a. Selecione **ativar o início de sessão único em**.

    b. Como **tipo de fornecedor de identidade**, selecione **SAML 2.0**.

    c. No **URL de emissor** caixa de texto, cole o valor de **Azure Ad identificador** que copiou do portal do Azure.

    d. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    e. Abra sua **base 64** codificado de certificado no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado** caixa.

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
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Intacct.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Intacct**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Intacct**.

    ![A ligação de Intacct na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-intacct-test-user"></a>Criar utilizador de teste Intacct

Para configurar a utilizadores do Azure AD para que possa iniciar Intacct, tem de ser aprovisionados em Intacct. Intacct, o aprovisionamento é uma tarefa manual.

**Para aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Intacct** inquilino.

1. Clique nas **empresa** separador e, em seguida, clique em **utilizadores**.

    ![Os utilizadores](./media/intacct-tutorial/ic790041.png "utilizadores")

1. Clique nas **adicionar** separador.

    ![Adicione](./media/intacct-tutorial/ic790042.png "adicionar")

1. Na **informações do utilizador** secção, execute os seguintes passos:

    ![Informações do usuário](./media/intacct-tutorial/ic790043.png "informações do utilizador")

    a. Introduza o **ID de utilizador**, o **Apelido**, **nome**, o **endereço de E-Mail**, o **Title**e o **Telefone** de uma conta do Azure AD que pretende aprovisionar para o **informações de utilizador** secção.

    b. Selecione o **privilégios de administrador** de uma conta do Azure AD que pretende aprovisionar.

    c. Clique em **Guardar**. O titular da conta do Azure AD recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory.

> [!NOTE]
> Para aprovisionar contas de utilizador do Azure AD, pode utilizar outras ferramentas de criação da conta de utilizador de Intacct ou APIs que são fornecidas pelo Intacct.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Intacct no painel de acesso, deve ser automaticamente sessão iniciada no Intacct para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

