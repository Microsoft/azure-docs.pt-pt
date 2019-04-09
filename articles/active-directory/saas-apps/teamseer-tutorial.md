---
title: 'Tutorial: Integração do Active Directory do Azure com TeamSeer | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e TeamSeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: ef11011ec37f72003dd6c238ce748d417bfcd505
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256622"
---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Tutorial: Integração do Active Directory do Azure com TeamSeer

Neste tutorial, saiba como integrar TeamSeer com o Azure Active Directory (Azure AD).
Integrar TeamSeer no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao TeamSeer.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para TeamSeer (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com TeamSeer, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* TeamSeer logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta TeamSeer **SP** iniciada SSO

## <a name="adding-teamseer-from-the-gallery"></a>Adicionando TeamSeer da Galeria

Para configurar a integração do TeamSeer com o Azure AD, terá de adicionar TeamSeer a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar TeamSeer a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **TeamSeer**, selecione **TeamSeer** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![TeamSeer na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com TeamSeer com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TeamSeer deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com TeamSeer, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar TeamSeer Single Sign-On](#configure-teamseer-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste TeamSeer](#create-teamseer-test-user)**  - para ter um equivalente da Eduarda Almeida na TeamSeer que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com TeamSeer, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **TeamSeer** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![TeamSeer domínio e URLs únicas início de sessão em informações](common/sp-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://www.teamseer.com/<companyid>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente TeamSeer](https://pages.theaccessgroup.com/solutions_business-suite_absence-management_contact.html) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar TeamSeer** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-teamseer-single-sign-on"></a>Configurar TeamSeer Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa TeamSeer como um administrador.

1. Aceda a **administrador de RH**.

    ![Administrador de RH](./media/teamseer-tutorial/ic789634.png "administrador de RH")

1. Clique em **configuração**.

    ![Programa de configuração](./media/teamseer-tutorial/ic789635.png "configuração")

1. Clique em **configurar informações de fornecedor SAML**.

    ![Definições de SAML](./media/teamseer-tutorial/ic789636.png "definições de SAML")

1. Na secção de detalhes do fornecedor de SAML, execute os seguintes passos:

    ![Definições de SAML](./media/teamseer-tutorial/ic789637.png "definições de SAML")

    a. Na **URL** caixa de texto, colar a **URL de início de sessão** valor, que copiou do portal do Azure.

    b. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo de-lo na sua área de transferência e, em seguida, cole-os para o **certificado público do IdP** caixa de texto.

1. Para concluir a configuração de fornecedores SAML, execute os seguintes passos:

    ![Definições de SAML](./media/teamseer-tutorial/ic789638.png "definições de SAML")

    a. Na **endereços de E-Mail de teste**, escreva o endereço de e-mail do utilizador de teste.
  
    b. Na **emissor** caixa de texto, escreva o URL do emissor do fornecedor de serviços.
  
    c. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para TeamSeer.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **TeamSeer**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **TeamSeer**.

    ![A ligação de TeamSeer na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-teamseer-test-user"></a>Criar utilizador de teste TeamSeer

Para ativar a utilizadores do Azure AD iniciar sessão no TeamSeer, eles têm de ser aprovisionados para ShiftPlanning. No caso de TeamSeer, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **TeamSeer** site da empresa como administrador.

1. Aceda a **administrador de RH \> usuários** e, em seguida, clique em **executar o Assistente de novo utilizador**.

    ![Administrador de RH](./media/teamseer-tutorial/ic789640.png "administrador de RH")

1. Na **detalhes de utilizador** secção, execute os seguintes passos:

    ![Detalhes de utilizador](./media/teamseer-tutorial/ic789641.png "detalhes de utilizador")

    a. Tipo de **nome próprio**, **Apelido**, **nome de utilizador (endereço de E-Mail)** de um Azure válido conta AD que pretende aprovisionar em para as caixas de texto relacionadas.
  
    b. Clique em **Seguinte**.

1. Siga na tela instruções para adicionar um novo utilizador e clique em **concluir**.

> [!NOTE]
> Pode utilizar quaisquer outras TeamSeer utilizador conta criação ferramentas ou APIs fornecidas pelo TeamSeer para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico TeamSeer no painel de acesso, deve ser automaticamente sessão iniciada no TeamSeer para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
