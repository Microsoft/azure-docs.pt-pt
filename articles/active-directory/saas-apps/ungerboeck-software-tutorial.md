---
title: 'Tutorial: Integração do Active Directory do Azure com o Software de Ungerboeck | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Software de Ungerboeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bd861069-8a15-4d29-aa26-0d7be3a0a94a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69c7a7a8fe0ef346cb26e3b0b4f14d3dae7211b9
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2019
ms.locfileid: "67370307"
---
# <a name="tutorial-integrate-ungerboeck-software-with-azure-active-directory"></a>Tutorial: Integrar o Software de Ungerboeck com o Azure Active Directory

Neste tutorial, irá aprender como integrar o Software de Ungerboeck com o Azure Active Directory (Azure AD). Quando integrar o Software de Ungerboeck com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Ungerboeck Software.
* Permita que os utilizadores ser automaticamente sessão iniciada para Ungerboeck Software com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Software de Ungerboeck início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Oferece suporte a Ungerboeck Software **SP** iniciada SSO.

## <a name="adding-ungerboeck-software-from-the-gallery"></a>Adicionar Ungerboeck Software a partir da Galeria

Para configurar a integração de Ungerboeck Software com o Azure AD, terá de adicionar Ungerboeck Software a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Ungerboeck Software** na caixa de pesquisa.
1. Selecione **Ungerboeck Software** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com o Software de Ungerboeck com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Ungerboeck Software.

Para configurar e testar o SSO do Azure AD com o Software de Ungerboeck, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO de Software Ungerboeck](#configure-ungerboeck-software-sso)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de Ungerboeck Software](#create-ungerboeck-software-test-user)**  para ter um equivalente de B.Simon no Ungerboeck Software que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), no **Ungerboeck Software** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    1. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.ungerboeck.com/prod`

    1. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão:
    
       **Para o ambiente de produção**:

       | |
       |-|
       | `https://<SUBDOMAIN>.ungerboeck.com/prod` |
       | `https://<SUBDOMAIN>.ungerboeck.net/prod` |
       | `https://<SUBDOMAIN>.ungerboeck.io/prod` |
       | |

        **Para o ambiente de teste**:

       | |
       |-|
       | `https://<SUBDOMAIN>.ungerboeck.com/test` |
       | `https://<SUBDOMAIN>.ungerboeck.net/test` |
       | `https://<SUBDOMAIN>.ungerboeck.io/test` |
       | |

   > [!NOTE]
   > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador que é explicado posteriormente no **configurar Ungerboeck Software Single Sign-On** secção do tutorial.

1. Na **certificado de assinatura SAML** secção, clique em **editar** botão para abrir **certificado de assinatura SAML** caixa de diálogo.

    ![Editar o certificado de assinatura de SAML](common/edit-certificate.png)

1. Na **certificado de assinatura SAML** secção, copie a **Thumbprint** e guarde-o no seu computador.

    ![Copie o valor do Thumbprint](common/copy-thumbprint.png)

1. Sobre o **configurar Ungerboeck Software** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-ungerboeck-software-sso"></a>Configurar o Software de Ungerboeck SSO

Para configurar o início de sessão único num **Ungerboeck Software** lado, terá de enviar o **valor do Thumbprint** e adequadas copiados URLs a partir do portal do Azure para [equipa de suporte do Software de Ungerboeck](mailto:Rhonda.Jannings@ungerboeck.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso ao Ungerboeck Software.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Ungerboeck Software**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-ungerboeck-software-test-user"></a>Criar utilizador de teste de Ungerboeck Software

Nesta secção, vai criar um usuário chamado B.Simon no Ungerboeck Software. Trabalhar com [equipa de suporte do Software de Ungerboeck](mailto:Rhonda.Jannings@ungerboeck.com) para adicionar os utilizadores na plataforma Ungerboeck Software. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico de Ungerboeck Software no painel de acesso, deve ser automaticamente conectado para o Software de Ungerboeck para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)