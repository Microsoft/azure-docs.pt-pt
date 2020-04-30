---
title: 'Tutorial: Azure Ative Directory Single sign-on integração com cisco Webex [ Integração de diretório ativo azure com cisco Webex ] Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01fe4d06d5f73eacee1d1cdaf1963232b84daf05
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77046794"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Tutorial: Azure Ative Directory Single sign-on (SSO) integração com cisco Webex

Neste tutorial, você vai aprender a integrar a Cisco Webex com o Azure Ative Directory (Azure AD). Quando integrar a Cisco Webex com o Azure AD, pode:

* Controlo em Azure AD que tem acesso à Cisco Webex.
* Ative que os seus utilizadores sejam automaticamente inscritos na Cisco Webex com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Cisco Webex (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Cisco Webex suporta **SP** iniciado SSO.
* A Cisco Webex suporta o fornecimento **automatizado** de utilizadores.
* Assim que configurar o Cisco Webex, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando Cisco Webex da galeria

Para configurar a integração da Cisco Webex em Azure AD, precisa de adicionar a Cisco Webex da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **cisco Webex** na caixa de pesquisa.
1. Selecione **Cisco Webex** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Configure e teste Azure AD único sign-on para Cisco Webex

Configure e teste Azure AD SSO com Cisco Webex utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Cisco Webex.

Para configurar e testar o Azure AD SSO com a Cisco Webex, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
2. **[Configure](#configure-cisco-webex)** o Cisco Webex para configurar as definições sSO no lado da aplicação.
    1. **[Crie](#create-cisco-webex-test-user)** o utilizador de teste Cisco Webex para ter uma contrapartida de B.Simon na Cisco Webex que está ligada à representação do utilizador da AD Azure.
3. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **da Cisco Webex,** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **basic SAML Configuration,** carregue o ficheiro de metadados do Fornecedor de **Serviços** descarregado e configure a aplicação executando os seguintes passos:

    >[!Note]
    >Obterá o ficheiro metadados do Fornecedor de Serviços na secção **Configure Cisco Webex,** o que é explicado mais tarde no tutorial. 

    a. Clique no **ficheiro de metadados de upload**.

    b. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

    c. Após a conclusão com sucesso do upload de ficheiros de metadados do Fornecedor de Serviços, os valores de URL do **Identificador** e **da Resposta** ficam povoados automaticamente na secção básica de **configuração SAML:**

    No **Sign on URL** textbox, colá o valor do URL de **resposta,** que é preenchido automaticamente pelo upload de ficheiros de metadados SP.

1. A aplicação Cisco Webex espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Cisco Webex espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
  
    | Nome |  Atributo fonte|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **'Configurar a Cisco Webex',** copie os URL(s) adequados com base no seu requisito.

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

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso à Cisco Webex.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Cisco Webex**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-cisco-webex"></a>Configure Cisco Webex

1. Para automatizar a configuração dentro do Webex da Cisco, é necessário instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar o Cisco Webex** irá direcioná-lo para a aplicação Cisco Webex. A partir daí, forneça as credenciais de administração para assinar na Cisco Webex. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-8.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se quiser configurar a Cisco Webex manualmente, inscreva-se na [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) com as suas credenciais completas de administrador.

4. Selecione **Definições** e sob a secção **de autenticação,** clique **em Modificar**.

    ![Configurar um único sinal](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. Selecione **Integrar um fornecedor de identidade de terceiros. (Avançado)** e ir para o próximo ecrã.

6. Na página **de Metadados Idp importado,** ou arraste e deixe cair o ficheiro de metadados Azure AD na página ou utilize a opção do navegador de ficheiros para localizar e carregar o ficheiro de metadados Azure AD. Em seguida, selecione **Exigir certificado assinado por uma autoridade de certificado em Metadados (mais seguro)** e clique **em Seguinte**.

    ![Configurar um único sinal](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Selecione **Test SSO Connection**, e quando um novo separador de navegador abrir, autenticar com AD Azure através do início de sessão.

8. Volte ao separador **cisco Cloud Collaboration Management** browser. Se o teste foi bem sucedido, selecione **Este teste foi bem sucedido. Ativar a opção De Adoção De Admissão única** e clique em **Next**.

### <a name="create-cisco-webex-test-user"></a>Criar o utilizador de teste Cisco Webex

Nesta secção, cria-se um utilizador chamado B.Simon na Cisco Webex. Nesta secção, cria-se um utilizador chamado B.Simon na Cisco Webex.

1. Vá à [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) com as suas credenciais de administrador completos.

2. Clique em **Utilizadores** **e,** em seguida, gerir utilizadores .
   
    ![Configurar um único sinal](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. Na janela **'Gerir utilizador',** selecione **Manualmente adicionar ou modificar os utilizadores** e clicar em **Seguinte**.

4. Selecione **Nomes e endereço de e-mail**. Em seguida, preencha a caixa de texto da seguinte forma:

    ![Configurar um único sinal](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. Na caixa de texto **First Name,** digite o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **Last Name,** escreva o sobrenome do utilizador como **Simon**.

    c. Na caixa de texto de endereço de b.simon@contoso.com **e-mail,** digite o endereço de e-mail do utilizador como .

5. Clique no sinal de adição de B.Simon. Em seguida, clique **em Next**.

6. Na janela **Adicionar Serviços para Utilizadores,** clique em **Guardar** **e,** em seguida, terminar .

## <a name="test-sso"></a>Teste SSO

Ao selecionar o azulejo Cisco Webex no Painel de Acesso, deve ser automaticamente inscrito no Webex cisco para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente cisco Webex com Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a Cisco Webex com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-webex)