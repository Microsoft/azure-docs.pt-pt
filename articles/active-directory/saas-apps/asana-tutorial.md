---
title: 'Tutorial: Integração do Active Directory do Azure com Asana | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc0fbc55c73f6d745d39873236df389f121dc9c7
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880925"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Integração do Active Directory do Azure com Asana

Neste tutorial, saiba como integrar Asana com o Azure Active Directory (Azure AD).
Integração do Asana com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Asana.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Asana (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Asana, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Asana logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Asana **SP** iniciada SSO

* Suporta Asana [ **automatizada** aprovisionamento de utilizadores](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>Adicionando Asana da Galeria

Para configurar a integração do Asana para o Azure AD, terá de adicionar Asana a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Asana a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Asana**, selecione **Asana** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Asana na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Asana com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Asana deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Asana, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Asana Single Sign-On](#configure-asana-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Asana](#create-asana-test-user)**  - para ter um equivalente da Eduarda Almeida no Asana, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Asana, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Asana** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Asana domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, URL de tipo: `https://app.asana.com/`

    b. Na **identificador (ID de entidade)** caixa de texto, URL de tipo: `https://app.asana.com/`

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar Asana** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-asana-single-sign-on"></a>Configurar Asana Single Sign-On

1. Numa janela do browser diferente, início de sessão na sua aplicação do Asana. Para configurar o SSO no Asana, aceder às definições da área de trabalho ao clicar no nome de área de trabalho no canto superior direito da tela. Em seguida, clique em  **\<o nome da sua área de trabalho\> definições**.

    ![Definições de sso do Asana](./media/asana-tutorial/tutorial_asana_09.png)

2. Sobre o **definições de organização** janela, clique em **administração**. Em seguida, clique em **membros têm de iniciar sessão através de SAML** para ativar a configuração de SSO. A executar os seguintes passos:

    ![Configurar as definições de organização de início de sessão único](./media/asana-tutorial/tutorial_asana_10.png)  

    a. Na **URL da página de início de sessão** caixa de texto, colar a **URL de início de sessão**.

    b. Clique com o botão direito do rato no certificado transferido a partir do portal do Azure, em seguida, abra o ficheiro de certificado com o bloco de notas ou no seu editor de texto preferido. Copie o conteúdo entre o início e o título de certificado do fim e cole-na **certificado X.509** caixa de texto.

3. Clique em **Guardar**. Aceda a [Guia do Asana para configurar o SSO](https://asana.com/guide/help/premium/authentication#gl-saml) se precisar de mais assistência.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Asana.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Asana**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Asana**.

    ![A ligação do Asana na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-asana-test-user"></a>Criar utilizador de teste do Asana

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Asana. Asana suporta o aprovisionamento automático de utilizadores, que está por predefinição, ativada. Pode encontrar mais detalhes [aqui](asana-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

**Se precisar de criar manualmente o utilizador, execute, os seguintes passos:**

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Asana.

1. No **Asana**, aceda ao **Equipes** secção no painel esquerdo. Clique no botão do sinal de adição.

    ![Criar um utilizador de teste do Azure AD](./media/asana-tutorial/tutorial_asana_12.png)

2. Escreva a mensagem de e-mail do utilizador, como **britta.simon@contoso.com** na caixa de texto e, em seguida, selecione **convidar**.

3. Clique em **Enviar convite**. O novo utilizador irá receber um e-mail para a conta de e-mail. utilizador terá de criar e validar a conta.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do Asana no painel de acesso, deve ser automaticamente sessão iniciada no Asana para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Configurar o aprovisionamento do utilizador](asana-provisioning-tutorial.md)
