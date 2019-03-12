---
title: 'Tutorial: Integração do Active Directory do Azure com o Marketo | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e do Marketo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/19/2019
ms.author: jeedes
ms.openlocfilehash: 0b6fed02aa7480f3c9e7be68c5d818e956cc436d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766330"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: Integração do Active Directory do Azure com o Marketo

Neste tutorial, saiba como integrar o Marketo com o Azure Active Directory (Azure AD).
Integração do Marketo com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Marketo.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada ao Marketo (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Marketo, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Marketo logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o Marketo **IDP** iniciada SSO

## <a name="adding-marketo-from-the-gallery"></a>Adicionando o Marketo da Galeria

Para configurar a integração do Marketo com o Azure AD, terá de adicionar Marketo a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Marketo a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Marketo**, selecione **Marketo** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Marketo na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Marketo com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Marketo tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o Marketo, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Marketo Single Sign-On](#configure-marketo-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Marketo](#create-marketo-test-user)**  - para ter um equivalente da Eduarda Almeida no Marketo que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Marketo, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Marketo** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![URLs de domínio do Marketo e únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://saml.marketo.com/sp`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Contacte [equipa de suporte de cliente do Marketo](http://investors.marketo.com/contactus.cfm) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar o Marketo** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-marketo-single-sign-on"></a>Configurar o início de sessão único do Marketo em

1. Para obter o Id de Munchkin da sua aplicação, inicie sessão no Marketo com credenciais de administrador e execute as ações seguintes:
   
    a. Inicie sessão na aplicação do Marketo com credenciais de administrador.
   
    b. Clique nas **administrador** botão no painel de navegação superior.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue para o menu de integração e clique nas **Munchkin link**.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Copie o Id de Munchkin aparecem no ecrã e concluir o seu URL de resposta no Assistente de configuração do Azure AD.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Para configurar o SSO na aplicação, siga os passos abaixo:
   
    a. Inicie sessão na aplicação do Marketo com credenciais de administrador.
   
    b. Clique nas **administrador** botão no painel de navegação superior.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue para o menu de integração e clique em **início de sessão único**.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Para ativar as definições de SAML, clique em **editar** botão.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Ativado** as definições de início de sessão único.
   
    f. Colar o **do Azure AD identificador**, na **ID do emissor** caixa de texto.
   
    g. Na **ID de entidade** caixa de texto, introduza o URL como `http://saml.marketo.com/sp`.
   
    h. Selecione a localização de ID de utilizador como **elemento de identificador de nome**.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Se o identificador de utilizador não é o valor UPN, em seguida, altere o valor no separador de atributo.
   
    i. Carregue o certificado que transferiu a partir do Assistente de configuração do Azure AD. **Guardar** as definições.
   
    j. Edite as definições de páginas de redirecionamento.
   
    k. Colar o **URL de início de sessão** no **URL de início de sessão** caixa de texto.
   
    l. Colar o **URL de fim de sessão** no **URL de fim de sessão** caixa de texto.
   
    m. Na **URL de erro**, cópia sua **URL de instância do Marketo** e clique em **guardar** botão para guardar as definições.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Para ativar o SSO para os utilizadores, conclua as seguintes ações:
   
    a. Inicie sessão na aplicação do Marketo com credenciais de administrador.
   
    b. Clique nas **administrador** botão no painel de navegação superior.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navegue para o **Security** menu e clique em **definições de início de sessão**.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Verifique os **requerem SSO** opção e **guardar** as definições.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_14.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao Marketo.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Marketo**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Marketo**.

    ![A ligação do Marketo na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-marketo-test-user"></a>Criar utilizador de teste do Marketo

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Marketo. Siga estes passos para criar um utilizador na plataforma do Marketo.

1. Inicie sessão na aplicação do Marketo com credenciais de administrador.

2. Clique nas **administrador** botão no painel de navegação superior.
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navegue para o **Security** menu e clique em **de utilizadores e funções**
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Clique nas **convidar novo utilizador** ligação no separador utilizadores
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. No Assistente de convidar novo utilizador preencher as seguintes informações
   
    a. Introduza o utilizador **E-Mail** endereço na caixa de texto
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Introduza o **nome próprio** na caixa de texto
   
    c. Introduza o **Apelido** na caixa de texto
   
    d. Clique em **Seguinte**

6. Na **permissões** separador, selecione a **funções de utilizador** e clique em **seguinte**
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Clique nas **enviar** botão para enviar o convite de utilizador
   
    ![Configurar o início de sessão único](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Recebe a notificação de e-mail e tem de clicar na ligação e alterar a palavra-passe para ativar a conta de utilizador. 

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do Marketo no painel de acesso, deve ser automaticamente sessão iniciada no Marketo para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

