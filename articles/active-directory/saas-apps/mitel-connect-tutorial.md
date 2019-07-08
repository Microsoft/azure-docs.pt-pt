---
title: 'Tutorial: Integração do Active Directory do Azure com o Mitel Connect | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Mitel ligar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: e03870322df94a4c9587a3395c70925d2d2e838d
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588229"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Tutorial: Integração do Active Directory do Azure com o Connect do Mitel MiCloud

Neste tutorial, irá aprender como integrar a ligar de MiCloud Mitel de mensagens em fila com o Azure Active Directory (Azure AD). Integrar o MiCloud ligar com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao MiCloud ligar aplicações através das respetivas credenciais empresariais.
* Pode permitir que os utilizadores na sua conta para ser automaticamente sessão iniciada para MiCloud ligar (Single Sign-On) com as suas contas do Azure AD.


Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com MiCloud ligar, precisa do seguinte:

* Uma subscrição do Azure

  Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Uma conta de ligação de MiCloud Mitel

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD início de sessão único (SSO).

* Ligar Mitel suporta **SP** iniciada SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>Adicionar Mitel ligar a partir da Galeria

Para configurar a integração do Mitel ligar para o Azure AD, terá de adicionar Mitel ligar partir da Galeria à sua lista de aplicações de SaaS geridas no portal do Azure.

**Para adicionar a Mitel ligar a partir da galeria, siga os passos abaixo:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação do lado esquerdo, clique em **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Clique em **aplicações empresariais** e, em seguida, clique em **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Clique em **nova aplicação**.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Tipo **ligar Mitel** no campo de pesquisa, clique em **Mitel ligar** do painel de resultados e clique em **Add**.

     ![Mitel ligar-se na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, irá configurar e teste do Azure AD início de sessão único com MiCloud estabelecer ligação com base num utilizador de teste com o nome **Eduarda Almeida**. Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no MiCloud ligar deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com MiCloud Connect, terá de concluir os seguintes passos:

1. **[Configurar o MiCloud ligar para SSO com o Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)**  - para permitir que os utilizadores para utilizar esta funcionalidade e configurar as definições de SSO no lado do aplicativo.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
4. **[Criar um utilizador de teste ligar do Mitel MiCloud](#create-a-mitel-micloud-connect-test-user)**  - para ter um equivalente da Eduarda Almeida na sua conta MiCloud ligar-se que está ligado a representação do Azure AD do utilizador.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Configurar MiCloud ligar para SSO com o Azure AD

Nesta secção, irá ativar o Azure AD início de sessão único para MiCloud ligar no portal do Azure e configurar a sua conta MiCloud ligar para permitir o SSO com o Azure AD.

Para configurar MiCloud ligar com o SSO para o Azure AD, é mais fácil abrir o portal do Azure e o portal de contas de Mitel lado a lado. Terá de copiar algumas informações no portal do Azure para o portal de contas de Mitel e alguns do portal de contas de Mitel ao portal do Azure.


1. Para abrir a página de configuração no [portal do Azure](https://portal.azure.com/), efetue o seguinte procedimento:

    a. Sobre o **Mitel ligar** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

    b. Na **selecionar um método de início de sessão único** caixa de diálogo, clique em **SAML**.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)
    
    Baseado em SAML início de sessão na página é apresentada.

2. Para abrir a caixa de diálogo de configuração no portal de contas de Mitel, faça o seguinte:

    a. Sobre o **sistema telefônico** menu, clique em **funcionalidades de suplemento**.

    b. À direita da **início de sessão único**, clique em **ativar** ou **definições**.
    
    É apresentada a caixa de diálogo Ligar único configurações de logon.
    
3. Selecione o **ativar o início de sessão único** caixa de verificação.
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. No portal do Azure, clique nas **editar** ícone na **configuração básica de SAML** secção.
    ![image](common/edit-urls.png)

    É apresentada a caixa de diálogo de configuração básica de SAML.

5.  Copie o URL a partir do **Mitel identificador (ID de entidade)** campo no portal de contas de Mitel e colá-lo no **identificador (ID de entidade)** campo no portal do Azure.

6. Copie o URL do **URL de resposta (URL do serviço de consumidor de asserção)** campo no portal de contas de Mitel e cole-o para o **URL de resposta (URL do serviço de consumidor de asserção)** campo no portal do Azure.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. Na **iniciar sessão no URL** caixa de texto, escreva um dos seguintes URLs:

    * **https://portal.shoretelsky.com** -Para utilizar o portal de contas de Mitel como seu aplicativo de Mitel padrão
    * **https://teamwork.shoretel.com** -Para utilizar o trabalho em equipe como seu aplicativo de Mitel padrão

    **NOTA**: O aplicativo de Mitel padrão é a aplicação acedida quando um usuário clica no mosaico Mitel ligar-se no painel de acesso. Isso também é a aplicação acedida durante a realização de uma configuração de teste do Azure AD.

8. Clique em **salvar** no **configuração básica de SAML** caixa de diálogo no portal do Azure.

9. No **certificado de assinatura SAML** secção sobre o **baseado em SAML início de sessão** página no portal do Azure, clique em **transferir** junto a **certificado (Base64)** para transferir os **certificado de assinatura** e guarde-o para o seu computador.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Abra o ficheiro de certificado de assinatura num editor de texto, copie todos os dados no ficheiro e, em seguida, cole os dados na **certificado de assinatura** campo no portal de contas de Mitel. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. No **configuração Mitel ligar** secção sobre o **baseado em SAML início de sessão** página do portal do Azure, efetue o seguinte procedimento:

    a. Copie o URL do **URL de início de sessão** campo e cole-o para o **URL de início de sessão** campo no portal de contas de Mitel.

    b. Copie o URL do **do Azure AD identificador** campo e colá-lo no **ID de entidade** campo no portal de contas de Mitel.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Clique em **guardar** sobre o **ligar único configurações de logon** caixa de diálogo no portal de contas de Mitel.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

Nesta secção, irá criar um utilizador de teste com o nome Eduarda Almeida no portal do Azure.

1. No portal do Azure, no painel esquerdo, clique em **do Azure Active Directory**, clique em **utilizadores**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Clique em **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Na caixa de diálogo de propriedades de utilizador, siga os passos abaixo:

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , digite **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite brittasimon @\<yourcompanydomain\>.\< extensão\>.  
Por exemplo, BrittaSimon@contoso.com.

    c. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso Mitel ligar.

1. No portal do Azure, clique em **aplicações empresariais**e, em seguida, clique em **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, clique em **Mitel ligar**.

    ![A ligação Mitel ligar-se na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique em **adicionar utilizador**, em seguida, clique em **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** no **utilizadores** lista, em seguida, clique em **selecione** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, selecione a função adequada para o utilizador na lista os **selecionar função** caixa de diálogo e clique em **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** caixa de diálogo, clique em **atribuir**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Criar um utilizador de teste ligar do Mitel MiCloud

Nesta secção, vai criar um utilizador com o nome Eduarda Almeida na sua conta MiCloud ligar. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

Para obter detalhes sobre como adicionar utilizadores no portal de contas de Mitel, consulte a [adição de um utilizador](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) artigo da Base de dados de conhecimento Mitel.

Crie um utilizador na sua conta MiCloud estabelecer ligação com os seguintes detalhes:

  * **Nome:** Britta Simon

* **Endereço de E-Mail da empresa:** `brittasimon@<yourcompanydomain>.<extension>`   
(Exemplo: [ brittasimon@contoso.com ](mailto:brittasimon@contoso.com))

* **Nome de utilizador:** `brittasimon@<yourcompanydomain>.<extension>`  
(Exemplo: [ brittasimon@contoso.com ](mailto:brittasimon@contoso.com); nome de utilizador normalmente é o mesmo endereço de e-mail empresarial do utilizador)

**NOTA:** Nome de utilizador de MiCloud ligar-se o utilizador tem de ser idêntico ao endereço de e-mail do utilizador no Azure.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, irá testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Mitel ligar-se no painel de acesso, deve ser redirecionado automaticamente para entrar para a aplicação ligar MiCloud configurado como padrão na **iniciar sessão no URL** campo. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
