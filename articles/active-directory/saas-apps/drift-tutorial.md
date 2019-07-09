---
title: 'Tutorial: Integração do Active Directory do Azure com Descompassos | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e desvios.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 39dcbb95-c192-448c-86a1-cedede1c0972
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4728ad4fcd44c754a62ec19037562e63d92ec304
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656614"
---
# <a name="tutorial-integrate-drift-with-azure-active-directory"></a>Tutorial: Integrar Descompassos com o Azure Active Directory

Neste tutorial, irá aprender como integrar os Descompassos com o Azure Active Directory (Azure AD). Quando integrar Descompassos com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Descompassos.
* Permita que os utilizadores ser automaticamente sessão iniciada para desvios com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Descompassos início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta de inconsistências **SP e IDP** iniciou o SSO e **Just In Time** aprovisionamento de utilizadores.

## <a name="adding-drift-from-the-gallery"></a>Adicionando os desvios da Galeria

Para configurar a integração de desvios para o Azure AD, terá de adicionar Descompassos a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Descompassos** na caixa de pesquisa.
1. Selecione **Descompassos** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com Descompassos com um utilizador de teste **B. Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Descompassos.

Para configurar e testar o SSO do Azure AD com Descompassos, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar Descompassos](#configure-drift)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Simon B.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Simon B. utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Descompassos](#create-drift-test-user)**  para ter um equivalente de Simon B. no Descompassos que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Descompassos** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção, a aplicação está pré-configurada e os URLs necessários já estão previamente preenchidos com o Azure. O utilizador tem de guardar a configuração ao clicar o **guardar** botão e execute os seguintes passos:

    a. Clique em **definir URLs adicionais**.
 
    b. Na **estado de reencaminhamento** caixa de texto, escreva um URL: `https://app.drift.com` 

    c. Se desejar configurar a aplicação no **SP** iniciado do modo de executar o passo seguinte:

    d. Na **URL de início de sessão** caixa de texto, escreva um URL: `https://start.drift.com`

6. Seu aplicativo Descompassos espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **editar** ícone para abrir a caixa de diálogo de atributos do utilizador.

    ![image](common/edit-attribute.png)

7. Além disso, para acima, divergir aplicativo espera mais alguns atributos a serem passados na resposta SAML. Na secção de afirmações de utilizador na caixa de diálogo de atributos de utilizador, execute os seguintes passos para adicionar o atributo de token SAML como mostra a tabela a seguir: 

    | Name | Atributo de origem|
    | ---------------| --------------- |    
    | Name | user.displayname |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **XML de metadados de Federação** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Sobre o **configurar Descompassos** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-drift"></a>Configurar Descompassos

1. Para automatizar a configuração no Descompassos, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **Descompassos de configuração** irá direcioná-lo para a aplicação de desvios. A partir daí, forneça as credenciais de administrador a iniciar sessão em desvios. A extensão de navegador automaticamente irá configurar o aplicativo para e automatizar passos 3 a 4.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se quiser manualmente os Descompassos de configuração, abra uma nova janela de browser e inicie sessão no site da sua empresa Descompassos como administrador e execute os seguintes passos:

4. Do lado esquerdo da barra de menu, clique em **ícone de definições** > **definições de aplicação** > **autenticação** e execute os seguintes passos:

    ![A ligação de administrador](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Carregar o **XML de metadados de Federação** que transferiu do portal do Azure, para o **ficheiro de metadados de carregar o fornecedor de identidade** caixa de texto.

    b. Depois de carregar o ficheiro de metadados, os restantes valores obtém automaticamente preenchido automaticamente na página.

    c. Clique em **ativar SAML**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Simon B.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `B. Simon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Simon B. utilizar o Azure início de sessão único ao conceder acesso para desvios.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Descompassos**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B. Simon** a partir da lista de utilizadores, em seguida, clique no **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-drift-test-user"></a>Criar utilizador de teste de desvios

Nesta secção, um usuário chamado Eduarda Almeida é criado na Descompassos. Descompassos suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no Descompassos, é criado um novo após a autenticação.

>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de desvios](mailto:integrations@drift.com).

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico Descompassos no painel de acesso, deve ser automaticamente conectado para desvios para a qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)