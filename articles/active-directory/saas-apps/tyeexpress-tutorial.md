---
title: 'Tutorial: Integração do Active Directory do Azure com o T & E Express | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e T & E Express.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: B42374E5-2559-4309-8EF2-820BEE7EBB0C
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 5dc1451d75b14c89fd9fa1742c3e0b078b035d7f
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361851"
---
# <a name="tutorial-azure-active-directory-integration-with-te-express"></a>Tutorial: Integração do Active Directory do Azure com o T & E Express

Neste tutorial, saiba como integrar o T & E Express com o Azure Active Directory (Azure AD).
T & E Express a integração com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao T & E Express.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o T & E Express (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o T & E Express, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* T & E Express único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* T & E Express suporta **IDP** iniciada SSO

## <a name="adding-te-express-from-the-gallery"></a>Adicionar T & E Express a partir da Galeria

Para configurar a integração de d & E Express com o Azure AD, terá de adicionar T & E Express a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar T & E Express a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **T & E Express**, selecione **T & E Express** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![T & E Express na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com o T & E Express com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único para funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no T & E Express ter de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o T & E Express, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar T & E Express início de sessão único](#configure-te-express-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste T & E Express](#create-te-express-test-user)**  - para ter um equivalente da Eduarda Almeida na T & E Express que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o T & E Express, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **T & E Express** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![T & URLs e domínio da Express E únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** texto, escreva o valor como URL usando o seguinte padrão: `https://<domain>.tyeexpress.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<domain>.tyeexpress.com/authorize/samlConsume.aspx`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Aqui iremos sugerir-lhe utilizar o valor único de cadeia de caracteres no identificador de. Contacte [equipa de suporte de T & cliente de Express E](http://www.tyeexpress.com/contacto.aspx) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **configurar T & E Express** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-te-express-single-sign-on"></a>Configurar T & E Express início de sessão único

1. Para configurar o início de sessão único num **T & E Express** lado, o início de sessão para as horas e despesas express aplicação sem início de sessão único SAML sobre como utilizar as credenciais de administrador.

1. Sob o **administrador** separador, clique em **domínio SAML** para abrir a página de definições de SAML.

    ![Configurar o início de sessão único](./media/tyeexpress-tutorial/tye-SAML.png)

1. Selecione o **Activar(Activate)** opção partir **não** para **SI(Yes)**. Na **metadados de fornecedor de identidade** caixa de texto, cole os metadados XML que transferiu do portal do Azure.

    ![Configurar o início de sessão único](./media/tyeexpress-tutorial/tyeAdmin.png)

1. Clique nas **Guardar(Save)** botão para guardar as definições.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a T & E Express.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **T & E Express**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **T & E Express**.

    ![A ligação de T & E Express na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-te-express-test-user"></a>Criar utilizador de teste T & E Express

Para habilitar logon T & E Express de utilizadores do Azure AD, tem de ser aprovisionados no T & E Express. Em caso de T & E Express, o aprovisionamento é uma tarefa manual.

**Para aprovisionar contas de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa T & E Express como administrador.

1. Na etiqueta do administrador, clique em utilizadores para abrir a página mestra de utilizadores.

    ![Adicionar o funcionário](./media/tyeexpress-tutorial/tye-adminusers.png)

1. Na home page, clique em **+** para adicionar os utilizadores.

    ![Adicionar o funcionário](./media/tyeexpress-tutorial/tye-usershome.png)

1. Introduza todos os detalhes obrigatórios como pedido no formulário e clique no botão Guardar para guardar os detalhes.

    ![Adicionar o funcionário](./media/tyeexpress-tutorial/tye-usersadd.png)

    ![Adicionar o funcionário](./media/tyeexpress-tutorial/tye-userssave.png)

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico T & E Express no painel de acesso, deve ser automaticamente conectado para o T e, em seguida, E Express para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

