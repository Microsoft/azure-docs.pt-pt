---
title: 'Tutorial: Integração do Active Directory do Azure com o Dropbox for Business | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 591d8d28be5fc9322de8cf4886dc5924f53b1deb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103788"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Tutorial: Integrar o Dropbox para empresas com o Azure Active Directory

Neste tutorial, irá aprender como integrar o Dropbox for Business no Azure Active Directory (Azure AD). Quando integrar o Dropbox for Business no Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Dropbox para empresas.
* Permita que os utilizadores ser automaticamente sessão iniciada para a Dropbox para empresas com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* O Dropbox for Business início de sessão único (SSO) ativar a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

* Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. O Dropbox for Business suporta **SP** iniciada SSO

* O Dropbox for Business suporta **Just In Time** aprovisionamento de utilizadores

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Adicionando o Dropbox for Business da Galeria

Para configurar a integração do Dropbox para empresas com o Azure AD, terá de adicionar Dropbox para a empresa a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Dropbox para empresas** na caixa de pesquisa.
1. Selecione **Dropbox para empresas** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com o Dropbox para a empresa através de um utilizador de teste **Eduarda Almeida**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Dropbox para empresas.

Para configurar e testar o SSO do Azure AD com o Dropbox for Business, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Dropbox para SSO de negócios](#configure-dropbox-for-business-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar Dropbox para o utilizador de teste de negócios](#create-dropbox-for-business-test-user)**  - para ter um equivalente da Eduarda Almeida na Dropbox para a empresa que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Dropbox para empresas** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://www.dropbox.com/sso/<id>`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um valor: `Dropbox`

    > [!NOTE]
    > O valor de URL de início de sessão anterior não é o valor real. Atualizar o valor com o início de sessão no URL real, que é explicado mais tarde no tutorial.

1. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar o Dropbox for Business** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-dropbox-for-business-sso"></a>Configurar o Dropbox para empresas SSO

1. Para automatizar a configuração no Dropbox para empresas, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **Dropbox de configuração para empresas** irá direcioná-lo à Dropbox para o aplicativo de negócios. A partir daí, forneça as credenciais de administrador para iniciar sessão no Dropbox para empresas. A extensão de navegador automaticamente irá configurar o aplicativo para e automatizar passos 3-8.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se desejar configurar manualmente o Dropbox para empresas, abra uma nova janela de browser e vá no seu Dropbox para o inquilino de negócios e inicie sessão seu Dropbox para o inquilino de negócios. e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/ic769509.png "configurar início de sessão único")

4. Clique nas **ícone de utilizador** e selecione **definições** separador.

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure1.png "configurar início de sessão único")

5. No painel de navegação no lado esquerdo, clique em **consola de administração**.

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure2.png "configurar início de sessão único")

6. Sobre o **consola de administração**, clique em **definições** no painel de navegação esquerdo.

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure3.png "configurar início de sessão único")

7. Selecione **início de sessão único** opção sob a **autenticação** secção.

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure4.png "configurar início de sessão único")

8. Na **início de sessão único** secção, execute os seguintes passos:  

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure5.png "configurar início de sessão único")

    a. Selecione **necessários** como uma opção na lista pendente para o **início de sessão único**.

    b. Clique em **adicione o URL de início de sessão** e, no **URL de início de sessão da fornecedor de identidade** caixa de texto, colar a **URL de início de sessão** valor que copiou do portal do Azure e, em seguida, selecione  **Feito**.

    ![Configurar o início de sessão único](./media/dropboxforbusiness-tutorial/configure6.png "configurar início de sessão único")

    c. Clique em **carregar certificado**e, em seguida, navegue até sua **ficheiro de certificado codificado Base64** que transferiu do portal do Azure.

    d. Clique em **copiar ligação** e cole o valor copiado para o **URL de início de sessão** caixa de texto de **Dropbox para o domínio de negócios e URLs** secção no portal do Azure.

    e. Clique em **Guardar**.

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

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para o Dropbox for Business.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Dropbox para empresas**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-dropbox-for-business-test-user"></a>Criar Dropbox para o utilizador de teste de negócios

Nesta secção, um usuário chamado Eduarda Almeida é criado na Dropbox para empresas. O Dropbox for Business suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Dropbox para empresas, é criado um novo após a autenticação.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte o [Dropbox para a equipa de suporte de empresas do cliente](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Teste SSO

Quando seleciona o Dropbox para o mosaico de negócios no painel de acesso, deve ser automaticamente sessão iniciada no Dropbox para empresas para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)