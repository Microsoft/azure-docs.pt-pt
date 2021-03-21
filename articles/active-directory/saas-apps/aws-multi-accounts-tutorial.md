---
title: 'Tutorial: Integração do Azure Ative Directory com a Amazon Web Services para ligar várias contas | Microsoft Docs'
description: Saiba como configurar um único sign-on entre a Azure AD e a Amazon Web Services (tutorial legado).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0ddcb239ba106bcdc2f0a29d68eb395876fadc06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100384117"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Tutorial: Integração do Azure Ative Directory com a Amazon Web Services

Neste tutorial, você aprende a integrar o Azure Ative Directory (Azure AD) com a Amazon Web Services (AWS) (tutorial legado).

Esta integração proporciona os seguintes benefícios:

- Você pode controlar em Azure AD que tem acesso a AWS.
- Pode ativar que os seus utilizadores acedam automaticamente à AWS utilizando um único sindem (SSO) com as suas contas AD Azure.
- Pode gerir as suas contas num local central, o portal Azure.

![Diagrama de integração AD AZure com AWS.](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Recomendamos que *não* conecte uma aplicação AWS a todas as suas contas AWS. Em vez disso, recomendamos que utilize [a integração Azure AD SSO com a AWS](./amazon-web-service-tutorial.md) para configurar várias instâncias da sua conta AWS para várias instâncias de aplicações AWS em Azure AD. 

Recomendamos que *não* conecte uma aplicação AWS a todas as suas contas AWS, pelas seguintes razões:

* Utilize esta abordagem apenas se tiver um pequeno número de contas e funções AWS, porque este modelo não é escalável à medida que o número de contas AWS e as funções dentro delas aumentam. A abordagem não utiliza a funcionalidade de importação de funções AWS com o fornecimento de utilizadores Azure AD, pelo que tem de adicionar, atualizar ou eliminar manualmente as funções. 

* Tem de utilizar a abordagem do Microsoft Graph Explorer para corrigir todas as funções na aplicação. Não recomendamos a utilização da abordagem de ficheiro manifesto.

* Os clientes relatam que depois de terem adicionado ~1.200 papéis de aplicação para uma única aplicação AWS, qualquer nova operação na app começa a lançar os erros relacionados com o tamanho. Há um limite de tamanho duro para o objeto de aplicação.

* Tem de atualizar manualmente as funções à medida que são adicionadas em qualquer uma das contas. Trata-se, infelizmente, de uma abordagem *de substituição* e não de *um apêndice.* Além disso, se os números  da sua conta estão a crescer, isto torna-se uma relação &times; *n n* com contas e papéis.

* Todas as contas AWS usam o mesmo ficheiro XML de metadados da federação. No momento da caduca do certificado, a atualização do certificado em todas as contas AWS ao mesmo tempo pode ser um exercício maciço.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a AWS, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma assinatura AD Azure, pode obter um [período experimental de um mês.](https://azure.microsoft.com/pricing/free-trial/)
* Uma subscrição ativada pelo AWS SSO.

> [!NOTE]
> Não recomendamos que teste os passos neste tutorial num ambiente de produção a menos que seja necessário.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

A AWS apoia sSO iniciado pelo SP e iniciado pelo IDP.

## <a name="add-aws-from-the-gallery"></a>Adicione AWS da galeria

Para configurar a integração da AWS no Azure AD, adicione AWS da galeria à sua lista de aplicações de software gerido como um serviço (SaaS).

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço AZure AD com o quais pretende trabalhar.
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** digite os **Serviços Web** da Amazon na caixa de pesquisa.
1. Na lista de resultados, selecione **Amazon Web Services** e adicione a aplicação. Em poucos segundos, a aplicação é adicionada ao seu inquilino.

1. Vá ao painel **de propriedades** e, em seguida, copie o valor que é apresentado na caixa de ID do **objeto.**

    ![Screenshot da caixa de identificação do objeto no painel de propriedades.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Nesta secção, configura e testa o Azure AD com um único sign-on com AWS baseado num utilizador de teste chamado "Britta Simon".

Para um único sinal de sação a funcionar, o Azure AD precisa de saber o que o utilizador homólogo em AWS é para o utilizador AZure AD. Por outras palavras, é necessário estabelecer uma relação de ligação entre o utilizador Azure AD e o mesmo utilizador em AWS.

Em AWS, atribua o valor do nome de **utilizador** em Azure AD como o valor do nome de **utilizador** AWS para estabelecer a relação de ligação.

Para configurar e testar o Azure AD com um único sinal de inscrição com AWS, faça o seguinte:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configure a AWS SSO](#configure-aws-sso)** para configurar as definições SSO no lado da aplicação.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Nesta secção, você ativa Azure AD SSO no portal Azure e configura SSO na sua aplicação AWS, fazendo o seguinte:

1. No portal Azure, no painel esquerdo da página de integração de aplicações **amazon Web Services (AWS),** selecione **Single sign-on**.

    ![Screenshot do comando "Single sign-on".](common/select-sso.png)

1. No painel **de método de inscrição,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súm.

    ![Screenshot do painel "Selecione um único método de inscrição".](common/select-saml-option.png)

1. No conjunto único Sign-On com painel **SAML,** selecione o botão  **Editar** (ícone de lápis).

    ![Screenshot do botão Editar no painel "Configurar single Sign-On com SAML".](common/edit-urls.png)

1. Abre-se o **painel de configuração SAML básico.** Ignore esta secção, porque a aplicação está pré-enraizada com o Azure. Selecione **Guardar**.

   A aplicação AWS espera as afirmações SAML num formato específico. Pode gerir os valores destes atributos a partir da secção **Atributos & Reclamações** do Utilizador na página de integração da **aplicação.** 
   
1. Na **configuração Sign-On única com** a página SAML, selecione o botão **Editar.**

    ![Screenshot do botão Editar no painel "Atributos do Utilizador".](common/edit-attribute.png)

1. Na secção **'Reclamações** de Utilizador' do painel Atributos do **Utilizador,** configurar o atributo símbolo SAML utilizando os valores no quadro seguinte:

    | Name  | Atributo de origem  | Espaço de Nomes |
    | --------------- | --------------- | --------------- |
    | Nome de FunSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Função | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessãoDuração | "fornecer um valor de 900 segundos (15 minutos) a 43200 segundos (12 horas)" |  `https://aws.amazon.com/SAML/Attributes` |
  
   a. **Selecione Adicione nova reclamação** e, em seguida, no painel de reclamações do **utilizador Gerir,** faça o seguinte:

   ![Screenshot dos botões "Adicionar nova reclamação" e "Guardar" no painel "Reclamações do Utilizador".](common/new-save-attribute.png)

   ![Screenshot do painel "Gerir as alegações do utilizador".](common/new-attribute-details.png)

   b. Na caixa **Nome,** insira o nome do atributo.  

   c. Na caixa **Namespace,** insira o valor do espaço de nome.

   d. Para a **Fonte**, selecione **Atributo**.

   e. Na lista de **atributos Source,** selecione o atributo.

   f. Selecione **Ok** e, em seguida, selecione **Guardar**.

   >[!NOTE]
   >Para obter mais informações sobre papéis em AZure AD, consulte [adicionar os papéis da aplicação à sua aplicação e recebê-las no token](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione **Descarregue** para descarregar o ficheiro XML de metadados da federação e guarde-o para o seu computador.

   ![Screenshot do link de descarregamento "Federation Metadata XML" no painel "Certificado de assinatura SAML".](common/metadataxml.png)

### <a name="configure-aws-sso"></a>Configurar aWS SSO

1. Numa nova janela do navegador, inscreva-se no site da empresa AWS como administrador.

1. Selecione o ícone **AWS Home.**

   ![Screenshot do ícone "AWS Home".][11]

1. No painel de **serviços AWS,** em **Segurança, Identidade & Compliance,** selecione **IAM (Gestão de Acesso & Identidade)**.

    ![Screenshot do link "Gestão de Identidade e Acesso" no painel "AWS Services".][12]

1. No painel esquerdo, selecione **Fornecedores de Identidade** e, em seguida, selecione **Create Provider**.

    ![Screenshot do botão "Criar Fornecedor".][13]

1. No painel **de configure provider,** faça o seguinte:

    ![Screenshot do painel "Configure Provider".][14]

    a. Na lista de drop-down **do tipo fornecedor,** selecione **SAML**.

    b. Na caixa **'Nome provedor',** insira um nome de fornecedor (por exemplo. *WAAD).*

    c. Junto à caixa **de Documento de Metadados,** selecione **Choose File** para carregar o ficheiro XML de metadados da federação descarregado para o portal Azure.

    d. Selecione **Next Step**.

1. No painel **de informações do fornecedor de verificação,** selecione **Criar**.

    ![Screenshot do painel "Verificar informações do fornecedor".][15]

1. No painel esquerdo, selecione **Roles** e, em seguida, selecione **Criar função**.

    ![Screenshot do botão "Criar papel" no painel Roles.][16]

    > [!NOTE]
    > O comprimento combinado do papel Amazon Resource Name (ARN) e o provedor SAML ARN para um papel que está a ser importado deve ser de 240 ou menos caracteres.

1. Na página **'Criar' função,** faça o seguinte:  

    ![Screenshot do botão de entidade confiável "SAML 2.0" na página "Criar papel".][19]

    a. Sob **o tipo de entidade fidedigna** Select , selecione **SAML 2.0 Federation**.

    b. Em **Escolha um fornecedor SAML 2.0**, selecione o fornecedor SAML que criou anteriormente (por exemplo, *WAAD)*

    c. Selecione **Permitir o acesso a consolas programáticas e AWS Management.**

    d. Selecione **Seguinte: Permissões**.

1. Na caixa de pesquisa, insira **o Acesso ao Administrador,** selecione a caixa **de verificação AdministratorAccess** e, em seguida, selecione **Seguinte: Tags**.

    ![Screenshot da lista "Nome da política" com a política do AdministradorAccess selecionada.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. No painel **de etiquetas adicionar (opcional),** faça o seguinte:

    ![Screenshot do painel "Adicionar tags (opcional)".](./media/aws-multi-accounts-tutorial/config2.png)

    a. Na caixa **Chave,** insira o nome chave (por exemplo, *Azureadtest).*

    b. Na caixa **Valor (opcional),** introduza o valor chave no seguinte formato: `<accountname-aws-admin>` . O nome da conta deve estar em todas as letras minúsculas.

    c. Selecione **Seguinte: Revisão**.

1. No painel **de avaliação,** faça o seguinte:

    ![Screenshot do painel de comentários, com as caixas "Role name" e "Role description" em destaque.][34]

    a. Na caixa **de nomes Role,** insira o valor no seguinte formato: `<accountname-aws-admin>` .

    b. Na caixa **de descrição de funções,** insira o valor que usou para o nome da função.

    c. Selecione **Criar função**.

    d. Crie as funções que precisar e mapee-as para o fornecedor de identidade.

    > [!NOTE]
    > Da mesma forma, pode criar outras funções, como *o nome-nome-finança-administração*, *nome de conta-só utilizador,* *nome-devops-utilizador,* ou *accountname-tpm-user*, cada um com uma política diferente anexada a ele. Pode alterar estas políticas de função mais tarde, de acordo com os requisitos para cada conta AWS. É uma boa ideia manter as mesmas políticas para cada papel nas contas da AWS.

1. Certifique-se de que note o ID da conta AWS, quer a partir do painel de propriedades da Amazon Elastic Compute Cloud (Amazon EC2) ou do painel de instrumentos IAM, como mostra a seguinte imagem:

    ![Screenshot mostrando onde o ID da conta é exibido no painel "Gestão de Identidade e Acesso".](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Inscreva-se no portal Azure e, em seguida, vá para **grupos**.

1. Crie novos grupos com o mesmo nome dos papéis IAM que criou anteriormente e, em seguida, note o valor na caixa de identificação de **objetos** de cada um destes novos grupos.

    ![Screenshot dos detalhes da conta para um novo grupo.](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Assine fora da conta AWS atual e, em seguida, inscreva-se em outra conta onde pretende configurar SSO com Azure AD.

1. Depois de criar todos os papéis nas contas, eles são exibidos na lista **de Papéis** para essas contas.

    ![Screenshot da lista de papéis, mostrando o nome, descrição e entidades fidedignas de cada papel.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

Em seguida, você precisa capturar todos os papéis ARNs e entidades de confiança para todos os papéis em todas as contas. Terá de mapeá-los manualmente com a aplicação AZure AD. Para tal:

1. Selecione cada função para copiar o seu papel ARN e valores de entidade fidedigna. Vais precisar deles para todos os papéis que vais criar no Azure AD.

    ![Screenshot do painel sumário para o papel ARNs e entidades de confiança.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Repita o passo anterior para todas as funções em todas as contas e, em seguida, guarde-as num ficheiro de texto no seguinte formato: `<Role ARN>,<Trusted entities>` .

1. Abra [o Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)e, em seguida, faça o seguinte:

    a. Inscreva-se no site do Microsoft Graph Explorer com as credenciais de administração global ou coadministração para o seu inquilino.

    b. Precisa de permissões suficientes para criar os papéis. Selecione **permissões de modificação.**

      ![Screenshot do link "modificar permissões" no painel de autenticação do Microsoft Graph Explorer.](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Na lista de permissões, se ainda não tiver as permissões que são mostradas na imagem seguinte, selecione cada uma e, em seguida, selecione **Modificar Permissões**. 

      ![Screenshot da lista de permissões do Microsoft Graph Explorer, com as permissões apropriadas realçadas.](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Inscreva-se novamente no Graph Explorer e aceite as condições de utilização do site. 

    e. Na parte superior do painel, selecione **GET** para o método, selecione **beta** para a versão e, em seguida, na caixa de consulta, insira qualquer uma das seguintes: 
    
    * Para ir buscar todos os diretores de serviço ao seu inquilino, `https://graph.microsoft.com/beta/servicePrincipals` use. 
    * Se estiver a utilizar vários diretórios, use `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` , que contenha o seu domínio primário.

    ![Screenshot do painel de perguntas "Request Body" do Microsoft Graph Explorer.](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Na lista de diretores de serviço, obtenha o que precisa para modificar. 
    
    Também pode pesquisar a aplicação de todos os principais do serviço listados selecionando Ctrl+F. Para obter um principal de serviço específico, inclua na consulta o ID principal do objeto de serviço, que copiou anteriormente do painel Azure AD Properties, como mostrado aqui:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

      ![Screenshot mostrando uma consulta principal de serviço que inclui o ID do objeto.](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    exemplo, Extrair a propriedade appRoles do objeto principal do serviço.

    ![Screenshot do código para extrair a propriedade appRoles do objeto principal de serviço.](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Agora precisa de gerar novas funções para a sua aplicação. 

    i. O seguinte código JSON é um exemplo de um objeto appRoles. Crie um objeto semelhante para adicionar as funções que pretende para a sua aplicação.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Só pode adicionar novas funções depois de ter adicionado *msiam_access* para a operação do patch. Também pode adicionar os papéis que quiser, dependendo das necessidades da sua organização. A Azure AD envia o *valor* destas funções como valor de reclamação na resposta SAML.

    j. No Microsoft Graph Explorer, altere o método de **GET** para **PATCH**. Remeje o objeto principal de serviço com as funções desejando atualizando a propriedade appRoles, como a mostrada no exemplo anterior. Selecione **'Fazer's Questionry** para executar a operação de correção. Uma mensagem de sucesso confirma a criação do papel para a sua aplicação AWS.

      ![Screenshot do painel microsoft graph explorer, com o método alterado para PATCH.](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Depois de o diretor de serviço ser corrigido com mais funções, pode atribuir utilizadores e grupos às respetivas funções. Fá-lo no portal Azure, indo para a aplicação AWS e, em seguida, selecionando o separador **Utilizadores e Grupos** no topo.

1. Recomendamos que crie um novo grupo para cada papel AWS para que possa atribuir esse papel específico no grupo. Este mapeamento um-para-um significa que um grupo está atribuído a uma função. Em seguida, pode adicionar os membros que pertencem a esse grupo.

1. Depois de criar os grupos, selecione o grupo e atribua-o à aplicação.

    ![Screenshot do painel "Utilizadores e grupos".](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Os grupos aninhados não são apoiados quando atribuem grupos.

1. Para atribuir o papel ao grupo, selecione a função e, em seguida, **selecione Atribuir**.

    ![Screenshot do painel "Adicionar Atribuição".](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Depois de atribuir os papéis, pode vê-los refrescando a sua sessão no portal Azure.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de Ad Azure utilizando as minhas apps do Microsoft My.

Quando seleciona o azulejo **AWS** nas Minhas Apps, a página de aplicação AWS abre com a opção de selecionar a função.

![Screenshot da página AWS para testar SSO.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Também pode verificar a resposta da SAML para ver as funções serem passadas como reivindicações.

![Screenshot da resposta SAML.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Para obter mais informações sobre as Minhas Apps, consulte [iniciar sôr-se e iniciar aplicações a partir do portal My Apps.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Depois de configurar a AWS, pode impor o controlo da sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. Para obter mais informações, consulte [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security.](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
