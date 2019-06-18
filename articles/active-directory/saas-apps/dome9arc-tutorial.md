---
title: 'Tutorial: Integração do Active Directory do Azure com o arco de Dome9 do CloudGuard de ponto de verificação | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e verifique o ponto de CloudGuard Dome9 arco.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdaaab8257d3a79130902e1ba0466f9cf15484f4
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147171"
---
# <a name="tutorial-integrate-check-point-cloudguard-dome9-arc-with-azure-active-directory"></a>Tutorial: Integrar o arco de Dome9 CloudGuard de ponto de verificação com o Azure Active Directory

Neste tutorial, irá aprender como integrar o arco de Dome9 do CloudGuard de ponto de verificação com o Azure Active Directory (Azure AD). Quando integrar o arco de Dome9 do CloudGuard de ponto de verificação com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao verificar o ponto de CloudGuard Dome9 arco.
* Permita que os utilizadores ser automaticamente sessão iniciada em verificar ponto CloudGuard Dome9 arco com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Verifique ponto CloudGuard Dome9 arco início de sessão único (SSO) ativada subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Verifique o ponto CloudGuard Dome9 arco suporta **SP e IDP** iniciada SSO.

## <a name="adding-check-point-cloudguard-dome9-arc-from-the-gallery"></a>Adicionando o arco de Dome9 do CloudGuard de ponto de verificação da Galeria

Para configurar a integração do arco Dome9 do CloudGuard ponto de verificação para o Azure AD, terá de adicionar arco Dome9 do CloudGuard ponto de verificação a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **verificar o ponto de CloudGuard Dome9 arco** na caixa de pesquisa.
1. Selecione **verificar o ponto de CloudGuard Dome9 arco** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com verificar ponto CloudGuard Dome9 arco com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado em arco Dome9 do CloudGuard ponto de verificação.

Para configurar e testar o SSO do Azure AD com o arco de Dome9 do CloudGuard de ponto de verificação, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar a verificar o ponto de CloudGuard Dome9 arco](#configure-check-point-cloudguard-dome9-arc)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de verificar o ponto CloudGuard Dome9 arco](#create-check-point-cloudguard-dome9-arc-test-user)**  ter um equivalente de B.Simon em verificar ponto CloudGuard Dome9 arco que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **verificar o ponto de CloudGuard Dome9 arco** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único** .
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://secure.dome9.com/`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Irá selecionar o valor de nome de empresa no portal de administração dome9, que é explicado mais tarde no tutorial.

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e URL de resposta real. Contacte [equipa de suporte de verificar o ponto CloudGuard Dome9 arco cliente](mailto:Dome9@checkpoint.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. Aplicação do ponto de verificação CloudGuard Dome9 arco espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir a caixa de diálogo de atributos do utilizador.

    ![image](common/edit-attribute.png)

7. Além dos acima, o aplicativo de verificar o ponto CloudGuard Dome9 arco espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir: 

    | Name |  Atributo de origem|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar a verificar o ponto de CloudGuard Dome9 arco** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-check-point-cloudguard-dome9-arc"></a>Configurar o arco de Dome9 CloudGuard de ponto de verificação

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa em verificar ponto CloudGuard Dome9 arco como administrador.

2. Clique nas **definições de perfil** no canto superior direito e clique em **definições da conta**. 

    ![Verifique a configuração do ponto CloudGuard Dome9 em arco](./media/dome9arc-tutorial/configure1.png)

3. Navegue para **SSO** e, em seguida, clique em **ATIVAR**.

    ![Verifique a configuração do ponto CloudGuard Dome9 em arco](./media/dome9arc-tutorial/configure2.png)

4. Na secção de configuração de SSO, execute os seguintes passos:

    ![Verifique a configuração do ponto CloudGuard Dome9 em arco](./media/dome9arc-tutorial/configure3.png)

    a. Introduza o nome da empresa no **ID da conta** caixa de texto. Este valor é a ser utilizado no URL de resposta mencionado no portal do Azure **configuração básica de SAML** secção.

    b. Na **emissor** caixa de texto, cole o valor de **do Azure AD identificador**, que tenha copiado o formulário do portal do Azure.

    c. Na **url de ponto final de Idp** caixa de texto, cole o valor de **URL de início de sessão**, que tenha copiado o formulário do portal do Azure.

    d. Abra o seu certificado codificado em Base64 transferido no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509** caixa de texto.

    e. Clique em **Guardar**.

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

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único, concedendo acesso para verificar o ponto de CloudGuard Dome9 arco.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **verificar o ponto de CloudGuard Dome9 arco**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-check-point-cloudguard-dome9-arc-test-user"></a>Criar utilizador de teste de verificar o ponto CloudGuard Dome9 arco

Permitir que os utilizadores do Azure AD entrar para verificar o ponto de CloudGuard Dome9 arco, tem de ser aprovisionados no aplicativo. Verifique o ponto de CloudGuard Dome9 arco suporta o aprovisionamento de just-in-time, mas para que isso funcione corretamente, o utilizador tem de selecionar particular **função** e atribuir o mesmo para o usuário.

   >[!Note]
   >Para **função** criação e outros detalhes de contacto [equipa de suporte de verificar o ponto CloudGuard Dome9 arco cliente](mailto:Dome9@checkpoint.com).

**Para Aprovisionar uma conta de utilizador manualmente, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa em verificar ponto CloudGuard Dome9 arco como um administrador.

2. Clique nas **utilizadores e funções** e, em seguida, clique em **utilizadores**.

    ![Adicionar o funcionário](./media/dome9arc-tutorial/user1.png)

3. Clique em **adicionar utilizador**.

    ![Adicionar o funcionário](./media/dome9arc-tutorial/user2.png)

4. Na **criar utilizador** secção, execute os seguintes passos:

    ![Adicionar o funcionário](./media/dome9arc-tutorial/user3.png)

    a. Na **E-Mail** caixa de texto, como o tipo de e-mail do utilizador B.Simon@contoso.com.

    b. Na **nome próprio** caixa de texto, tipo primeiro nome do utilizador, como o B.

    c. Na **Apelido** apelido tipo do utilizador, como Simon, caixa de texto.

    d. Tornar **utilizador SSO** como **no**.

    e. Clique em **criar**.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico de verificar o ponto CloudGuard Dome9 arco no painel de acesso, deve ser automaticamente conectado a verificar ponto CloudGuard Dome9 em arco para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)