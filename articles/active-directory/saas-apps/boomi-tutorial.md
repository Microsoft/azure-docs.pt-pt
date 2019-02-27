---
title: 'Tutorial: Integração do Active Directory do Azure com Boomi | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15149cd172611bdb9b18d27c40cbfa44099330d3
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868991"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Tutorial: Integração do Active Directory do Azure com Boomi

Neste tutorial, saiba como integrar Boomi com o Azure Active Directory (Azure AD).
Integrar Boomi no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Boomi.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Boomi (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Boomi, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Boomi logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Boomi **IDP** iniciada SSO

## <a name="adding-boomi-from-the-gallery"></a>Adicionando Boomi da Galeria

Para configurar a integração do Boomi com o Azure AD, terá de adicionar Boomi a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Boomi a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Boomi**, selecione **Boomi** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Boomi na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Boomi com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Boomi deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Boomi, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Boomi Single Sign-On](#configure-boomi-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Boomi](#create-boomi-test-user)**  - para ter um equivalente da Eduarda Almeida na Boomi que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Boomi, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Boomi** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **configuração básica de SAML** caixa de diálogo.

    ![Boomi domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL: `https://platform.boomi.com/`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE]
    > O valor de URL de resposta não é real. Atualize o valor com o URL de resposta real. Contacte [equipa de suporte de cliente Boomi](https://boomi.com/company/contact/) para obter este valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação de Boomi espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name |  Atributo de origem|
    | ---------------|  --------- |
    | FEDERATION_ID | user.mail |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar Boomi** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-boomi-single-sign-on"></a>Configurar Boomi Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Boomi como um administrador. 

2. Navegue para **nome da empresa** e aceda à **configurar**.

3. Clique nas **opções de SSO** separador e executar passos abaixo.

    ![Configurar o início de sessão único no lado de aplicação](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Verifique **ativar SAML Single Sign-On** caixa de verificação.

    b. Clique em **importação** para carregar o certificado transferido do Azure AD para **certificado do fornecedor de identidade**.

    c. Na **URL de início de sessão do fornecedor de identidade** caixa de texto, colocar o valor de **URL de início de sessão** partir da janela de configuração de aplicação do Azure AD.

    d. Como **localização de Id de Federação**, selecione **Id de Federação é no elemento de atributo FEDERATION_ID** botão de opção.

    e. Clique em **guardar** botão.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Boomi.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Boomi**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Boomi**.

    ![A ligação de Boomi na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-boomi-test-user"></a>Criar utilizador de teste Boomi

Para habilitar os utilizadores do Azure AD iniciar sessão no Boomi, tem de ser aprovisionados em Boomi. No caso de Boomi, o aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para Aprovisionar uma conta de utilizador, execute os seguintes passos:

1. Inicie sessão no site da sua empresa Boomi como administrador.

2. Após iniciar sessão, navegue até **gestão de utilizadores** e aceda à **utilizadores**.

    ![Os utilizadores](./media/boomi-tutorial/tutorial_boomi_001.png "utilizadores")

3. Clique em **+** ícone e o **funções de utilizador de adicionar/mantenha** é aberta a caixa de diálogo.

    ![Os utilizadores](./media/boomi-tutorial/tutorial_boomi_002.png "utilizadores")

    ![Os utilizadores](./media/boomi-tutorial/tutorial_boomi_003.png "utilizadores")

    a. Na **endereço de email do usuário** caixa de texto, como o tipo de e-mail do utilizador BrittaSimon@contoso.com.

    b. Na **nome próprio** caixa de texto, tipo, o primeiro nome de utilizador, como a Eduarda.

    c. Na **Apelido** caixa de texto, digite o apelido do utilizador, como Simon.

    d. Introduza o utilizador **ID de Federação**. Cada utilizador tem de ter um ID de federação que identifica unicamente o usuário dentro da conta.

    e. Atribuir a **usuário padrão** função ao utilizador. Não atribua a função de administrador porque isso ofereceria em contato com acesso de atmosfera normal, bem como acesso de início de sessão único.

    f. Clique em **OK**.

    > [!NOTE]
    > O utilizador não irá receber um e-mail de notificação de boas-vindas que contém uma palavra-passe que pode ser utilizada para iniciar sessão na conta de AtomSphere porque a palavra-passe é gerenciada através do fornecedor de identidade. Pode utilizar quaisquer outras Boomi utilizador conta criação ferramentas ou APIs fornecidas pelo Boomi para aprovisionar contas de utilizador do AAD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Boomi no painel de acesso, deve ser automaticamente sessão iniciada no Boomi para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

