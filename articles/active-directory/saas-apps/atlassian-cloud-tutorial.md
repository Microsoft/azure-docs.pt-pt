---
title: 'Tutorial: Integração do Active Directory do Azure com a nuvem da Atlassian | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e na Cloud da Atlassian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 601f2dc69c7b390ea05cb66976dc7bbcc98d9502
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56880503"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Tutorial: Integração do Active Directory do Azure com a nuvem da Atlassian

Neste tutorial, saiba como integrar Atlassian Cloud no Azure Active Directory (Azure AD).
Integrar Atlassian Cloud no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso à nuvem da Atlassian.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a Cloud da Atlassian (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com a nuvem da Atlassian, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Atlassian Cloud logon único habilitado subscrição
* Para ativar a Security Assertion Markup Language (SAML) início de sessão único para produtos da Atlassian Cloud, terá de configurar o acesso da Atlassian. Saiba mais sobre [Atlassian acesso]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta a nuvem da Atlassian **SP e IDP** iniciada SSO

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Adicionar Atlassian Cloud a partir da Galeria

Para configurar a integração da Cloud da Atlassian para o Azure AD, terá de adicionar Atlassian Cloud a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Atlassian Cloud a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Atlassian Cloud**, selecione **Atlassian Cloud** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Atlassian Cloud na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com a nuvem da Atlassian com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Cloud da Atlassian deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Atlassian Cloud, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Atlassian Cloud início de sessão único](#configure-atlassian-cloud-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste na Cloud da Atlassian](#create-atlassian-cloud-test-user)**  - para ter um equivalente da Eduarda Almeida na Cloud da Atlassian que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com a nuvem da Atlassian, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Atlassian Cloud** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![[Nome da aplicação] Informações de início de sessão de único domínio e URLs](common/idp-relay.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://auth.atlassian.com/saml/<unique ID>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Clique em **definir URLs adicionais**.

    d. Na **estado de reencaminhamento** caixa de texto, escreva um URL com o seguinte padrão: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Os valores anteriores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Irá obter estes valores real a partir do ecrã de configuração de SAML do Cloud Atlassian que é explicado mais tarde no tutorial.

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![[Nome da aplicação] Informações de início de sessão de único domínio e URLs](common/both-signonurl.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<instancename>.atlassian.net`

    > [!NOTE]
    > O início de sessão anterior valor de URL não é real. Atualize o valor com o início de sessão real no URL. Contacte [equipa de suporte de cliente de Cloud da Atlassian](https://support.atlassian.com/) para obter este valor.

6. Seu aplicativo em nuvem da Atlassian espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Espera que a aplicação na Cloud da Atlassian **nameidentifier** seja mapeado com **user.mail**, por isso, precisa editar o mapeamento do atributo, clicando em **editar** ícone e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar Atlassian Cloud** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-atlassian-cloud-single-sign-on"></a>Configurar Atlassian Cloud início de sessão único

1. Para obter SSO configurado para a sua aplicação, inicie sessão no portal da Atlassian com credenciais de administrador.

2. Terá de verificar o seu domínio antes de ir para configurar o início de sessão único. Para obter mais informações, consulte [verificação de domínio da Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) documento.

3. No painel esquerdo, selecione **SAML início de sessão único**. Se ainda não o fez, inscreva-se para Atlassian Identity Manager.

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

4. Na **configuração SAML do adicionar** janela, efetue o seguinte procedimento:

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Na **ID de entidade do fornecedor de identidade** caixa, cole o ID de entidade SAML que copiou do portal do Azure.

    b. Na **fornecedor de identidade SSO URL** caixa, cole o URL do serviço de início de início de sessão único SAML que copiou do portal do Azure.

    c. Abra o certificado transferido do portal do Azure num arquivo. txt, copie o valor (sem o *Begin Certificate* e *End Certificate* linhas) e, em seguida, cole-a no **X509 pública certificado** caixa.

    d. Clique em **Guardar configuração**.

5. Para garantir que tem de configurar os URLs corretos, Atualize as definições do Azure AD efetuando o seguinte procedimento:

    ![Configurar o início de sessão único](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Na janela de SAML, copie o **ID de identidade de SP** e, em seguida, no portal do Azure, em nuvem da Atlassian **domínio e URLs**, cole-a no **identificador** caixa.

    b. Na janela de SAML, copie os **URL do serviço de consumidor de asserção de SP** e, em seguida, no portal do Azure, em nuvem da Atlassian **domínio e URLs**, cole-a no **URL de resposta** caixa. O URL de início de sessão é o URL de inquilino da sua Atlassian Cloud.

    > [!NOTE]
    > Se for um cliente existente, depois de atualizar o **ID de identidade de SP** e **URL do serviço de consumidor de asserção de SP** valores no portal do Azure, selecione **Sim, atualizar a configuração do**. Se for um novo cliente, pode ignorar este passo.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à nuvem da Atlassian.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Atlassian Cloud**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Atlassian Cloud**.

    ![A ligação de nuvem da Atlassian na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-atlassian-cloud-test-user"></a>Criar utilizador de teste na Cloud da Atlassian

Para ativar a utilizadores do Azure AD iniciar sessão na Cloud da Atlassian, Aprovisione as contas de utilizador manualmente na Cloud de Atlassian efetuando o seguinte procedimento:

1. Na **Administration** painel, selecione **utilizadores**.

    ![A ligação de utilizadores de nuvem da Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Para criar um utilizador na Cloud da Atlassian, selecione **utilizador de convite**.

    ![Criar um utilizador de nuvem da Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. Na **endereço de E-Mail** caixa, introduza o endereço de e-mail do utilizador e, em seguida, atribua o acesso de aplicação.

    ![Criar um utilizador de nuvem da Atlassian](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Para enviar um convite por e-mail ao utilizador, selecione **convidar utilizadores**. Um convite por e-mail é enviado para o utilizador e, depois de aceitar o convite, o utilizador está ativo no sistema.

> [!NOTE]
> Também pode efetuar em massa-criar utilizadores, selecionando o **criar em massa** botão no **utilizadores** secção.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de nuvem da Atlassian no painel de acesso, deve ser automaticamente conectado para a Cloud da Atlassian para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
   
