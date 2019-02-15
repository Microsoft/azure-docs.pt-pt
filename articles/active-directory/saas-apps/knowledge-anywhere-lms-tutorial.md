---
title: 'Tutorial: Integração do Active Directory do Azure com dados de conhecimento em qualquer lugar LMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e LMS do conhecimento em qualquer lugar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.openlocfilehash: 0ac3522d4182ef7cbf35eb8505dede2aff233937
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56302676"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledge-anywhere-lms"></a>Tutorial: Integração do Active Directory do Azure com LMS do conhecimento em qualquer lugar

Neste tutorial, saiba como integrar LMS do conhecimento em qualquer lugar com o Azure Active Directory (Azure AD).
Integração de LMS do conhecimento em qualquer lugar com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD, quem tem acesso a dados de conhecimento em qualquer lugar LMS.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para dados de conhecimento em qualquer lugar LMS (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LMS do conhecimento em qualquer lugar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Dados de conhecimento em qualquer lugar LMS único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporte de dados de conhecimento em qualquer lugar LMS **SP e IDP** iniciada SSO
* Suporte de dados de conhecimento em qualquer lugar LMS **Just In Time** aprovisionamento de utilizadores

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Adicionando LMS do conhecimento em qualquer lugar da Galeria

Para configurar a integração de LMS do conhecimento em qualquer lugar com o Azure AD, terá de adicionar LMS do conhecimento em qualquer lugar a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar LMS do conhecimento em qualquer lugar a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **LMS do conhecimento em qualquer lugar**, selecione **LMS do conhecimento em qualquer lugar** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Dados de conhecimento em qualquer lugar LMS na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com dados de conhecimento em qualquer lugar LMS com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em dados de conhecimento em qualquer lugar LMS deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com LMS do conhecimento em qualquer lugar, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar dados de conhecimento em qualquer lugar LMS início de sessão único](#configure-knowledge-anywhere-lms-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de dados de conhecimento em qualquer lugar LMS](#create-knowledge-anywhere-lms-test-user)**  - para ter um equivalente da Eduarda Almeida em dados de conhecimento em qualquer lugar LMS que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com LMS do conhecimento em qualquer lugar, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **LMS do conhecimento em qualquer lugar** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![Domínio de LMS do conhecimento em qualquer lugar e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta, o que é explicado mais tarde no tutorial.

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Domínio de LMS do conhecimento em qualquer lugar e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > O valor de URL de início de sessão não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de LMS do conhecimento em qualquer lugar](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) para obter este valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

7. Sobre o **configurar dados de conhecimento em qualquer lugar LMS** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-knowledge-anywhere-lms-single-sign-on"></a>Configurar dados de conhecimento em qualquer lugar LMS início de sessão único

1. Na outra janela do browser, abra o portal de administração de LMS do conhecimento em qualquer lugar.

2. Selecione sobre o **Site** separador.

    ![Dados de conhecimento em qualquer lugar LMS configuração](./media/knowledge-anywhere-lms-tutorial/configure1.png)

3. Selecione sobre o **definições de SAML** separador.

    ![Dados de conhecimento em qualquer lugar LMS configuração](./media/knowledge-anywhere-lms-tutorial/configure2.png)

4. Clique nas **adicionar novo**.

    ![Dados de conhecimento em qualquer lugar LMS configuração](./media/knowledge-anywhere-lms-tutorial/configure3.png)

5. Sobre o **adicionar/atualizar definições de SAML** página, execute os seguintes passos:

    ![Dados de conhecimento em qualquer lugar LMS configuração](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Introduza o nome de IDP de acordo com a sua organização. Para ex:- `Azure`.

    b. Na **ID de entidade do IDP** caixa de texto, colar **Azure Ad identificador** valor que copiou do portal do Azure.

    c. Na **URL de IDP** caixa de texto, colar **URL de início de sessão** valor que copiou do portal do Azure.

    d. Abra o ficheiro de certificado transferido a partir do portal do Azure no bloco de notas, copie o conteúdo do certificado e colá-lo no **certificado** caixa de texto.

    e. Na **URL de fim de sessão** caixa de texto, colar **URL de fim de sessão** valor que copiou do portal do Azure.

    f. Selecione **Site de Main** na lista pendente para o **domínio**.

    g. Cópia a **ID de entidade de SP** valor e cole-a na **identificador** caixa de texto **configuração básica de SAML** secção no portal do Azure.

    h. Copiar o **SP Response(ACS) URL** valor e cole-a na **URL de resposta** caixa de texto **configuração básica de SAML** secção no portal do Azure.

    i. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a dados de conhecimento em qualquer lugar LMS.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **dados de conhecimento em qualquer lugar LMS**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **LMS do conhecimento em qualquer lugar**.

    ![A ligação de LMS do conhecimento em qualquer lugar na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Criar utilizador de teste de LMS do conhecimento em qualquer lugar

Nesta secção, um usuário chamado Eduarda Almeida é criado na LMS do conhecimento em qualquer lugar. Dados de conhecimento em qualquer lugar LMS suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no LMS do conhecimento em qualquer lugar, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de LMS do conhecimento em qualquer lugar no painel de acesso, deve ser automaticamente conectado para os dados de conhecimento em qualquer lugar LMS para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)