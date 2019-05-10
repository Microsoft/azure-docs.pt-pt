---
title: 'Tutorial: Integração do Active Directory do Azure com o webMethods Integration Suite | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o webMethods Integration Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 97261535-7a2d-4d73-94c8-38116b8a776e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 8dead16c60a26e2fc53953ed65337195c3b2aa67
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470797"
---
# <a name="tutorial-azure-active-directory-integration-with-webmethods-integration-suite"></a>Tutorial: Integração do Active Directory do Azure com o webMethods Integration Suite

Neste tutorial, saiba como integrar o webMethods Integration Suite no Azure Active Directory (Azure AD).
Integrar o webMethods Integration Suite no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao webMethods Integration Suite.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para webMethods Integration Suite (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o webMethods Integration Suite, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* webMethods Integration Suite único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* oferece suporte a Integration Suite webMethods **SP** e **IDP** iniciada SSO

* oferece suporte a Integration Suite webMethods **just-in-time** aprovisionamento de utilizadores

## <a name="adding-webmethods-integration-suite-from-the-gallery"></a>Adicionando o webMethods Integration Suite da Galeria

Para configurar a integração de webMethods Integration Suite para o Azure AD, terá de adicionar o webMethods Integration Suite a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o webMethods Integration Suite a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **webMethods Integration Suite**, selecione **webMethods Integration Suite** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![webMethods Integration Suite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com o webMethods Integration Suite com base num utilizador de teste chamado **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no webMethods Integration Suite tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o webMethods Integration Suite, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o webMethods Integration Suite início de sessão único](#configure-webmethods-integration-suite-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do pacote de integração de webMethods](#create-webmethods-integration-suite-test-user)**  - para ter um equivalente da Eduarda Almeida na webMethods Integration Suite, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o webMethods Integration Suite, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **webMethods Integration Suite** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Para configurar o **webMethods Cloud de integração**, na **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar os seguintes passos:

    ![informações de webMethods URLs de domínio do pacote de integração e início de sessão único](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `<SUBDOMAIN>.webmethodscloud.com` |
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoResponse` |
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoResponse` |
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoResponse` |

    c. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![informações de webMethods URLs de domínio do pacote de integração e início de sessão único](common/metadata-upload-additional-signon.png)

    d. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<SUBDOMAIN>.webmethodscloud.com/integration/live/saml/ssoRequest` |
    | `https://<SUBDOMAIN>.webmethodscloud.eu/integration/live/saml/ssoRequest` |
    | `https://<SUBDOMAIN>.webmethodscloud.de/integration/live/saml/ssoRequest` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de webMethods Integration Suite Client](https://empower.softwareag.com/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Para configurar o **webMethods Cloud de API**, na **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, executar o passos seguintes:

    ![informações de webMethods URLs de domínio do pacote de integração e início de sessão único](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `<SUBDOMAIN>.webmethodscloud.com` |
    | `<SUBDOMAIN>.webmethodscloud.eu` |
    | `<SUBDOMAIN>.webmethodscloud.de` |

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<SUBDOMAIN>.webmethodscloud.com/umc/rest/saml/initsso` |
    | `https://<SUBDOMAIN>.webmethodscloud.eu/umc/rest/saml/initsso` |
    | `https://<SUBDOMAIN>.webmethodscloud.de/umc/rest/saml/initsso` |

    c. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![informações de webMethods URLs de domínio do pacote de integração e início de sessão único](common/metadata-upload-additional-signon.png)

    d. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://api.webmethodscloud.com/umc/rest/saml/initsso/?tenant=<TENANTID>` |
    | `https://api.webmethodscloud.eu/umc/rest/saml/initsso/?tenant=<TENANTID>` |
    | `https://api.webmethodscloud.de/umc/rest/saml/initsso/?tenant=<TENANTID>` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de webMethods Integration Suite Client](https://empower.softwareag.com/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar o webMethods Integration Suite** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-webmethods-integration-suite-single-sign-on"></a>Configurar o webMethods Integration Suite início de sessão único

Para configurar o início de sessão único num **webMethods Integration Suite** lado, terá de enviar o transferido **XML de metadados de Federação** e adequadas copiados URLs a partir do portal do Azure para [webMethods Equipa de suporte do pacote de integração](https://empower.softwareag.com/). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos webMethods Integration Suite.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **webMethods Integration Suite**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **webMethods Integration Suite**.

    ![O webMethods ligação Integration Suite na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-webmethods-integration-suite-test-user"></a>Criar o webMethods utilizador de teste do pacote de integração

Nesta secção, um usuário chamado Eduarda Almeida é criado na webMethods Integration Suite. webMethods Integration Suite suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no webMethods Integration Suite, é criado um novo após a autenticação.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o webMethods mosaico Integration Suite no painel de acesso, deve ser automaticamente conectado com o pacote de integração de webMethods para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

