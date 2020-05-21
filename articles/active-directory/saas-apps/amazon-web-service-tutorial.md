---
title: 'Tutorial: Azure Ative Directory integração individual (SSO) com a Amazon Web Services (AWS) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e a Amazon Web Services (AWS).
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
ms.date: 04/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75b6ba110264ae3826093222e9cd3c4073bc17f0
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683589"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Tutorial: Azure Ative Directory integração individual (SSO) com a Amazon Web Services (AWS)

Neste tutorial, você vai aprender a integrar a Amazon Web Services (AWS) com o Azure Ative Directory (Azure AD). Quando integrar os Serviços Web da Amazon (AWS) com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Amazon Web Services (AWS).
* Permita que os seus utilizadores sejam automaticamente inscritos na Amazon Web Services (AWS) com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagrama da relação Azure AD e AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Pode configurar vários identificadores em várias instâncias. Por exemplo:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com estes valores, a Azure AD remove o valor de **#** , e envia o valor correto como URL do público no `https://signin.aws.amazon.com/saml` token SAML.

Recomendamos esta abordagem pelas seguintes razões:

- Cada aplicação fornece-lhe um certificado X509 único. Cada instância de uma instância de aplicação AWS pode então ter uma data de validade de certificado diferente, que pode ser gerida numa base individual de conta AWS. A capotação global do certificado é mais fácil neste caso.

- Pode ativar o fornecimento de utilizadores com uma aplicação AWS em Azure AD, e depois o nosso serviço adquire todas as funções dessa conta AWS. Não é preciso adicionar ou atualizar manualmente as funções AWS na aplicação.

- Pode atribuir o proprietário da aplicação individualmente para a aplicação. Esta pessoa pode gerir a aplicação diretamente em Azure AD.

> [!Note]
> Certifique-se de que utiliza apenas uma aplicação de galeria.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição ativada por um único sinal AWS (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Amazon Web Services (AWS) suporta **SP e IDP** iniciadoS SSO
* Assim que configurar os Serviços Web da Amazon (AWS), pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Controlo de Sessão estende-se a partir de Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia, pelo que apenas uma instância pode ser configurada num inquilino.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar Serviços Web da Amazon (AWS) da galeria

Para configurar a integração da Amazon Web Services (AWS) em Azure AD, você precisa adicionar Amazon Web Services (AWS) da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho, conta escolar ou conta pessoal da Microsoft.
1. No portal Azure, procure e selecione **Azure Ative Directory**.
1. Dentro do menu de visão geral do Diretório Ativo Azure, escolha **Aplicações Empresariais**  >  **Todas as aplicações**.
1. Selecione **Nova aplicação** para adicionar uma aplicação.
1. No Add da secção **de galeria,** digite **a Amazon Web Services (AWS)** na caixa de pesquisa.
1. Selecione **Amazon Web Services (AWS)** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Configure e teste Azure AD single sign-on para Amazon Web Services (AWS)

Configure e teste Azure AD SSO com Amazon Web Services (AWS) utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado nos Serviços Web da Amazon (AWS).

Para configurar e testar o Azure AD SSO com a Amazon Web Services (AWS), complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure os Serviços Web da Amazon (AWS) SSO](#configure-amazon-web-services-aws-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Crie o utilizador de testes da Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** - para ter uma contrapartida de B.Simon na Amazon Web Services (AWS) que está ligada à representação do utilizador da AD Azure.
    1. **[Como configurar o fornecimento de papéis em Amazon Web Services (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Amazon Web Services (AWS),** encontre a secção **Gerir** e selecione um **único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica do SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com o Azure. O utilizador precisa de guardar a configuração selecionando **Save**.

1. Quando estiver a configurar mais de um caso, forneça um valor de identificador. A partir de segunda instância, utilize o seguinte formato, incluindo um **#** sinal para especificar um valor SPN único.

    `https://signin.aws.amazon.com/saml#2`

1. A aplicação AWS espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação AWS espera que poucos atributos sejam retransmitidos na resposta SAML que são mostradas abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name  | Atributo de origem  | Espaço de Nomes |
    | --------------- | --------------- | --------------- |
    | Nome rolesession | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Função            | user.atribuídos |  `https://aws.amazon.com/SAML/Attributes` |
    | Duração das sessões             | "fornecer um valor entre 900 segundos (15 minutos) a 43200 segundos (12 horas)" |  `https://aws.amazon.com/SAML/Attributes` |

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção Configurar os **Serviços Web da Amazon (AWS),** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. No portal Azure, procure e selecione **Azure Ative Directory**.
1. Dentro do menu de visão geral do Diretório Ativo Azure, escolha **utilizadores**  >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de **nome do Utilizador,** introduza o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que a B.Simon utilize um único sign-on azure, concedendo acesso aos Serviços Web da Amazon (AWS).

1. No portal Azure, procure e selecione **Azure Ative Directory**.
1. Dentro do menu de visão geral do Diretório Ativo Azure, escolha **Aplicações Empresariais**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **Amazon Web Services (AWS)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-amazon-web-services-aws-sso"></a>Configure Serviços Web da Amazon (AWS) SSO

1. Numa janela de navegador diferente, inscreva-se no site da sua empresa AWS como administrador.

2. Selecione **AWS Home**.

    ![Screenshot do site da empresa AWS, com ícone AWS Home em destaque][11]

3. Selecione **Identidade e Gestão de Acesso.**

    ![Screenshot da página de serviços DaWS, com IAM em destaque][12]

4. Selecione **Fornecedores de Identidade**  >  **Criar Fornecedor**.

    ![Screenshot da página IAM, com Fornecedores de Identidade e Fornecedor de Criação em destaque][13]

5. Na página **Configure Provider,** execute os seguintes passos:

    ![Screenshot do Fornecedor de Configuração][14]

    a. Para **o Tipo de Fornecedor,** selecione **SAML**.

    b. Para **denominação de fornecedor,** escreva um nome de fornecedor (por exemplo: *WAAD*).

    c. Para fazer o upload do ficheiro de **metadados** descarregado sabotado do portal Azure, **selecione 'Escolher Ficheiro**' .

    d. Selecione **Próximo Passo**.

6. Na página informações do fornecedor de **verificação,** selecione **Criar**.

    ![Screenshot de Verificar informações do fornecedor, com Criar em destaque][15]

7. Selecione **Funções**  >  **Criar função**.

    ![Screenshot da página De Papéis][16]

8. Na página de **funções Criar,** execute os seguintes passos:  

    ![Screenshot da página de função Criar][19]

    a. Sob **o tipo de seleto de entidade fidedigna,** selecione a federação **SAML 2.0**.

    b. Em **'Escolha um Fornecedor SAML 2.0',** selecione o **fornecedor SAML** que criou anteriormente (por exemplo: *WAAD*).

    c. **Selecione Permitir o acesso programático e a consola de gestão AWS**.
  
    d. Selecione **Seguinte: Permissões**.

9. Na caixa de diálogo de **políticas de permissões Attach,** fixe a política adequada, de acordo com a sua organização. Em seguida, selecione **Seguinte: Rever**.  

    ![Screenshot da caixa de diálogo de permissões Attach][33]

10. Na caixa de diálogo **Review,** execute os seguintes passos:

    ![Screenshot da caixa de diálogo de revisão][34]

    a. No **nome do papel,** introduza o seu nome de papel.

    b. Na **descrição do papel,** insira a descrição.

    c. Selecione **Criar função**.

    d. Crie o número de funções necessárias e mapeie-as para o fornecedor de identidade.

11. Utilize credenciais de conta de serviço AWS para obter as funções da conta AWS no fornecimento de utilizadores Da AD Azure. Para isso, abra a consola AWS para casa.

12. Selecione **Serviços**. Em **Segurança, Identidade & Conformidade,** selecione **IAM**.

    ![Screenshot da consola AWS casa, com Serviços e IAM em destaque](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Na secção IAM, selecione **Políticas**.

    ![Screenshot da secção IAM, com Políticas em destaque](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Crie uma nova política selecionando a **política Create** para obter as funções da conta AWS no fornecimento de utilizadores da AD Azure.

    ![Screenshot de Criar página de papel, com a política Create em destaque](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Crie a sua própria política para obter todos os papéis das contas DaWS.

    ![Screenshot da página de política Create, com JSON em destaque](./media/amazon-web-service-tutorial/policy1.png)

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

    ![Screenshot da página de política Criar](./media/amazon-web-service-tutorial/policy5.png)

16. Defina a nova política.

    ![Screenshot de criar página de política, com campos de nome e descrição destacados](./media/amazon-web-service-tutorial/policy2.png)

    a. Para **Nome,** insira **AzureAD_SSOUserRole_Policy.**

    b. Para **descrição,** insira **Esta política permitirá obter as funções das contas AWS**.

    c. Selecione **Criar política**.

17. Crie uma nova conta de utilizador no serviço AWS IAM.

    a. Na consola AWS IAM, selecione **Utilizadores**.

    ![Screenshot da consola AWS IAM, com utilizadores em destaque](./media/amazon-web-service-tutorial/policy3.png)

    b. Para criar um novo utilizador, selecione **Adicionar utilizador**.

    ![Screenshot do botão de utilizador adicionar](./media/amazon-web-service-tutorial/policy4.png)

    c. Na secção **Adicionar utilizador:**

    ![Screenshot da página de utilizador adicionar, com nome de utilizador e tipo de acesso destacado](./media/amazon-web-service-tutorial/adduser1.png)

    * Introduza o nome de utilizador como **AzureADRoleManager**.

    * Para o tipo de acesso, selecione **acesso programático**. Desta forma, o utilizador pode invocar as APIs e obter as funções da conta AWS.

    * Selecione **Próximas Permissões**.

18. Crie uma nova política para este utilizador.

    ![Screenshot do utilizador adicionar](./media/amazon-web-service-tutorial/adduser2.png)

    a. **Selecione fixe as políticas existentes diretamente**.

    b. Procure a política recém-criada na secção **filtrante AzureAD_SSOUserRole_Policy**.

    c. Selecione a apólice e, em seguida, selecione **Seguinte: Rever**.

19. Reveja a política ao utilizador em anexo.

    ![Screenshot da página de utilizador adicionar, com criar o utilizador destacado](./media/amazon-web-service-tutorial/adduser3.png)

    a. Reveja o nome do utilizador, o tipo de acesso e a política mapeada para o utilizador.

    b. Selecione **Criar o utilizador**.

20. Descarregue as credenciais de utilizador de um utilizador.

    ![Screenshot do utilizador adicionar](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copie a **chave de acesso** do utilizador ID e chave de acesso **secreto**.

    b. Introduza estas credenciais na secção de fornecimento de utilizadores da AD Azure para obter as funções da consola AWS.

    c. Selecione **Fechar**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Como configurar o fornecimento de papéis em Amazon Web Services (AWS)

1. No portal de gestão da AD Azure, na aplicação AWS, vá ao **Provisioning**.

    ![Screenshot da app AWS, com provisionamento em destaque](./media/amazon-web-service-tutorial/provisioning.png)

2. Introduza a chave de acesso e segredo nos campos secretos e **secretos** do **cliente,** respectivamente.

    ![Screenshot da caixa de diálogo de credenciais de administrador](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Introduza a chave de acesso ao utilizador AWS no campo **clientsecret.**

    b. Insira o segredo do utilizador da AWS no campo **Secret Token.**

    c. Selecione **Ligação de teste**.

    d. Guarde a definição selecionando **Save**.

3. Na secção **Definições,** para **o Estado de Provisionamento,** selecione **On**. Em seguida, selecione **Guardar**.

    ![Screenshot da secção Definições, com On destacado](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> O serviço de provisionamento importa funções apenas de AWS para Azure AD. O serviço não presta aos utilizadores e grupos da Azure AD para a AWS.

> [!NOTE]
> Depois de guardar as credenciais de fornecimento, deve esperar que o ciclo de sincronização inicial decorra. Sync geralmente leva cerca de 40 minutos para terminar. Pode ver o estado na parte inferior da página de **provisionamento,** em **estado atual**.

### <a name="create-amazon-web-services-aws-test-user"></a>Criar o utilizador de teste Amazon Web Services (AWS)

O objetivo desta secção é criar um utilizador chamado B.Simon in Amazon Web Services (AWS). A Amazon Web Services (AWS) não precisa de um utilizador para ser criado no seu sistema para o SSO, pelo que não precisa de realizar qualquer ação aqui.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Amazon Web Services (AWS) no Painel de Acesso, deve ser automaticamente inscrito nos Serviços Web da Amazon (AWS) para os quais configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="known-issues"></a>Problemas conhecidos

 * Na secção **provisionamento,** a subsecção de **Mapeamentos** mostra um "Loading..." mensagem, e nunca exibe os mapeamentos de atributos. O único fluxo de trabalho suportado hoje é a importação de funções da AWS para a AD Azure para seleção durante uma atribuição de utilizador ou grupo. Os mapeamentos de atributos para isto são pré-determinados, e não são configuráveis.

 * A secção **de provisionamento** apenas suporta a introdução de um conjunto de credenciais para um inquilino DaWS de cada vez. Todas as funções importadas são escritas na `appRoles` propriedade do [ `servicePrincipal` objeto](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) Azure AD para o inquilino da AWS.

   Vários inquilinos da AWS (representados por `servicePrincipals` ) podem ser adicionados à Azure AD a partir da galeria para o provisionamento. Há uma questão conhecida, no entanto, de não poder escrever automaticamente todas as funções importadas a partir do múltiplo AWS `servicePrincipals` usado para o fornecimento no único usado para `servicePrincipal` SSO.

   Como suposições, pode utilizar a [API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) do Microsoft Graph para extrair toda a `appRoles` importação em cada AWS onde o `servicePrincipal` provisionamento está configurado. Posteriormente, pode adicionar estas cordas de função ao AWS onde o `servicePrincipal` SSO está configurado.

* As funções devem satisfazer os seguintes requisitos a serem elegíveis para serem importados da AWS para a AD Azure:

  * As funções devem ter exatamente um fornecedor de saml definido em AWS

  * A duração combinada do papel ARN e do prestador de saml ARN para um papel importado deve ser igual ou inferior a 119 caracteres

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

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