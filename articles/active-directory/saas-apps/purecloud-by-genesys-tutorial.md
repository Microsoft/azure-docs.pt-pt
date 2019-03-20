---
title: 'Tutorial: Integração do Active Directory do Azure com PureCloud por Genesys | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e PureCloud por Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: d1cc7735b8caa952a5ab7695d3e1f35b03fec1bb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890683"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>Tutorial: Integração do Active Directory do Azure com PureCloud por Genesys

Neste tutorial, saiba como integrar PureCloud por Genesys com o Azure Active Directory (Azure AD).
Integrar PureCloud por Genesys no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao PureCloud por Genesys.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para PureCloud por Genesys (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com PureCloud por Genesys, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição de ativado PureCloud por Genesys início de sessão único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta PureCloud por Genesys **SP e IDP** iniciada SSO

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Adicionando PureCloud por Genesys da Galeria

Para configurar a integração do PureCloud por Genesys com o Azure AD, terá de adicionar PureCloud por Genesys a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar PureCloud por Genesys a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **PureCloud por Genesys**, selecione **PureCloud por Genesys** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![PureCloud por Genesys na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com PureCloud por Genesys com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no PureCloud por Genesys deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com PureCloud por Genesys, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar PureCloud por Genesys início de sessão único](#configure-purecloud-by-genesys-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar PureCloud por utilizador de teste Genesys](#create-purecloud-by-genesys-test-user)**  - para ter um equivalente da Eduarda Almeida na PureCloud por Genesys que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com PureCloud por Genesys, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **PureCloud por Genesys** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    ![PureCloud por Genesys domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL de acordo com a sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Na **URL de resposta** caixa de texto, escreva um URL de acordo com a sua região:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![PureCloud por Genesys domínio e URLs únicas início de sessão em informações](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL de acordo com a sua região:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. PureCloud pelo aplicativo Genesys espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos do utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. Além disso, anteriormente, PureCloud pelo aplicativo Genesys espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | Email | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

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

9. Sobre o **configure PureCloud, Genesys** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>Configurar PureCloud por Genesys Single Sign-On

1. Numa janela do browser web diferente, início de sessão para PureCloud por Genesys como administrador.

2. Clique em **administrador** na parte superior e navegue até à **início de sessão único** sob **integrações**.

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure01.png)

3. Mude para o **ADFS/Azure AD(Premium)** separador e execute os seguintes passos:

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Clique em **navegue** carregar a base 64 codificada certificado que transferiu do portal do Azure, para o **ADFS certificado**.

    b. Na **URI de emissor de ADFS** caixa de texto, cole o valor de **do Azure AD identificador** que copiou do portal do Azure.

    c. Na **URI de destino** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    d. Para **identificador da entidade Confiadora** valor, tem de aceder ao portal do Azure, no **PureCloud por Genesys** página de integração de aplicação, clique em **propriedades** separador e cópia o **ID da aplicação** valor. Cole-a no **identificador da entidade Confiadora** caixa de texto. 

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Clicar em **Guardar**   

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para PureCloud por Genesys.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **PureCloud por Genesys**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **PureCloud por Genesys**.

    ![PureCloud por Genesys link na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-purecloud-by-genesys-test-user"></a>Criar PureCloud por utilizador de teste Genesys

Para ativar a utilizadores do Azure AD iniciar sessão no PureCloud por Genesys, eles têm de ser aprovisionados em PureCloud por Genesys. PureCloud por Genesys, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no PureCloud por Genesys como administrador.

2. Clique em **administrador** na parte superior e navegue até à **pessoas** sob **pessoas e as permissões**.

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure03.png)

3. Na página de pessoas, clique em **pessoa adicionar**.

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure04.png)

4. Sobre o **Adicione pessoas à organização** pop-up, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Na **FullName** texto, introduza o nome de utilizador, como **Brittasimon**.

    b. Na **E-Mail** texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.
    
    c. Clique em **Criar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o PureCloud por Genesys mosaico no painel de acesso, deve ser automaticamente sessão iniciada no PureCloud por Genesys para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

