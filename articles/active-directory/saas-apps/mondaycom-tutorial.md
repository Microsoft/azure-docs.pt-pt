---
title: 'Tutorial: Integração do Active Directory do Azure com monday.com | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93c5e473c62dc6b38f0b2c2906560d6099842d49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718490"
---
# <a name="tutorial-integrate-mondaycom-with-azure-active-directory"></a>Tutorial: Integrar monday.com com o Azure Active Directory

Neste tutorial, irá aprender como integrar monday.com com o Azure Active Directory (Azure AD). Quando integrar monday.com com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao monday.com.
* Permita que os utilizadores ser automaticamente sessão iniciada para monday.com com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Monday.com início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. suporta Monday.com **SP e IDP** iniciou o SSO e suporta **Just In Time** aprovisionamento de utilizadores.

## <a name="adding-mondaycom-from-the-gallery"></a>Adicionando monday.com da Galeria

Para configurar a integração do monday.com com o Azure AD, terá de adicionar monday.com a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **monday.com** na caixa de pesquisa.
1. Selecione **monday.com** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com monday.com com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no monday.com.

Para configurar e testar o SSO do Azure AD com monday.com, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar monday.com](#configure-mondaycom)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste monday.com](#create-mondaycom-test-user)**  ter um equivalente de B.Simon no monday.com que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **monday.com** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Na **configuração básica de SAML** painel, se tiver um ficheiro de metadados do fornecedor de serviço e que pretende configurar na **iniciada pelo IDP** modo, execute os seguintes passos:

    1. Selecione **carregamento de ficheiro de metadados**.

    1. Para selecionar o ficheiro de metadados, selecione o ícone de pasta e, em seguida, selecione **carregar**.

    1. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores são preenchidos automaticamente na **configuração básica de SAML** painel:

       > [!Note]
       > Se o **identificador** e **URL de resposta** valores não são preenchidos automaticamente, em seguida, preencha os valores manualmente. O **identificador** e o **URL de resposta** são iguais e o valor é o seguinte padrão: `https://<your-domain>.monday.com/saml/saml_callback`

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<your-domain>.monday.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, o URL de resposta e URL de início de sessão. Contacte [equipa de suporte de cliente monday.com](mailto:dev@food.ee) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. Seu aplicativo monday.com espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir a caixa de diálogo de atributos do utilizador.

    ![O painel de atributos de utilizador](common/edit-attribute.png)

1. Além dos acima, o aplicativo monday.com espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Remover os **espaço de nomes**.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **OK**.

    g. Clique em **Guardar**.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar monday.com** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-mondaycom"></a>Configurar monday.com

1. Para automatizar a configuração no monday.com, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão para o navegador, clique em **configurar monday.com** que irá direcioná-lo para o aplicativo monday.com. A partir daí, forneça as credenciais de administrador a iniciar sessão em monday.com. A extensão do browser irá configurar o aplicativo para e automatizar passos 3 a 6 automaticamente.

    ![Configuração do programa de configuração](common/setup-sso.png)

1. Se desejar configurar manualmente o monday.com, abra uma nova janela de browser e inicie sessão no monday.com como administrador e execute os seguintes passos:

1. Vá para o **perfil** no canto superior direito da página e clique em **administrador**.

    ![configuração de Monday.com](./media/mondaycom-tutorial/configuration01.png)

1. Selecione **Security** e certifique-se de clicar em **aberto** ao lado de SAML.

    ![configuração de Monday.com](./media/mondaycom-tutorial/configuration02.png)

1. Preencha os detalhes abaixo do IDP.

    ![configuração de Monday.com](./media/mondaycom-tutorial/configuration03.png)

    >[!NOTE]
    >Para obter mais detalhes, consulte [isso](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) artigo

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado B.Simon.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso a monday.com.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **monday.com**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-mondaycom-test-user"></a>Criar utilizador de teste monday.com

Nesta secção, um usuário chamado B.Simon é criado na monday.com. Monday.com suporta o aprovisionamento de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no monday.com, uma nova é criada quando tentar acessar monday.com.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico monday.com no painel de acesso, deve ser automaticamente sessão iniciada no monday.com para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
