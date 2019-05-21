---
title: 'Tutorial: Integração do Active Directory do Azure com o Suite de vida de SilkRoad | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e SilkRoad vida Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: bcad6232de7fa257b58fe6d84f2c2ff794b64589
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65902395"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: Integração do Active Directory do Azure com o Suite de vida de SilkRoad

Neste tutorial, saiba como integrar SilkRoad vida Suite com o Azure Active Directory (Azure AD).
Integrar SilkRoad vida Suite no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao conjunto de vida de SilkRoad.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada ao Suite de vida de SilkRoad (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Suite de vida de SilkRoad, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Conjunto de vida de SilkRoad início de sessão único ativada subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o conjunto de vida de SilkRoad **SP** iniciada SSO

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Adicionar conjunto de vida de SilkRoad a partir da Galeria

Para configurar a integração do SilkRoad vida Suite para o Azure AD, terá de adicionar SilkRoad vida Suite a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SilkRoad vida Suite a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Suite de vida de SilkRoad**, selecione **SilkRoad vida Suite** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Conjunto de vida de SilkRoad na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Suite de vida de SilkRoad com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no pacote de vida de SilkRoad deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com SilkRoad vida Suite, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar SilkRoad vida Suite início de sessão único](#configure-silkroad-life-suite-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do pacote de vida de SilkRoad](#create-silkroad-life-suite-test-user)**  - para ter um equivalente da Eduarda Almeida no conjunto de vida de SilkRoad que está ligada à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Suite de vida de SilkRoad, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SilkRoad vida Suite** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    > [!NOTE]
    > Obterá o **ficheiro de metadados do fornecedor de serviços** explicado posteriormente neste tutorial.

    a. Clique em **carregamento de ficheiro de metadados**.

    ![image](common/upload-metadata.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![image](common/browse-upload-metadata.png)

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores ficam automaticamente preenchida na seção de configuração de SAML básica:

    ![image](common/sp-identifier-reply.png)

    > [!Note]
    > Se o **identificador** e **URL de resposta** valores não estiver a obter polulated automática, em seguida, preencha os valores manualmente de acordo com seus requisitos.

    d. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. Sobre o **configuração básica de SAML** secção, se não tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    ![SilkRoad vida Suite domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP`|
    | `https://<subdomain>.silkroad.com/Authentication/SP`|

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/`|
    | `https://<subdomain>.silkroad.com/Authentication/`|

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente de conjunto de vida de SilkRoad](https://www.silkroad.com/locations/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar SilkRoad vida Suite** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Configurar SilkRoad vida Suite início de sessão único

1. Inicie sessão no site da sua empresa SilkRoad como administrador.

    > [!NOTE]
    > Para obter acesso à aplicação SilkRoad vida Suite autenticação para configurar a Federação com o Microsoft Azure AD, contacte o suporte de SilkRoad ou o seu representante de SilkRoad serviços.

1. Aceda a **fornecedor de serviços**e, em seguida, clique em **detalhes de Federação**.

    ![O Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Clique em **transferir metadados de Federação**e, em seguida, guarde o ficheiro de metadados no seu computador. Utilizar metadados de Federação transferido como um **ficheiro de metadados do fornecedor de serviços** no **configuração básica de SAML** secção no portal do Azure.

    ![O Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. No seu **SilkRoad** aplicação, clique em **autenticação origens**.

    ![O Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Clique em **Adicionar origem de autenticação**.

    ![O Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Na **Adicionar origem de autenticação** secção, execute os seguintes passos:

    ![O Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. Sob **opção 2 - ficheiro de metadados**, clique em **procurar** para carregar o ficheiro de metadados baixado a partir do portal do Azure.
  
    b. Clique em **fornecedor de identidade criar utilizando dados de ficheiro**.

1. Na **origens de autenticação** secção, clique em **editar**.

    ![O Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. Sobre o **Editar origem de autenticação** caixa de diálogo, execute os seguintes passos:

    ![O Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Como **Enabled**, selecione **Sim**.

    b. Na **EntityId** caixa de texto, cole o valor de **do Azure AD identificador** que copiou do portal do Azure.

    c. Na **IdP Descrição** caixa de texto, digite uma descrição para a sua configuração (por exemplo: *SSO do Azure AD*).

    d. Na **ficheiro de metadados** caixa de texto, carregamento a **metadados** ficheiro que transferiu a partir do portal do Azure.
  
    e. Na **IdP nome** caixa de texto, escreva um nome específico à sua configuração (por exemplo: *Azure SP*).
  
    f. Na **URL de serviço de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    g. Na **URL do serviço de início de sessão** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    h. Clique em **Guardar**.

1. Desative todas as outras origens de autenticação.

    ![O Azure AD Single Sign-On](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao SilkRoad vida Suite.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SilkRoad vida Suite**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Suite de vida de SilkRoad**.

    ![A ligação de SilkRoad vida Suite na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-silkroad-life-suite-test-user"></a>Criar utilizador de teste SilkRoad vida Suite

Nesta secção, vai criar um usuário chamado Eduarda Almeida no SilkRoad vida Suite. Trabalhar com [equipa de suporte de cliente de conjunto de vida de SilkRoad](https://www.silkroad.com/locations/) para adicionar os utilizadores na plataforma SilkRoad vida Suite. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico SilkRoad vida Suite no painel de acesso, deve ser automaticamente conectado para o conjunto de vida de SilkRoad para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
