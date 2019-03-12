---
title: 'Tutorial: Integração do Active Directory do Azure com o Workday | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: ed2792c3bb599a0e5654be836d440b61fc803012
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780308"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Tutorial: Integração do Active Directory do Azure com o Workday

Neste tutorial, saiba como integrar o dia de trabalho com o Azure Active Directory (Azure AD).
Integração do Workday com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Workday.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada no Workday (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Workday, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Workday logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o workday **SP** e **IDP** iniciada SSO

## <a name="adding-workday-from-the-gallery"></a>Adicionando o Workday da Galeria

Para configurar a integração do Workday para o Azure AD, terá de adicionar dia de trabalho a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar dia de trabalho a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Workday**, selecione **Workday** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Dia de trabalho na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Workday com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Workday deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Workday, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Workday Single Sign-On](#configure-workday-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Workday](#create-workday-test-user)**  - para ter um equivalente da Eduarda Almeida no Workday que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Workday, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Workday** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Workday URLs de domínio e única informações de início de sessão](common/sp-identifier.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://www.workday.com`

5. Clique em **definir URLs adicionais** e executar o passo seguinte:

    ![Workday URLs de domínio e única informações de início de sessão](./media/workday-tutorial/reply.png)

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:  `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Estes valores não são o real. Atualize estes valores com o URL de início de sessão real e o URL de resposta. O URL de resposta tem de ter um subdomínio por exemplo: www, wd2, wd3, wd3 impl, wd5, wd5 impl).
    > Através de algo como "*http://www.myworkday.com*" funciona, mas "*http://myworkday.com*" não existir. Contacte [equipa de suporte de cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. A aplicação de dia de trabalho espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Aplicação workday espera **nameidentifier** seja mapeado com **user.mail**, **UPN** etc, por isso terá de editar o mapeamento do atributo clicando no **editar**  ícone e altere o mapeamento do atributo.

    ![imagem](common/edit-attribute.png)

    > [!NOTE]
    > Aqui estamos tiver mapeado a ID de nome com o UPN (user.userprincipalname) como predefinição. Tem de mapear o ID de nome com o ID de utilizador real na sua conta do Workday (seu e-mail, etc. UPN) para trabalhar com êxito do SSO.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar o Workday** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-workday-single-sign-on"></a>Configurar o Workday início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Workday como administrador.

2. Na **caixa de pesquisa** pesquisa com o nome **Editar configuração de inquilino – segurança** na parte superior esquerda do lado da home page.

    ![Editar a segurança de inquilino](./media/workday-tutorial/IC782925.png "Editar segurança do inquilino")

3. Na **URLs de redirecionamento** secção, execute os seguintes passos:

    ![URLs de redirecionamento](./media/workday-tutorial/IC7829581.png "URLs de redirecionamento")

    a. Clique em **Adicionar linha**.

    b. Na **URL de redireccionamento de início de sessão** caixa de texto e o **URL de redirecionamento do Mobile** caixa de texto, tipo o **URL de início de sessão** inserido o **configuração básica de SAML**  seção do portal do Azure.

    c. No portal do Azure, no **configurar Workday** secção, copie a **URL de fim de sessão**e, em seguida, cole-o para o **URL de redirecionamento de fim de sessão** caixa de texto.

    d. Na **utilizado para ambientes** caixa de texto, selecione o nome do ambiente.  

    >[!NOTE]
    > O valor do atributo de ambiente está associado ao valor do URL de inquilino:  
    >-Se o nome de domínio do URL de inquilino do Workday começa com impl por exemplo: *https://impl.workday.com/\<tenant\>/login-saml2.flex*), o **ambiente** atributo tem de ser definido para a implementação.  
    >– Se o nome de domínio é iniciado com algo mais, terá de contactar [equipa de suporte de cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter a correspondência **ambiente** valor.

4. Na **programa de configuração de SAML** secção, execute os seguintes passos:

    ![Configuração SAML](./media/workday-tutorial/IC782926.png "configuração SAML")

    a.  Selecione **ativar a autenticação SAML**.

    b.  Clique em **Adicionar linha**.

5. Na **fornecedores de identidade de SAML** secção, execute os seguintes passos:

    ![Fornecedores de identidade SAML](./media/workday-tutorial/IC7829271.png "fornecedores de identidade SAML")

    a. Na **nome do fornecedor de identidade** caixa de texto, escreva um nome de fornecedor (por exemplo: *SPInitiatedSSO*).

    b. No portal do Azure, no **configurar Workday** secção, copie a **identificador do Azure AD** valor e, em seguida, cole-o para o **emissor** caixa de texto.

    ![Fornecedores de identidade SAML](./media/workday-tutorial/IC7829272.png "fornecedores de identidade SAML")

    c. No portal do Azure, no **configurar Workday** secção, copie a **URL de fim de sessão** valor e, em seguida, cole-o para o **URL de resposta de fim de sessão** caixa de texto.

    d. No portal do Azure, no **configurar Workday** secção, copie a **URL de início de sessão** valor e, em seguida, cole-o para o **URL do serviço de SSO de IdP** caixa de texto.

    e. Na **utilizado para ambientes** caixa de texto, selecione o nome do ambiente.

    f. Clique em **certificado de chave pública fornecedor de identidade**e, em seguida, clique em **criar**.

    ![Crie](./media/workday-tutorial/IC782928.png "criar")

    g. Clique em **criar x509 chave pública**.

    ![Crie](./media/workday-tutorial/IC782929.png "criar")

6. Na **chave pública do Vista x509** secção, execute os seguintes passos:

    ![Chave pública do Vista x509](./media/workday-tutorial/IC782930.png "vista x509 de chave pública")

    a. Na **nome** caixa de texto, escreva um nome para o seu certificado (por exemplo: *PPE\_SP*).

    b. Na **válido de** caixa de texto, escreva a partir do valor de atributo do seu certificado válido.

    c.  Na **válido para** caixa de texto, digite o valor de atributo do seu certificado válido.

    > [!NOTE]
    > Pode obter o válido de data e a válido até à data de certificado transferido clicando nela.  As datas estão listadas na **detalhes** separador.
    >
    >

    d.  Abra o certificado com codificação base 64 no bloco de notas e, em seguida, copie o conteúdo do mesmo.

    e.  Na **certificado** caixa de texto, cole o conteúdo da sua área de transferência.

    f.  Clique em **OK**.

7. Execute os seguintes passos:

    ![Configuração de SSO](./media/workday-tutorial/WorkdaySSOConfiguratio.png "configuração de SSO")

    a.  Na **ID do fornecedor de serviço** caixa de texto, tipo **https://www.workday.com**.

    b. Selecione **não Deflate o pedido de autenticação iniciado por SP**.

    c. Como **método de assinatura de pedidos de autenticação**, selecione **SHA256**.

    ![Método de assinatura do pedido de autenticação](./media/workday-tutorial/WorkdaySSOConfiguration.png "método de assinatura do pedido de autenticação") 

    d. Clique em **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Certifique-se de que configura o início de sessão único corretamente. No caso de ativar o início de sessão único com a configuração incorreta, não poderá introduzir a aplicação com as suas credenciais e são bloqueados. Nesta situação, o dia de trabalho fornece um url de início de sessão cópia de segurança em que os utilizadores podem iniciar sessão com o respetivo nome de utilizador normal e palavra-passe no seguinte formato: [Your Workday URL]/login.flex?redirect=n

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

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso no Workday.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Workday**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Workday**.

    ![A ligação de dia de trabalho na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-workday-test-user"></a>Criar utilizador de teste do Workday

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Workday. Trabalhar com [equipa de suporte de cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para adicionar os utilizadores na plataforma do Workday. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de dia de trabalho no painel de acesso, deve ser automaticamente sessão iniciada no dia de trabalho para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

