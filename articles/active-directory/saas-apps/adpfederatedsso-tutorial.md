---
title: 'Tutorial: Integração do Active Directory do Azure com ADP | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ADP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7be5331b-0481-48f7-9d6b-619dfec657e1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb6943ef6c75f634cdb68557ed2339574bdc2602
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56878290"
---
# <a name="tutorial-azure-active-directory-integration-with-adp"></a>Tutorial: Integração do Active Directory do Azure com ADP

Neste tutorial, saiba como integrar ADP com o Azure Active Directory (Azure AD).
Integrar ADP no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao ADP.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para ADP (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com ADP, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* ADP logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta ADP **IDP** iniciada SSO

## <a name="adding-adp-from-the-gallery"></a>Adicionando ADP da Galeria

Para configurar a integração do ADP com o Azure AD, terá de adicionar ADP a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar ADP a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ADP**, selecione **ADP** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![ADP na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com ADP com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ADP deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com ADP, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar ADP Single Sign-On](#configure-adp-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste ADP](#create-adp-test-user)**  - para ter um equivalente da Eduarda Almeida na ADP que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com ADP, execute os seguintes passos:

1. No portal do Azure, sobre o **ADP** página de integração de aplicação, clique em **separador Propriedades** e execute os seguintes passos: 

    ![Propriedades de início de sessão único](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Definir o **ativada para os utilizadores para início de sessão** campo valor a **Sim**.

    b. Copiar o **URL de acesso de utilizador** e tiver colá-lo na **seção URL de início de sessão em configurar**, que é explicado mais tarde no tutorial.

    c. Definir o **atribuição utiliz. necessária** campo valor a **Sim**.

    d. Definir o **visível aos usuários** campo valor a **não**.

2. Na [portal do Azure](https://portal.azure.com/), na **ADP** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

3. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

5. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![ADP domínio e URLs únicas início de sessão em informações](common/idp-identifier.png)

    Na **identificador (ID de entidade)** caixa de texto, escreva um URL:  `https://fed.adp.com`

6. Aplicação de ADP espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo. O nome de afirmação sempre será **"PersonImmutableID"** e o valor que mostramos que mapear com **employeeid**. 

    O mapeamento de utilizador do Azure AD para ADP será feito sobre o **employeeid** , mas pode mapeá-la para um valor diferente com base nas suas definições de aplicação. Pelo que deve trabalho com [equipa de suporte de ADP](https://www.adp.com/contact-us/overview.aspx) primeiro para utilizar o identificador correto de um usuário e mapear esse valor com o **"PersonImmutableID"** de afirmação.

    ![image](common/edit-attribute.png)

7. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Name | Atributo de origem | 
    | ---------------| --------------- |
    | PersonImmutableID  | user.employeeid |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

    > [!NOTE] 
    > Antes de poder configurar a asserção de SAML, terá de contactar o seu [equipa de suporte de ADP](https://www.adp.com/contact-us/overview.aspx) e pedir o valor do atributo de identificador exclusivo do utilizador para o seu inquilino. Vai precisar deste valor para configurar a afirmação personalizada para a sua aplicação. 

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-adp-single-sign-on"></a>Configurar ADP Single Sign-On

Para configurar o início de sessão único em **ADP** lado, terá de carregar o transferido **XML de metadados** sobre o [site ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Este processo pode demorar alguns dias.

### <a name="configure-your-adp-services-for-federated-access"></a>Configurar o seu serviço ou serviços de ADP para acesso federado

>[!Important]
> Os seus funcionários que precisam de acesso federado aos seus serviços ADP tem de ser atribuídos para a aplicação de serviço ADP e, em seguida, os utilizadores devem ser reatribuídos para o serviço de ADP específico.
Após a receção de confirmação do seu representante de ADP, configure os utilizadores de serviços e atribuir/gerir ADP para controlar o acesso de utilizador para o serviço de ADP específico.

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **ADP**, selecione **ADP** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![ADP na lista de resultados](common/search-new-app.png)

5. No portal do Azure, no seu **ADP** página de integração de aplicação, clique em **separador Propriedades** e execute os seguintes passos:  

    ![Linkedproperties de início de sessão único](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Definir o **ativada para os utilizadores para início de sessão** campo valor a **Sim**.

    b.  Definir o **atribuição utiliz. necessária** campo valor a **Sim**.

    c.  Definir o **visível aos usuários** campo valor a **Sim**.

6. Na [portal do Azure](https://portal.azure.com/), na **ADP** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

7. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **modo** como **ligado**. para ligar a sua aplicação à **ADP**.

    ![Início de sessão único ligado](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

8. Navegue para o **URL de início de sessão de configurar** secção, execute os seguintes passos:

    ![Prop de início de sessão único](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Colar o **URL de acesso de utilizador**, que copiou acima **separador Propriedades** (a partir da aplicação de ADP principal).
                                                             
    b. Seguem-se a 5 as aplicações que suportam diferentes **URLs de estado de reencaminhamento**. Terá de acrescentar o adequado **URL de estado de reencaminhamento** manualmente para o valor para o aplicativo em particular a **URL de acesso de utilizador**.
    
    * **Agora força de trabalho de ADP**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP força de trabalho melhoradas tempo**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>?relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Guardar** as suas alterações.

10. Após a receção de confirmação do seu representante de ADP, começa o teste com um ou dois utilizadores.

    a. Atribua alguns utilizadores para o serviço de ADP aplicativo federado acesso de teste.

    b. Teste é efetuada com êxito quando os utilizadores acederem à aplicação de serviço ADP na galeria e podem acessar seu serviço ADP.
 
11. No caso de confirmação de um teste com êxito, atribua o serviço de ADP federado para utilizadores individuais ou grupos de utilizadores, que é explicado mais tarde no tutorial e distribuí-lo aos seus funcionários.
 
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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para ADP.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **ADP**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **ADP**.

    ![A ligação de ADP na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-adp-test-user"></a>Criar utilizador de teste ADP

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no ADP. Trabalhar com [equipa de suporte de ADP](https://www.adp.com/contact-us/overview.aspx) para adicionar os utilizadores na conta ADP. 

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico ADP no painel de acesso, deve ser automaticamente sessão iniciada no ADP para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

