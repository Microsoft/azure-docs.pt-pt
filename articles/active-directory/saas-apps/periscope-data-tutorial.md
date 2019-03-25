---
title: 'Tutorial: Integração do Active Directory do Azure com dados de Periscope | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Periscope dados.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f378edb-9ac9-494d-a84a-03357b923ee1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 0d8fd6a0422ee26610f58f872cba4487d4e01e6b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403634"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Tutorial: Integração do Active Directory do Azure com dados de Periscope

Neste tutorial, saiba como integrar dados Periscope com o Azure Active Directory (Azure AD).
Integração de dados de Periscope com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso a dados Periscope.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para dados de Periscope (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com dados de Periscope, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Dados de periscope único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suportam dados periscope **SP** iniciada SSO

## <a name="adding-periscope-data-from-the-gallery"></a>Adicionando dados Periscope da Galeria

Para configurar a integração de dados de Periscope com o Azure AD, terá de adicionar Periscope dados a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Periscope dados a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **dados Periscope**, selecione **dados Periscope** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Dados de periscope na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com dados de Periscope com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Periscope dados deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Periscope dados, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Periscope dados de início de sessão único](#configure-periscope-data-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de dados de Periscope](#create-periscope-data-test-user)**  - para ter um equivalente da Eduarda Almeida no Periscope dados que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com dados de Periscope, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **dados Periscope** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Periscope dados domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** texto, escreva qualquer um dos URLs:
    
    | |
    |--|
    | `https://app.periscopedata.com/` |
    | ` https://app.periscopedata.com/app/<SITENAME>` |

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://app.periscopedata.com/<SITENAME>/sso`

    > [!NOTE]
    > O valor do URL de início de sessão não é real. Atualize os valores com o início de sessão real no URL. Contacte [equipa de suporte de cliente de dados de Periscope](mailto:support@periscopedata.com) para obter este valor e o valor do identificador receberá dos **configurar Periscope dados Single Sign-On** secção que é explicada mais tarde no tutorial. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Configurar Periscope dados início de sessão único

1. Numa janela do browser web diferente, inicie sessão no Periscope dados como um administrador.

2. Abra o menu de engrenagem no canto inferior esquerdo e abra o **faturação** > **segurança** menu e execute os seguintes passos. Apenas os administradores têm acesso a estas definições.

    ![Informações de dados configurar periscope](./media/periscope-data-tutorial/configure01.png)

    a. Copiar o **URL de metadados de Federação de aplicação** do passo 5 de # **certificado de assinatura SAML** e abra-o num browser. Esta ação irá abrir um documento XML.

    b. Na **início de sessão único** caixa de texto, selecione **Azure Active Directory**.

    c. Localize a marca **SingleSignOnService** e cole o **localização** valor no **SSO URL** caixa de texto.

    d. Localize a marca **SingleLogoutService** e cole o **localização** valor no **SLO URL** caixa de texto.

    e. Copiar o **identificador** para a sua instância de valor e cole-o na **identificador (ID de entidade)** caixa de texto de **configuração básica de SAML** secção no portal do Azure.

    f. Localizar a primeira etiqueta do ficheiro XML, copie o valor da **entityID** e cole-a no **emissor** caixa de texto.

    g. Localize a marca **IDPSSODescriptor** com o protocolo SAML. Nessa secção, localize a marca **KeyDescriptor** com **utilizar = assinatura**. Copie o valor da **X509Certificate** e cole-a no **certificado** caixa de texto.

    h. Sites com vários espaços de podem escolher o espaço de padrão do **espaço predefinido** menu pendente. Este será o espaço de novos utilizadores adicionados ao quando iniciar sessão no Periscope dados pela primeira vez e são aprovisionados através do Active Directory início de sessão único.

    i. Por fim, clique em **salvar** e **confirmar** as definições de SSO alterar digitando **fim de sessão**.

    ![Informações de dados configurar periscope](./media/periscope-data-tutorial/configure02.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos dados Periscope.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Periscope dados**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Periscope dados**.

    ![A ligação de dados de Periscope na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-periscope-data-test-user"></a>Criar utilizador de teste de dados de Periscope

Para ativar a utilizadores do Azure AD iniciar sessão no Periscope dados, tem de ser aprovisionados no Periscope dados. No Periscope dados, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Periscope dados como administrador.

2. Clique no **definições** ícone no canto inferior esquerdo do menu e navegue até à **permissões**.

    ![Informações de dados configurar periscope](./media/periscope-data-tutorial/configure03.png)

3. Clique nas **adicionar utilizador** e execute os seguintes passos:

      ![Informações de dados configurar periscope](./media/periscope-data-tutorial/configure04.png)

    a. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **sobrenome** texto, digite o apelido do utilizador, como **Simon**.

    c. Na **E-Mail** texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    d. Clique em **adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de dados de Periscope no painel de acesso, deve ser automaticamente sessão iniciada no Periscope dados para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

