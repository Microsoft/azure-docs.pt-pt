---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com a NetSuite [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a920e58f1ffd4c3e3e9769bf6346100a8677b90
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760062"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Tutorial: Integrar o Single Sign-on azure AD (SSO) com netSuite

Neste tutorial, você vai aprender a integrar a NetSuite com o Azure Ative Directory (Azure AD). Quando integrar a NetSuite com o Azure AD, pode:

* Controle em Azure AD que tem acesso à NetSuite.
* Ative que os seus utilizadores sejam automaticamente inscritos na NetSuite com as suas contas Azure AD.
* Gerencie as suas contas num local central, o portal Azure.

Para saber mais sobre a integração de apps saaS com a Azure AD, veja [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição ativada por um único sinal NetSuite (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. 

Suportes NetSuite:

* SSO iniciado por IDP.
* Fornecimento de utilizadores JIT (just-in-time).
* [Fornecimento automatizado de utilizadores.](NetSuite-provisioning-tutorial.md)
* Assim que configurar o NetSuite, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Como o identificador desta aplicação é um valor fixo de cadeia, apenas uma instância pode ser configurada num inquilino.

## <a name="add-netsuite-from-the-gallery"></a>Adicione netSuite da galeria

Para configurar a integração da NetSuite em Azure AD, adicione a NetSuite da galeria à sua lista de aplicações saaS geridas fazendo o seguinte:

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite netSuite** na caixa de pesquisa.
1. No painel de resultados, selecione **NetSuite**e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Configure e teste Azure AD single sign-on para NetSuite

Configure e teste Azure AD SSO com NetSuite utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na NetSuite.

Para configurar e testar o Azure AD SSO com a NetSuite, complete os seguintes blocos de construção:

1. [Configure O SSO AD Azure](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    * [Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user) para testar o único sinal de Azure AD com o utilizador B.Simon.  
    * [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir ao utilizador B.Simon utilizar um único sinal de AD Azure.
1. [Configure o NetSuite SSO](#configure-netsuite-sso) para configurar as definições de inscrição únicas no lado da aplicação.
    * [Crie o utilizador de teste NetSuite](#create-the-netsuite-test-user) para ter uma contraparte do utilizador B.Simon na NetSuite que esteja ligada à representação da AD Azure do utilizador.
1. [Teste sSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para permitir o Azure AD SSO no portal Azure, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **NetSuite,** procure a secção **Gerir** e, em seguida, selecione **o single sign-on**.
1. No Select um único painel de método de **sinalização,** selecione **SAML**.
1. No **set up Single Sign-On com painel SAML,** selecione o ícone **Editar** ("lápis") ao lado da **configuração Básica do SAML**.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração SAML Básica,** na caixa de texto **URL de resposta,** digite um URL num dos seguintes formatos:

    ||
    |-|
    | `https://<Account ID>.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.sandbox.NetSuite.com/saml2/acs`|

    * Você terá ** < `Account ID` ** o valor na secção de configuração Netsuite que é explicado mais tarde no tutorial no passo 8 sob configuração Netsuite. Encontrará o domínio exato (como system.na0.netsuite.com neste caso).

        ![Configurar o início de sessão único](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Os valores nos URLs anteriores não são reais. Atualize-os com o URL de Resposta real. Para obter o valor, contacte a equipa de suporte ao [Cliente NetSuite.](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) Também pode consultar os formatos mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação NetSuite espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação NetSuite espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo de origem |
    | ---------------| --------------- |
    | conta  | `account id` |

    > [!NOTE]
    > O valor do atributo da conta não é real. Você vai atualizar este valor, como explicado mais tarde neste tutorial.

1. Na configuração de um único sessão com a página SAML, na secção Certificado de Assinatura SAML, encontre metadados da Federação XML e selecione Descarregar para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do certificado](common/metadataxml.png)

1. Na secção Configurar a **NetSuite,** copie os URL ou URLs apropriados, dependendo da sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. No painel esquerdo do portal Azure,**Users** > selecione Utilizadores de **Diretório** > Ativo Azure**Todos os utilizadores**.

1. Selecione **Novo utilizador** na parte superior do ecrã.

1. No painel de propriedades do **Utilizador,** siga estes passos:

   a. Na caixa **de nomes,** insira **B.Simon.**  
   b. Na caixa de **nomes do Utilizador,** introduza a username@companydomain.extension (por exemplo, B.Simon@contoso.com).  
   c. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**  
   d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite ao utilizador B.Simon utilizar o único sign-on do Azure, concedendo acesso à NetSuite.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **NetSuite**.
1. No painel de visão geral, procure a secção **Gerir** e, em seguida, selecione o link **Utilizadores e grupos.**

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador** e, em seguida, no painel **adicionar atribuição,** selecione Utilizadores e **grupos**.

    ![O botão "Adicionar utilizador"](common/add-assign-user.png)

1. No painel **utilizadores e grupos,** na lista de lançamento seletiva dos **Utilizadores,** selecione **B.Simon**e, em seguida, selecione o botão **Select** na parte inferior do ecrã.
1. Se está à espera de algum valor de papel na afirmação da SAML, faça o seguinte:

   a. No painel **Select Role,** na lista de drop-down, selecione a função adequada para o utilizador.  
   b. Na parte inferior do ecrã, selecione o botão **Select.**
1. No painel **adicionar atribuição,** selecione o botão **Atribuir.**

## <a name="configure-netsuite-sso"></a>Configure NetSuite SSO

1. Abra um novo separador no seu navegador e inscreva-se no site da sua empresa NetSuite como administrador.

2. Na barra de navegação superior, selecione **Configuração**, e, em seguida, selecione**Funcionalidades de Ativação**da **Empresa** > .

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na barra de ferramentas no meio da página, selecione **SuiteCloud**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Em **'Autenticação de Gestão',** selecione a caixa **de verificação de sinal único SAML** para ativar a opção de inscrição única SAML na NetSuite.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na barra de navegação superior, **selecione Configurar**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

6. Na lista de Tarefas de **Configuração, selecione** **Integração**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-integration.png)

7. Em **'Autenticação de Gestão',** selecione **SAML Single Sign-on**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-saml.png)

8. No painel **de configuração SAML,** sob **configuração NetSuite,** faça o seguinte:

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selecione a caixa de verificação do método de **autenticação primária.**

    b. Em Dados de Fornecedor de **Identidade SAMLV2,** selecione **Upload IDP Metadata File**, e, em seguida, selecione **Browse** para carregar o ficheiro de metadados que descarregou do portal Azure.

    c. Selecione **Submeter**.

9. Na barra de navegação top NetSuite, selecione **Configuração**, e, em seguida, selecione **Informações** > da**Empresa**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-com.png)

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-account-id.png)

    b. No painel informativo da **empresa,** na coluna certa, copie o valor de ID da **conta.**

    c. Colhe o ID de **conta** que copiou da conta NetSuite para a caixa de valor do **atributo** em AD Azure.

10. Antes que os utilizadores possam realizar um único sessão no NetSuite, devem primeiro ser-lhes atribuídas as permissões adequadas no NetSuite. Para atribuir estas permissões, faça o seguinte:

    a. Na barra de navegação superior, **selecione Configurar**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

    b. No painel esquerdo, selecione **Utilizadores/Funções**e, em seguida, selecione **Funções de Gestão**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Selecione **Novo Papel**.

    d. Insira um **nome** para o novo papel.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-new-role.png)

    e. Selecione **Guardar**.

    f. Na barra de navegação superior, selecione **Permissões**. Em seguida, **selecione Configurar**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecione **SAML Single Sign-on**, e, em seguida, **selecione Adicionar**.

    h. Selecione **Guardar**.

    i. Na barra de navegação superior, **selecione Configurar**, e, em seguida, selecione **'Configurar ' Gestor**de Configuração .

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-setup.png)

    j. No painel esquerdo, selecione **Utilizadores/Funções**, e, em seguida, **selecione Gerir utilizadores**.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecione um utilizador de teste, selecione **Editar**e, em seguida, selecione o separador **Access.**

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-edit-user.png)

    l. No painel **Roles,** atribua o papel adequado que criou.

    ![Configurar o início de sessão único](./media/NetSuite-tutorial/ns-add-role.png)

    m. Selecione **Guardar**.

### <a name="create-the-netsuite-test-user"></a>Criar o utilizador de teste NetSuite

Nesta secção, um utilizador chamado B.Simon é criado na NetSuite. A NetSuite suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para ti nesta secção. Se um utilizador já não existir no NetSuite, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo NetSuite no Painel de Acesso, deve ser automaticamente inscrito na NetSuite para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Experimente netSuite com Azure AD](https://aad.portal.azure.com/)
- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a NetSuite com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)