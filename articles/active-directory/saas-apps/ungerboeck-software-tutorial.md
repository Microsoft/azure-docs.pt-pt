---
title: 'Tutorial: Integração de Diretório Sonérório Ativo Azure com o Software Ungerboeck [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ungerboeck Software.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67370307"
---
# <a name="tutorial-integrate-ungerboeck-software-with-azure-active-directory"></a>Tutorial: Integrar software Ungerboeck com Diretório Ativo Azure

Neste tutorial, aprenderá a integrar o Software Ungerboeck com o Azure Ative Directory (Azure AD). Quando integrar o Software Ungerboeck com a Azure AD, pode:

* Controle em Azure AD que tem acesso a Ungerboeck Software.
* Ative que os seus utilizadores sejam automaticamente inscritos no Software Ungerboeck com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* A subscrição ativada por um único sinal de insessão (SSO) da Ungerboeck Software.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. Ungerboeck Software suporta **SP** iniciado SSO.

## <a name="adding-ungerboeck-software-from-the-gallery"></a>Adicionando software Ungerboeck da galeria

Para configurar a integração do Software Ungerboeck no Azure AD, precisa de adicionar software Ungerboeck da galeria à sua lista de aplicações geridas pela SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **O Software Ungerboeck** na caixa de pesquisa.
1. Selecione **O Software Ungerboeck** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Ungerboeck Software utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Software Ungerboeck.

Para configurar e testar o Azure AD SSO com o Ungerboeck Software, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure O Software Ungerboeck SSO](#configure-ungerboeck-software-sso)** para configurar as definições sSO no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste ungerboeck software](#create-ungerboeck-software-test-user)** para ter uma contrapartida de B.Simon no Software Ungerboeck que está ligada à representação do utilizador da AD Azure.
6. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **ungerboeck Software,** encontre a secção **Gerir** e selecione **um único sinal .**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

    1. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<SUBDOMAIN>.ungerboeck.com/prod`

    1. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:
    
       **Para o ambiente de produção:**

       | |
       |-|
       | `https://<SUBDOMAIN>.ungerboeck.com/prod` |
       | `https://<SUBDOMAIN>.ungerboeck.net/prod` |
       | `https://<SUBDOMAIN>.ungerboeck.io/prod` |
       | |

        **Para o ambiente**de ensaio:

       | |
       |-|
       | `https://<SUBDOMAIN>.ungerboeck.com/test` |
       | `https://<SUBDOMAIN>.ungerboeck.net/test` |
       | `https://<SUBDOMAIN>.ungerboeck.io/test` |
       | |

   > [!NOTE]
   > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador que é explicado mais tarde na secção de início único de sinal de **software Da Configuração Ungerboeck** do tutorial.

1. Na secção Certificado de **Assinatura SAML,** clique no botão **Editar** para abrir o diálogo do Certificado de **Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção certificado de **assinatura SAML,** copie a **impressão digital** e guarde-a no seu computador.

    ![Copiar valor de impressão digital](common/copy-thumbprint.png)

1. Na secção **DeConfiguração ungerboeck Software,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-ungerboeck-software-sso"></a>Configure Ungerboeck Software SSO

Para configurar um único sign-on no lado **do Software Ungerboeck,** é necessário enviar o **valor de impressão digital e** URLs copiados apropriados do portal Azure para a equipa de suporte do Software [Ungerboeck](mailto:Rhonda.Jannings@ungerboeck.com). Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso ao Software Ungerboeck.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Ungerboeck Software**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-ungerboeck-software-test-user"></a>Criar o utilizador de teste de software Ungerboeck

Nesta secção, cria-se um utilizador chamado B.Simon no Software Ungerboeck. Trabalhe com a equipa de suporte da [Ungerboeck Software](mailto:Rhonda.Jannings@ungerboeck.com) para adicionar os utilizadores na plataforma Ungerboeck Software. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo ungerboeck software no Painel de Acesso, deve ser automaticamente inscrito no Software Ungerboeck para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)