---
title: 'Tutorial: Integração do Active Directory do Azure com a nuvem da Atlassian | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e na Cloud da Atlassian.
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
ms.devlang: na
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbfb07b73d591bbab64f38e8c986fb1b7e072a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106592"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Tutorial: Integrar Atlassian Cloud com o Azure Active Directory

Neste tutorial, irá aprender como integrar Atlassian Cloud no Azure Active Directory (Azure AD). Quando integrar Atlassian Cloud no Azure AD, pode:

* Controlar no Azure AD que tenha acesso à nuvem da Atlassian.
* Permita que os utilizadores ser automaticamente sessão iniciada para Atlassian Cloud com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Atlassian Cloud início de sessão único (SSO) ativado na subscrição.
* Para ativar a Security Assertion Markup Language (SAML) início de sessão único para produtos da Atlassian Cloud, terá de configurar o acesso da Atlassian. Saiba mais sobre [Atlassian acesso]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta a nuvem da Atlassian **SP e IDP** iniciada SSO

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adicionar Atlassian Cloud a partir da Galeria

Para configurar a integração da Cloud da Atlassian para o Azure AD, terá de adicionar Atlassian Cloud a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Atlassian Cloud** na caixa de pesquisa.
1. Selecione **Atlassian Cloud** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com a Cloud de Atlassian através de um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Cloud da Atlassian.

Para configurar e testar o SSO do Azure AD com a nuvem da Atlassian, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO de nuvem da Atlassian](#configure-atlassian-cloud-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste na Cloud da Atlassian](#create-atlassian-cloud-test-user)**  - para ter um equivalente de B.Simon na Cloud da Atlassian que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Atlassian Cloud** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, introduza os valores para os seguintes campos:

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://auth.atlassian.com/saml/<unique ID>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Clique em **definir URLs adicionais**.

    d. Na **estado de reencaminhamento** caixa de texto, escreva um URL com o seguinte padrão: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Os valores anteriores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Irá obter estes valores reais do **configuração de SAML do Cloud Atlassian** ecrã que é explicado posteriormente no **configurar Atlassian Cloud Single Sign-On** do tutorial.

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<instancename>.atlassian.net`

    > [!NOTE]
    > O valor do URL de início de sessão não é real. Cole o valor da instância que utilizar para a sessão para o portal de administração da Atlassian Cloud.

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Seu aplicativo em nuvem da Atlassian espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Espera que a aplicação na Cloud da Atlassian **nameidentifier** seja mapeado com **user.mail**, por isso, precisa editar o mapeamento do atributo, clicando em **editar** ícone e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar Atlassian Cloud** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-atlassian-cloud-sso"></a>Configurar a Cloud da Atlassian SSO

1. Para automatizar a configuração em nuvem da Atlassian, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **configuração Atlassian Cloud** irá direcioná-lo para a aplicação na Cloud da Atlassian. A partir daí, forneça as credenciais de administrador para iniciar sessão na Cloud da Atlassian. A extensão de navegador automaticamente irá configurar o aplicativo para e automatizar passos 3 a 7.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se desejar configurar manualmente o Atlassian Cloud, abra uma nova janela de browser e inicie sessão no site da sua empresa na Cloud da Atlassian como administrador e execute os seguintes passos:

4. Terá de verificar o seu domínio antes de ir para configurar o início de sessão único. Para obter mais informações, consulte [verificação de domínio da Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) documento.

5. No painel esquerdo, selecione **Security** > **SAML início de sessão único**. Se ainda não o fez, inscreva-se para Atlassian Identity Manager.

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

6. Na **configuração SAML do adicionar** janela, efetue o seguinte procedimento:

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Na **fornecedor de identidade ID de entidade** caixa, cole a **do Azure AD identificador** que copiou do portal do Azure.

    b. Na **fornecedor de identidade SSO URL** caixa, cole a **URL de início de sessão** que copiou do portal do Azure.

    c. Abra o certificado transferido do portal do Azure num arquivo. txt, copie o valor (sem o *Begin Certificate* e *End Certificate* linhas) e, em seguida, cole-a no **X509 pública certificado** caixa.

    d. Clique em **Guardar configuração**.

7. Para garantir que tem de configurar os URLs corretos, Atualize as definições do Azure AD efetuando o seguinte procedimento:

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. Na janela de SAML, copie o **ID de identidade de SP** e, em seguida, no portal do Azure, em nuvem da Atlassian **configuração básica de SAML**, cole-a no **identificador** caixa.

    b. Na janela de SAML, copie os **URL do serviço de consumidor de asserção de SP** e, em seguida, no portal do Azure, em nuvem da Atlassian **configuração básica de SAML**, cole-a no **URL de resposta**caixa. O URL de início de sessão é o URL de inquilino da sua Atlassian Cloud.

    > [!NOTE]
    > Se for um cliente existente, depois de atualizar o **ID de identidade de SP** e **URL do serviço de consumidor de asserção de SP** valores no portal do Azure, selecione **Sim, atualizar a configuração do**. Se for um novo cliente, pode ignorar este passo.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado B.Simon.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso à nuvem da Atlassian.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Atlassian Cloud**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-atlassian-cloud-test-user"></a>Criar utilizador de teste na Cloud da Atlassian

Para ativar a utilizadores do Azure AD iniciar sessão na Cloud da Atlassian, Aprovisione as contas de utilizador manualmente na Cloud de Atlassian efetuando o seguinte procedimento:

1. Na **Administration** painel, selecione **utilizadores**.

    ![A ligação de utilizadores de nuvem da Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

2. Para criar um utilizador na Cloud da Atlassian, selecione **utilizador de convite**.

    ![Criar um utilizador de nuvem da Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

3. Na **endereço de E-Mail** caixa, introduza o endereço de e-mail do utilizador e, em seguida, atribua o acesso de aplicação.

    ![Criar um utilizador de nuvem da Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

4. Para enviar um convite por e-mail ao utilizador, selecione **convidar utilizadores**. Um convite por e-mail é enviado para o utilizador e, depois de aceitar o convite, o utilizador está ativo no sistema.

> [!NOTE]
> Também pode efetuar em massa-criar utilizadores, selecionando o **criar em massa** botão no **utilizadores** secção.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico de nuvem da Atlassian no painel de acesso, deve ser automaticamente conectado para a Cloud da Atlassian para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)