---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Nuvem Atlassian [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c48d6f7d751e6d767921fad21e6213c6b1203b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384029"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Tutorial: Integrar nuvem atlassiana com diretório ativo Azure

Neste tutorial, você vai aprender a integrar a Nuvem Atlassian com o Diretório Ativo Azure (Azure AD). Quando integrar a Nuvem Atlassian com o Azure AD, pode:

* Controlo em Azure AD que tem acesso à Nuvem Atlassiana.
* Permita que os seus utilizadores sejam automaticamente inscritos na Atlassian Cloud com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode ter um mês de experiência gratuita [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* O único sinal de subscrição (SSO) da Atlassian Cloud (SSO) permitiu a subscrição.
* Para permitir a inscrição individual da Linguagem de Marcação de Afirmação de Segurança (SAML) para produtos Atlassian Cloud, é necessário configurar o Acesso Atlassian. Saiba mais sobre [o Acesso Atlassian.]( https://www.atlassian.com/enterprise/cloud/identity-manager)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. 

* Atlassian Cloud suporta **SP e IDP** iniciado SSO
* Atlassian Cloud suporta [fornecimento automático de utilizadores e desprovisionamento](atlassian-cloud-provisioning-tutorial.md)
* Assim que configurar a Cloud Atlassian, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .
## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adicionando nuvem atlassiana da galeria

Para configurar a integração da Atlassian Cloud em Azure AD, você precisa adicionar Atlassian Cloud da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite **Cloud Atlassian** na caixa de pesquisa.
1. **Selecione Atlassian Cloud** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Atlassian Cloud utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Atlassian Cloud.

Para configurar e testar o Azure AD SSO com a Cloud Atlassian, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Atlassian Cloud SSO](#configure-atlassian-cloud-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie o utilizador de teste Atlassian Cloud](#create-atlassian-cloud-test-user)** - para ter uma contrapartida de B.Simon na Nuvem Atlassian a que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **atlassian Cloud,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://auth.atlassian.com/saml/<unique ID>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Clique em **definir URLs adicionais**.

    d. Na caixa de texto **do Estado relé,** digite um URL utilizando o seguinte padrão:`https://<instancename>.atlassian.net`

    > [!NOTE]
    > Os valores anteriores não são reais. Atualize estes valores com o identificador real e responda URL. Você receberá estes valores reais do ecrã de **configuração Atlassian Cloud SAML** que é explicado mais tarde no passo 7 do **Configure Atlassian Cloud SSO** no tutorial.

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<instancename>.atlassian.net`

    > [!NOTE]
    > O valor do Signon on URL não é real. Colhe o valor da instância que usa para assinar no portal de administração Atlassian Cloud.

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. A aplicação Atlassian Cloud espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos saml. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** A aplicação Atlassian Cloud espera que o **identificador** de nomes seja mapeado com **user.mail**, por isso precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção Configurar a **Cloud Atlassian,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso à Nuvem Atlassian.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Atlassian Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-atlassian-cloud-sso"></a>Configure Atlassian Cloud SSO

1. Para automatizar a configuração dentro da Cloud Atlassian, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

1. Depois de adicionar extensão ao navegador, clique em **Configurar atlassian Cloud** irá direcioná-lo para a aplicação Atlassian Cloud. A partir daí, forneça as credenciais de administração para assinar na Nuvem Atlassiana. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração de configuração](common/setup-sso.png)

1. Se quiser configurar a Atlassian Cloud manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Atlassian Cloud como administrador e execute os seguintes passos:

1. Tem de verificar o seu domínio antes de configurar uma única inscrição. Para mais informações, consulte o documento de verificação de [domínio atlassiano.](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)

1. No painel esquerdo, selecione **Segurança** > **SAML single sign-on**. Se ainda não o fez, subscreva o Gestor de Identidade atlassiano.

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

1. Na janela de **configuração Add SAML,** faça o seguinte:

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Na caixa id da **Entidade fornecedora** de Identidade, cola o **Identificador Azure AD** que copiou do portal Azure.

    b. Na caixa **DE URL do fornecedor de identidade SSO,** colhe o URL de **Login** que copiou do portal Azure.

    c. Abra o certificado descarregado do portal Azure num ficheiro .txt, copie o valor (sem as linhas de *Certificado* de Início e *Certificado Final),* e, em seguida, cole-o na caixa de **certificados Public X509.**

    d. Clique na **configuração de guardar**.

1. Para garantir que configura os URLs corretos, atualize as definições de AD Azure fazendo o seguinte:

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. Na janela SAML, copie o ID de **identidade SP** e, em seguida, no portal Azure, sob a **Configuração SAML De**Nuvem Atlassian, cole-o na caixa **de identificador.**

    b. Na janela SAML, copie o URL do Serviço de **Consumo de Afirmação SP** e, em seguida, no portal Azure, sob a **configuração SAML de**Nuvem Atlassian, cole-o na caixa **DEURL resposta.** O URL de inscrição é o URL do inquilino da sua Nuvem Atlassian.

    > [!NOTE]
    > Se for um cliente existente, depois de atualizar os valores url url do Serviço de Consumo **de Identidade SP** e **SP Afirmação** no portal Azure, selecione **Sim, configuração de atualização**. Se for um novo cliente, pode saltar este passo.

### <a name="create-atlassian-cloud-test-user"></a>Criar utilizador de teste Atlassian Cloud

Para permitir que os utilizadores de Anúncios Azure insinuem na Atlassian Cloud, disponibilize as contas do utilizador manualmente na Cloud Atlassian, fazendo o seguinte:

1. No painel **da Administração,** selecione **Utilizadores**.

    ![A ligação atlassian Cloud Users](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Para criar um utilizador na Cloud Atlassian, selecione **Convidar o utilizador.**

    ![Criar um utilizador atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. Na caixa de **endereços de e-mail,** introduza o endereço de e-mail do utilizador e, em seguida, atribua o acesso à aplicação.

    ![Criar um utilizador atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Para enviar um convite por e-mail ao utilizador, selecione **Convidar os utilizadores.** Um convite por e-mail é enviado ao utilizador e, após a aceitação do convite, o utilizador está ativo no sistema.

> [!NOTE]
> Também pode criar utilizadores em massa selecionando o botão Criar a **Granel** na secção **Utilizadores.**

### <a name="test-sso"></a>Teste SSO

Ao selecionar o azulejo Atlassian Cloud no Painel de Acesso, deve ser automaticamente inscrito na Nuvem Atlassian para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Nuvem Atlassian com AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Nuvem Atlassiana com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)