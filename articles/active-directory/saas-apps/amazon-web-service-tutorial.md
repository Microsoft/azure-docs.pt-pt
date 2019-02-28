---
title: 'Tutorial: Integração do Active Directory do Azure com o Amazon Web Services (AWS) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e49bc5a468777197eaf88a492566a606e7b9f93
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961878"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Tutorial: Integração do Active Directory do Azure com o Amazon Web Services (AWS)

Neste tutorial, saiba como integrar o Amazon Web Services (AWS) com o Azure Active Directory (Azure AD).
Integrar o Amazon Web Services (AWS) com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso para o Amazon Web Services (AWS).
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Amazon Web Services (AWS) (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Pode configurar vários identificadores para múltiplas instâncias conforme mostrado a seguir.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com estes valores, do Azure AD irá remover o valor de **#** e enviar o valor correto `https://signin.aws.amazon.com/saml` como o URL de audiência no SAML Token.

**Recomendamos usar essa abordagem pelos seguintes motivos:**

a. Cada aplicativo fornecerá a o exclusivo de X509 certificado e por isso, cada instância podem ter uma data de expiração de certificado diferente e pode gerenciar isso com base no facto de conta AWS individual. Em geral rollover de certificado será fácil neste caso.

b. Pode ativar o aprovisionamento de utilizadores com aplicação AWS no Azure AD e, em seguida, o nosso serviço irá buscar o todas as funções a partir dessa conta AWS. Não é preciso adicionar manualmente ou atualizar as funções AWS na aplicação.

c. Pode atribuir o proprietário do aplicativo individualmente para a aplicação que pode gerir a aplicação diretamente no Azure AD.

> [!Note]
> Certifique-se de que utilizar apenas as aplicações de galeria

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Amazon Web Services (AWS), terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Amazon Web Services (AWS) único início de sessão na subscrição ativada

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Se pretender integrar várias contas AWS para uma conta do Azure para início de sessão único no, consulte [isso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) artigo.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Amazon Web Services (AWS) suporta **SP e IDP** iniciada SSO

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionando o Amazon Web Services (AWS) da Galeria

Para configurar a integração do Amazon Web Services (AWS) para o Azure AD, terá de adicionar Amazon Web Services (AWS) a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Amazon Web Services (AWS) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Amazon Web Services (AWS)**, selecione **Amazon Web Services (AWS)** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Amazon Web Services (AWS) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Amazon Web Services (AWS) com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Amazon Web Services (AWS) deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Amazon Web Services (AWS), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Amazon Web Services (AWS) início de sessão único](#configure-amazon-web-services-aws-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)**  - para ter um equivalente da Eduarda Almeida na Amazon Web Services (AWS) que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o Amazon Web Services (AWS), execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Amazon Web Services (AWS)** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, o utilizador não tem de realizar qualquer passo como a aplicação já está pré-integrada com o Azure.  Clique em **Guardar**.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. Quando estiver a configurar mais de uma instância, forneça o valor do identificador. Da segunda instância e posteriores, forneça o valor do identificador no seguinte formato. Utilize um **#** inscrever-se de especificar um valor exclusivo do SPN.

    `https://signin.aws.amazon.com/saml#2`

    ![URLs de domínio do Amazon Web Services (AWS) e únicas início de sessão em informações](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. Aplicação do Amazon Web Services (AWS) espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name  | Atributo de origem  | Espaço de nomes |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Função            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "fornecer um valor entre 900 segundos (15 minutos) para 43200 segundos (12 horas)" |  https://aws.amazon.com/SAML/Attributes |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Na **espaço de nomes** caixa de texto, digite o valor de espaço de nomes mostrado para essa linha.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Sobre o **definido no Amazon Web Services (AWS)** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Configurar o Amazon Web Services (AWS) início de sessão único

1. Numa janela do browser diferente, início de sessão no site da sua empresa Amazon Web Services (AWS) como administrador.

2. Clique em **AWS Home**.

    ![Configurar a página inicial do início de sessão único][11]

3. Clique em **gestão de acesso e identidades**.

    ![Configurar a identidade de início de sessão única][12]

4. Clique em **fornecedores de identidade**e, em seguida, clique em **criar fornecedor**.

    ![Configurar o fornecedor de início de sessão único][13]

5. Sobre o **configurar o fornecedor** caixa de diálogo página, execute os seguintes passos:

    ![Configurar o início de sessão único de caixa de diálogo][14]

    a. Como **tipo de fornecedor**, selecione **SAML**.

    b. Na **nome do fornecedor** caixa de texto, escreva um nome de fornecedor (por exemplo: *WAAD*).

    c. A carregar seu transferido **ficheiro de metadados** a partir do portal do Azure, clique em **Escolher ficheiro**.

    d. Clique em **passo seguinte**.

6. Sobre o **Certifique-se de informações do fornecedor** página de diálogo, clique em **criar**.

    ![Configurar o início de sessão único verificar][15]

7. Clique em **funções**e, em seguida, clique em **criar função**.

    ![Configurar funções de início de sessão únicas][16]

8. Sobre o **criar função** página, execute os seguintes passos:  

    ![Configurar a confiança de início de sessão única][19]

    a. Selecione **Federação SAML 2.0** sob **selecione o tipo de entidade fidedigna**.

    b. Sob **escolher uma secção de SAML 2.0 fornecedor**, selecione a **fornecedor de SAML** que criou anteriormente (por exemplo: *WAAD*)

    c. Selecione **permitir programática e o acesso à consola de gestão do AWS**.
  
    d. Clique em **seguinte: Permissões**.

9. Sobre o **políticas de permissões de anexar** caixa de diálogo, anexe política adequada de acordo com a sua organização. Clique em **seguinte: Revisão**.  

    ![Configurar a política de início de sessão única][33]

10. Sobre o **revisão** caixa de diálogo, execute os seguintes passos:

    ![Configurar a revisão de início de sessão único][34]

    a. Na **nome da função** caixa de texto, introduza o seu nome de função.

    b. Na **descrição da função** caixa de texto, introduza a descrição.

    c. Clique em **criar função**.

    d. Crie tantas funções conforme necessário e mapeá-los para o fornecedor de identidade.

11. Utilize as credenciais de conta de serviço do AWS para obter as funções da conta AWS no aprovisionamento de utilizador do Azure AD. Para isso, abra a consola AWS doméstica.

12. Clique em **serviços** -> **segurança, identidade e conformidade** -> **IAM**.

    ![a obter as funções do conta AWS](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Selecione o **políticas** separador na secção IAM.

    ![a obter as funções do conta AWS](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Criar uma nova política ao clicar em **criar política** para obter as funções da conta AWS no aprovisionamento de utilizador do Azure AD.

    ![Criar nova política](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Crie a sua política para obter todas as funções das contas AWS, efetuando os seguintes passos:

    ![Criar nova política](./media/amazon-web-service-tutorial/policy1.png)

    a. Na **"Criar política de"** secção, clique em **"JSON"** separador.

    b. No documento da política, adicione o abaixo JSON.

    ```

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

    c. Clique em **botão rever política** para validar a política.

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy5.png)

16. Definir o **nova política** , efetuando os seguintes passos:

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy2.png)

    a. Forneça o **nome da política** como **AzureAD_SSOUserRole_Policy**.

    b. Pode fornecer **Descrição** para a política conforme **esta política permite para buscar as funções de contas AWS**.

    c. Clique em **"Criar a política"** botão.

17. Crie uma nova conta de utilizador no AWS IAM serviço, efetuando os seguintes passos:

    a. Clique em **utilizadores** navegação na consola do AWS IAM.

    ![Definir a nova política](./media/amazon-web-service-tutorial/policy3.png)

    b. Clique em **adicionar utilizador** botão para criar um novo utilizador.

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/policy4.png)

    c. Na **adicionar utilizador** secção, execute os seguintes passos:

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/adduser1.png)

    * Introduza o nome de utilizador como **AzureADRoleManager**.

    * O tipo de acesso, selecione o **acesso programático** opção. Desta forma, o utilizador pode invocar as APIs e obter as funções de conta AWS.

    * Clique nas **permissões seguinte** botão no canto inferior direito.

18. Agora, crie uma nova política para este utilizador, efetuando os seguintes passos:

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/adduser2.png)

    a. Clique nas **anexar as políticas existentes diretamente** botão.

    b. Pesquisa para a política recentemente criada na secção dos filtros **AzureAD_SSOUserRole_Policy**.

    c. Selecione o **diretiva** e, em seguida, clique no **seguinte: Revisão** botão.

19. Rever a política para o usuário anexado ao realizar os passos seguintes:

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/adduser3.png)

    a. Reveja o nome de utilizador, o tipo de acesso e a política mapeado para o utilizador.

    b. Clique nas **criar utilizador** botão no canto inferior direito para criar o utilizador.

20. Transferir as credenciais de utilizador de um utilizador, efetuando os seguintes passos:

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copie o usuário **acesso ID da chave** e **chave de acesso a segredos**.

    b. Introduza estas credenciais para o utilizador do Azure AD aprovisionamento secção para obter as funções a partir da consola do AWS.

    c. Clique em **fechar** na parte inferior.

21. Navegue para **aprovisionamento de utilizadores** secção da aplicação do Amazon Web Services no Portal de gestão do Azure AD.

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/provisioning.png)

22. Introduza o **chave de acesso** e **segredo** no **segredo do cliente** e **segredo de Token** campo, respetivamente.

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Introduza a chave de acesso de utilizador do AWS no **clientsecret** campo.

    b. Introduza o segredo de utilizador do AWS no **segredo de Token** campo.

    c. Clique nas **Testar ligação** botão e deve conseguir testar esta ligação com êxito.

    d. Guardar a definição ao clicar no **guardar** botão na parte superior.

23. Agora, certifique-se de que ativa o estado de aprovisionamento **no** da secção definições da, fazendo a mudança no e, em seguida, clicando no **guardar** botão na parte superior.

    ![Adicionar utilizador](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para o Amazon Web Services (AWS).

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Amazon Web Services (AWS)**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Amazon Web Services (AWS)**.

    ![A ligação do Amazon Web Services (AWS) na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-amazon-web-services-aws-test-user"></a>Criar utilizador de teste do Amazon Web Services (AWS)

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida na Amazon Web Services (AWS). Amazon Web Services (AWS) não precisa de um utilizador a ser criado no seu sistema para SSO, por isso não terá de efetuar qualquer ação aqui.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do Amazon Web Services (AWS) no painel de acesso, deve ser automaticamente conectado para o Amazon Web Services (AWS) para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Problemas conhecidos

 * Na **aprovisionamento** secção, o **mapeamentos** subsecção mostrará uma mensagem "A carregar..." e nunca apresentar os mapeamentos de atributos. O fluxo de trabalho de aprovisionamento apenas suportado atualmente é a importação das funções do AWS para o Azure AD para seleção durante a atribuição de utilizador/grupo. Os mapeamentos de atributos para isso são predeterminado e não é configurável.
 
 * O **aprovisionamento** secção suporta apenas a introdução de um conjunto de credenciais para um inquilino do AWS ao mesmo tempo. Todas as funções importadas são escritas para a propriedade appRoles do Azure AD [objeto servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) para o AWS de inquilino. Vários inquilinos AWS (representados por servicePrincipals) podem ser adicionados para o Azure AD a partir da Galeria para o aprovisionamento, no entanto, é um problema conhecido com a não ser possível escrever todas as funções importadas automaticamente da vários servicePrincipals AWS utilizado para o aprovisionamento no serviceprincipal tem um único utilizado para início de sessão único. Como solução, o [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) pode ser utilizado para extrair todos appRoles importados para cada servicePrincipal AWS em que o aprovisionamento está configurado. Essas cadeias de caracteres de função podem ser adicionadas posteriormente para o servicePrincipal AWS em que o início de sessão único está configurado.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

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
