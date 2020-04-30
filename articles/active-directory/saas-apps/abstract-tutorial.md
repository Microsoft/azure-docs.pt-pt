---
title: 'Tutorial: Integração do Diretório Ativo Azure com Resumo / Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo azure e o Abstract.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81886fa9165269d89bde8306c5829be41952c190
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68302593"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Tutorial: Integrar abstrato com diretório ativo azure

Neste tutorial, você vai aprender a integrar o Abstract com o Azure Ative Directory (Azure AD). Quando integrar o Abstract com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Resumo.
* Permita que os seus utilizadores sejam automaticamente inscritos no Abstract com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura abstrata de inscrição única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Resumo suporta **SP e IDP** iniciadoS SSO

## <a name="adding-abstract-from-the-gallery"></a>Adicionar Resumo da galeria

Para configurar a integração do Abstract em Azure AD, você precisa adicionar Abstract da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Resumo** na caixa de pesquisa.
1. Selecione **Resumo** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Resumo utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Abstract.

Para configurar e testar o Azure AD SSO com resumo, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Resumo SSO](#configure-abstract-sso)** - para configurar as definições de início de sessão simples no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Criar o utilizador de teste abstrato](#create-abstract-test-user)** - para ter uma contrapartida de Britta Simon em Resumo que esteja ligada à representação da AD Azure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **abstratas,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica saml,** a aplicação está pré-configurada no modo iniciado **idp** e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL:`https://app.abstract.com/signin`

4. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique no botão de cópia para copiar o Url de **Metadados da Federação** da Aplicação e guarde-o no seu computador.

    ![O link de descarregamento do Certificado](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Configure SSO abstrato

Certifique-se de `App Federation Metadata Url` recuperar `Azure AD Identifier` o seu e o portal Azure, pois você precisará deles para configurar SSO em Resumo.

Encontrará essas informações no **set up Single Sign-On com página SAML:**

* A `App Federation Metadata Url` secção de Certificado de Assinatura SAML está localizada na secção certificado de **assinatura SAML.**
* A `Azure AD Identifier` secção Resumo configurada está localizada na **secção set up Abstract.**


Está agora pronto para configurar o SSO em Resumo:

>[!Note]
>Você precisará autenticar com uma conta de administração da organização para aceder às definições SSO no Resumo.

1. Abra a [aplicação web Abstrato.](https://app.abstract.com/)
2. Vá para a página **de Permissões** na barra lateral esquerda.
3. Na secção **Configure SSO,** introduza o URL de **Metadados** e **id da entidade**.
4. Introduza quaisquer exceções manuais que possa ter. Os e-mails listados na secção de exceções manuais passarão pelo SSO e poderão iniciar sessão com e-mail e senha. 
5. Clique em **Guardar Alterações**.

>[!Note] 
>Terá de usar endereços de e-mail primários na lista de exceções manuais. A ativação do SSO falhará se o e-mail que lista for um e-mail secundário de um utilizador. Se isso acontecer, verá uma mensagem de erro com o e-mail principal para a conta falhada. Adicione esse e-mail primário à lista de exceções manuais depois de ter verificado que conhece o utilizador.

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso ao Abstract.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Abstract**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-abstract-test-user"></a>Criar utilizador de teste abstrato

Para testar o SSO em Resumo:

1. Abra a [aplicação web Abstrato.](https://app.abstract.com/)
2. Vá para a página **de Permissões** na barra lateral esquerda.
3. Clique em **Testar com a minha Conta**. Se o teste falhar, [contacte a nossa equipa](https://www.abstract.com/help/contact/)de suporte.

>[!Note]
>Você precisará autenticar com uma conta de administração da organização para aceder às definições SSO no Resumo.
Esta conta admin a organização terá de ser atribuída ao Abstract no portal Azure.

### <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Abstrato no Painel de Acesso, deve ser automaticamente inscrito no Resumo para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

