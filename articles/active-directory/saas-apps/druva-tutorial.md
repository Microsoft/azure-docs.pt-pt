---
title: 'Tutorial: Integração do Active Directory do Azure com o Druva | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf359f38bd7032b4de48a17b91ba1c10b165c91
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807784"
---
# <a name="tutorial-integrate-druva-with-azure-active-directory"></a>Tutorial: Integrar o Druva com o Azure Active Directory

Neste tutorial, irá aprender como integrar o Druva com o Azure Active Directory (Azure AD). Quando integrar o Druva com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Druva.
* Permita que os utilizadores ser automaticamente sessão iniciada para Druva com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* Druva início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta Druva **SP** e **IDP** iniciada SSO

## <a name="adding-druva-from-the-gallery"></a>Adicionando Druva da Galeria

Para configurar a integração do Druva, para o Azure AD, terá de adicionar Druva a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Druva** na caixa de pesquisa.
1. Selecione **Druva** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com o Druva com um utilizador de teste **Eduarda Almeida**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Druva.

Para configurar e testar o SSO do Azure AD com o Druva, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Druva SSO](#configure-druva-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Druva](#create-druva-test-user)**  - para ter um equivalente da Eduarda Almeida na Druva que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Druva** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação na **IDP** modo iniciado, o utilizador não tem de realizar qualquer passo dado que a aplicação já está pré-integrado com o Azure.

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://login.druva.com/login`

1. Aplicação de Druva espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

1. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name | Atributo de origem|
    | ------------------- | -------------------- |
    | insync\_auth\_token |Introduza o valor gerado token |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar Druva** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-druva-sso"></a>Configurar o Druva SSO

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Druva como um administrador.

1. Aceda a **gerenciar \> definições**.

    ![As definições](./media/druva-tutorial/ic795091.png "definições")

1. Na caixa de diálogo Definições de início de sessão único, execute os seguintes passos:

    ![Único configurações de logon](./media/druva-tutorial/ic795092.png "único configurações de logon")

    a. Na **URL de início de sessão do fornecedor de ID** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    b. Na **URL de fim de sessão do fornecedor de ID** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure

    c. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado do fornecedor de ID** caixa de texto

    d. Para abrir o **configurações** página, clique em **guardar**.

1. Sobre o **configurações** página, clique em **gerar o Token de SSO**.

    ![As definições](./media/druva-tutorial/ic795093.png "definições")

1. Sobre o **único início de sessão no Token de autenticação** caixa de diálogo, execute os seguintes passos:

    ![O Token de SSO](./media/druva-tutorial/ic795094.png "o Token de SSO")

    a. Clique em **cópia**, colar copiados valor na **valor** caixa de texto no **adicionar atributo** secção no portal do Azure.

    b. Clique em **Fechar**.

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

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Druva.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Druva**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-druva-test-user"></a>Criar utilizador de teste Druva

Para habilitar os utilizadores do Azure AD iniciar sessão no Druva, tem de ser aprovisionados em Druva. No caso de Druva, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Druva** site da empresa como administrador.

1. Aceda a **gerenciar \> utilizadores**.

    ![Gerir utilizadores](./media/druva-tutorial/ic795097.png "gerir utilizadores")

1. Clique em **criar um novo**.

    ![Gerir utilizadores](./media/druva-tutorial/ic795098.png "gerir utilizadores")

1. Na caixa de diálogo Criar um novo utilizador, execute os seguintes passos:

    ![Criar NewUser](./media/druva-tutorial/ic795099.png "criar NewUser")

    a. Na **endereço de E-Mail** caixa de texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    b. Na **Name** caixa de texto, introduza o nome de utilizador, como **BrittaSimon**.

    c. Clique em **criar utilizador**.

> [!NOTE]
> Pode utilizar quaisquer outras Druva utilizador conta criação ferramentas ou APIs fornecidas pelo Druva para aprovisionar contas de utilizador do Azure AD.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico Druva no painel de acesso, deve ser automaticamente sessão iniciada no Druva para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
