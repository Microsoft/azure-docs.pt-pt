---
title: 'Tutorial: Integração do Active Directory do Azure com Ceridian Dayforce HCM | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Ceridian Dayforce HCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7adf1eb3-d063-45d6-96a8-fd53b329b3f3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79741d9ec71dd7735ef94ef8172921965a9e9df1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "65863777"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: Integração do Active Directory do Azure com Ceridian Dayforce HCM

Neste tutorial, saiba como integrar Ceridian Dayforce HCM com o Azure Active Directory (Azure AD).
Integrar Ceridian Dayforce HCM no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Ceridian Dayforce HCM.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Ceridian Dayforce HCM (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Ceridian Dayforce HCM, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Ceridian Dayforce HCM logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Ceridian Dayforce HCM **SP** iniciada SSO

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Adicionando Ceridian Dayforce HCM da Galeria

Para configurar a integração do Ceridian Dayforce HCM com o Azure AD, terá de adicionar Ceridian Dayforce HCM a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Ceridian Dayforce HCM a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Ceridian Dayforce HCM**, selecione **Ceridian Dayforce HCM** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Ceridian Dayforce HCM na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Ceridian Dayforce HCM com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Ceridian Dayforce HCM deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Ceridian Dayforce HCM, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Ceridian Dayforce HCM início de sessão único](#configure-ceridian-dayforce-hcm-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Ceridian Dayforce HCM](#create-ceridian-dayforce-hcm-test-user)**  - para ter um equivalente da Eduarda Almeida na Ceridian Dayforce HCM que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Ceridian Dayforce HCM, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Ceridian Dayforce HCM** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Ceridian Dayforce HCM domínio e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva o URL utilizado pelos seus utilizadores para início de sessão na sua aplicação Ceridian Dayforce HCM.

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Para teste | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Para teste | `https://fs-test.dayforcehcm.com/sp` |

    c. Na **URL de resposta** caixa de texto, escreva o URL é utilizado pelo Azure AD para publicar a resposta.

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Para teste | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão, identificador e o URL de resposta real. Contacte [equipa de suporte de cliente de HCM Ceridian Dayforce](https://www.ceridian.com/support) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação de Ceridian Dayforce HCM espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Name | Atributo de origem|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** , selecione o atributo de utilizador que pretende utilizar para a sua implementação. Por exemplo, se pretender utilizar o campo IDdeEmpregado como identificador de utilizador exclusivo e armazenou o valor do atributo a ExtensionAttribute2, em seguida, selecione user.extensionattribute2.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **metadados XML**entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

8. Sobre o **configurar Ceridian Dayforce HCM** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-ceridian-dayforce-hcm-single-sign-on"></a>Configurar Ceridian Dayforce HCM início de sessão único

Para configurar o início de sessão único num **Ceridian Dayforce HCM** lado, terá de enviar o transferido **XML de metadados** e adequadas copiados URLs a partir do portal do Azure para [suporte Ceridian Dayforce HCM equipe](https://www.ceridian.com/support). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Ceridian Dayforce HCM.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Ceridian Dayforce HCM**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Ceridian Dayforce HCM**.

    ![A ligação de Ceridian Dayforce HCM na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Criar utilizador de teste Ceridian Dayforce HCM

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Ceridian Dayforce HCM. Trabalhar com [equipa de suporte de Ceridian Dayforce HCM](https://www.ceridian.com/support) para adicionar os utilizadores na plataforma Ceridian Dayforce HCM. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Ceridian Dayforce HCM no painel de acesso, deve ser automaticamente sessão iniciada no HCM Dayforce Ceridian para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

