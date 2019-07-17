---
title: 'Tutorial: Integração do Active Directory do Azure com Abstrato | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o resumo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8038784c9da30a42541688536169576cd9a92e9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235012"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Tutorial: Integrar Abstrata com o Azure Active Directory

Neste tutorial, irá aprender como integrar Abstrata com o Azure Active Directory (Azure AD). Quando integrar Abstrata com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Abstrata.
* Permita que os utilizadores ser automaticamente sessão iniciada para Abstrata com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Abstrata início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste.

* Abstrair suporta **SP e IDP** iniciada SSO

## <a name="adding-abstract-from-the-gallery"></a>Adicionando Abstrata da Galeria

Para configurar a integração da Abstração para o Azure AD, terá de adicionar Abstrato a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Abstrato** na caixa de pesquisa.
1. Selecione **Abstrata** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com Abstrata com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Abstrata.

Para configurar e testar o SSO do Azure AD com Abstrato, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO Abstrata](#configure-abstract-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Abstrata](#create-abstract-test-user)**  - para ter um equivalente da Eduarda Almeida na Abstrato que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), no **Abstrato** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção a aplicação está previamente configurada no **IDP** modo iniciado e os URLs necessários já estão previamente preenchidos com o Azure. O utilizador tem de guardar a configuração ao clicar o **guardar** botão.

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://app.abstract.com/signin`

4. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Configurar o SSO Abstrato

Certifique-se obter sua `App Federation Metadata Url` e o `Azure AD Identifier` do portal do Azure, à medida que terá de os configurar o SSO Abstrata.

Encontrará essas informações sobre o **definir a segurança de início de sessão único com o SAML** página:

* O `App Federation Metadata Url` está localizado no **certificado de assinatura SAML** secção.
* O `Azure AD Identifier` está localizado no **configurar Abstrato** secção.


Está agora pronto para configurar o SSO em Abstrato:

>[!Note]
>Terá de autenticar com uma conta de administrador para acessar as configurações de SSO no resumo de organização.

1. Abra o [aplicação web Abstrata](https://app.abstract.com/).
2. Vá para o **permissões** página na barra do lado esquerdo.
3. Na **configurar SSO** , digite seu **URL de metadados** e **ID de entidade**.
4. Introduza quaisquer exceções manuais, que pode ter. Mensagens de e-mail listadas na seção manual exceções irão ignorar o SSO e ser capaz de iniciar sessão com o e-mail e palavra-passe. 
5. Clique em **guardar alterações**.

>[!Note] 
>Terá de utilizar endereços de e-mail principal na lista de exceções manual. Ativação do SSO irá falhar se o e-mail de que listar é o e-mail secundário de um utilizador. Se isto acontecer, verá uma mensagem de erro com o e-mail de principal para a conta de falha. Adicione esse e-mail principal à lista de exceções manual depois de verificar que souber que o usuário.

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

Nesta secção, irá ativar B.Simon utilizar do Azure-início de sessão único, concedendo acesso para Abstrata.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Abstrata**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-abstract-test-user"></a>Criar utilizador de teste Abstrato

Para testar o SSO no Abstrato:

1. Abra o [aplicação web Abstrata](https://app.abstract.com/).
2. Vá para o **permissões** página na barra do lado esquerdo.
3. Clique em **teste com a minha conta**. Se o teste falhar, tente [contacte a nossa equipa de suporte](https://www.abstract.com/help/contact/).

>[!Note]
>Terá de autenticar com uma conta de administrador para acessar as configurações de SSO no resumo de organização.
Esta conta de administrador da organização tem de ser atribuídos a Abstrata no portal do Azure.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico Abstrato no painel de acesso, deve ter automaticamente sessão iniciada no Abstrata para os quais configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

