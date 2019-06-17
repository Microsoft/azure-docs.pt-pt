---
title: 'Tutorial: Integração do Active Directory do Azure com o Kit de ferramentas do Azure AD SAML | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Kit de ferramentas do Azure AD SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4baeda4fca69969fae3f866e642ad3dc8ad46509
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091924"
---
# <a name="tutorial-integrate-azure-ad-saml-toolkit-with-azure-active-directory"></a>Tutorial: Integrar o Kit de ferramentas do Azure AD SAML com o Azure Active Directory

Neste tutorial, irá aprender como integrar o Kit de ferramentas do Azure AD SAML com o Azure Active Directory (Azure AD). Quando integrar o Kit de ferramentas do Azure AD SAML com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Kit de ferramentas do Azure AD SAML.
* Permita que os utilizadores ser automaticamente sessão iniciada ao Kit de ferramentas do Azure AD SAML com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* O Azure AD SAML Toolkit início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Kit de ferramentas de SAML do Azure AD suporta **SP** iniciada SSO.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Adicionando o Kit de ferramentas do Azure AD SAML da Galeria

Para configurar a integração do Kit de ferramentas do Azure AD SAML para o Azure AD, terá de adicionar o Toolkit de SAML do Azure AD a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Kit de ferramentas do Azure AD SAML** na caixa de pesquisa.
1. Selecione **Kit de ferramentas do Azure AD SAML** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com o Kit de ferramentas do Azure AD SAML com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Kit de ferramentas do Azure AD SAML.

Para configurar e testar o SSO do Azure AD com o Kit de ferramentas do Azure AD SAML, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar o Kit de ferramentas do Azure AD SAML SSO](#configure-azure-ad-saml-toolkit-sso)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Kit de ferramentas do Azure AD SAML](#create-azure-ad-saml-toolkit-test-user)**  para ter um equivalente de B.Simon no Kit de ferramentas de SAML do Azure AD que está ligada à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Kit de ferramentas do Azure AD SAML** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    1. Na **iniciar sessão no URL** caixa de texto, escreva um URL: `https://samltoolkit.azurewebsites.net/`

    1. Na **identificador (ID de entidade)** caixa de texto, escreva um URL: `https://samltoolkit.azurewebsites.net`

    1. Na **URL de resposta** caixa de texto, escreva um URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Sobre o **configurar o Kit de ferramentas do Azure AD SAML** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-azure-ad-saml-toolkit-sso"></a>Configurar o SSO de Kit de ferramentas SAML do Azure AD

1. Abra uma nova janela de browser, se não se registou no Web site do Kit de ferramentas do Azure AD SAML, primeiro Registre-se ao clicar no **registar**. Se se registrou já, inicie sessão no Kit de ferramentas do Azure AD SAML site da sua empresa utilizar o início de sessão registado nas credenciais.

    ![Registre-se de Kit de ferramentas do Azure AD SAML](./media/saml-toolkit-tutorial/register.png)

1. Clique nas **configuração SAML do**.

    ![Configuração de SAML do Kit de ferramentas do Azure AD SAML](./media/saml-toolkit-tutorial/saml-configure.png)

1. Clique em **Criar**.

    ![Kit de ferramentas do Azure AD SAML criar SSO](./media/saml-toolkit-tutorial/createsso.png)

1. Sobre o **SAML SSO configuração** página, execute os seguintes passos:

    ![Kit de ferramentas do Azure AD SAML criar SSO](./media/saml-toolkit-tutorial/fill-details.png)

    1. Na **URL de início de sessão** caixa de texto, colar a **URL de início de sessão** valor, que copiou do portal do Azure.

    1. Na **do Azure AD identificador** caixa de texto, colar a **do Azure AD identificador** valor, que copiou do portal do Azure.

    1. Na **URL de fim de sessão** caixa de texto, colar a **URL de fim de sessão** valor, que copiou do portal do Azure.

    1. Clique em **Escolher ficheiro** e carregar o **XML de metadados de Federação** ficheiro que transferiu do portal do Azure.

    1. Clique em **Criar**.

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

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único, concedendo acesso ao Kit de ferramentas do Azure AD SAML.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Kit de ferramentas do Azure AD SAML**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Criar utilizador de teste do Kit de ferramentas do Azure AD SAML

Nesta secção, um usuário chamado B.Simon é criado no Kit de ferramentas do Azure AD SAML. Kit de ferramentas de SAML do AD do Azure suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Kit de ferramentas do Azure AD SAML, é criado um novo após a autenticação.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico do Kit de ferramentas do Azure AD SAML no painel de acesso, deve ser automaticamente conectado ao Kit de ferramentas do Azure AD SAML para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
