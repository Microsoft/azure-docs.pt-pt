---
title: 'Tutorial: A integração do Azure Active Directory com SAP Cloud Platform | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a7fa71f2cb9d2c5326eea75af40e1ab6b986eb9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56864877"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: Integração do Azure Active Directory com SAP Cloud Platform

Neste tutorial, saiba como integrar a plataforma de Cloud de SAP no Azure Active Directory (Azure AD).
Integrar a plataforma de Cloud de SAP no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SAP Cloud Platform.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o SAP Cloud Platform (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SAP Cloud Platform, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SAP Cloud Platform logon único habilitado subscrição

Depois de concluir este tutorial, os utilizadores do Azure AD tiver atribuído a SAP Cloud Platform será capazes de início de sessão único para a aplicação a utilizar o [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Terá de implementar a sua própria aplicação ou subscrever uma aplicação na sua conta do SAP Cloud Platform, para testar logon único. Neste tutorial, uma aplicação é implementada na conta.
> 

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a SAP Cloud Platform **SP** iniciada SSO

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Adicionando a plataforma de Cloud de SAP da Galeria

Para configurar a integração da plataforma de Cloud de SAP no Azure AD, terá de adicionar plataforma de Cloud de SAP a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar plataforma Cloud do SAP a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SAP Cloud Platform**, selecione **SAP Cloud Platform** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Plataforma de Cloud SAP na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o SAP Cloud Platform, com base num utilizador de teste **Eduarda Almeida**.
Para início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na plataforma de Cloud de SAP tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o SAP Cloud Platform, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SAP Cloud Platform início de sessão único](#configure-sap-cloud-platform-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de SAP Cloud Platform](#create-sap-cloud-platform-test-user)**  - para ter um equivalente da Eduarda Almeida na plataforma de Cloud de SAP que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SAP Cloud Platform, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SAP Cloud Platform** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio de plataforma de Cloud do SAP e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para iniciar sessão no seu **SAP Cloud Platform** aplicação. Este é o URL de conta específica de um recurso protegido em seu aplicativo SAP Cloud Platform. O URL baseia-se no seguinte padrão: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Este é o URL na sua aplicação SAP Cloud Platform, que requer que o utilizador autenticar.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. Na **identificador** caixa de texto, deve fornecer sua plataforma de Cloud de SAP escreva um URL através de um dos padrões seguintes: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente de plataforma de Cloud de SAP](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) para obter o URL de início de sessão e o identificador. URL de resposta, pode obter da secção de gestão de fidedignidade que é explicada mais tarde no tutorial.
    > 
4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Configurar o SAP Cloud Platform início de sessão único

1. Numa janela do browser web diferente, inicie sessão cabine de plataforma de Cloud de SAP em `https://account.<landscape host>.ondemand.com/cockpit`(por exemplo: https://account.hanatrial.ondemand.com/cockpit).

2. Clique nas **confiar** separador.
   
    ![Confiar](./media/sap-hana-cloud-platform-tutorial/ic790800.png "confiar")

3. Na secção Gestão confiar, sob **fornecedor de serviços locais**, execute os seguintes passos:

    ![Gestão de confiança](./media/sap-hana-cloud-platform-tutorial/ic793931.png "confiar gestão")
   
    a. Clique em **Editar**.

    b. Como **tipo de configuração**, selecione **personalizado**.

    c. Como **nome do fornecedor de Local**, deixe o valor predefinido. Copie este valor e cole-o para o **identificador** campo na configuração do AD do Azure para SAP Cloud Platform.

    d. Para gerar um **chave de assinatura** e uma **certificado de assinatura** par de chaves, clique em **gerar par de chave**.

    e. Como **propagação Principal**, selecione **desativado**.

    f. Como **autenticação de força**, selecione **desativado**.

    g. Clique em **Guardar**.

4. Depois de guardar o **fornecedor de serviços locais** definições, efetue o seguinte para obter o URL de resposta:
   
    ![Obter metadados](./media/sap-hana-cloud-platform-tutorial/ic793930.png "obter metadados")

    a. Transfira o ficheiro de metadados de SAP Cloud Platform clicando **obter metadados**.

    b. Abra o ficheiro XML de metadados SAP Cloud Platform transferido e, em seguida, localize a **ns3:AssertionConsumerService** marca.
 
    c. Copie o valor do **localização** atributo e, em seguida, cole-o para o **URL de resposta** campo na configuração do AD do Azure para SAP Cloud Platform.

5. Clique nas **fornecedor de identidade fidedignos** separador e, em seguida, clique em **Adicionar fornecedor de identidade fidedignos**.
   
    ![Gestão de confiança](./media/sap-hana-cloud-platform-tutorial/ic790802.png "confiar gestão")
   
    >[!NOTE]
    >Para gerir a lista de fornecedores de identidade fidedignos, terá de escolher o tipo de configuração personalizada na secção de fornecedor de serviços locais. Para o tipo de configuração padrão, tem uma confiança implícita e não editáveis para o serviço de ID de SAP. Para None, não tem quaisquer definições de confiança.
    > 
    > 

6. Clique nas **gerais** separador e, em seguida, clique em **procurar** para carregar o ficheiro de metadados baixado.
    
    ![Gestão de confiança](./media/sap-hana-cloud-platform-tutorial/ic793932.png "confiar gestão")
    
    >[!NOTE]
    >Depois de carregar o ficheiro de metadados, os valores para **URL de início de sessão único**, **URL de fim de sessão único**, e **certificado de assinatura** são preenchidos automaticamente.
    > 
     
7. Clique no separador **Atributos**.

8. Sobre o **atributos** separador, executar o passo seguinte:
    
    ![Atributos](./media/sap-hana-cloud-platform-tutorial/ic790804.png "atributos") 

    a. Clique em **Add Assertion-Based atributo**e, em seguida, adicione os seguintes atributos de asserção com base em:
       
    | Atributo de asserção | Atributo principal |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |FirstName |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Apelido |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-mail |
   
     >[!NOTE]
     >A configuração dos atributos depende de como as aplicações no SCP são desenvolvidas, ou seja, quais atributos esperam na resposta SAML e em que nome (atributo Principal) acederem este atributo no código.
     > 
    
    b. O **atributo predefinido** na captura de ecrã é apenas para fins de ilustração. Não é necessário para tornar o cenário de trabalho.  
 
    c. Os nomes e valores para **atributo Principal** mostrado na captura de ecrã dependem de como o aplicativo é desenvolvido. É possível que a sua aplicação requer mapeamentos diferentes.

### <a name="assertion-based-groups"></a>Grupos com base em asserção

Como passo opcional, pode configurar grupos com base em asserção para o seu fornecedor de identidade de diretório Active Directory do Azure.

Utilizar grupos na plataforma do SAP na Cloud permite-lhe atribuir dinamicamente um ou mais utilizadores a uma ou mais funções nas suas aplicações SAP Cloud Platform, determinadas pelos valores de atributos na asserção de SAML 2.0. 

Por exemplo, se a asserção contém o atributo "*contrato = temporário*", poderá pretender que os utilizadores as ser adicionado ao grupo"*temporário*". O grupo de "*temporária*" pode conter uma ou mais funções de uma ou mais aplicações implementadas na sua conta do SAP Cloud Platform.
 
Utilize grupos com base em asserção quando deseja atribuir simultaneamente muitos utilizadores a uma ou mais funções de aplicações na sua conta do SAP Cloud Platform. Se pretender atribuir apenas um único ou pequeno número de utilizadores a funções específicas, recomendamos que atribuí-las diretamente na "**autorizações**" Guia de cabine SAP Cloud Platform.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à plataforma de Cloud de SAP.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SAP Cloud Platform**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **SAP Cloud Platform**.

    ![A ligação de SAP Cloud Platform na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sap-cloud-platform-test-user"></a>Criar utilizador de teste de SAP Cloud Platform

Para habilitar os utilizadores do Azure AD iniciar sessão na plataforma Cloud do SAP, tem de atribuir funções na plataforma da Cloud de SAP aos mesmos.

**Para atribuir uma função a um utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **SAP Cloud Platform** cockpit.

2. Execute o seguinte:
   
    ![As autorizações](./media/sap-hana-cloud-platform-tutorial/ic790805.png "autorizações")
   
    a. Clique em **autorização**.

    b. Clique nas **utilizadores** separador.

    c. Na **utilizador** caixa de texto, escreva o endereço de e-mail do utilizador.

    d. Clique em **atribuir** atribuir o utilizador a uma função.

    e. Clique em **Guardar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de SAP Cloud Platform no painel de acesso, deve iniciar sessão automaticamente plataforma de Cloud do SAP para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

