---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Tecido de Segurança da Nuvem [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cloud Security Fabric.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 549e8810-1b3b-4351-bf4b-f07de98980d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0b1471abd7e057af919ed274547daf94d356c2b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "70213602"
---
# <a name="tutorial-integrate-the-cloud-security-fabric-with-azure-active-directory"></a>Tutorial: Integrar o tecido de segurança da nuvem com diretório ativo Azure

Neste tutorial, você vai aprender a integrar o Tecido de Segurança da Nuvem com o Diretório Ativo Azure (Azure AD). Quando integrar o Tecido de Segurança da Nuvem com AD Azure, pode:

* Controlo em Azure AD que tem acesso ao Tecido de Segurança da Nuvem.
* Ative que os seus utilizadores sejam automaticamente inscritos no Tecido de Segurança da Nuvem com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* O único sinal de inscrição (SSO) ativado pelo Cloud Security Fabric (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* O Tecido de Segurança da Nuvem suporta **SP** iniciado SSO

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Adicionando o Tecido de Segurança da Nuvem da galeria

Para configurar a integração do Tecido de Segurança da Nuvem em Azure AD, você precisa adicionar o Tecido de Segurança da Nuvem da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite o Tecido de **Segurança da Nuvem** na caixa de pesquisa.
1. Selecione o Tecido de **Segurança da Nuvem** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com o Tecido de Segurança da Nuvem utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Tecido de Segurança da Nuvem.

Para configurar e testar o Azure AD SSO com o Tecido de Segurança da Nuvem, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Cloud Security Fabric SSO](#configure-the-cloud-security-fabric-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador](#create-the-cloud-security-fabric-test-user)** de teste Cloud Security Fabric - para ter uma contrapartida de B.Simon no Tecido de Segurança da Nuvem que esteja ligado à representação do utilizador da AD Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Cloud Security Fabric,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    a. No **Sign on URL** text box, digite um URL:

    | |
    |--|
    | `https://platform.cloudlock.com` |
    | `https://app.cloudlock.com` |

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:

    | |
    |--|
    | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
    | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |

    > [!NOTE]
    > O valor do identificador não é real. Atualize o valor com o identificador real. Contacte a equipa de suporte do Cliente de [Tecido de Segurança Cloud](mailto:support@cloudlock.com) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

4. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

5. Para modificar as opções de **Assinatura** de acordo com o seu requisito, clique no botão **Editar** para abrir o diálogo do **Certificado de Assinatura SAML.**

    ![Resposta Saml](./media/ciscocloudlock-tutorial/saml.png)

    a. Selecione a opção de **resposta e afirmação Sign SAML** para **a Opção de Assinatura**.

    b. Selecione a opção **SHA-256** para **algoritmo de assinatura**.

    c. Clique em **Guardar**.  

6. Na **secção Configurar a** Secção cloud Security Fabric, copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-the-cloud-security-fabric-sso"></a>Configure o Tecido de Segurança da Nuvem SSO

Para configurar um único sinal no lado do Tecido de Segurança da **Nuvem,** você precisa enviar os metadados da **Federação XML** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte do Tecido](mailto:support@cloudlock.com)de Segurança da Nuvem . Eles definiram esta definição para ter a ligação SAML SSO corretamente definida em ambos os lados.
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Tecido de Segurança da Nuvem.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione O Tecido de **Segurança da Nuvem**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-the-cloud-security-fabric-test-user"></a>Criar o utilizador de teste cloud security fabric

Nesta secção, cria-se um utilizador chamado B.Simon no Tecido de Segurança da Nuvem. Trabalhe com a equipa de suporte do [Tecido de Segurança da Nuvem](mailto:support@cloudlock.com) para adicionar os utilizadores na plataforma Cloud Security Fabric. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo do Tecido de Segurança da Nuvem no Painel de Acesso, deve ser automaticamente inscrito no Tecido de Segurança da Nuvem para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
