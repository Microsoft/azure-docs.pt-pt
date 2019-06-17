---
title: 'Tutorial: Integração do Active Directory do Azure com o planejamento de gama Predictix | Documentos da Microsoft'
description: Neste tutorial, irá aprender como configurar o início de sessão único entre o Azure Active Directory e o planejamento de gama Predictix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 37e686ff-f8e5-40b1-9d7e-f64b076917b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: bc3ea2f6fddc233a69d96c0c885ab310ed1e77c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094158"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Tutorial: Integração do Active Directory do Azure com o planejamento de gama Predictix

Neste tutorial, irá aprender como integrar o planejamento de gama Predictix com o Azure Active Directory (Azure AD).
Esta integração oferece estes benefícios:

* Pode utilizar o Azure AD para controlar quem tem acesso a variedade de Predictix planejamento.
* Pode permitir que os utilizadores ter automaticamente sessão iniciada no planejamento de gama Predictix (início de sessão único) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central: portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [início de sessão único para aplicações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Se não tiver uma subscrição do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o planejamento de gama Predictix, tem de ter:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Uma subscrição de planejamento de gama Predictix que tem início de sessão único ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único num ambiente de teste.

* Planeamento da variedade de Predictix suporta SSO iniciado por SP.

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>Adicionar o planejamento de gama Predictix a partir da Galeria

Para configurar a integração do planeamento de gama Predictix com o Azure AD, terá de adicionar Predictix variedade de planeamento da Galeria à sua lista de aplicações de SaaS geridas.

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**:

    ![Selecione Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais** > **todas as aplicações**:

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma aplicação, selecione **nova aplicação** na parte superior da janela:

    ![Selecionar novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **planejamento de gama Predictix**. Selecione **planejamento de gama Predictix** nos resultados da pesquisa e, em seguida, selecione **Add**.

     ![Resultados da pesquisa](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e testar o Azure AD início de sessão único com o planejamento de gama Predictix com um utilizador de teste com o nome Eduarda Almeida.
Para ativar o início de sessão único, tem de estabelecer uma relação entre um utilizador do Azure AD e o utilizador correspondente no planeamento da variedade de Predictix.

Para configurar e testar o Azure AD início de sessão único com o planejamento de gama Predictix, tem de concluir estes passos:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  para ativar a funcionalidade para os seus utilizadores.
2. **[Configurar o planejamento de gama Predictix início de sessão único](#configure-predictix-assortment-planning-single-sign-on)**  no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar o Azure AD início de sessão único para o utilizador.
5. **[Criar um utilizador de teste de planejamento de gama Predictix](#create-a-predictix-assortment-planning-test-user)**  associado a representação do Azure AD do utilizador.
6. **[Testar início de sessão único](#test-single-sign-on)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, irá ativar o Azure AD início de sessão único no portal do Azure.

Para configurar do Azure AD início de sessão único com o planejamento de gama Predictix, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **planejamento de gama Predictix** página de integração de aplicações, selecione **início de sessão único**:

    ![Selecione início de sessão único](common/select-sso.png)

2. Na **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único:

    ![Selecionar um método de início de sessão único](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, selecione a **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo:

    ![Ícone editar](common/edit-urls.png)

4. Na **configuração básica de SAML** diálogo caixa, conclua os passos seguintes.

    ![Caixa de diálogo de configuração de SAML básica](common/sp-identifier.png)

    1. Na **iniciar sessão no URL** , introduza um URL neste padrão:

       | |
        |--|
        | `https://<sub-domain>.ap.predictix.com/sso/request`|
        | `https://<sub-domain>.dev.ap.predictix.com/`|
        | |

    1. Na **identificador (ID de entidade)** , introduza um URL neste padrão:

        | |
        |--|
        | `https://<sub-domain>.ap.predictix.com`|
        | `https://<sub-domain>.dev.ap.predictix.com`|
        | |

    > [!NOTE]
    > Estes valores são marcadores de posição. Tem de utilizar o URL de início de sessão real e o identificador. Contacte os [equipa de suporte de planeamento de gama Predictix](https://www.infor.com/support) para obter os valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** caixa de diálogo no portal do Azure.

5. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, selecione o **transferir** ligação junto a **certificado (Base64)** acordo com suas necessidades e guarde o certificado no seu computador:

    ![Ligação de transferência do certificado](common/certificatebase64.png)

6. Na **configurar o planejamento de gama Predictix** secção, copie os URLs adequados, com base nos seus requisitos:

    ![Copie os URLs de configuração](common/copy-configuration-urls.png)

    1. **URL de início de sessão**.

    1. **Azure AD Identifier**.

    1. **URL de fim de sessão**.

### <a name="configure-predictix-assortment-planning-single-sign-on"></a>Configurar o planejamento de gama Predictix início de sessão único

Para configurar o início de sessão único no lado do planeamento do Predictix variedade, terá de enviar o certificado que transferiu e os URLs que copiou do portal do Azure para o [equipa de suporte de planeamento de gama Predictix](https://www.infor.com/support). Esta equipe garante que a ligação de SAML SSO está definida corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, selecione **do Azure Active Directory** no painel esquerdo, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**:

    ![Selecione todos os utilizadores](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã:

    ![Selecione o novo utilizador](common/new-user.png)

3. Na **utilizador** diálogo caixa, siga os passos seguintes.

    ![Caixa de diálogo de utilizador](common/user-properties.png)

    1. Na **Name** , introduza **BrittaSimon**.
  
    1. Na **nome de utilizador** , introduza **BrittaSimon @\<yourcompanydomain >.\< extensão >** . (Por exemplo, BrittaSimon@contoso.com.)

    1. Selecione **palavra-passe de Show**e, em seguida, anote o valor no **palavra-passe** caixa.

    1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure AD início de sessão único, concedendo acesso para planejamento de gama Predictix.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **planejamento de gama Predictix**.

    ![Aplicações Empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **planejamento de gama Predictix**.

    ![Lista de aplicações](common/all-applications.png)

3. No painel esquerdo, selecione **utilizadores e grupos**:

    ![Selecionar utilizadores e grupos](common/users-groups-blade.png)

4. Selecione **adicionar utilizador**e, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![Selecione adicionar utilizador](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores e, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se um valor de função na asserção de SAML, que espera do **selecionar função** diálogo caixa, selecione a função adequada para o utilizador a partir da lista. Clique nas **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, selecione **atribuir**.

### <a name="create-a-predictix-assortment-planning-test-user"></a>Criar um utilizador de teste Predictix variedade de planeamento

Em seguida, terá de criar um utilizador com o nome Eduarda Almeida no planeamento da variedade de Predictix. Trabalhar com o [equipa de suporte de planeamento de gama Predictix](https://www.infor.com/support) para adicionar utilizadores. Os utilizadores têm de ser criado e ativado antes de utilizar o início de sessão único.

> [!NOTE]
> O titular da conta do Azure AD recebe uma mensagem de e-mail e seleciona uma ligação para confirmar a conta até se tornar Active Directory.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Agora precisa testar a configuração do Azure AD única início de sessão utilizando o painel de acesso.

Ao selecionar o mosaico de planejamento de gama Predictix no painel de acesso, deve ser automaticamente conectado para a instância de planejamento do Predictix variedade de mensagens em fila para o qual configura o SSO. Para obter mais informações, consulte [aplicações de acesso e a utilização no portal minhas aplicações](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)