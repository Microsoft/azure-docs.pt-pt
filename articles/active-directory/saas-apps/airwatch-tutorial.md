---
title: 'Tutorial: Integração do Active Directory do Azure com AirWatch | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0a684c6a38ba3a95438941f668b36b23d278df
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227651"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: Integrar AirWatch com o Azure Active Directory

Neste tutorial, irá aprender como integrar AirWatch com o Azure Active Directory (Azure AD). Quando integrar AirWatch com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao AirWatch.
* Permita que os utilizadores ser automaticamente sessão iniciada para AirWatch com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* AirWatch início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta AirWatch **SP** iniciada SSO.

## <a name="adding-airwatch-from-the-gallery"></a>Adicionando AirWatch da Galeria

Para configurar a integração da AirWatch com o Azure AD, terá de adicionar AirWatch a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **AirWatch** na caixa de pesquisa.
1. Selecione **AirWatch** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com AirWatch com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no AirWatch.

Para configurar e testar o SSO do Azure AD com AirWatch, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO da AirWatch](#configure-airwatch-sso)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Criar utilizador de teste da AirWatch](#create-airwatch-test-user)**  - para ter um equivalente da Eduarda Almeida na AirWatch que está ligado à representação de utilizador do Azure AD.
5. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
6. **[Testar o SSO](#test-sso)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **AirWatch** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    1. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Na **identificador (ID de entidade)** texto, escreva o valor como: `AirWatch`

    > [!NOTE]
    > Este valor não é a verdadeira. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente da AirWatch](https://www.air-watch.com/company/contact-us/) para obter este valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. Aplicação da AirWatch espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

1. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Nome |  Atributo de origem|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **XML de metadados de Federação** e selecione **transferir** para transferir o XML de metadados e guarde-o no seu computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Sobre o **configurar AirWatch** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Configurar o SSO da AirWatch

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa AirWatch como um administrador.

1. Na página de definições. Selecione **definições > integração empresarial > Serviços de diretório**.

   ![As definições](./media/airwatch-tutorial/ic791921.png "definições")

1. Clique nas **usuário** separador a **Base DN** caixa de texto, escreva o seu nome de domínio e, em seguida, clique em **guardar**.

   ![Usuário](./media/airwatch-tutorial/ic791922.png "utilizador")

1. Clique nas **servidor** separador.

   ![Servidor](./media/airwatch-tutorial/ic791923.png "Server")

1. Execute os seguintes passos no **LDAP** secção:

    ![Carregue](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Como **tipo de diretório**, selecione **nenhum**.

    b. Selecione **utilizar SAML para autenticação**.

1. Sobre o **SAML 2.0** secção, para carregar o certificado transferido, clique em **carregar**.

    ![Carregue](./media/airwatch-tutorial/ic791932.png "carregar")

1. Na **pedir** secção, execute os seguintes passos:

    ![Pedir](./media/airwatch-tutorial/ic791925.png "do pedido")  

    a. Como **solicitar tipo de enlace**, selecione **POST**.

    b. No portal do Azure, no **configurar o início de sessão único em AirWatch** página de diálogo, copie a **URL de início de sessão** valor e, em seguida, cole-o no **fornecedor único início de sessão no URL de identidade** caixa de texto.

    c. Como **formato NameID**, selecione **endereço de E-Mail**.

    d. Como **segurança de pedido de autenticação**, selecione **nenhum**.

    e. Clique em **Guardar**.

1. Clique nas **utilizador** separador novamente.

    ![Usuário](./media/airwatch-tutorial/ic791926.png "utilizador")

1. Na **atributo** secção, execute os seguintes passos:

    ![Atributo](./media/airwatch-tutorial/ic791927.png "atributo")

    a. Na **identificador de objeto** caixa de texto, tipo `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Na **nome de utilizador** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Na **nome a apresentar** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Na **nome próprio** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Na **sobrenome** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Na **E-Mail** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Clique em **Guardar**.

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

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único ao conceder acesso para AirWatch.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **AirWatch**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-airwatch-test-user"></a>Criar utilizador de teste da AirWatch

Para ativar a utilizadores do Azure AD iniciar sessão no AirWatch, eles têm de ser aprovisionados para AirWatch. No caso da AirWatch, aprovisionamento é uma tarefa manual.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **AirWatch** site da empresa como administrador.

2. No painel de navegação no lado esquerdo, clique em **contas**e, em seguida, clique em **utilizadores**.
  
   ![Os utilizadores](./media/airwatch-tutorial/ic791929.png "utilizadores")

3. Na **usuários** menu, clique em **vista de lista**e, em seguida, clique em **Add > Adicionar utilizador**.
  
   ![Adicionar utilizador](./media/airwatch-tutorial/ic791930.png "adicionar utilizador")

4. Sobre o **Adicionar / Editar utilizador** caixa de diálogo, execute os seguintes passos:

   ![Adicionar utilizador](./media/airwatch-tutorial/ic791931.png "adicionar utilizador")

   a. Tipo de **nome de utilizador**, **palavra-passe**, **Confirmar palavra-passe**, **nome próprio**, **Apelido**,  **Endereço de e-mail** de uma conta válida do Azure Active Directory que pretende aprovisionar em caixas de texto relacionadas.

   b. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras AirWatch utilizador conta criação ferramentas ou APIs fornecidas pelo AirWatch para aprovisionar contas de utilizador do AAD.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico da AirWatch no painel de acesso, deve ser automaticamente sessão iniciada no AirWatch para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
