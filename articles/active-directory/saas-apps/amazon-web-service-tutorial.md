---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com a Amazon Web Services (AWS) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Amazon Web Services (AWS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 237ed71f058ae099f19924481eb4dbeb2e2aba05
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589445"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Amazon Web Services (AWS)

Neste tutorial, você vai aprender a integrar a Amazon Web Services (AWS) com o Azure Ative Directory (Azure AD). Quando integra a Amazon Web Services (AWS) com Azure AD, pode:

* Control em Azure AD que tem acesso aos Serviços Web da Amazon (AWS).
* Capacitar os seus utilizadores a serem automaticamente inscritos na Amazon Web Services (AWS) com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

> [!Note]
> A Azure AD não suporta uma única integração de sign-on com a AWS SSO, é um produto diferente da AWS. Embora a AWS mencione sobre o mesmo [aqui,](https://docs.aws.amazon.com/singlesignon/latest/userguide/azure-ad-idp.html)a Azure AD recomenda aos clientes que utilizem a integração AWS IAM, em vez disso, para que possa obter melhores controlos de segurança usando políticas de Acesso Condicional em contas individuais e também fazer uma melhor governação destas aplicações.

![Diagrama da relação Azure AD e AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Pode configurar vários identificadores em vários casos. Por exemplo:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com estes valores, a Azure AD remove o valor **#** de, e envia o valor correto `https://signin.aws.amazon.com/saml` como URL de audiência no token SAML.

Recomendamos esta abordagem pelas seguintes razões:

- Cada aplicação fornece-lhe um certificado X509 único. Cada instância de uma instância de aplicação AWS pode então ter uma data de validade de certificado diferente, que pode ser gerida numa base de conta AWS individual. Neste caso, a caduga global do certificado é mais fácil.

- Pode ativar o fornecimento de utilizadores com uma aplicação AWS em AZure AD, e então o nosso serviço recolhe todas as funções dessa conta AWS. Não tem de adicionar ou atualizar manualmente as funções AWS na aplicação.

- Pode atribuir o proprietário da aplicação individualmente para a aplicação. Esta pessoa pode gerir a aplicação diretamente no Azure AD.

> [!Note]
> Certifique-se de que utiliza apenas uma aplicação de galeria.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada por um único sign-on AWS (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Amazon Web Services (AWS) suporta **SP e IDP** iniciado SSO

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar Serviços Web amazon (AWS) da galeria

Para configurar a integração da Amazon Web Services (AWS) no AD Azure, é necessário adicionar a Amazon Web Services (AWS) da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho, conta escolar ou conta pessoal da Microsoft.
1. No portal Azure, procure e selecione **O Diretório Ativo Azure**.
1. Dentro do menu geral do Azure Ative Directory, escolha **Aplicações Empresariais**  >  **Todas as aplicações**.
1. Selecione **Nova aplicação** para adicionar uma aplicação.
1. Na secção Add a partir da secção **de galeria,** digite Amazon **Web Services (AWS)** na caixa de pesquisa.
1. Selecione **Amazon Web Services (AWS)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-amazon-web-services-aws"></a>Configure e teste Azure AD SSO para serviços web da Amazon (AWS)

Configure e teste Azure AD SSO com Amazon Web Services (AWS) usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Amazon Web Services (AWS).

Para configurar e testar o Azure AD SSO com a Amazon Web Services (AWS), execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Amazon Web Services (AWS) SSO](#configure-amazon-web-services-aws-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Amazon Web Services (AWS) test user](#create-amazon-web-services-aws-test-user)** - para ter uma contrapartida de B.Simon na Amazon Web Services (AWS) que está ligada à representação AD AD do utilizador.
    1. **[Como configurar o fornecimento de funções nos Serviços Web da Amazon (AWS)](#how-to-configure-role-provisioning-in-amazon-web-services-aws)**
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações da **Amazon Web Services (AWS),** encontre a secção **Gerir** e selecione um único sinal de **sação.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** atualize tanto **o Identifier (ID da Entidade) como** o URL de **resposta** com o mesmo valor predefinido: `https://signin.aws.amazon.com/saml` . Tem de selecionar **Guardar** para guardar as alterações de configuração.

1. Quando estiver a configurar mais de um caso, forneça um valor de identificador. A partir de segunda instância, utilize o seguinte formato, incluindo um **#** sinal para especificar um valor SPN único.

    `https://signin.aws.amazon.com/saml#2`

1. A aplicação AWS espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação AWS espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome  | Atributo de origem  | Espaço de Nomes |
    | --------------- | --------------- | --------------- |
    | Nome de FunSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Função | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes` |
    | SessãoDuração | "fornecer um valor entre 900 segundos (15 minutos) a 43200 segundos (12 horas)" |  `https://aws.amazon.com/SAML/Attributes` |

    > [!NOTE]
    > A AWS espera funções para utilizadores atribuídos à aplicação. Por favor, crie estas funções em Azure AD para que os utilizadores possam ser atribuídos as funções apropriadas. Para entender como configurar papéis em Azure AD, consulte [aqui](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui)

1. Na **configuração de um único sinal com** a página SAML, na caixa de diálogo do Certificado de Assinatura **SAML** (Passo 3), selecione **Adicionar um certificado**.

    ![Criar novo Certificado SAML](common/add-saml-certificate.png)

1. Gere um novo certificado de assinatura SAML e, em seguida, selecione **Novo Certificado**. Insira um endereço de e-mail para notificações de certificado.
   
    ![Novo Certificado SAML](common/new-saml-certificate.png) 

1. Na secção **certificado de assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](./media/amazon-web-service-tutorial/certificate.png)

1. Na secção Configurar os **Serviços Web da Amazon (AWS),** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. No portal Azure, procure e selecione **O Diretório Ativo Azure**.
1. Dentro do menu geral do Azure Ative Directory, escolha **Utilizadores**  >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso aos Serviços Web da Amazon (AWS).

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Amazon Web Services (AWS)**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-amazon-web-services-aws-sso"></a>Configure Amazon Web Services (AWS) SSO

1. Numa janela de navegador diferente, inscreva-se no site da empresa AWS como administrador.

2. Selecione **AWS Home**.

    ![Screenshot do site da empresa AWS, com ícone AWS Home em destaque][11]

3. Selecione **Gestão de Identidade e Acesso.**

    ![Screenshot da página de serviços AWS, com IAM em destaque][12]

4. Selecione **Fornecedores de**  >  **Identidade Criar Fornecedor.**

    ![Screenshot da página IAM, com Fornecedores de Identidade e Provedor de Criação em destaque][13]

5. Na página **Configure Provider,** execute os seguintes passos:

    ![Screenshot do Fornecedor de Configuração][14]

    a. Para **o Tipo de Fornecedor**, selecione **SAML**.

    b. Para **o Nome do Fornecedor,** escreva um nome de fornecedor (por exemplo: *WAAD*).

    c. Para fazer o upload do **ficheiro de metadados** descarregado a partir do portal Azure, selecione **Choose File**.

    d. Selecione **Next Step**.

6. Na página **'Verificar informações do fornecedor',** selecione **Criar**.

    ![Screenshot de Verificar Informações do Fornecedor, com Criar em destaque][15]

7. Selecione **Funções**  >  **Criar função**.

    ![Screenshot da página Roles][16]

8. Na página **'Criar' função,** execute os seguintes passos:  

    ![Screenshot da página de função Criar][19]

    a. Sob **o tipo de entidade fidedigna** Select , selecione **SAML 2.0 Federation**.

    b. Em **Escolha um Fornecedor SAML 2.0**, selecione o fornecedor **SAML** que criou anteriormente (por exemplo: *WAAD*).

    c. Selecione **Permitir o acesso a consolas programáticas e AWS Management.**
  
    d. Selecione **Seguinte: Permissões**.

9. Na caixa de diálogo **de políticas de permissões anexar,** anexe a política apropriada, por sua organização. Em seguida, selecione **Seguinte: Revisão**.  

    ![Screenshot da caixa de diálogo de política de permissões attach][33]

10. Na caixa de diálogo **review,** execute os seguintes passos:

    ![Screenshot da caixa de diálogo review][34]

    a. Em **nome de papel,** insira o seu nome de papel.

    b. Na **descrição da função,** insira a descrição.

    c. Selecione **Criar função**.

    d. Crie o número de papéis necessários e mapee-os para o fornecedor de identidade.

11. Utilize credenciais de conta de serviço AWS para obter as funções da conta AWS no provisionamento do utilizador Azure AD. Para isso, abra a casa de consola AWS.

12. Selecione **Serviços**. Em Conformidade de **Segurança, Identidade &,** selecione **IAM**.

    ![Screenshot da casa de consola AWS, com Serviços e IAM em destaque](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Na secção IAM, selecione **Políticas**.

    ![Screenshot da secção IAM, com Políticas em destaque](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Crie uma nova política selecionando **Criar a política** para obter as funções da conta AWS no provisionamento do utilizador Azure AD.

    ![Screenshot da página de função Create, com a política de criar em destaque](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Crie a sua própria política para obter todos os papéis a partir de contas AWS.

    ![Screenshot da página de política create, com JSON em destaque](./media/amazon-web-service-tutorial/policy1.png)

    a. Na **política Criar,** selecione o separador **JSON.**

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

    c. Selecione **a política de Revisão** para validar a política.

    ![Screenshot da página de política criar](./media/amazon-web-service-tutorial/policy5.png)

16. Defina a nova política.

    ![Screenshot da página de política create, com os campos de nome e descrição em destaque](./media/amazon-web-service-tutorial/policy2.png)

    a. Para **citar nome,** **insira AzureAD_SSOUserRole_Policy**.

    b. Para **descrição**, introduza **Esta política permitirá obter as funções a partir de contas AWS**.

    c. Selecione **Criar política**.

17. Crie uma nova conta de utilizador no serviço AWS IAM.

    a. Na consola AWS IAM, selecione **Utilizadores**.

    ![Screenshot da consola AWS IAM, com os Utilizadores em destaque](./media/amazon-web-service-tutorial/policy3.png)

    b. Para criar um novo utilizador, **selecione Adicionar utilizador**.

    ![Screenshot do botão de utilizador Adicionar](./media/amazon-web-service-tutorial/policy4.png)

    c. Na secção **utilizador Adicionar:**

    ![Screenshot da página de utilizador Adicionar, com nome de utilizador e tipo de acesso em destaque](./media/amazon-web-service-tutorial/adduser1.png)

    * Introduza o nome de utilizador como **AzureADRoleManager**.

    * Para o tipo de acesso, selecione **acesso programático**. Desta forma, o utilizador pode invocar as APIs e obter as funções da conta AWS.

    * Selecione **As Próximas Permissões**.

18. Crie uma nova política para este utilizador.

    ![A screenshot mostra a página do utilizador Adicionar onde pode criar uma política para o utilizador.](./media/amazon-web-service-tutorial/adduser2.png)

    a. **Selecione Fixe diretamente as políticas existentes**.

    b. Procure a política recém-criada na secção **de filtros AzureAD_SSOUserRole_Policy**.

    c. Selecione a apólice e, em seguida, selecione **Seguinte: Revisão**.

19. Reveja a política ao utilizador anexo.

    ![Screenshot da página de utilizador Adicionar, com o utilizador Criar em destaque](./media/amazon-web-service-tutorial/adduser3.png)

    a. Reveja o nome de utilizador, o tipo de acesso e a política mapeada ao utilizador.

    b. Selecione **Criar utilizador**.

20. Descarregue as credenciais de utilizador de um utilizador.

    ![A screenshot mostra a página do utilizador Adicionar com um botão Download c s v para obter credenciais do utilizador.](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copie a chave de acesso ao utilizador **ID** e **a chave de acesso secreta.**

    b. Introduza estas credenciais na secção de provisionamento do utilizador Azure AD para obter as funções da consola AWS.

    c. Selecione **Fechar**.

### <a name="how-to-configure-role-provisioning-in-amazon-web-services-aws"></a>Como configurar o fornecimento de funções nos Serviços Web da Amazon (AWS)

1. No portal de gestão AZure AD, na app AWS, aceda ao **Provisioning**.

    ![Screenshot da app AWS, com Provisioning em destaque](./media/amazon-web-service-tutorial/provisioning.png)

2. Insira a chave de acesso e o segredo nos campos de segredo de **clientes** e **Token Secreto,** respectivamente.

    ![Screenshot da caixa de diálogo de credenciais de Admin](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Introduza a chave de acesso ao utilizador AWS no campo **de segredo de cliente.**

    b. Insira o segredo do utilizador da AWS no campo **Secret Token.**

    c. Selecione **Testar Ligação**.

    d. Guarde a definição selecionando **Guardar**.

3. Na secção **Definições,** para **o Estado de Provisionamento**, selecione **On**. Em seguida, selecione **Guardar**.

    ![Screenshot da secção Definições, com On em destaque](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> O serviço de fornecimento importa funções apenas de AWS a Azure AD. O serviço não fornece utilizadores e grupos de Azure AD a AWS.

> [!NOTE]
> Depois de guardar as credenciais de provisionamento, deve esperar que o ciclo de sincronização inicial se deslo sente. O sync geralmente demora cerca de 40 minutos a terminar. Pode ver o estado na parte inferior da página **de Provisionamento,** em **Estado Atual**.

### <a name="create-amazon-web-services-aws-test-user"></a>Criar utilizador de teste amazon Web Services (AWS)

O objetivo desta secção é criar um utilizador chamado B.Simon in Amazon Web Services (AWS). A Amazon Web Services (AWS) não precisa de um utilizador para ser criado no seu sistema para SSO, por isso não precisa de realizar nenhuma ação aqui.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Amazon Web Services (AWS) Assinar no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de login da Amazon Web Services (AWS) e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito nos Serviços Web da Amazon (AWS) para os quais configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo da Amazon Web Services (AWS) no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito nos Serviços Web da Amazon (AWS) para os quais configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="known-issues"></a>Problemas conhecidos

 * Na secção **de Provisionamento,** a subsecção **de Mapeamentos** mostra um "Loading..." mensagem, e nunca exibe os mapeamentos de atributos. O único fluxo de trabalho de provisionamento suportado hoje é a importação de funções da AWS para a Azure AD para seleção durante uma atribuição de utilizador ou grupo. Os mapeamentos de atributos para isto são pré-determinados, e não são configuráveis.

 * A secção **de Provisionamento** só suporta a entrada de um conjunto de credenciais para um inquilino da AWS de cada vez. Todas as funções importadas são escritas para a `appRoles` propriedade do [ `servicePrincipal` objeto](/graph/api/resources/serviceprincipal?view=graph-rest-beta) AD AZure para o inquilino da AWS.

   Vários inquilinos da AWS (representados `servicePrincipals` por) podem ser adicionados à Azure AD da galeria para provisionamento. Há um problema conhecido, no entanto, com não ser capaz de escrever automaticamente todas as funções importadas a partir dos múltiplos AWS `servicePrincipals` usados para provisão para o único `servicePrincipal` usado para SSO.

   Como solução alternativa, pode utilizar a API do [Microsoft Graph](/graph/api/resources/serviceprincipal?view=graph-rest-beta) para extrair todos os `appRoles` importados em cada AWS `servicePrincipal` onde o provisionamento está configurado. Pode adicionar posteriormente estas cordas de função à AWS onde o `servicePrincipal` SSO está configurado.

* As funções devem satisfazer os seguintes requisitos a serem elegíveis para serem importados da AWS para a Azure AD:

  * As funções devem ter exatamente um fornecedor de saml definido em AWS
  * O comprimento combinado do ARN (Nome de Recursos da Amazon) para o papel e o ARN para o fornecedor de saml associado deve ser inferior a 120 caracteres

## <a name="next-steps"></a>Passos seguintes

Assim que configurar a Amazon Web Services (AWS) pode impor o Session Control, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)


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