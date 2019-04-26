---
title: 'Tutorial: Integração do Active Directory do Azure com os recursos de infraestrutura de segurança de Cloud | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e os recursos de infraestrutura de segurança de Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 549e8810-1b3b-4351-bf4b-f07de98980d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a556b38ca4947b71555ba7b023607b392900bdaf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60429605"
---
# <a name="tutorial-azure-active-directory-integration-with-the-cloud-security-fabric"></a>Tutorial: Integração do Active Directory do Azure com os recursos de infraestrutura de segurança de Cloud

Neste tutorial, saiba como integrar os recursos de infraestrutura de segurança de Cloud com o Azure Active Directory (Azure AD).

Integrar os recursos de infraestrutura de segurança de Cloud no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso aos recursos de infraestrutura de segurança a Cloud.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a infraestrutura de segurança de Cloud (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com os recursos de infraestrutura de segurança de Cloud, terá dos seguintes itens:

- Uma subscrição do Azure
- Um a recursos de infraestrutura de segurança de Cloud logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar os recursos de infraestrutura de segurança de Cloud a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Adicionar os recursos de infraestrutura de segurança de Cloud a partir da Galeria
Para configurar a integração de recursos de infraestrutura de segurança a Cloud com o Azure AD, terá de adicionar os recursos de infraestrutura de segurança de Cloud a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar os recursos de infraestrutura de segurança de Cloud a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **a recursos de infraestrutura de segurança de Cloud**, selecione **os recursos de infraestrutura de segurança de Cloud** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Recursos de infraestrutura de segurança da Cloud na lista de resultados](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com a infraestrutura de segurança de Cloud com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte nos recursos de infraestrutura de segurança a Cloud para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado nos recursos de infraestrutura de segurança a nuvem tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com os recursos de infraestrutura de segurança de Cloud, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste os recursos de infraestrutura de segurança de Cloud](#create-a-the-cloud-security-fabric-test-user)**  - para ter um equivalente da Eduarda Almeida no Fabric de segurança de Cloud que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação de acesso a recursos de infraestrutura de segurança de Cloud.

**Para configurar o Azure AD início de sessão único com os recursos de infraestrutura de segurança de Cloud, execute os seguintes passos:**

1. No portal do Azure, sobre o **a recursos de infraestrutura de segurança de Cloud** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_samlbase.png)

1. Sobre o **o domínio de recursos de infraestrutura de segurança na Cloud e URLs** secção, execute os seguintes passos:

    ![Os URLs e do domínio de recursos de infraestrutura de segurança de Cloud única informações de início de sessão](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL:

    | |
    |--|
    | `https://platform.cloudlock.com` |
    | `https://app.cloudlock.com` |

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
    | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |

    > [!NOTE]
    > O valor do identificador não é real. Atualize o valor com o identificador real. Contacte [equipa de suporte do cliente de recursos de infraestrutura de segurança de Cloud](mailto:support@cloudlock.com) para obter o valor. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_certificate.png)

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/ciscocloudlock-tutorial/tutorial_general_400.png)

1. Para configurar o início de sessão único num **a recursos de infraestrutura de segurança de Cloud** lado, terá de enviar o transferido **XML de metadados** para [equipa de suporte a recursos de infraestrutura de segurança de Cloud](mailto:support@cloudlock.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/ciscocloudlock-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/ciscocloudlock-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/ciscocloudlock-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/ciscocloudlock-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.

### <a name="create-a-the-cloud-security-fabric-test-user"></a>Criar um utilizador de teste os recursos de infraestrutura de segurança de Cloud

Nesta secção, vai criar um usuário chamado Eduarda Almeida nos recursos de infraestrutura de segurança a Cloud. Trabalhar com [equipa de suporte a recursos de infraestrutura de segurança de Cloud](mailto:support@cloudlock.com) para adicionar os utilizadores na plataforma de recursos de infraestrutura de segurança a Cloud. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso aos recursos de infraestrutura de segurança a Cloud.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida para os recursos de infraestrutura de segurança de Cloud, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201]

1. Na lista de aplicações, selecione **a recursos de infraestrutura de segurança de Cloud**.

    ![A ligação de recursos de infraestrutura de segurança de Cloud na lista de aplicações](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica em mosaico a recursos de infraestrutura de segurança de Cloud, no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação de acesso a recursos de infraestrutura de segurança de Cloud.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/ciscocloudlock-tutorial/tutorial_general_01.png
[2]: ./media/ciscocloudlock-tutorial/tutorial_general_02.png
[3]: ./media/ciscocloudlock-tutorial/tutorial_general_03.png
[4]: ./media/ciscocloudlock-tutorial/tutorial_general_04.png

[100]: ./media/ciscocloudlock-tutorial/tutorial_general_100.png

[200]: ./media/ciscocloudlock-tutorial/tutorial_general_200.png
[201]: ./media/ciscocloudlock-tutorial/tutorial_general_201.png
[202]: ./media/ciscocloudlock-tutorial/tutorial_general_202.png
[203]: ./media/ciscocloudlock-tutorial/tutorial_general_203.png
