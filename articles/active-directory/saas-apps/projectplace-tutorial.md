---
title: 'Tutorial: Integração do Active Directory do Azure com Projectplace | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e do Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aae6de49a3df3f1e648b99aa9936d6af85fc020f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497292"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Tutorial: Integrar Projectplace com o Azure Active Directory

Neste tutorial, irá aprender como integrar Projectplace com o Azure Active Directory (Azure AD). Quando integrar Projectplace com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Projectplace.
* Permita que os utilizadores ser automaticamente sessão iniciada ao Projectplace com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.
* Os utilizadores podem ser aprovisionados automaticamente no Projectplace.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Projectplace início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta Projectplace **SP e IDP** iniciou o SSO e suporta **Just In Time** aprovisionamento de utilizadores.

## <a name="adding-projectplace-from-the-gallery"></a>Adicionando Projectplace da Galeria

Para configurar a integração do Projectplace para o Azure AD, terá de adicionar Projectplace a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Projectplace** na caixa de pesquisa.
1. Selecione **Projectplace** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com Projectplace com um utilizador de teste **B. Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Projectplace.

Para configurar e testar o SSO do Azure AD com Projectplace, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar Projectplace](#configure-projectplace)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Simon B.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Simon B. utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Projectplace](#create-projectplace-test-user)**  para ter um equivalente de Simon B. no Projectplace que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Projectplace** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação na **IDP** modo iniciado, a aplicação está pré-configurada e os URLs necessários já estão previamente preenchidos com o Azure . O utilizador tem de guardar a configuração ao clicar o **guardar** botão.

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://service.projectplace.com`

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em Copiar **ícone** para copiar o **Url de metadados de Federação de aplicação** , de acordo com seus requisitos e guarde-o no bloco de notas.

   ![O link de download de certificado](common/copy-metadataurl.png)

1. Sobre o **configurar Projectplace** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Configurar Projectplace

Para configurar o início de sessão único no **Projectplace** lado, terá de enviar o copiado **Url de metadados de Federação de aplicação** do portal do Azure para o [equipa de suporte do Projectplace](https://success.planview.com/Projectplace/Support) . Esta equipe garante que a ligação de SAML SSO está definida corretamente em ambos os lados.

>[!NOTE]
>A configuração de início de sessão única tem de ser efetuadas pela [equipa de suporte do Projectplace](https://success.planview.com/Projectplace/Support). Vai receber uma notificação quando a configuração está concluída. 

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

Nesta secção, irá ativar a Simon B. utilizar o Azure início de sessão único, concedendo acesso ao Projectplace.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Projectplace**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B. Simon** a partir da lista de utilizadores, em seguida, clique no **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-projectplace-test-user"></a>Criar utilizador de teste do Projectplace

>[!NOTE]
>Pode ignorar este passo se tiver ativado no Projectplace de aprovisionamento. Pode pedir a [equipa de suporte do Projectplace](https://success.planview.com/Projectplace/Support) para ativar provisoning, assim que os utilizadores done serão criados na Projectplace durante o primeiro início de sessão.

Para permitir que utilizadores do Azure AD iniciar sessão no Projectplace, terá de adicioná-los ao Projectplace. Tem de adicioná-los manualmente.

**Para criar uma conta de utilizador, siga estes passos:**

1. Inicie sessão no seu **Projectplace** site da empresa como administrador.

2. Aceda a **pessoas**e, em seguida, selecione **membros**:
   
    ![Vá para as pessoas e, em seguida, selecionar membros](./media/projectplace-tutorial/ic790228.png "pessoas")

3. Selecione **Adicionar membro**:
   
    ![Selecione Adicionar membro](./media/projectplace-tutorial/ic790232.png "adicionar membros")

4. Na **Adicionar membro** secção, siga os passos seguintes.
   
    ![Adicionar membro seção](./media/projectplace-tutorial/ic790233.png "novos membros")
   
    1. Na **novos membros** , introduza o endereço de e-mail de um Azure válido conta AD que pretende adicionar.
   
    1. Selecione **Enviar**.

   Uma mensagem de e-mail que contenha uma hiperligação para confirmar a conta até se tornar Active Directory é enviada para o titular da conta do Azure AD.

>[!NOTE]
>Também pode usar qualquer outra ferramenta de criação de conta de utilizador ou API fornecidos pela Projectplace para adicionar contas de utilizador do Azure AD.


### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico do Projectplace no painel de acesso, deve ser automaticamente conectado ao Projectplace para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)