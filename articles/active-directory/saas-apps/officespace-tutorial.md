---
title: 'Tutorial: Integração do Active Directory do Azure com o Software de OfficeSpace | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Software de OfficeSpace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 42ef2d9bbdf825eb0a872e9fe6fa66b34faa2cad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180573"
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Tutorial: Integração do Active Directory do Azure com o Software de OfficeSpace

Neste tutorial, saiba como integrar o Software de OfficeSpace com o Azure Active Directory (Azure AD).
Integrar o Software de OfficeSpace no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao OfficeSpace Software.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o Software de OfficeSpace (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Software de OfficeSpace, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Software de OfficeSpace logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a OfficeSpace Software **SP** iniciada SSO

* Oferece suporte a OfficeSpace Software **Just In Time** aprovisionamento de utilizadores

## <a name="adding-officespace-software-from-the-gallery"></a>Adicionar OfficeSpace Software a partir da Galeria

Para configurar a integração de OfficeSpace Software com o Azure AD, terá de adicionar OfficeSpace Software a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar OfficeSpace Software a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **OfficeSpace Software**, selecione **OfficeSpace Software** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Software de OfficeSpace na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Software de OfficeSpace com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no OfficeSpace Software deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Software de OfficeSpace, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar OfficeSpace Software início de sessão único](#configure-officespace-software-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de OfficeSpace Software](#create-officespace-software-test-user)**  - para ter um equivalente da Eduarda Almeida no OfficeSpace Software, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Software de OfficeSpace, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **OfficeSpace Software** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![OfficeSpace Software domínio e URLs únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente de Software OfficeSpace](mailto:support@officespacesoftware.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação de OfficeSpace Software espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Espera que a aplicação de OfficeSpace Software **nameidentifier** seja mapeado com **user.mail**, por isso, precisa editar o mapeamento do atributo, clicando em **editar** ícone e alteração o mapeamento do atributo.

    ![image](common/edit-attribute.png)

6. Além dos acima, o aplicativo de OfficeSpace Software espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | e-mail | user.mail |
    | nome | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. Na **certificado de assinatura SAML** secção, clique em **editar** botão para abrir **certificado de assinatura SAML** caixa de diálogo.

    ![Editar o certificado de assinatura de SAML](common/edit-certificate.png)

8. Na **certificado de assinatura SAML** secção, copie a **Thumbprint** e guarde-o no seu computador.

    ![Copie o valor do Thumbprint](common/copy-thumbprint.png)

9. Sobre o **configurar OfficeSpace Software** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-officespace-software-single-sign-on"></a>Configurar o Software de OfficeSpace início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu inquilino OfficeSpace Software como um administrador.

2. Aceda a **configurações** e clique em **conectores**.

    ![Configurar o início de sessão único no lado de aplicação](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Clique em **autenticação SAML**.

    ![Configurar o início de sessão único no lado de aplicação](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Na **autenticação SAML** secção, execute os seguintes passos:

    ![Configurar o início de sessão único no lado de aplicação](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Na **url de fim de sessão do fornecedor** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    b. Na **url de destino do cliente idp** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Colar o **Thumbprint** valor que copiou do portal do Azure, para o **impressão digital do certificado de cliente IDP** caixa de texto. 

    d. Clique em **guardar as definições de**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao OfficeSpace Software.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **OfficeSpace Software**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **OfficeSpace Software**.

    ![A ligação de OfficeSpace Software na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-officespace-software-test-user"></a>Criar utilizador de teste de OfficeSpace Software

Nesta secção, um usuário chamado Eduarda Almeida é criado na OfficeSpace Software. Software de OfficeSpace suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no OfficeSpace Software, é criado um novo após a autenticação.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, terá de contacto [equipa de suporte do Software de OfficeSpace](mailto:support@officespacesoftware.com).

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de OfficeSpace Software no painel de acesso, deve ser automaticamente conectado para o Software de OfficeSpace para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

