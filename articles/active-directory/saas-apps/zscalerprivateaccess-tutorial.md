---
title: 'Tutorial: Integração do Active Directory do Azure com o Zscaler privada acesso (ZPA) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o acesso privado Zscaler (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 3213667e95c1e5cb68a849d6031db9629e5b273b
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65895976"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>Tutorial: Integração do Active Directory do Azure com o Zscaler privada acesso (ZPA)

Neste tutorial, saiba como integrar o acesso privado Zscaler (ZPA) com o Azure Active Directory (Azure AD).
Integrar o acesso privado Zscaler (ZPA) no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso para Zscaler privada acesso (ZPA).
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Zscaler privada acesso (ZPA) (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Zscaler privada acesso (ZPA), terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Acesso privado Zscaler (ZPA) início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Acesso privado Zscaler (ZPA) suporta **SP** iniciada SSO

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Adicionando o acesso privado Zscaler (ZPA) da Galeria

Para configurar a integração do Zscaler privada acesso (ZPA) para o Azure AD, terá de adicionar acesso privado Zscaler (ZPA) a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar acesso privado Zscaler (ZPA) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Zscaler privada acesso (ZPA)**, selecione **Zscaler privada acesso (ZPA)** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Zscaler privada acesso (ZPA) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Zscaler privada acesso (ZPA) com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zscaler privada acesso (ZPA) deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Zscaler privada acesso (ZPA), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o acesso privado Zscaler (ZPA) início de sessão único](#configure-zscaler-private-access-zpa-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de acesso privado Zscaler (ZPA)](#create-zscaler-private-access-zpa-test-user)**  - para ter um equivalente da Eduarda Almeida na Zscaler privada acesso (ZPA) que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Zscaler privada acesso (ZPA), execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Zscaler privada acesso (ZPA)** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Acesso privado Zscaler (ZPA) de domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > O **iniciar sessão no URL** valor não é real. Atualize o valor com o início de sessão real no URL. Contacte [equipa de suporte de cliente de acesso privado Zscaler (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **definir segurança Zscaler privada acesso (ZPA)** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-zscaler-private-access-zpa-single-sign-on"></a>Configurar Zscaler privada acesso (ZPA) início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Zscaler acesso (ZPA privada) como administrador.

2. Navegue para **administrador** e, em seguida, clique em **configuração do Idp**.

    ![Configurar o início de sessão único no lado de aplicação](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

3. Na **configuração do Idp** secção, clique em **adicione a nova configuração do IDP**.

    ![Configurar o início de sessão único no lado de aplicação](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

4. Na **configuração do IDP novo** secção, execute os seguintes passos:

    ![Configurar o início de sessão único no lado de aplicação](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. Clique em **selecionar ficheiro** e carregar o ficheiro de metadados baixado.

    b. Clique em **guardar** botão.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Zscaler privada acesso (ZPA).

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Zscaler privada acesso (ZPA)**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler privada acesso (ZPA)**.

    ![A ligação de acesso privado Zscaler (ZPA) na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Criar utilizador de teste de acesso privado Zscaler (ZPA)

Nesta secção, vai criar um usuário chamado Eduarda Almeida na Zscaler privada acesso (ZPA). Trabalhe em conjunto com [equipa de suporte de acesso privado Zscaler (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para adicionar os utilizadores na plataforma do Zscaler privada acesso (ZPA).

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de acesso privado Zscaler (ZPA) no painel de acesso, deve ser automaticamente conectado para o Zscaler privada acesso (ZPA) para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

