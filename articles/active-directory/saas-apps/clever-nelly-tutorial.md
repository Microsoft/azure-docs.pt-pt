---
title: 'Tutorial: Integração do Active Directory do Azure com Nelly inteligente | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Nelly inteligente.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af3c925e-92ec-431d-80cf-2e0861cd4ba5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac9647bf6a9e2e763bcfb6e3d840cdccd2b6c305
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595014"
---
# <a name="tutorial-integrate-clever-nelly-with-azure-active-directory"></a>Tutorial: Integrar Nelly inteligente com o Azure Active Directory

Neste tutorial, irá aprender como integrar Nelly inteligente com o Azure Active Directory (Azure AD). Quando integrar Nelly inteligente com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Nelly inteligente.
* Permita que os utilizadores ser automaticamente sessão iniciada para Nelly inteligentes com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Inteligente Nelly início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Oferece suporte a Nelly inteligente **SP e IDP** iniciada SSO.

## <a name="adding-clever-nelly-from-the-gallery"></a>Adicionando Nelly inteligente da Galeria

Para configurar a integração do Nelly inteligente para o Azure AD, terá de adicionar Nelly inteligente a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Nelly inteligente** na caixa de pesquisa.
1. Selecione **Nelly inteligente** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com Nelly inteligente com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Nelly inteligente.

Para configurar e testar o SSO do Azure AD com Nelly inteligente, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO de Nelly inteligente](#configure-clever-nelly-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Nelly inteligente](#create-clever-nelly-test-user)**  - para ter um equivalente da Eduarda Almeida na Nelly inteligente que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Nelly inteligente** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, introduza os valores para os seguintes campos:

    a. Na **identificador** caixa de texto, escreva um URL:

    | Ambiente | Padrão de URL |
    | - | - |
    | Teste | `https://test.elephantsdontforget.com/plato`|
    | Produção | `https://secure.elephantsdontforget.com/plato` |
    | | |

    b. Na **URL de resposta** caixa de texto, escreva um URL:

    | Ambiente | Padrão de URL |
    | - | - |
    | Teste | `https://test.elephantsdontforget.com/plato/callback?client_name=SAML2Client`|
    | Produção | `https://secure.elephantsdontforget.com/plato/callback?client_name=SAML2Client` |
    | | |

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL:

    | Ambiente | Padrão de URL |
    | - | - |
    | Teste | `https://test.elephantsdontforget.com/plato/sso/microsoft/index.xhtml`|
    | Produção | `https://secure.elephantsdontforget.com/plato/sso/microsoft/index.xhtml` |
    | | |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de cliente inteligente do Nelly](mailto:support@elephantsdontforget.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-clever-nelly-sso"></a>Configurar o SSO de Nelly inteligente

Para configurar o início de sessão único num **Nelly inteligente** lado, terá de enviar o **Url de metadados de Federação de aplicação** para [equipa de suporte de Nelly inteligente](mailto:support@elephantsdontforget.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso a Nelly inteligente.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Nelly inteligente**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-clever-nelly-test-user"></a>Criar utilizador de teste Nelly inteligente

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Nelly inteligente. Trabalhar com [equipa de suporte de Nelly inteligente](mailto:support@elephantsdontforget.com) para adicionar os utilizadores na plataforma Nelly inteligente. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Teste SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Nelly inteligente no painel de acesso, deve ser automaticamente sessão iniciada na Nelly inteligente para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)