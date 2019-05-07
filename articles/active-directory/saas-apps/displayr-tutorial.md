---
title: 'Tutorial: Integração do Active Directory do Azure com Displayr | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdb4cc26a435ba161532b324ae5a04fed8eb9437
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158407"
---
# <a name="tutorial-azure-active-directory-integration-with-displayr"></a>Tutorial: Integração do Active Directory do Azure com Displayr

Neste tutorial, saiba como integrar Displayr com o Azure Active Directory (Azure AD).
Integrar Displayr no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Displayr.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Displayr (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Displayr, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Displayr logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Displayr **SP** iniciada SSO

## <a name="adding-displayr-from-the-gallery"></a>Adicionando Displayr da Galeria

Para configurar a integração do Displayr com o Azure AD, terá de adicionar Displayr a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Displayr a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Displayr**, selecione **Displayr** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Displayr na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Displayr com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Displayr deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Displayr, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Displayr Single Sign-On](#configure-displayr-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Displayr](#create-displayr-test-user)**  - para ter um equivalente da Eduarda Almeida na Displayr que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Displayr, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Displayr** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **configuração de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, executar o passo seguinte:

    ![Displayr domínio e URLs únicas início de sessão em informações](common/sp-intiated.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://app.displayr.com/Login`

5. Na **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (bruto)** entre as opções de determinado de acordo com seus requisitos e guarde-o em seu computador.

    ![O link de download de certificado](common/certificateraw.png)

6. Aplicação de Displayr espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir a caixa de diálogo de atributos do utilizador.

    ![image](common/edit-attribute.png)

7. Além dos acima, o aplicativo Displayr espera mais alguns atributos a serem passados na resposta SAML. No **atributos de utilizador e afirmações** secção sobre o **afirmações de grupo (pré-visualização)** caixa de diálogo, execute os seguintes passos:

    a. Clique nas **caneta** junto a **grupos devolvido na afirmação**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Selecione **todos os grupos** na lista de rádio.

    c. Selecione **atributo de origem** dos **ID de grupo**.

    d. Verifique **personalizar o nome da afirmação de grupo**.

    e. Verifique **emitir grupos como declarações de função**.

    f. Clique em **Guardar**.

8. Sobre o **Displayr configuração** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-displayr-single-sign-on"></a>Configurar Displayr Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no Displayr como um administrador.

2. Clique em **configurações** , em seguida, navegue até à **conta**.

    ![Configuração](./media/displayr-tutorial/config01.png)

3. Mude para o **configurações** no menu superior e desloque-se para baixo na página clicando **configurar único início de sessão (SAML)**.

    ![Configuração](./media/displayr-tutorial/config02.png)

4. Sobre o **único início de sessão (SAML)** página, execute os seguintes passos:

    ![Configuração](./media/displayr-tutorial/config03.png)

    a. Verifique os **ativar único início de sessão (SAML)** caixa.

    b. Na **emissor** texto caixa, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    c. Na **URL de início de sessão** texto caixa, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    d. Na **URL de fim de sessão** texto caixa, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    e. Abrir o certificado (bruto) no bloco de notas, copiar o conteúdo e cole-o para o **certificado** caixa de texto.

    f. **Mapeamentos de grupo** são opcionais.

    g. Clique em **Guardar**.  

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Displayr.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Displayr**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Displayr**.

    ![A ligação de Displayr na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-displayr-test-user"></a>Criar utilizador de teste Displayr

Para ativar os utilizadores do Azure AD, inicie sessão para Displayr, eles têm de ser aprovisionados em Displayr. Displayr, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Displayr como administrador.

2. Clique em **configurações** , em seguida, navegue até à **conta**.

    ![Configuração de Displayr](./media/displayr-tutorial/config01.png)

3. Mude para o **definições** no menu superior e desloque-se para baixo na página, até **utilizadores** secção, em seguida, clique em **novo utilizador**.

    ![Configuração de Displayr](./media/displayr-tutorial/config07.png)

4. Sobre o **novo utilizador** página, execute os seguintes passos:

    ![Configuração de Displayr](./media/displayr-tutorial/config06.png)

    a. Na **Name** texto, introduza o nome de utilizador, como **Brittasimon**.

    b. Na **E-Mail** texto, introduza o e-mail do utilizador, como `Brittasimon@contoso.com`.

    c. Selecione seu apropriado **associação de grupo**.

    d. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Displayr no painel de acesso, deve ser automaticamente sessão iniciada no Displayr para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

