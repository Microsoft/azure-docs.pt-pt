---
title: 'Tutorial: Integração de Diretório Ativo Azure com autenticação de identidade da plataforma SAP Cloud [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e a Autenticação de Identidade da Plataforma De Nuvem SAP.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: db852254d7f6ec9c0cb76151fa23e58e2d97bd2c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981804"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Tutorial: Azure Ative Diretório integração de um único sign-on (SSO) com autenticação de identidade da plataforma de nuvem SAP

Neste tutorial, você aprenderá a integrar a autenticação de identidade da plataforma De nuvem SAP com o Diretório Ativo Azure (Azure AD). Quando integrar a autenticação de identidade da plataforma SAP Cloud com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à Autenticação de Identidade da Plataforma SAP Cloud.
* Ative que os seus utilizadores sejam automaticamente inscritos na Autenticação de Identidade da Plataforma SAP Cloud com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A autenticação de identidade da plataforma SAP Cloud (SSO) ativada por subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Autenticação de identidade da plataforma SAP Cloud suporta **SP** e **IDP** iniciado SSO
* Assim que configurar a Autenticação de Identidade da Plataforma De Nuvem SAP, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

Antes de mergulhar nos detalhes técnicos, é vital entender os conceitos que vai ver. Os Serviços de Autenticação de Identidade e Direção Ativa da Plataforma SAP permitem implementar o SSO através de aplicações ou serviços protegidos pela Azure AD (como idP) com aplicações e serviços SAP protegidos pela Autenticação de Identidade da Plataforma SAP Cloud.

Atualmente, a autenticação de identidade da plataforma SAP Cloud funciona como fornecedor de identidade proxy para aplicações SAP. O Azure Ative Directory, por sua vez, atua como o principal Fornecedor de Identidade nesta configuração. 

O diagrama seguinte ilustra esta relação:

![Criação de um utilizador de teste azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Com esta configuração, o seu inquilino de autenticação de identidade da plataforma SAP Cloud está configurado como uma aplicação fidedigna no Diretório Ativo Azure.

Todas as aplicações e serviços SAP que pretende proteger desta forma estão posteriormente configurados na consola de gestão de autenticação de identidade da Plataforma Cloud SAP.

Por isso, a autorização para o acesso a aplicações e serviços SAP tem de ser efetuada na Autenticação de Identidade da Plataforma SAP Cloud (em oposição ao Diretório Ativo do Azure).

Ao configurar a autenticação de identidade da plataforma De nuvem SAP como uma aplicação através do Mercado de Diretórios Ativos Do Azure, não precisa de configurar reclamações individuais ou afirmações SAML.

> [!NOTE]
> Atualmente, apenas o Web SSO foi testado por ambas as partes. Os fluxos necessários para a comunicação App-to-API ou API-to-API devem funcionar, mas ainda não foram testados. Serão testados durante as atividades subsequentes.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Adicionar autenticação de identidade da plataforma de nuvem SAP da galeria

Para configurar a integração da Autenticação de Identidade da Plataforma SAP Cloud em Azure AD, é necessário adicionar a Autenticação de Identidade da Plataforma Cloud SAP da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite a autenticação de identidade da **plataforma de nuvem SAP** na caixa de pesquisa.
1. Selecione A autenticação de identidade da **plataforma De nuvem SAP** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Configure e teste Azure AD único sinal para autenticação de identidade da plataforma de nuvem SAP

Configure e teste Azure AD SSO com autenticação de identidade da plataforma da nuvem SAP utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Autenticação de Identidade da Plataforma SAP Cloud.

Para configurar e testar o Azure AD SSO com a autenticação de identidade da plataforma sap cloud, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    * Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o SSO](#configure-sap-cloud-platform-identity-authentication-sso)** de Autenticação de Identidade da Plataforma De Nuvem SAP - para configurar as definições de início de sessão individuais no lado da aplicação.
    * **[Crie](#create-sap-cloud-platform-identity-authentication-test-user)** o utilizador do teste de autenticação de identidade da plataforma SAP Cloud - para ter uma contraparte de B.Simon na Autenticação de Identidade da Plataforma Cloud SAP que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações de autenticação de identidade da **Plataforma SAP Cloud,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** se desejar configurar no modo iniciado pelo **IDP,** execute os seguintes passos:

    ![Domínio de autenticação de identidade da plataforma de nuvem SAP e informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`<IAS-tenant-id>.accounts.ondemand.com`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e url de resposta. Contacte a equipa de suporte ao Cliente de Autenticação de Identidade da [Plataforma SAP Cloud](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) para obter estes valores. Se não entender o valor do identificador, leia a documentação de autenticação de identidade da plataforma Denuvem SAP sobre a [configuração do Tenant SAML 2.0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado por **SP:**

    ![Domínio de autenticação de identidade da plataforma de nuvem SAP e informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL de inscrição real. Por favor, utilize o url de inscrição específico da sua aplicação de negócio. Contacte a equipa de suporte ao Cliente de Autenticação de Identidade da [Plataforma SAP Cloud,](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) caso tenha dúvidas.

1. A aplicação de autenticação de identidade da plataforma SAP Cloud espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação de autenticação de identidade da plataforma SAP Cloud espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo fonte|
    | ---------------| --------------- |
    | nomePróprio | user.givenname |

8. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Metadata XML** a partir das opções dadas e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

9. Na secção de autenticação de identidade da plataforma de **nuvem SAP,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador de anúncio sinuoso

    c. Logout URL

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso à Autenticação de Identidade da Plataforma Cloud SAP.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **SAP Cloud Platform Identity Authentication**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Configure SSO de autenticação de identidade da plataforma de nuvem SAP

1. Para obter O SSO configurado para a sua aplicação, vá à consola de autenticação de identidade da Plataforma SAP Cloud. O URL tem o seguinte padrão: `https://<tenant-id>.accounts.ondemand.com/admin` . Em seguida, leia a documentação sobre a autenticação de identidade da plataforma SAP Cloud em integração com o [Microsoft Azure AD](https://developers.sap.com/tutorials/cp-ias-azure-ad.html).

2. No portal Azure, selecione o botão **Guardar.**

3. Continue com o seguinte apenas se quiser adicionar e ativar o SSO para outra aplicação SAP. Repita os passos sob a secção Adicionar Autenticação de Identidade da **Plataforma Nuvem SAP a partir da galeria**.

4. No portal Azure, na página de integração de aplicações de autenticação de identidade da **Plataforma SAP Cloud,** selecione **Linked Sign-on**.

    ![Configurar sinal ligado](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Guarde a configuração.

> [!NOTE]
> A nova aplicação aproveita a configuração única de inscrição da aplicação SAP anterior. Certifique-se de que utiliza os mesmos Fornecedores de Identidade Corporativa na consola de autenticação de identidade da Plataforma SAP Cloud.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Criar o utilizador do teste de autenticação de identidade da plataforma SAP Cloud

Não é necessário criar um utilizador na Autenticação de Identidade da Plataforma SAP Cloud. Os utilizadores que se encontram na loja de utilizadores da AD Azure podem utilizar a funcionalidade SSO.

A autenticação de identidade da plataforma SAP Cloud suporta a opção Da Federação de Identidade. Esta opção permite que a aplicação verifique se existem utilizadores autenticados pelo fornecedor de identidade corporativa na loja de utilizadores da Autenticação de Identidade da Plataforma SAP Cloud.

A opção Da Federação de Identidade é desativada por defeito. Se a Federação de Identidade estiver ativada, apenas os utilizadores que são importados na Autenticação de Identidade da Plataforma SAP Cloud podem aceder à aplicação.

Para obter mais informações sobre como ativar ou desativar a Federação de Identidade com autenticação de identidade da plataforma De cloud SAP, consulte "Ativar a Federação de Identidade com A autenticação de identidade da plataforma de nuvem SAP" na [Configuração da Federação de Identidade com a Loja de Utilizadores da Autenticação de Identidade da Plataforma De Nuvem SAP.](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de autenticação de identidade da plataforma SAP Cloud no Painel de Acesso, deve ser automaticamente inscrito na Autenticação de Identidade da Plataforma Cloud SAP para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente a autenticação de identidade da plataforma de nuvem SAP com a AD Azure](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger a autenticação de identidade da plataforma de nuvem SAP com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)