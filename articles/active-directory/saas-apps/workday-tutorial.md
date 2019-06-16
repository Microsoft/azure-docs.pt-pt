---
title: 'Tutorial: Integração do Active Directory do Azure com o Workday | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aba2d3bab7d709b4bb9ac18e4a9c6ed052a5fb83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086972"
---
# <a name="tutorial-integrate-workday-with-azure-active-directory"></a>Tutorial: Integrar o dia de trabalho com o Azure Active Directory

Neste tutorial, irá aprender como integrar o dia de trabalho com o Azure Active Directory (Azure AD). Quando integrar o dia de trabalho com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Workday.
* Permita que os utilizadores ser automaticamente sessão iniciada no Workday com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Workday início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta o workday **SP** iniciada SSO.

## <a name="adding-workday-from-the-gallery"></a>Adicionando o Workday da Galeria

Para configurar a integração do Workday para o Azure AD, terá de adicionar dia de trabalho a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Workday** na caixa de pesquisa.
1. Selecione **Workday** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com o Workday através de um utilizador de teste **Eduarda Almeida**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Workday.

Para configurar e testar o SSO do Azure AD com o Workday, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar o Workday](#configure-workday)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Workday](#create-workday-test-user)**  para ter um equivalente da Eduarda Almeida no dia de trabalho que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Workday** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://www.workday.com`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Estes valores não são o real. Atualize estes valores com o URL de início de sessão real e o URL de resposta. O URL de resposta tem de ter um subdomínio por exemplo: www, wd2, wd3, wd3 impl, wd5, wd5 impl).
    > Através de algo como `http://www.myworkday.com` funciona, mas `http://myworkday.com` não existir. Contacte [equipa de suporte de cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. A aplicação de dia de trabalho espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Aplicação workday espera **nameidentifier** seja mapeado com **user.mail**, **UPN**, etc., por isso terá de editar o mapeamento do atributo clicando no  **Editar** ícone e altere o mapeamento do atributo.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Aqui estamos tiver mapeado a ID de nome com o UPN (user.userprincipalname) como predefinição. É necessário mapear o ID de nome com o ID de utilizador real na sua conta do Workday (seu e-mail, UPN, etc.) para trabalhar com êxito do SSO.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Para modificar a **assinatura** opções de acordo com seus requisitos, clique em **editar** botão para abrir **certificado de assinatura SAML** caixa de diálogo.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Selecione **asserção e resposta SAML de início de sessão** para **opção assinatura**.

    b. Clique em **Guardar**

1. Sobre o **configurar o Workday** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-workday"></a>Configurar o Workday

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Workday como um administrador.

2. Na **caixa de pesquisa** pesquisa com o nome **Editar configuração de inquilino – segurança** na parte superior esquerda do lado da home page.

    ![Editar a segurança de inquilino](./media/workday-tutorial/IC782925.png "Editar segurança do inquilino")

3. Na **URLs de redirecionamento** secção, execute os seguintes passos:

    ![URLs de redirecionamento](./media/workday-tutorial/IC7829581.png "URLs de redirecionamento")

    a. Clique em **Adicionar linha**.

    b. Na **URL de redireccionamento de início de sessão**, **URL de redireccionamento de tempo limite** e **URL de redirecionamento do Mobile** caixa de texto, colar a **URL de início de sessão** que copiou do **configurar o Workday** seção do portal do Azure.

    c. Na **URL de redirecionamento de fim de sessão** caixa de texto, colar a **URL de fim de sessão** que copiou do **configurar Workday** seção do portal do Azure.

    d. Na **utilizado para ambientes** caixa de texto, selecione o nome do ambiente.  

   > [!NOTE]
   > O valor do atributo de ambiente está associado ao valor do URL de inquilino:  
   > -Se o nome de domínio do URL de inquilino do Workday começa com impl por exemplo: *https:\//impl.workday.com/\<inquilino\>/login-saml2.flex*), o **ambiente**atributo tem de ser definido para a implementação.  
   > – Se o nome de domínio é iniciado com algo mais, terá de contactar [equipa de suporte de cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter a correspondência **ambiente** valor.

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

    a.  Na **ID do fornecedor de serviço** caixa de texto, tipo **https://www.workday.com** .

    b. Selecione **não Deflate o pedido de autenticação iniciado por SP**.

    c. Como **método de assinatura de pedidos de autenticação**, selecione **SHA256**.

    ![Método de assinatura do pedido de autenticação](./media/workday-tutorial/WorkdaySSOConfiguration.png "método de assinatura do pedido de autenticação") 

    d. Clique em **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Certifique-se de que configura o início de sessão único corretamente. No caso de ativar o início de sessão único com a configuração incorreta, não poderá introduzir a aplicação com as suas credenciais e são bloqueados. Nesta situação, o dia de trabalho fornece um url de início de sessão cópia de segurança em que os utilizadores podem iniciar sessão com o respetivo nome de utilizador normal e palavra-passe no seguinte formato: [Your Workday URL]/login.flex?redirect=n

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso no Workday.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Workday**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-workday-test-user"></a>Criar utilizador de teste do Workday

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Workday. Trabalhar com [equipa de suporte de cliente do Workday](https://www.workday.com/en-us/partners-services/services/support.html) para adicionar os utilizadores na plataforma do Workday. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico do dia de trabalho no painel de acesso, deve ser automaticamente sessão iniciada no dia de trabalho para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
