---
title: 'Tutorial: Integração do Active Directory do Azure com Deskradar | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Deskradar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 5ff3e014-b79f-4a6c-bb0b-38462bea5d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95451bff6074c0beb220bf3c6edb5570a5258b1a
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310279"
---
# <a name="tutorial-integrate-deskradar-with-azure-active-directory"></a>Tutorial: Integrar Deskradar com o Azure Active Directory

Neste tutorial, irá aprender como integrar Deskradar com o Azure Active Directory (Azure AD). Quando integrar Deskradar com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Deskradar.
* Permita que os utilizadores ser automaticamente sessão iniciada para Deskradar com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Deskradar início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta Deskradar **SP e IDP** iniciada SSO.

## <a name="adding-deskradar-from-the-gallery"></a>Adicionando Deskradar da Galeria

Para configurar a integração do Deskradar com o Azure AD, terá de adicionar Deskradar a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Deskradar** na caixa de pesquisa.
1. Selecione **Deskradar** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com Deskradar com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Deskradar.

Para configurar e testar o SSO do Azure AD com Deskradar, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Deskradar SSO](#configure-deskradar-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Deskradar](#create-deskradar-test-user)**  - para ter um equivalente da Eduarda Almeida na Deskradar que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Deskradar** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://YOURDOMAIN.deskradar.cloud`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/consume`

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://YOURDOMAIN.deskradar.cloud/auth/sso/saml/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Substitua **OSEUDOMÍNIO** com o domínio de instância Deskradar. Contacte [equipa de suporte de cliente Deskradar](mailto:support@deskradar.com) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. Aplicação de Deskradar espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

1. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name | Atributo de origem|
    | ---------------| --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.userprincipalname |
    | | |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **OK**.

    g. Clique em **Guardar**.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar Deskradar** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-deskradar-sso"></a>Configurar o Deskradar SSO

1. Para automatizar a configuração no Deskradar, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

1. Depois de adicionar a extensão para o navegador, clique em **Deskradar configuração** irá direcioná-lo para o aplicativo Deskradar. A partir daí, forneça as credenciais de administrador a iniciar sessão em Deskradar. A extensão de navegador automaticamente irá configurar o aplicativo para e automatizar passos 3 a 7.

    ![Configuração do programa de configuração](common/setup-sso.png)

1. Se desejar configurar manualmente o Deskradar, abra uma nova janela de browser e inicie sessão no site da sua empresa Deskradar como administrador e execute os seguintes passos:

1. Open **equipe** painel ao clicar no ícone na barra lateral.

1. Mude para o **autenticação** separador.

1. Sobre o **SAML 2.0** separador, execute os seguintes passos:

    ![Configuração de Deskradar](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. Ativar **SAML** método de autenticação.

    b. Na **URL de SSO SAML** caixa de texto, introduza o **URL de início de sessão** valor, que copiou do portal do Azure.

    c. Na **emissor do fornecedor de identidade** caixa de texto, introduza o **do Azure AD identificador** valor, que copiou do portal do Azure.

1. Abra o transferido **certificado (Base64)** ficheiro com um editor de texto e copie e cole-os em **certificado público** na Deskradar.

    ![Configuração de Deskradar](./media/deskradar-tutorial/15-paste-certificate.jpg)

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

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso para Deskradar.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Deskradar**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-deskradar-test-user"></a>Criar utilizador de teste Deskradar

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Deskradar. Trabalhar com [equipa de suporte de cliente Deskradar](mailto:support@deskradar.com) para adicionar os utilizadores na plataforma Deskradar. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico Deskradar no painel de acesso, deve ser automaticamente sessão iniciada no Deskradar para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)