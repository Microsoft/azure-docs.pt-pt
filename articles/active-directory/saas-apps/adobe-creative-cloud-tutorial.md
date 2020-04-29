---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com a Adobe Creative Cloud [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25dd638c15fecbef787e4ceabea9ae7cb4359582
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76120371"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Tutorial: Azure Ative Directory integração individual (SSO) com Adobe Creative Cloud

> [!NOTE]
> Este artigo descreve a configuração personalizada da Adobe Admin Console para o Azure Ative Directory (Azure AD). Para novas configurações, recomendamos que utilize o [Conector AD Azure](https://helpx.adobe.com/enterprise/using/sso-setup-azure.html). O Conector Azure AD pode ser configurado em minutos e encurta o processo de reclamação de domínio, configuração de inscrição única e sincronização do utilizador.

Neste tutorial, você vai aprender a integrar a Adobe Creative Cloud com o Azure Ative Directory (Azure AD). Quando integrar a Adobe Creative Cloud com o Azure AD, pode:

* Controle em Azure AD que tem acesso a Adobe Creative Cloud.
* Ative que os seus utilizadores sejam automaticamente inscritos na Adobe Creative Cloud com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Aadobe Creative Cloud single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Adobe Creative Cloud suporta **SP** iniciado SSO





## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Adicionando Adobe Creative Cloud da galeria

Para configurar a integração da Adobe Creative Cloud em Azure AD, você precisa adicionar Adobe Creative Cloud da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite **Adobe Creative Cloud** na caixa de pesquisa.
1. Selecione **Adobe Creative Cloud** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-adobe-creative-cloud"></a>Configure e teste Azure AD single sign-on para Adobe Creative Cloud

Configure e teste Azure AD SSO com Adobe Creative Cloud utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Adobe Creative Cloud.

Para configurar e testar o Azure AD SSO com a Adobe Creative Cloud, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure O SSO de Nuvem Criativa adobe](#configure-adobe-creative-cloud-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador de teste Adobe Creative Cloud](#create-adobe-creative-cloud-test-user)** - para ter uma contrapartida de B.Simon na Adobe Creative Cloud que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da **Adobe Creative Cloud,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** introduza os valores para os seguintes campos:

    a. No **Sign on URL** text box, digite um URL:`https://adobe.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > O valor do identificador não é real. Atualize este valor com o identificador real. Contacte a equipa de [suporte ao Cliente Creative Cloud da Adobe](https://www.adobe.com/au/creativecloud/business/teams/plans.html) para obter este valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. A aplicação Adobe Creative Cloud espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Adobe Creative Cloud espera que poucos atributos sejam passados na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Nome | Atributo fonte|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | Email | utilizador.mail |

    > [!NOTE]
    > Os utilizadores precisam de ter uma licença ExO do Office 365 válida para o valor da reclamação de e-mail para ser preenchido na resposta SAML.

1. Na configuração de um único registo com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre dados da **Federação XML,** e, em seguida, selecione **Descarregar** para descarregar o ficheiro de metadados XML e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção **Configurar a Nuvem Criativa adobe,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use o único sign-on Azure, concedendo acesso à Adobe Creative Cloud.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Adobe Creative Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-adobe-creative-cloud-sso"></a>Configure Adobe Creative Cloud SSO

1. Numa janela de navegador web diferente, inscreva-se na [Adobe Admin Console](https://adminconsole.adobe.com) como administrador do sistema.

1. Vá a **Definições** na barra de navegação superior e, em seguida, escolha **identidade**. A lista de diretórios abre. Selecione o diretório federado que deseja.

1. Na página Detalhes do **Diretório,** **selecione Configurar**.

1. Copie o ID da Entidade e o URL ACS (URL de Serviço ao Consumidor de Afirmação ou URL de Resposta). Introduza os URLs nos campos apropriados do portal Azure.

    ![Configure um único sinal no lado da aplicação](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Utilize o valor id da entidade que a Adobe lhe forneceu para **identificador** na caixa de diálogo **configurar definições** de aplicações.

    b. Utilize o valor DOM (URL de Serviço ao Consumidor de Afirmação) que a Adobe lhe forneceu para **o URL de resposta** na caixa de diálogo **configurar definições** de aplicações.

1. Perto da parte inferior da página, faça o upload do ficheiro **Federation Data XML** que descarregou a partir do portal Azure. 

    ![Ficheiro XML de Dados da Federação](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "IdP Metadata XML")

1. Selecione **Guardar**.


### <a name="create-adobe-creative-cloud-test-user"></a>Criar o utilizador de teste De Adobe Creative Cloud

Para permitir que os utilizadores de Anúncios Azure assinem na Adobe Creative Cloud, devem ser aprovisionados na Adobe Creative Cloud. No caso da Adobe Creative Cloud, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para fornecer uma conta de utilizador, execute os seguintes passos:

1. Inscreva-se no site da [Adobe Admin Console](https://adminconsole.adobe.com) como administrador.

2. Adicione o utilizador dentro da consola da Adobe como ID federado e atribua-o a um Perfil de Produto. Para obter informações detalhadas sobre a adição de utilizadores, consulte [Adicionar utilizadores na Consola Adobe Admin](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Neste ponto, digite o seu endereço de e-mail/upn no sinal da Adobe no formulário, no separador de imprensa, e deve ser federado de volta para Azure AD:
   * Acesso à\.Web: www adobe.com > de inscrição
   * Dentro da aplicação de desktop utilitário > iniciar sessão
   * Dentro da aplicação > ajudar > o inscreveu-se

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Adobe Creative Cloud no Painel de Acesso, deve ser automaticamente inscrito na Nuvem Criativa do Adobe para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a Nuvem Criativa adobe com anúncio azure](https://aad.portal.azure.com/)

- [Criar uma identidade (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-identity.html)
  
- [Configure Azure para utilização com Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

