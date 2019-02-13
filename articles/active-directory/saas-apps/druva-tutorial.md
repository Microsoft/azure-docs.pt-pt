---
title: 'Tutorial: Integração do Active Directory do Azure com o Druva | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b82efa4b14800eabbbcb4b977f256a5d885fc93b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211840"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Tutorial: Integração do Active Directory do Azure com o Druva

Neste tutorial, saiba como integrar o Druva com o Azure Active Directory (Azure AD).
Integrar o Druva no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Druva.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Druva (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Druva, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Druva logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Druva **SP** e **IDP** iniciada SSO

## <a name="adding-druva-from-the-gallery"></a>Adicionando Druva da Galeria

Para configurar a integração do Druva, para o Azure AD, terá de adicionar Druva a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Druva a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Druva**, selecione **Druva** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Druva na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Druva com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Druva deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Druva, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Druva Single Sign-On](#configure-druva-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Druva](#create-druva-test-user)**  - para ter um equivalente da Eduarda Almeida na Druva que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Druva, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Druva** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar o passo seguinte:

    ![Druva domínio e URLs únicas início de sessão em informações](common/idp-identifier.png)

    Na **identificador** texto, escreva um valor de cadeia de caracteres:  `druva-cloud`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![image](common/both-preintegrated-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://cloud.druva.com/home`

6. Aplicação de Druva espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos: 

    | Name | Atributo de origem|
    | ------------------- | -------------------- |
    | insync\_auth\_token |Introduza o valor gerado token |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Sobre o **configurar Druva** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-druva-single-sign-on"></a>Configurar Druva Single Sign-On

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Druva como administrador.

2. Aceda a **gerenciar \> definições**.

    ![As definições](./media/druva-tutorial/ic795091.png "definições")

3. Na caixa de diálogo Definições de início de sessão único, execute os seguintes passos:

    ![Único configurações de logon](./media/druva-tutorial/ic795092.png "único configurações de logon")
    
    a. Na **URL de início de sessão do fornecedor de ID** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.
        
    b. Na **URL de fim de sessão do fornecedor de ID** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure
        
    c. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado do fornecedor de ID** caixa de texto
     
    d. Para abrir o **configurações** página, clique em **guardar**.

4. Sobre o **configurações** página, clique em **gerar o Token de SSO**.

    ![As definições](./media/druva-tutorial/ic795093.png "definições")

5. Sobre o **único início de sessão no Token de autenticação** caixa de diálogo, execute os seguintes passos:

    ![O Token de SSO](./media/druva-tutorial/ic795094.png "o Token de SSO")
    
    a. Clique em **cópia**, colar copiados valor na **valor** caixa de texto no **adicionar atributo** secção no portal do Azure.
    
    b. Clique em **Fechar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Druva.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Druva**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Druva**.

    ![A ligação de Druva na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-druva-test-user"></a>Criar utilizador de teste Druva

Para habilitar os utilizadores do Azure AD iniciar sessão no Druva, tem de ser aprovisionados em Druva. No caso de Druva, o aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Druva** site da empresa como administrador.

2. Aceda a **gerenciar \> utilizadores**.
   
    ![Gerir utilizadores](./media/druva-tutorial/ic795097.png "gerir utilizadores")

3. Clique em **criar um novo**.
   
    ![Gerir utilizadores](./media/druva-tutorial/ic795098.png "gerir utilizadores")

4. Na caixa de diálogo Criar um novo utilizador, execute os seguintes passos:
   
    ![Criar NewUser](./media/druva-tutorial/ic795099.png "criar NewUser")
   
    a. Na **endereço de E-Mail** caixa de texto, introduza o e-mail do utilizador, como **brittasimon@contoso.com**.
   
    b. Na **Name** caixa de texto, introduza o nome de utilizador, como **BrittaSimon**.
   
    c. Clique em **criar utilizador**.

>[!NOTE]
>Pode utilizar quaisquer outras Druva utilizador conta criação ferramentas ou APIs fornecidas pelo Druva para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Druva no painel de acesso, deve ser automaticamente sessão iniciada no Druva para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

