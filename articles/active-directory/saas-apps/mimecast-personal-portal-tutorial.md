---
title: 'Tutorial: Integração do Active Directory do Azure com o Portal de pessoal de Mimecast | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Portal de pessoal de Mimecast.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37ffea61df1091cb7517014c4796537cdd40d62b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765301"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Tutorial: Integração do Active Directory do Azure com o Portal de pessoal de Mimecast

Neste tutorial, saiba como integrar o Portal de Mimecast pessoais com o Azure Active Directory (Azure AD).
Integrar Portal de Mimecast pessoais no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Portal de Mimecast pessoais.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Mimecast Portal pessoal (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Portal de pessoal de Mimecast, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Portal de pessoal de Mimecast logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* O Portal pessoal Mimecast suporta **SP** iniciada SSO

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Adicionando o Portal de pessoal de Mimecast da Galeria

Para configurar a integração do Portal de Mimecast pessoais no Azure AD, terá de adicionar Mimecast de Portal pessoais a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o Portal de pessoal de Mimecast partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Portal de pessoal de Mimecast**, selecione **Portal pessoal de Mimecast** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Portal de pessoal de Mimecast na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Mimecast Portal pessoal, com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Portal de pessoal de Mimecast deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Portal de pessoal de Mimecast, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Mimecast pessoais Portal início de sessão único](#configure-mimecast-personal-portal-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Portal de pessoal de Mimecast](#create-mimecast-personal-portal-test-user)**  - para ter um equivalente da Eduarda Almeida no Portal de pessoal de Mimecast que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Portal de pessoal de Mimecast, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Portal pessoal de Mimecast** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de Portal pessoal Mimecast e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL: 

    | Região  |  Value | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | No exterior        | `https://jer-api.mimecast.com/login/saml`|

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | Região  |  Value | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Estados Unidos   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | África do Sul    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Austrália       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | No exterior        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. Na **URL de resposta** caixa de texto, escreva um URL: 

    | Região  |  Value | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Estados Unidos   | `https://us-api.mimecast.com/login/saml`|
    | África do Sul    | `https://za-api.mimecast.com/login/saml`|
    | Austrália       | `https://au-api.mimecast.com/login/saml`|
    | No exterior        | `https://jer-api.mimecast.com/login/saml`|

    > [!NOTE]
    > O valor do identificador não é real. Atualize o valor com o identificador real. Contacte [equipa de suporte de cliente de Portal pessoal Mimecast](https://www.mimecast.com/customer-success/technical-support/) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

6. Na **configurar o Portal de pessoal de Mimecast** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-mimecast-personal-portal-single-sign-on"></a>Configurar Mimecast pessoal Portal início de sessão único

1. Numa janela do browser web diferente, inicie sessão no seu Portal pessoal Mimecast como um administrador.

2. Aceda a **serviços \> aplicativos**.
   
    ![Aplicativos](./media/mimecast-personal-portal-tutorial/ic794998.png "aplicativos")

3. Clique em **perfis de autenticação**.
   
    ![Perfis de autenticação](./media/mimecast-personal-portal-tutorial/ic794999.png "perfis de autenticação")

4. Clique em **novo perfil de autenticação**.
   
    ![Novo perfil de autenticação](./media/mimecast-personal-portal-tutorial/ic795000.png "novo perfil de autenticação")

5. Na **perfil de autenticação** secção, execute os seguintes passos:
   
    ![Perfil de autenticação](./media/mimecast-personal-portal-tutorial/ic795001.png "perfil de autenticação")
   
    a. Na **Descrição** caixa de texto, escreva um nome para a sua configuração.
   
    b. Selecione **impor autenticação SAML para o Portal de pessoal de Mimecast**.
   
    c. Como **fornecedor**, selecione **Azure Active Directory**.
   
    d. No **URL de emissor** caixa de texto, cole o valor de **Azure Ad identificador**, que copiou do portal do Azure.
   
    e. Na **URL de início de sessão** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.
   
    f. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    g. Abra sua **base 64** certificado codificado no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado do fornecedor de identidade (metadados)** caixa de texto.

    h. Selecione **permitir o início de sessão único em**.
   
    i. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso ao Portal de Mimecast pessoais.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Portal pessoal de Mimecast**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Portal de pessoal de Mimecast**.

    ![A ligação do Portal de pessoal de Mimecast na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-mimecast-personal-portal-test-user"></a>Criar utilizador de teste do Portal de pessoal de Mimecast

Para permitir que os utilizadores do Azure AD inicie sessão no Portal de pessoal de Mimecast, tem de ser aprovisionados no Portal de Mimecast pessoais. No caso do Portal de pessoal Mimecast, aprovisionamento é uma tarefa manual.

É necessário registrar um domínio, antes de poder criar os utilizadores.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Portal de pessoal de Mimecast** como administrador.

2. Aceda a **diretórios \> interno**.
   
    ![Diretórios](./media/mimecast-personal-portal-tutorial/ic795003.png "diretórios")

3. Clique em **registar o novo domínio**.
   
    ![Registar o novo domínio](./media/mimecast-personal-portal-tutorial/ic795004.png "registar o novo domínio")

4. Depois de ter sido criado o seu novo domínio, clique em **novo endereço**.
   
    ![Novo endereço](./media/mimecast-personal-portal-tutorial/ic795005.png "novo endereço")

5. Na caixa de diálogo novo endereço, execute os seguintes passos de um Azure válido conta AD que pretende aprovisionar:
   
    ![Save](./media/mimecast-personal-portal-tutorial/ic795006.png "Save")
   
    a. Na **endereço de E-Mail** caixa de texto, tipo **endereço de E-Mail** do utilizador como **BrittaSimon@contoso.com**.
    
    b. Na **nome Global** caixa de texto, tipo a **nome de utilizador** como **BrittaSimon**.

    c. Na **palavra-passe**, e **Confirmar palavra-passe** caixas de texto, tipo o **palavra-passe** do utilizador.
   
    b. Clique em **Guardar**.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas de criação da conta de utilizador de Portal Mimecast de pessoais ou APIs fornecidas pelo Portal de pessoal de Mimecast para aprovisionar contas de utilizador do Azure AD.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Mimecast de Portal pessoal no painel de acesso, deve ser automaticamente sessão iniciada no Portal Mimecast pessoal para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

