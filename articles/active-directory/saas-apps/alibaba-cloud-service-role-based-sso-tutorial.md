---
title: 'Tutorial: Integração do Active Directory do Azure com o serviço de Cloud Alibaba (SSO baseado em funções) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o serviço de Cloud Alibaba (SSO baseado em funções).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b03dfa5a33850dfedf23375536278c4e08bed68
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687152"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Tutorial: Integração do Active Directory do Azure com o serviço de Cloud Alibaba (SSO baseado em funções)

Neste tutorial, saiba como integrar o serviço de Cloud Alibaba (SSO baseado em funções) no Azure Active Directory (Azure AD).
Integrar o serviço de Cloud Alibaba (SSO baseado em funções) no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao serviço de Cloud Alibaba (SSO baseado em funções).
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o serviço de Cloud de Alibaba (SSO baseado em funções) (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o serviço de Cloud Alibaba (SSO baseado em funções), terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter um [conta gratuita](https://azure.microsoft.com/free/)
* Serviço de Cloud Alibaba (SSO baseado em funções) início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Serviço de nuvem Alibaba (SSO baseado em funções) suporta **IDP** iniciada SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Adicionar serviço de nuvem de Alibaba (baseado em funções SSO) partir da Galeria

Para configurar a integração do serviço em nuvem do Alibaba (SSO baseado em funções) no Azure AD, terá de Adicionar serviço de Cloud Alibaba (baseado em funções SSO) a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar serviço de Cloud Alibaba (baseado em funções SSO) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Alibaba Cloud Service (SSO baseado em funções)**, selecione **Alibaba Cloud Service (SSO baseado em funções)** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

    ![Serviço de Cloud Alibaba (SSO baseado em funções) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com o serviço de Cloud Alibaba (baseado em funções SSO) com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no serviço de Cloud Alibaba (SSO baseado em funções) deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o serviço de Cloud Alibaba (SSO baseado em funções), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o serviço de Cloud Alibaba (SSO baseado em funções) início de sessão único](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do serviço de Cloud Alibaba (SSO baseado em funções)](#create-alibaba-cloud-service-role-based-sso-test-user)**  - para ter um equivalente da Eduarda Almeida no serviço de Cloud de Alibaba (SSO baseado em funções) que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o serviço de Cloud Alibaba (SSO baseado em funções), execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Alibaba Cloud Service (SSO baseado em funções)** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![URLs de domínio do serviço de nuvem Alibaba (SSO baseado em funções) e únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** texto, escreva qualquer do URL:
    
    | |
    |--|
    | `urn:alibaba:cloudcomputing` |
    | `urn:alibaba:cloudcomputing:international` |

    b. Na **URL de resposta** texto, escreva qualquer do URL:

    | |
    |--|
    | `https://signin.aliyun.com/saml-role/SSO` |
    | `https://signin.alibabacloud.com/saml-role/SSO` |

5. Aplicação de serviço de nuvem Alibaba (SSO baseado em funções) espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. Além dos acima, a aplicação de serviço de nuvem Alibaba (SSO baseado em funções) espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | Função | user.assignedroles |
    | RoleSessionName | user.mail |

    > [!NOTE]
    > Clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) saber como configurar **função** no Azure AD

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

8. Sobre o **configurar o serviço de nuvem de Alibaba (SSO baseado em funções)** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Configurar Alibaba Cloud Service (SSO baseado em funções) início de sessão único

Para configurar o início de sessão único num **Alibaba Cloud Service (SSO baseado em funções)** lado, terá de enviar o transferido **XML de metadados de Federação** e copiados URLs partir do portal do Azure para [ Equipa de suporte do serviço de Cloud Alibaba (SSO baseado em funções)](https://www.aliyun.com/service/). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao serviço de nuvem Alibaba (SSO baseado em funções).

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Alibaba Cloud Service (SSO baseado em funções)**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Alibaba Cloud Service (SSO baseado em funções)**.

    ![A ligação de serviço de Cloud Alibaba (SSO baseado em funções) na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Criar utilizador de teste do serviço de Cloud Alibaba (SSO baseado em funções)

Nesta secção, vai criar um usuário chamado Eduarda Almeida no serviço de Cloud Alibaba (SSO baseado em funções). Trabalhar com [equipa de suporte do serviço de Cloud Alibaba (SSO baseado em funções)](https://www.aliyun.com/service/) para adicionar os utilizadores na plataforma do serviço de Cloud Alibaba (SSO baseado em funções). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do serviço de Cloud Alibaba (SSO baseado em funções) no painel de acesso, deve ser automaticamente conectado para o serviço de nuvem Alibaba (SSO baseado em funções) para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

