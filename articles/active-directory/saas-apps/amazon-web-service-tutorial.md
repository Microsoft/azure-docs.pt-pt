---
title: 'Tutorial: Integração do Active Directory do Azure com o Amazon Web Services (AWS) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f897653442a3e1b2d6098b3be60c85e75ca54f9a
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551485"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Tutorial: Integrar o Amazon Web Services (AWS) com o Azure Active Directory

Neste tutorial, irá aprender como integrar o Amazon Web Services (AWS) com o Azure Active Directory (Azure AD). Quando integrar o Amazon Web Services (AWS) com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso para o Amazon Web Services (AWS).
* Permita que os utilizadores ser automaticamente sessão iniciada para o Amazon Web Services (AWS) com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Pode configurar vários identificadores para múltiplas instâncias conforme mostrado a seguir.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Com estes valores, do Azure AD irá remover o valor de **#** e enviar o valor correto `https://signin.aws.amazon.com/saml` como o URL de audiência no SAML Token.

**Recomendamos usar essa abordagem pelos seguintes motivos:**

a. Cada aplicativo irá fornecer-lhe um exclusivo X509 certificado. Cada instância de instância da aplicação do AWS, em seguida, pode ter uma data de expiração de certificado diferente que pode ser gerenciada individualmente conta AWS. Em geral rollover de certificado será mais fácil neste caso.

b. Pode ativar o aprovisionamento de utilizadores com aplicação AWS no Azure AD e, em seguida, o nosso serviço irá buscar o todas as funções a partir dessa conta AWS. Não é preciso adicionar manualmente ou atualizar as funções AWS na aplicação.

c. Pode atribuir o proprietário do aplicativo individualmente para a aplicação que pode gerir a aplicação diretamente no Azure AD.

> [!Note]
> Certifique-se de que utilizar apenas as aplicações de galeria

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Amazon Web Services (AWS) início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Amazon Web Services (AWS) suporta **SP e IDP** iniciada SSO.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionando o Amazon Web Services (AWS) da Galeria

Para configurar a integração do Amazon Web Services (AWS) para o Azure AD, terá de adicionar Amazon Web Services (AWS) a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Amazon Web Services (AWS)** na caixa de pesquisa.
1. Selecione **Amazon Web Services (AWS)** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com o Amazon Web Services (AWS) com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Amazon Web Services (AWS).

Para configurar e testar o SSO do Azure AD com o Amazon Web Services (AWS), conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar o Amazon Web Services (AWS)](#configure-amazon-web-services-aws)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)**  ter um equivalente de B.Simon no Amazon Web Services (AWS) que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Amazon Web Services (AWS)** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, a aplicação está pré-configurada e os URLs necessários já estão previamente preenchidos com o Azure. O utilizador tem de guardar a configuração ao clicar o **guardar** botão.

5. Quando estiver a configurar mais de uma instância, forneça o valor do identificador. Da segunda instância e posteriores, forneça o valor do identificador no seguinte formato. Utilize um **#** inscrever-se de especificar um valor exclusivo do SPN.

    `https://signin.aws.amazon.com/saml#2`

6. Aplicação do Amazon Web Services (AWS) espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir a caixa de diálogo de atributos do utilizador.

    ![image](common/edit-attribute.png)

7. Além dos acima, o aplicativo do Amazon Web Services (AWS) espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:

    | Name  | Atributo de origem  | Espaço de Nomes |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
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

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **XML de metadados de Federação** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Sobre o **definir a cópia de segurança do Amazon Web Services (AWS)** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-amazon-web-services-aws"></a>Configure Amazon Web Services (AWS)

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

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado B.Simon.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único, concedendo acesso para o Amazon Web Services (AWS).

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Amazon Web Services (AWS)** .
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-amazon-web-services-aws-test-user"></a>Criar utilizador de teste do Amazon Web Services (AWS)

O objetivo desta secção é criar um utilizador chamado B.Simon no Amazon Web Services (AWS). Amazon Web Services (AWS) não precisa de um utilizador a ser criado no seu sistema para SSO, por isso não terá de efetuar qualquer ação aqui.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico do Amazon Web Services (AWS) no painel de acesso, deve ser automaticamente conectado para o Amazon Web Services (AWS) para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Problemas conhecidos

 * Na **aprovisionamento** secção, o **mapeamentos** subsecção mostrará uma mensagem "A carregar..." e nunca apresentar os mapeamentos de atributos. O fluxo de trabalho de aprovisionamento apenas suportado atualmente é a importação das funções do AWS para o Azure AD para seleção durante a atribuição de utilizador/grupo. Os mapeamentos de atributos para isso são predeterminado e não é configurável.
 
 * O **aprovisionamento** secção suporta apenas a introdução de um conjunto de credenciais para um inquilino do AWS ao mesmo tempo. Todas as funções importadas são escritas para a propriedade appRoles do Azure AD [objeto servicePrincipal](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) para o AWS de inquilino. Vários inquilinos AWS (representados por servicePrincipals) podem ser adicionados para o Azure AD a partir da Galeria para o aprovisionamento, no entanto, é um problema conhecido com a não ser possível escrever todas as funções importadas automaticamente da vários servicePrincipals AWS utilizado para o aprovisionamento no serviceprincipal tem um único utilizado para início de sessão único. Como solução, o [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) pode ser utilizado para extrair todos appRoles importados para cada servicePrincipal AWS em que o aprovisionamento está configurado. Essas cadeias de caracteres de função podem ser adicionadas posteriormente para o servicePrincipal AWS em que o início de sessão único está configurado.

## <a name="additional-resources"></a>Recursos Adicionais

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
