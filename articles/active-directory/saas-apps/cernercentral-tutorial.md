---
title: 'Tutorial: Integração do Active Directory do Azure com o Cerner Central | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Cerner Central.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d2bc549d-d286-4679-854e-bb67c62b0475
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c097fb045db1afe65a84a2a96dc202c57e8a449e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57975547"
---
# <a name="tutorial-azure-active-directory-integration-with-cerner-central"></a>Tutorial: Integração do Active Directory do Azure com o Cerner Central

Neste tutorial, saiba como integrar Cerner Central com o Azure Active Directory (Azure AD).

Integrar Cerner Central no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Cerner Central
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao Cerner Central (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Cerner Central, terá dos seguintes itens:

- Uma subscrição do Azure
- Um aprovados Cerner Central conta do sistema

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Cerner Central da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-cerner-central-from-the-gallery"></a>Adicionando Cerner Central da Galeria
Para configurar a integração do Cerner Central para o Azure AD, terá de adicionar Cerner Central a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Cerner Central a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]

1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Cerner Central**.

    ![Criar um utilizador de teste do Azure AD](./media/cernercentral-tutorial/tutorial_cernercentral_search.png)

1. No painel de resultados, selecione **Cerner Central**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/cernercentral-tutorial/tutorial_cernercentral_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Cerner Central com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Cerner Central a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Central de Cerner deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Cerner Central, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Cerner Central](#creating-a-cerner-central-test-user)**  - para ter um equivalente da Eduarda Almeida na Central de Cerner que está ligado a representação do Azure AD do utilizador.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Cerner Central.

**Para configurar o Azure AD início de sessão único com Cerner Central, execute os seguintes passos:**

1. No portal do Azure, sobre o **Cerner Central** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Configurar o início de sessão único](./media/cernercentral-tutorial/tutorial_cernercentral_samlbase.png)

1. Sobre o **Cerner de domínio Central e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/cernercentral-tutorial/tutorial_cernercentral_url.png)

    a. Na **identificador** caixa de texto, digite o valor usando os padrões seguintes:

    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/metadata` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/metadata` |
    
    b. Na **URL de resposta** caixa de texto, escreva um URL com os seguintes padrões:
    
    | |
    |--|
    | `https://<instancename>.cernercentral.com/session-api/protocol/saml2/sso` |
    | `https://<instancename>.sandboxcernercentral.com/session-api/protocol/saml2/sso` |

    > [!NOTE]
    > Estes valores não são o real. Atualize estes valores com o identificador real e o URL de resposta. Contacte [equipa de suporte de Central de Cerner](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations) obter esses valores.

1. Sobre o **certificado de assinatura SAML** secção, clique no botão de cópia para copiar **Url de metadados de Federação de aplicação** e cole-o no bloco de notas.

    ![Configurar o início de sessão único](./media/cernercentral-tutorial/tutorial_metadataurl.png)

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/cernercentral-tutorial/tutorial_general_400.png)

1. Para configurar o início de sessão único num **Cerner Central** lado, terá de enviar o **Url de metadados de Federação de aplicação** para [suporte Cerner Central](https://wiki.ucern.com/display/CernerCentral/Contacting+Cloud+Operations). Configurar o SSO no lado de aplicação para concluir a integração.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/cernercentral-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.

    ![Criar um utilizador de teste do Azure AD](./media/cernercentral-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add**.

    ![Criar um utilizador de teste do Azure AD](./media/cernercentral-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:

    ![Criar um utilizador de teste do Azure AD](./media/cernercentral-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** da Eduarda Almeida.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.

### <a name="creating-a-cerner-central-test-user"></a>Criar um utilizador de teste Cerner Central

**Central de Cerner** aplicação permite a autenticação de qualquer fornecedor de identidade federada. Se um usuário é capaz de iniciar sessão na home page do aplicativo, eles estão federados e não há necessidade de aprovisionamento manual. Pode encontrar mais detalhes [aqui](cernercentral-provisioning-tutorial.md) sobre como configurar o aprovisionamento automático de utilizadores.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao Cerner Central.

![Atribuir utilizador][200]

**Para atribuir a Eduarda Almeida a Central de Cerner, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201]

1. Na lista de aplicações, selecione **Cerner Central**.

    ![Configurar o início de sessão único](./media/cernercentral-tutorial/tutorial_cernercentral_app.png)

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Cerner Central no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação Cerner Central. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o aprovisionamento do utilizador](cernercentral-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/cernercentral-tutorial/tutorial_general_01.png
[2]: ./media/cernercentral-tutorial/tutorial_general_02.png
[3]: ./media/cernercentral-tutorial/tutorial_general_03.png
[4]: ./media/cernercentral-tutorial/tutorial_general_04.png

[100]: ./media/cernercentral-tutorial/tutorial_general_100.png

[200]: ./media/cernercentral-tutorial/tutorial_general_200.png
[201]: ./media/cernercentral-tutorial/tutorial_general_201.png
[202]: ./media/cernercentral-tutorial/tutorial_general_202.png
[203]: ./media/cernercentral-tutorial/tutorial_general_203.png
