---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Plataforma de Patentes IamIP  Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a IamIP Patent Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5b4fc1-e8fd-4418-a369-189272fef80d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d487aaf7ba4aaf666962cf91ca86d46115055b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190743"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>Tutorial: Azure Ative Directory integração de um único sign-on (SSO) com a Plataforma de Patentes IamIP

Neste tutorial, você aprenderá a integrar a Plataforma de Patentes IamIP com o Azure Ative Directory (Azure AD). Quando integrar a Plataforma de Patentes IamIP com a Azure AD, pode:

* Utilize a AD Azure para controlar quem pode aceder à Plataforma de Patentes IamIP.
* Ative que os seus utilizadores sejam automaticamente inscritos na Plataforma de Patentes IamIP com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre a integração de aplicações saaS com a Azure AD, consulte [o single sign-on para aplicações no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição da Plataforma de Patentes IamIP com um único sinal (SSO) ativado.

## <a name="tutorial-description"></a>Descrição tutorial

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste.

A Plataforma de Patentes IamIP suporta sSO iniciado sp-iniciado e idp-iniciado.

Depois de configurar a Plataforma de Patentes IamIP, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .


## <a name="add-iamip-patent-platform-from-the-gallery"></a>Adicione plataforma de patentes IamIP da galeria

Para configurar a integração da Plataforma de Patentes IamIP em Azure AD, você precisa adicionar a Plataforma de Patentes IamIP da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de trabalho ou escola ou com uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite plataforma de **patentes IamIP** na caixa de pesquisa.
1. Selecione A Plataforma de **Patentes IamIP** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>Configure e teste Azure AD SSO para a Plataforma de Patentes IamIP

Você vai configurar e testar Azure AD SSO com a Plataforma de Patentes IamIP usando um utilizador de teste chamado B.Simon. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador correspondente na Plataforma de Patentes IamIP.

Para configurar e testar o Azure AD SSO com a Plataforma de Patentes IamIP, você tomará estes passos de alto nível:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem a funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de AD Azure.
    * **[Conceder acesso ao utilizador do teste](#grant-access-to-the-test-user)** para permitir ao utilizador utilizar um único sinal de Acesso AD Azure.

1. **[Configure a Plataforma de Patentes IamIP SSO](#configure-iamip-patent-platform-sso)** do lado da aplicação.
    * **[Crie um utilizador](#create-iamip-patent-platform-test-user)** de teste da Plataforma de Patentes IamIP como contrapartida da representação da AD Azure do utilizador.

1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configure Azure AD SSO

Siga estes passos para permitir o Azure AD SSO no portal Azure:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da Plataforma de **Patentes IamIP,** na secção **Gerir,** selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. Na **configuração do "Single Sign-On" com** a página SAML, selecione o botão de lápis para a **configuração Básica do SAML** para editar as definições:

   ![Botão de lápis para configuração Básica saml](common/edit-urls.png)

1. Na secção **basic SAML Configuration,** se tiver um ficheiro de metadados do Fornecedor de Serviços e pretender configurar o SSO no modo iniciado pelo IDP, tome estas medidas:

    a. Selecione **ficheiro de metadados de upload:**

    ![Upload ficheiro de metadados](common/upload-metadata.png)

    b. Selecione o botão de pasta, selecione o ficheiro metadados e, em seguida, **selecione Upload:**

    ![Botões de pasta e upload](common/browse-upload-metadata.png)

    c. Após o upload do ficheiro de metadados, os valores DE URL do **Identificador** e **da Resposta** povoam automaticamente na secção de **Configuração SAML Básica:**

    ![Valores de URL de identificação e resposta](common/idp-intiated.png)

    > [!Note]
    > Se os valores de URL do **Identificador** e **resposta** não forem automaticamente povoados, forneça os valores manualmente de acordo com os seus requisitos.

1. Selecione **Definir URLs adicionais** e completar o seguinte passo se pretender configurar a aplicação no modo iniciado por SP:

    Na caixa **de URL Sign-on,** introduza **https:\//patents.iamip.com/login-user**.

1. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** selecione o link **de descarregamento** para **Certificado (Raw)** para descarregar o certificado e guardá-lo no seu computador:

    ![Link de descarregamento de certificado](common/certificateraw.png)

1. Na secção De Configuração da Plataforma de **Patentes IamIP,** copie os URL ou URLs apropriados, com base nos seus requisitos:

    ![URLs de configuração de cópia](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, você vai criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**. Selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** preencha estes passos:
   1. Na caixa **de nomes,** insira **B.Simon.**  
   1. Na caixa de **nome do Utilizador,** introduza \<username>@\<companydomain>.\<extensão>. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar palavra-passe**e, em seguida, anote o valor que está apresentado na caixa **password.**
   1. Selecione **Criar**.

### <a name="grant-access-to-the-test-user"></a>Conceder acesso ao utilizador do teste

Nesta secção, permitirá que a B.Simon utilize um único sign-on do Azure, concedendo a esse utilizador acesso à Plataforma de Patentes IamIP.

1. No portal Azure, selecione **aplicações Enterprise**, e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **IamIP Patent Platform**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos:**

   ![Selecione Utilizadores e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**, e, em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **de atribuição de adicionar:**

    ![Selecione Adicionar utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **De utilizadores e grupos,** selecione **B.Simon** na lista **de Utilizadores** e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione o botão **Atribuir.**

## <a name="configure-iamip-patent-platform-sso"></a>Configure IIP Patent Platform SSO

Para configurar um único sinal no lado da Plataforma de Patentes IamIP, você precisa enviar o certificado bruto descarregado e os URLs apropriados que copiou do portal Azure para a equipe de suporte da Plataforma de [Patentes IamIP](mailto:info@iamip.com). Configuram a ligação SAML SSO para estar correta em ambos os lados.

### <a name="create-iamip-patent-platform-test-user"></a>Criar o utilizador de teste da Plataforma de Patentes IamIP

Trabalhe com a equipa de suporte da Plataforma de [Patentes IamIP](mailto:info@iamip.com) para adicionar um utilizador chamado B.Simon na Plataforma de Patentes IamIP. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

## <a name="test-sso"></a>Teste SSO

Nesta secção, irá testar a configuração Azure AD SSO utilizando o Painel de Acesso.

Ao selecionar o azulejo da Plataforma de Patentes IamIP no Painel de Acesso, deve ser automaticamente inscrito na plataforma de patentes IamIP para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar aplicações saaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Plataforma de Patentes IamIP com a AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
