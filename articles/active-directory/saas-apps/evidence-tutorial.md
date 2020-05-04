---
title: 'Tutorial: Integração do Diretório Ativo Azure com Evidence.com Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e Evidence.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f9a7cb7c-ff67-40dc-872c-1fa35f9dd03b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b2e04c49b05173c6eef27da30c96e2fd8b40b5d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734693"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evidencecom"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com Evidence.com

Neste tutorial, aprenderá a integrar Evidence.com com o Azure Ative Directory (Azure AD). Quando integrar Evidence.com com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Evidence.com.
* Permita que os seus utilizadores sejam automaticamente inscritos para Evidence.com com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Evidence.com única subscrição ativada por sessão (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Evidence.com apoia **SP** iniciado SSO
* Assim que configurar Evidence.com pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)da sessão com o Microsoft Cloud App Security .

## <a name="adding-evidencecom-from-the-gallery"></a>Adicionando Evidence.com da galeria

Para configurar a integração de Evidence.com em Azure AD, precisa adicionar Evidence.com da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **escreva Evidence.com** na caixa de pesquisa.
1. Selecione **Evidence.com** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evidencecom"></a>Configure e teste azure AD único signo para Evidence.com

Configure e teste Azure AD SSO com Evidence.com utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Evidence.com.

Para configurar e testar o Azure AD SSO com Evidence.com, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure Evidence.com SSO](#configure-evidencecom-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie Evidence.com utilizador](#create-evidencecom-test-user)** de teste - para ter uma contraparte de B.Simon em Evidence.com que esteja ligado à representação da AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Evidence.com,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. No **set -up single sign-on com** a página SAML, execute os seguintes passos:

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<yourtenant>.evidence.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<yourtenant>.evidence.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<your tenant>.evidence.com/?class=UIX&proc=Login`
    
    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL, Identifier e URL de Resposta real. Contacte [Evidence.com equipa](https://communities.taser.com/support/SupportContactUs?typ=LE) de apoio ao Cliente para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar Evidence.com,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso a Evidence.com.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Evidence.com**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-evidencecom-sso"></a>Configure Evidence.com SSO

1. Numa janela separada do navegador web, inscreva-se no seu Evidence.com inquilino como administrador e navegue para **O Separador Admin**

2. Clique no **sinal único** da agência

3. Selecione **SAML Based Single Sign On**

4. Copie os valores de URL do **Identificador Azure AD,** **URL de login** e **logout** mostrados no portal Azure e nos campos correspondentes em Evidence.com.

5. Abra o ficheiro Certificado descarregado (Base64) no bloco de notas, copie o conteúdo do mesmo na sua área de transferência e, em seguida, cole-o na caixa de Certificado de **Segurança.** 

6. Guarde a configuração em Evidence.com.

### <a name="create-evidencecom-test-user"></a>Criar Evidence.com utilizador de teste

Para que os utilizadores da AD Azure possam iniciar o seu acesso, devem ser provisionados para acesso no interior da aplicação Evidence.com. Esta secção descreve como criar contas de utilizadores da AD Azure dentro Evidence.com

**Para fornecer uma conta de utilizador em Evidence.com:**

1. Numa janela do navegador web, inscreva-se no site da sua empresa Evidence.com como administrador.

2. Navegue para o separador **Admin.**

3. Clique em **Adicionar Utilizador**.

4. Clique no botão **Adicionar**.

5. O Endereço de **E-mail** do utilizador adicionado deve coincidir com o nome de utilizador dos utilizadores em Azure AD a quem pretende dar acesso. Se o nome de utilizador e o endereço de e-mail não tiverem o mesmo valor na sua organização, pode utilizar a **Evidence.com > Atributos >** secção de entrada única do portal Azure para alterar o nomeidenitifer enviado para Evidence.com ser o endereço de e-mail.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Evidence.com no Painel de Acesso, deve ser automaticamente inscrito no Evidence.com para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente Evidence.com com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger Evidence.com com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

