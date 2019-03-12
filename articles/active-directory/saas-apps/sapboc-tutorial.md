---
title: 'Tutorial: Integração do Active Directory do Azure com a nuvem de objeto do SAP Business | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e na Cloud de objeto do SAP Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7af211710d6b1cce2b06999f9d15882b163dbf60
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766382"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Tutorial: Integração do Active Directory do Azure com o SAP Cloud de objeto de negócio

Neste tutorial, saiba como integrar a Cloud do SAP Business objeto com o Azure Active Directory (Azure AD).
Integrar a Cloud de objeto de negócios SAP no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso à nuvem de objeto do SAP Business.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para a Cloud de objeto do SAP Business (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SAP Cloud de objeto de negócios, precisa do seguinte:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SAP na Cloud do objeto de negócios logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Cloud de objeto do SAP Business suporta **SP** iniciada SSO

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Adicionando o SAP Cloud de objeto de negócio da Galeria

Para configurar a integração da Cloud de objeto de negócios SAP no Azure AD, terá de adicionar SAP Cloud de objeto comercial a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SAP Cloud de objeto comercial a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **na Cloud de objeto do SAP Business**, selecione **Cloud de objeto do SAP Business** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![SAP Business objeto na Cloud, na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o SAP Cloud de objeto de negócios com base num utilizador de teste **Eduarda Almeida**.
Para início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Cloud do SAP Business objeto tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o SAP Cloud de objeto de negócio, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SAP Business objeto Cloud início de sessão único](#configure-sap-business-object-cloud-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste na Cloud de objeto do SAP Business](#create-sap-business-object-cloud-test-user)**  - para ter um equivalente da Eduarda Almeida na Cloud de objeto de negócios SAP que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SAP Business objeto Cloud, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Cloud de objeto do SAP Business** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![URLs de domínio na Cloud do SAP Business objeto e únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Os valores estes URLs são para apenas demonstração. Atualize os valores com o URL de início de sessão real e o URL de identificador. Para obter o URL de início de sessão, entre em contato com o [equipa de suporte de cliente de Cloud do SAP Business objeto](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Pode obter o URL de identificador ao transferir os metadados de nuvem de objeto de negócios SAP a partir da consola de administrador. Isso é explicado mais tarde no tutorial.

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>Configurar o SAP Business objeto Cloud início de sessão único

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa na Cloud do SAP Business objeto como um administrador.

2. Selecione **Menu** > **sistema** > **administração**.
    
    ![Selecione o Menu, em seguida, sistema e, em seguida, administração](./media/sapboc-tutorial/config1.png)

3. Sobre o **Security** separador, selecione a **editar** ícone (caneta).
    
    ![Na guia segurança, selecione o ícone de edição](./media/sapboc-tutorial/config2.png)  

4. Para **método de autenticação**, selecione **SAML único início de sessão (SSO)**.

    ![Selecione SAML início de sessão único para o método de autenticação](./media/sapboc-tutorial/config3.png)  

5. Para transferir os metadados do fornecedor de serviço (passo 1), selecione **transferir**. No ficheiro de metadados, localizar e copiar o **entityID** valor. No portal do Azure, no **configuração básica de SAML** caixa de diálogo, cole o valor no **identificador** caixa.

    ![Copie e cole o valor de entityID](./media/sapboc-tutorial/config4.png)  

6. Para carregar os metadados do fornecedor de serviço (etapa 2) no ficheiro que transferiu a partir do portal do Azure, em **metadados de carregar o fornecedor de identidade**, selecione **carregar**.  

    ![Em metadados de carregar o fornecedor de identidade, selecionar o carregamento](./media/sapboc-tutorial/config5.png)

7. Na **atributo de utilizador** , selecione o atributo de utilizador (etapa 3) que pretende utilizar para a sua implementação. Este atributo de utilizador é mapeado para o fornecedor de identidade. Para introduzir um atributo personalizado na página do utilizador, utilize o **mapeamento de SAML personalizada** opção. Em alternativa, pode selecionar **E-Mail** ou **ID de utilizador** como o atributo de utilizador. No nosso exemplo, selecionamos **E-Mail** porque estamos mapeado a afirmação de identificador de utilizador com o **userprincipalname** atributo o **atributos de utilizador e afirmações** secção o portal do Azure. Esta opção fornece um e-mail de utilizador exclusivo, que é enviado para a aplicação na Cloud de objeto do SAP Business em cada resposta SAML com êxito.

    ![Selecione o atributo de utilizador](./media/sapboc-tutorial/config6.png)

8. Para verificar a conta com o fornecedor de identidade (etapa 4), na **credenciais de início de sessão (E-Mail)** , introduza o endereço de e-mail do utilizador. Em seguida, selecione **Verifique se conta**. O sistema adiciona as credenciais de início de sessão para a conta de utilizador.

    ![Introduza o e-mail e selecionar verificar conta](./media/sapboc-tutorial/config7.png)

9. Selecione o **guardar** ícone.

    ![Ícone de guardar](./media/sapboc-tutorial/save.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso à nuvem de objeto do SAP Business.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **na Cloud de objeto do SAP Business**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SAP Business objeto Cloud**.

    ![A ligação de Cloud do SAP Business objeto na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sap-business-object-cloud-test-user"></a>Criar utilizador de teste na Cloud de objeto do SAP Business

Utilizadores do Azure AD tem de ser aprovisionados na Cloud do SAP Business objeto antes de poderem iniciar sessão na cloud de objeto do SAP Business. Na Cloud de objeto de negócios SAP, o aprovisionamento é uma tarefa manual.

Para Aprovisionar uma conta de utilizador:

1. Inicie sessão no site da sua empresa na Cloud do SAP Business objeto como um administrador.

2. Selecione **Menu** > **segurança** > **utilizadores**.

    ![Adicionar o funcionário](./media/sapboc-tutorial/user1.png)

3. Sobre o **usuários** página, para adicionar novos detalhes de utilizador, selecione **+**. 

    ![Adicionar página usuários](./media/sapboc-tutorial/user4.png)

    Em seguida, conclua os seguintes passos:

    a. Na **ID de utilizador** , introduza o ID de utilizador do utilizador, como **Eduarda**.

    b. Na **nome próprio** , introduza o nome do utilizador, como **Eduarda**.

    c. Na **SOBRENOME** , introduza o apelido do utilizador, como **Simon**.

    d. Na **nome a apresentar** , introduza o nome completo do utilizador, como **Eduarda Almeida**.

    e. Na **email** , introduza o endereço de e-mail do utilizador, como **brittasimon@contoso.com**.

    f. Sobre o **selecionar funções** página, selecione a função adequada para o utilizador e, em seguida, selecione **OK**.

      ![Selecionar função](./media/sapboc-tutorial/user3.png)

    g. Selecione o **guardar** ícone.    

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de Cloud do SAP Business objeto no painel de acesso, deve ser automaticamente conectado para a Cloud de objeto de negócios SAP para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

