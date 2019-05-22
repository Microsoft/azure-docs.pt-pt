---
title: 'Tutorial: Integração do Active Directory do Azure com a MyWorkDrive | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 61173d21c52d061f0d02ab02eb2f1083507983c2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991652"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Tutorial: Integrar o MyWorkDrive no Azure Active Directory

Neste tutorial, irá aprender como integrar a MyWorkDrive no Azure Active Directory (Azure AD). Quando integrar MyWorkDrive no Azure AD, pode:

* Controlar no Azure AD, quem tem acesso a MyWorkDrive.
* Permita que os utilizadores ser automaticamente sessão iniciada a MyWorkDrive com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* MyWorkDrive início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Oferece suporte a MyWorkDrive **SP** e **IDP** iniciada SSO

## <a name="adding-myworkdrive-from-the-gallery"></a>Adicionando a MyWorkDrive da Galeria

Para configurar a integração da MyWorkDrive no Azure AD, terá de adicionar MyWorkDrive a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **MyWorkDrive** na caixa de pesquisa.
1. Selecione **MyWorkDrive** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com MyWorkDrive com um utilizador de teste **Eduarda Almeida**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no MyWorkDrive.

Para configurar e testar o SSO do Azure AD com MyWorkDrive, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO da MyWorkDrive](#configure-myworkdrive-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste da MyWorkDrive](#create-myworkdrive-test-user)**  - para ter um equivalente da Eduarda Almeida na MyWorkDrive que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **MyWorkDrive** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, se desejar configurar a aplicação no **IDP** iniciada pelo modo, introduza os valores para o seguinte campo:

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:  `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta e o URL de início de sessão real. Entrada name:e.g de anfitrião do servidor da MyWorkDrive da sua própria empresa.
    > 
    > URL de resposta: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > URL de início de sessão:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Contacte [equipa de suporte da MyWorkDrive](mailto:support@myworkdrive.com) se não souber como configurar o seu próprio nome de anfitrião e o certificado SSL para esses valores.

1. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** para sua área de transferência.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Configurar o SSO da MyWorkDrive

1. Numa janela do browser web diferente, inicie sessão no MyWorkDrive como um administrador de segurança.

2. No servidor MyWorkDrive no painel administração, clique em **ENTERPRISE** e execute os seguintes passos:

    ![O administrador](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Ativar **SSO SAML/ADFS**.

    b. Selecione **SAML - Azure AD**

    c. Na **Url de metadados de Federação de aplicação do Azure** caixa de texto, cole o valor de **Url de metadados de Federação de aplicação** que copiou do portal do Azure.

    d. Clique em **Guardar**

    > [!NOTE]
    > Para informações adicionais, veja a [artigo de suporte do Azure AD de MyWorkDrive](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a MyWorkDrive.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **MyWorkDrive**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-myworkdrive-test-user"></a>Criar utilizador de teste da MyWorkDrive

Nesta secção, vai criar um usuário chamado Eduarda Almeida no MyWorkDrive. Trabalhar com [equipa de suporte da MyWorkDrive](mailto:support@myworkdrive.com) para adicionar os utilizadores na plataforma da MyWorkDrive. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico da MyWorkDrive no painel de acesso, deve ser automaticamente sessão iniciada no MyWorkDrive para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)