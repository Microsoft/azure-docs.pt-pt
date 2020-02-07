---
title: 'Tutorial: integração de SSO (logon único) do Azure Active Directory com o Amazon Web Services (AWS) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 986aa000102cfed01666c8a95c00847ecc0cd651
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047268"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Tutorial: integração de SSO (logon único) do Azure Active Directory com o Amazon Web Services (AWS)

Neste tutorial, você aprenderá a integrar o Amazon Web Services (AWS) ao Azure Active Directory (Azure AD). Ao integrar o Amazon Web Services (AWS) ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Amazon Web Services (AWS).
* Habilite seus usuários a entrar automaticamente no Amazon Web Services (AWS) com suas contas do Azure AD.
* Gerencie suas contas em um local central-o portal do Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagrama do relacionamento do Azure AD e do AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Você pode configurar vários identificadores para várias instâncias. Por exemplo:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com estes valores, a Azure AD remove o valor de **#** , e envia o valor correto `https://signin.aws.amazon.com/saml` como url do público no token SAML.

Recomendamos essa abordagem pelos seguintes motivos:

- Cada aplicativo fornece um certificado X509 exclusivo. Cada instância de uma instância de aplicativo AWS pode ter uma data de expiração de certificado diferente, que pode ser gerenciada em uma base de conta de AWS individual. A substituição geral do certificado é mais fácil nesse caso.

- Você pode habilitar o provisionamento de usuário com um aplicativo AWS no Azure AD e, em seguida, nosso serviço busca todas as funções dessa conta do AWS. Você não precisa adicionar ou atualizar manualmente as funções AWS no aplicativo.

- Você pode atribuir o proprietário do aplicativo individualmente para o aplicativo. Essa pessoa pode gerenciar o aplicativo diretamente no Azure AD.

> [!Note]
> Certifique-se de usar apenas um aplicativo de galeria.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura do AWS habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Amazon Web Services (AWS) suporta **SP e IDP** iniciadoS SSO
* Assim que configurar os Serviços Web da Amazon (AWS), pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo, de modo que apenas uma instância pode ser configurada em um locatário.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adição de Amazon Web Services (AWS) da Galeria

Para configurar a integração do Amazon Web Services (AWS) ao Azure AD, você precisa adicionar o Amazon Web Services (AWS) da Galeria à sua lista de aplicativos SaaS gerenciados.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho, conta escolar ou conta pessoal da Microsoft.
1. No portal Azure, procure e selecione **Azure Ative Directory**.
1. Dentro do menu de visão geral do Diretório Ativo Azure, escolha **aplicações empresariais** > **todas as aplicações.**
1. Selecione **Nova aplicação** para adicionar uma aplicação.
1. No Add da secção **de galeria,** digite **a Amazon Web Services (AWS)** na caixa de pesquisa.
1. Selecione **Amazon Web Services (AWS)** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Configurar e testar o logon único do Azure AD para o Amazon Web Services (AWS)

Configure e teste Azure AD SSO com Amazon Web Services (AWS) utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Amazon Web Services (AWS).

Para configurar e testar o SSO do Azure AD com o Amazon Web Services (AWS), conclua os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure os Serviços Web da Amazon (AWS) SSO](#configure-amazon-web-services-aws-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador de testes da Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** - para ter uma contrapartida de B.Simon na Amazon Web Services (AWS) que está ligada à representação do utilizador da AD Azure.
    1. **[Como configurar o fornecimento de papéis em Amazon Web Services (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Amazon Web Services (AWS),** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar configuração básica de SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica do SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração selecionando **Save**.

1. Quando você estiver configurando mais de uma instância, forneça um valor de identificador. A partir de segunda instância, utilize o seguinte formato, incluindo um sinal **de#** para especificar um valor SPN único.

    `https://signin.aws.amazon.com/saml#2`

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

1. Na secção Configurar os **Serviços Web da Amazon (AWS),** copie os URL(s) adequados com base na sua exigência.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Simon.

1. No portal Azure, procure e selecione **Azure Ative Directory**.
1. Dentro do menu de visão geral do Diretório Ativo Azure, escolha **Utilizadores** > **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **de nome do utilizador,** introduza o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta seção, você habilitará B. Simon para usar o logon único do Azure concedendo acesso ao Amazon Web Services (AWS).

1. No portal Azure, procure e selecione **Azure Ative Directory**.
1. Dentro do menu de visão geral do Diretório Ativo Azure, escolha **aplicações empresariais** > **todas as aplicações.**
1. Na lista de aplicações, selecione **Amazon Web Services (AWS)** .
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![O link Adicionar usuário](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-amazon-web-services-aws-sso"></a>Configurar o SSO do Amazon Web Services (AWS)

1. Em uma janela de navegador diferente, faça logon em seu site de empresa do AWS como administrador.

2. Selecione **AWS Home**.

    ![Captura de tela do site da empresa AWS, com o ícone Home do AWS realçado][11]

3. Selecione **Identidade e Gestão de Acesso.**

    ![Captura de tela da página de serviços AWSs, com IAM realçado][12]

4. Selecione **Fornecedores de Identidade** > **Criar Fornecedor.**

    ![Captura de tela da página IAM, com provedores de identidade e para criar provedor realçado][13]

5. Na página **Configure Provider,** execute os seguintes passos:

    ![Captura de tela do provedor de configuração][14]

    a. Para **o Tipo de Fornecedor,** selecione **SAML**.

    b. Para **denominação de fornecedor,** escreva um nome de fornecedor (por exemplo: *WAAD*).

    c. Para fazer o upload do ficheiro de **metadados** descarregado sabotado do portal Azure, **selecione 'Escolher Ficheiro**' .

    d. Selecione **Próximo Passo**.

6. Na página informações do fornecedor de **verificação,** selecione **Criar**.

    ![Captura de tela de verificar informações do provedor, com criar realçado][15]

7. Selecione **Funções** > **Criar função**.

    ![Captura de tela da página funções][16]

8. Na página de **funções Criar,** execute os seguintes passos:  

    ![Captura de tela da página Criar função][19]

    a. Sob **o tipo de seleto de entidade fidedigna,** selecione a federação **SAML 2.0**.

    b. Em **'Escolha um Fornecedor SAML 2.0',** selecione o **fornecedor SAML** que criou anteriormente (por exemplo: *WAAD*).

    c. **Selecione Permitir o acesso programático e a consola de gestão AWS**.
  
    d. Selecione **Seguinte: Permissões**.

9. Na caixa de diálogo de **políticas de permissões Attach,** fixe a política adequada, de acordo com a sua organização. Em seguida, selecione **Seguinte: Rever**.  

    ![Captura de tela da caixa de diálogo política de permissões de anexação][33]

10. Na caixa de diálogo **Review,** execute os seguintes passos:

    ![Captura de tela da caixa de diálogo de revisão][34]

    a. No **nome do papel,** introduza o seu nome de papel.

    b. Na **descrição do papel,** insira a descrição.

    c. Selecione **Criar função**.

    d. Crie quantas funções forem necessárias e mapeie-as para o provedor de identidade.

11. Use as credenciais de conta de serviço do AWS para buscar as funções da conta AWS no provisionamento de usuário do Azure AD. Para isso, abra a página inicial do console do AWS.

12. Selecione **Serviços**. Em **Segurança, Identidade e Conformidade,** selecione **IAM**.

    ![Captura de tela da página inicial do console do AWS, com serviços e IAM realçados](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Na secção IAM, selecione **Políticas**.

    ![Captura de tela da seção IAM, com políticas realçadas](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Crie uma nova política selecionando a **política Create** para obter as funções da conta AWS no fornecimento de utilizadores da AD Azure.

    ![Captura de tela da página Criar função, com criar política realçada](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Crie sua própria política para buscar todas as funções de contas do AWS.

    ![Captura de tela da página Criar política, com JSON realçado](./media/amazon-web-service-tutorial/policy1.png)

    a. Na **política Create,** selecione o separador **JSON.**

    b. No documento de política, adicione o seguinte JSON:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Selecione **a política de revisão** para validar a política.

    ![Captura de tela da página Criar política](./media/amazon-web-service-tutorial/policy5.png)

16. Defina a nova política.

    ![Captura de tela da página Criar política, com campos nome e descrição realçados](./media/amazon-web-service-tutorial/policy2.png)

    a. Para **Nome,** insira **AzureAD_SSOUserRole_Policy.**

    b. Para **descrição,** insira **Esta política permitirá obter as funções das contas AWS**.

    c. Selecione **Criar a política**.

17. Crie uma nova conta de usuário no serviço IAM AWS.

    a. Na consola AWS IAM, selecione **Utilizadores**.

    ![Captura de tela do console IAM AWS, com usuários realçados](./media/amazon-web-service-tutorial/policy3.png)

    b. Para criar um novo utilizador, selecione **Adicionar utilizador**.

    ![Captura de tela do botão Adicionar usuário](./media/amazon-web-service-tutorial/policy4.png)

    c. Na secção **Adicionar utilizador:**

    ![Captura de tela da página Adicionar usuário, com o nome de usuário e o tipo de acesso realçados](./media/amazon-web-service-tutorial/adduser1.png)

    * Introduza o nome de utilizador como **AzureADRoleManager**.

    * Para o tipo de acesso, selecione **acesso programático**. Dessa forma, o usuário pode invocar as APIs e buscar as funções da conta AWS.

    * Selecione **Próximas Permissões**.

18. Crie uma nova política para este usuário.

    ![Captura de tela de Adicionar usuário](./media/amazon-web-service-tutorial/adduser2.png)

    a. **Selecione fixe as políticas existentes diretamente**.

    b. Procure a política recém-criada na secção **filtrante AzureAD_SSOUserRole_Policy**.

    c. Selecione a apólice e, em seguida, selecione **Seguinte: Rever**.

19. Examine a política para o usuário anexado.

    ![Captura de tela da página Adicionar usuário, com Create User realçado](./media/amazon-web-service-tutorial/adduser3.png)

    a. Examine o nome de usuário, o tipo de acesso e a política mapeados para o usuário.

    b. Selecione **Criar o utilizador**.

20. Baixe as credenciais de usuário de um usuário.

    ![Captura de tela de Adicionar usuário](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copie a **chave de acesso** do utilizador ID e chave de acesso **secreto**.

    b. Insira essas credenciais na seção provisionamento de usuário do Azure AD para buscar as funções no console do AWS.

    c. Selecione **Fechar**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Como configurar o provisionamento de função no Amazon Web Services (AWS)

1. No portal de gestão da AD Azure, na aplicação AWS, vá ao **Provisioning**.

    ![Captura de tela do aplicativo AWS, com provisionamento realçado](./media/amazon-web-service-tutorial/provisioning.png)

2. Introduza a chave de acesso e segredo nos campos secretos e **secretos** do **cliente,** respectivamente.

    ![Captura de tela da caixa de diálogo credenciais de administrador](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Introduza a chave de acesso ao utilizador AWS no campo **clientsecret.**

    b. Insira o segredo do utilizador da AWS no campo **Secret Token.**

    c. Selecione **Ligação de teste**.

    d. Guarde a definição selecionando **Save**.

3. Na secção **Definições,** para **o Estado de Provisionamento,** selecione **On**. Em seguida, selecione **Guardar**.

    ![Captura de tela da seção Configurações, com em realçado](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> O serviço de provisionamento importará apenas funções do AWS para o Azure AD. Esse serviço não provisionará usuários e grupos do Azure AD de volta para o AWS.

### <a name="create-amazon-web-services-aws-test-user"></a>Criar usuário de teste do Amazon Web Services (AWS)

O objetivo desta seção é criar um usuário chamado B. Simon em Amazon Web Services (AWS). Amazon Web Services (AWS) não precisa que um usuário seja criado em seu sistema para SSO, portanto, você não precisa executar nenhuma ação aqui.

## <a name="test-sso"></a>Testar SSO

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Amazon Web Services (AWS) no painel de acesso, você deverá ser conectado automaticamente ao Amazon Web Services (AWS) para o qual você configurou o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="known-issues"></a>Problemas conhecidos

 * Na secção **provisionamento,** a subsecção de **Mapeamentos** mostra um "Loading..." mensagem, e nunca exibe os mapeamentos de atributos. O único fluxo de trabalho de provisionamento com suporte hoje é a importação de funções do AWS para o Azure AD para seleção durante uma atribuição de usuário ou de grupo. Os mapeamentos de atributo para isso são predeterminados e não são configuráveis.

 * A secção **de provisionamento** apenas suporta a introdução de um conjunto de credenciais para um inquilino DaWS de cada vez. Todas as funções importadas são escritas para a propriedade `appRoles` do`servicePrincipal` Azure [AD](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) para o inquilino da AWS.

   Vários inquilinos da AWS (representados por `servicePrincipals`) podem ser adicionados à Azure AD a partir da galeria para o provisionamento. Há uma questão conhecida, no entanto, de não poder escrever automaticamente todas as funções importadas a partir dos múltiplos AWS `servicePrincipals` usados para o fornecimento no único `servicePrincipal` usado para SSO.

   Como suposições, pode utilizar a [API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) do Microsoft Graph para extrair todos os `appRoles` importados em cada `servicePrincipal` AWS onde o provisionamento está configurado. Posteriormente, pode adicionar estas cordas de função ao `servicePrincipal` AWS onde o SSO está configurado.

* As funções devem atender aos seguintes requisitos para que sejam elegíveis para serem importadas do AWS para o Azure AD:

  * As funções devem ter exatamente um provedor SAML definido em AWS

  * O comprimento combinado da função ARN e do ARN do provedor SAML para uma função que está sendo importada deve ter de 119 caracteres ou menos

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente os Serviços Web da Amazon (AWS) com a Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger os Serviços Web da Amazon (AWS) com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-aws)

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png