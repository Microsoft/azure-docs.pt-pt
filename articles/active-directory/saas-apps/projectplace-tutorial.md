---
title: 'Tutorial: Integração do Active Directory do Azure com Projectplace | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o início de sessão único entre o Azure Active Directory e do Projectplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 17fbc6bc4f022a15c34c5ca7b9465be392cdc639
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560635"
---
# <a name="tutorial-azure-active-directory-integration-with-projectplace"></a>Tutorial: Integração do Active Directory do Azure com Projectplace

Neste tutorial, irá aprender como integrar Projectplace com o Azure Active Directory (Azure AD).

Esta integração oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso ao Projectplace.
* Pode permitir que os utilizadores iniciar sessão automaticamente ao Projectplace (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Projectplace, terá de:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode inscrever-se para obter um [versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/) subscrição.
* Uma subscrição do Projectplace que tem início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único num ambiente de teste.

* Projectplace suporta SSO iniciado por SP.

## <a name="add-projectplace-from-the-gallery"></a>Adicionar Projectplace a partir da Galeria

Para configurar a integração do Projectplace para o Azure AD, terá de adicionar Projectplace a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** > **todas as aplicações**:

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Projectplace**. Selecione **Projectplace** nos resultados da pesquisa e, em seguida, selecione **Add**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e testar o Azure AD início de sessão único com Projectplace com um utilizador de teste com o nome Eduarda Almeida.
Para ativar o início de sessão único, tem de estabelecer uma relação entre um utilizador do Azure AD e o utilizador correspondente no Projectplace.

Para configurar e testar o Azure AD início de sessão único com Projectplace, tem de concluir estes passos:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para ativar a funcionalidade para os seus utilizadores.
2. **[Configurar Projectplace início de sessão único](#configure-projectplace-single-sign-on)**  no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar o Azure AD início de sessão único para o utilizador.
5. **[Criar um utilizador de teste do Projectplace](#create-a-projectplace-test-user)**  associado a representação do Azure AD do utilizador.
6. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Projectplace, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Projectplace** página de integração de aplicações, selecione **início de sessão único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único:

    ![Selecione um método de início de sessão único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo:

    ![Ícone editar](common/edit-urls.png)

4. Na **configuração básica de SAML** caixa de diálogo a **URL de início de sessão** , introduza um URL neste padrão:

    `https://<company>.projectplace.com`

   ![Caixa de diálogo de configuração de SAML básica](common/sp-signonurl.png)
    > [!NOTE]
    > Este valor é um marcador de posição. Tem de utilizar o URL de início de sessão real. Contacte os [equipa de suporte do Projectplace](https://success.planview.com/Projectplace/Support) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** caixa de diálogo no portal do Azure.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **transferir** ligação junto a **XML de metadados de Federação** acordo com suas necessidades e guarde o certificado no seu computador:

    ![Ligação de transferência do certificado](common/metadataxml.png)

6. Na **configurar Projectplace** secção, copie os URLs adequados, com base nos seus requisitos.

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de início de sessão**.

    1. **Azure AD Identifier**.

    1. **URL de fim de sessão**.

### <a name="configure-projectplace-single-sign-on"></a>Configurar Projectplace início de sessão único

Para configurar o início de sessão único no **Projectplace** lado, terá de enviar o transferido **XML de metadados de Federação** certificados e os URLs que copiou do portal do Azure para o [ A equipa de suporte do Projectplace](https://success.planview.com/Projectplace/Support). Esta equipe garante que a ligação de SAML SSO está definida corretamente em ambos os lados.

>[!NOTE]
>A configuração de início de sessão única tem de ser efetuadas pela [equipa de suporte do Projectplace](https://success.planview.com/Projectplace/Support). Vai receber uma notificação quando a configuração está concluída.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** no painel esquerdo, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**:

    ![Selecione todos os utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã:

    ![Selecione o novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga os passos seguintes.

    ![Caixa de diálogo de utilizador](common/user-properties.png)

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **BrittaSimon @\<yourcompanydomain >.\< extensão >**. (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **mostrar palavra-passe**e, em seguida, anote o valor que está a **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure AD início de sessão único, concedendo acesso ao Projectplace.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **Projectplace**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Projectplace**.

    ![Lista de aplicações](common/all-applications.png)

3. No painel esquerdo, selecione **utilizadores e grupos**:

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Selecione adicionar utilizador](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se um valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-a-projectplace-test-user"></a>Criar um utilizador de teste do Projectplace

Para permitir que utilizadores do Azure AD iniciar sessão no Projectplace, terá de adicioná-los ao Projectplace. Tem de adicioná-los manualmente.

Para criar uma conta de utilizador, siga estes passos:

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

### <a name="test-single-sign-on"></a>Testar início de sessão único

Agora precisa testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.

Ao selecionar o mosaico do Projectplace no painel de acesso, deve ser automaticamente conectado à instância do Projectplace para o qual configura o SSO. Para obter mais informações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
