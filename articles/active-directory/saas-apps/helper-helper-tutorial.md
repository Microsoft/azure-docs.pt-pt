---
title: 'Tutorial: Integração do Active Directory do Azure com o programa auxiliar de programa auxiliar | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e do programa auxiliar de programa auxiliar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5f42e4d7-4d92-4096-a0d5-02fa438a5dfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19969588001bd48bd1c998fe49061db80d94c636
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66518523"
---
# <a name="tutorial-integrate-helper-helper-with-azure-active-directory"></a>Tutorial: Integrar o auxiliar de auxiliares com o Azure Active Directory

Neste tutorial, irá aprender como integrar o auxiliar de auxiliar no Azure Active Directory (Azure AD). Quando integrar o auxiliar de auxiliar no Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao programa auxiliar de programa auxiliar.
* Permita que os utilizadores ser automaticamente sessão iniciada para auxiliar auxiliar com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Auxiliar auxiliar início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta o auxiliar de programa auxiliar **SP e IDP** iniciou o SSO e suporta **Just In Time** aprovisionamento de utilizadores.

## <a name="adding-helper-helper-from-the-gallery"></a>Adicionando o auxiliar de programa auxiliar da Galeria

Para configurar a integração do programa auxiliar de programa auxiliar para o Azure AD, terá de adicionar programa auxiliar de auxiliar a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **auxiliares de programa auxiliar** na caixa de pesquisa.
1. Selecione **auxiliar auxiliar** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com através de um utilizador de teste com o nome do programa auxiliar do programa auxiliar **B. Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no programa auxiliar de programa auxiliar.

Para configurar e testar o SSO do Azure AD com o programa auxiliar de programa auxiliar, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar o auxiliar de programa auxiliar](#configure-helper-helper)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Simon B.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Simon B. utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do programa auxiliar de programa auxiliar](#create-helper-helper-test-user)**  para ter um equivalente de Simon B. no programa auxiliar de programa auxiliar que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na **auxiliares de programa auxiliar** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Na **configuração básica de SAML** secção, se tiver **ficheiro de metadados do fornecedor de serviços** e deseja configurar no **IDP** modo iniciado execute os seguintes passos:

    >[!NOTE]
    >Aceda ao url `https://sso.helperhelper.com/saml/<customer_id>` para obter o ficheiro de metadados do fornecedor de serviços. Contacte [equipa de suporte de cliente do programa auxiliar de programa auxiliar](mailto:info@helperhelper.com) para `<customer_id>`.

    a. Clique em **carregamento de ficheiro de metadados**.

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores ficam automaticamente preenchida na seção de configuração básica de SAML.

    > [!Note]
    > Se o **identificador** e **URL de resposta** valores não obter polulated automática, em seguida, preencha os valores manualmente de acordo com seus requisitos.

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://sso.helperhelper.com/saml/<customer_id>/login`

    > [!NOTE]
    > O valor de URL de início de sessão não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente do programa auxiliar de programa auxiliar](mailto:info@helperhelper.com) para obter este valor. Também pode consultar os padrões que mostra a **configuração básica de SAML** secção o portal.l do Azure.

1. No **definir a segurança de início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o em seu bloco de notas .

   ![O link de download de certificado](common/copy-metadataurl.png)

1. Sobre o **configurar o auxiliar de programa auxiliar** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-helper-helper"></a>Configurar o auxiliar de programa auxiliar

Para configurar o início de sessão único em **auxiliar auxiliar** lado, terá de enviar o **Url de metadados de Federação de aplicação** para [equipa de suporte do programa auxiliar de programa auxiliar](mailto:info@helperhelper.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Simon B.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Simon B. utilizar do Azure-início de sessão único, concedendo acesso para auxiliar auxiliar.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **auxiliar auxiliar**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B. Simon** a partir da lista de utilizadores, em seguida, clique no **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-helper-helper-test-user"></a>Criar utilizador de teste do programa auxiliar de programa auxiliar

Nesta secção, um usuário chamado Eduarda Almeida é criado no programa auxiliar de programa auxiliar. Programa auxiliar de programa auxiliar suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no programa auxiliar de programa auxiliar, é criado um novo após a autenticação.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico de programa auxiliar de auxiliar no painel de acesso, deve ser automaticamente conectado para o auxiliar de programa auxiliar para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)