---
title: 'Tutorial: Integração do Active Directory do Azure com HighGear | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e HighGear.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61010399f6f2f5b87c1aa5e1c342a97e6b34bb45
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765523"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Tutorial: Integração do Active Directory do Azure com HighGear

Neste tutorial, pode saber como integrar HighGear com o Azure Active Directory (Azure AD).
Integrar HighGear no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao HighGear.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para HighGear (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com HighGear, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Um sistema de HighGear com uma licença do Enterprise ou ilimitado

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, pode aprender a configurar e testar o Azure AD início de sessão único num ambiente de teste.

* Suporta HighGear **SP e IdP** iniciada SSO

## <a name="adding-highgear-from-the-gallery"></a>Adicionando HighGear da Galeria

Para configurar a integração do HighGear com o Azure AD, terá de adicionar HighGear a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar HighGear a partir da galeria, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **HighGear**, selecione **HighGear** do painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

     ![HighGear na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, pode aprender a configurar e testar o Azure AD início de sessão único com o seu sistema HighGear com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no seu sistema HighGear deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o seu sistema HighGear, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar HighGear Single Sign-On](#configure-highgear-single-sign-on)**  - para configurar as definições de início de sessão único no lado de aplicativo HighGear.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste HighGear](#create-highgear-test-user)**  - para ter um equivalente da Eduarda Almeida na HighGear que está ligado a representação do Azure AD do utilizador. 
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode saber como ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o seu sistema HighGear, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **HighGear** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Na **definir a segurança de início de sessão único com o SAML** página, clique no **editar** ícone para abrir o **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![HighGear domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** texto caixa, cole o valor da **ID de entidade do fornecedor de serviço** campo que está na página de definições de início de sessão único no seu sistema HighGear.

    ![O campo de ID de entidade do fornecedor de serviço](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Terá de iniciar sessão no seu sistema de HighGear para acessar a página de definições de início de sessão único. Assim que está conectado, mova o rato sobre a guia de administração no HighGear e clique no item de menu de definições de início de sessão único.
    
    ![O item de menu de definições de início de sessão único](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. Na **URL de resposta** texto caixa, cole o valor da **URL de serviço de consumidor de asserção (ACS)** partir da página de definições de início de sessão único no seu sistema HighGear.

    ![O campo de URL do serviço de consumidor de asserção (ACS)](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

     ![HighGear domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

     Na **URL de início de sessão** texto caixa, cole o valor da **ID de entidade do fornecedor de serviço** campo que está na página de definições de início de sessão único no seu sistema HighGear. (Este ID de entidade é também o URL de base do sistema HighGear que está a ser utilizado para iniciado por SP início de sessão.)

    ![O campo de ID de entidade do fornecedor de serviço](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real do **definições de início de sessão único** página no seu sistema HighGear. Se precisar de ajuda, entre em contato com o [equipa de suporte de HighGear](mailto:support@highgear.com).

4. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** e guarde-o no seu computador. Precisará das mesmas num passo posterior da configuração do início de sessão único.

    ![O link de download de certificado](common/certificatebase64.png)

6. Sobre o **configurar HighGear** secção, tenha em atenção a localização dos seguintes URLs.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão. Este valor no passo n. º 2 em será necessário **configurar HighGear Single Sign-On** abaixo.

    b. Azure AD Identifier. Este valor no passo 3 em será necessário **configurar HighGear Single Sign-On** abaixo.

    c. URL de fim de sessão. Precisará este valor na etapa 4 sob **configurar HighGear Single Sign-On** abaixo.

### <a name="configure-highgear-single-sign-on"></a>Configurar HighGear Single Sign-On

Para configurar HighGear para início de sessão único, inicie sessão seu sistema HighGear. Assim que está conectado, mova o rato sobre a guia de administração no HighGear e clique no item de menu de definições de início de sessão único.

![O item de menu de definições de início de sessão único](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. Na **nome do fornecedor de identidade**, escreva uma breve descrição que será apresentado no início de sessão único botão do HighGear na página de início de sessão. Por exemplo: Azure AD

2. No **único início de sessão (SSO) URL** pole Je uvedeno HighGear, cole o valor do **URL de início de sessão** campo que está no **configurar HighGear** secção no Azure.

3. Na **ID de entidade do fornecedor de identidade** pole Je uvedeno HighGear, cole o valor do **do Azure AD identificador** campo que está no **configurar HighGear** secção no Azure.

4. Na **URL de fim de sessão único (SLO)** pole Je uvedeno HighGear, cole o valor do **URL de fim de sessão** campo que está no **configurar HighGear** secção no Azure.

5. Utilize o bloco de notas para abrir o certificado que transferiu a partir da **certificado de assinatura SAML** secção no Azure. Deve ter transferido o **certificado (Base64)** formato. Copiar o conteúdo do certificado de bloco de notas e cole-o para o **certificado do fornecedor de identidade** na HighGear.

6. E-mail a [equipa de suporte de HighGear](mailto:support@highgear.com) para pedir o certificado de HighGear. Siga as instruções que recebe dos mesmos para preencher os **certificado HighGear** e **palavra-passe de certificado de HighGear** campos.

7. Clique nas **guardar** botão para guardar a configuração HighGear Single Sign-On.

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

    c. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para HighGear.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **HighGear**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **HighGear**.

    ![A ligação de HighGear na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-highgear-test-user"></a>Criar utilizador de teste HighGear

Para criar um utilizador de teste para testar a configuração de início de sessão único, inicie sessão no seu sistema HighGear HighGear.

1. Clique nas **criar novo contacto** botão.

    ![O botão Criar novo contacto](media/highgear-tutorial/create-new-contact-button.png)

    Um menu aparecerá permitindo-lhe escolher o tipo de contacto que pretende criar.

2. Clique nas **individuais** item de menu para criar um utilizador HighGear.

    Um painel deslizam à direita, para que pode digitar as informações para o novo utilizador.  
    ![O formulário de novo contacto](media/highgear-tutorial/new-contact-form.png)

3. Na **nome** , digite um nome para o contacto. Por exemplo: Britta Simon

4. Clique nas **mais opções** menu e selecione o **informações da conta** item de menu.

    ![Clicar no item de menu de informações da conta](media/highgear-tutorial/account-info-menu-item.png)

5. Definir o **podem Log de** campo como Yes.

    O **ativar o início de sessão único** campo será automaticamente definido como Yes também.

6. Na **único Id de início de sessão utilizador** , digite a id do utilizador. Por exemplo: BrittaSimon@contoso.com

    A secção de informações da conta deve agora ter um aspeto semelhante ao seguinte:  
    ![A secção de informações da conta foi concluída](media/highgear-tutorial/finished-account-info-section.png)

7. Para guardar o contacto, clique a **guardar** na parte inferior do painel.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico HighGear no painel de acesso, deve ser automaticamente sessão iniciada no HighGear para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

