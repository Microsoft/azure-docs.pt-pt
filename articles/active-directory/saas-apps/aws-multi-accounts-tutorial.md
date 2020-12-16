---
title: 'Tutorial: Integração do Azure Ative Directory com a Amazon Web Services (AWS) para ligar várias contas Microsoft Docs'
description: Saiba como configurar um único sign-on entre a Azure AD e a Amazon Web Services (AWS) (Legacy Tutorial).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: jeedes
ms.openlocfilehash: 7e71058e1899cf83e712025b534e51a1be1f6bdb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591791"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Tutorial: Integração do Azure Ative Directory com a Amazon Web Services (AWS) (Tutorial Legado)

Neste tutorial, você aprende a integrar o Azure Ative Directory (Azure AD) com a Amazon Web Services (AWS) (Legacy Tutorial).

A integração da Amazon Web Services (AWS) com a Azure AD proporciona-lhe os seguintes benefícios:

- Você pode controlar em Azure AD que tem acesso a Amazon Web Services (AWS).
- Pode permitir que os seus utilizadores se inscrevam automaticamente nos Serviços Web da Amazon (AWS) (Sign-on Único) com as suas contas AD Azure.
- Pode gerir as suas contas numa localização central - o portal Azure.

![Amazon Web Services (AWS) na lista de resultados](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Por favor, note que ligar uma aplicação AWS a todas as suas contas AWS não é a nossa abordagem recomendada. Em vez disso, recomendamos que utilize [esta](./amazon-web-service-tutorial.md) abordagem para configurar várias instâncias da conta AWS para várias instâncias de aplicações AWS em Azure AD. Só deve utilizar esta abordagem se tiver poucas Contas e Funções AWS, este modelo não é escalável à medida que as contas e funções AWS dentro destas contas crescem. Esta abordagem não utiliza a funcionalidade de importação de AWS Role utilizando o Provisioning do Utilizador Azure AD, pelo que tem de adicionar/atualizar/eliminar manualmente as funções. Para outras limitações nesta abordagem, consulte os detalhes abaixo.

**Por favor, note que não recomendamos a utilização desta abordagem por seguintes razões:**

* Tem de utilizar a abordagem do Microsoft Graph Explorer para corrigir todas as funções na aplicação. Não recomendamos a utilização da abordagem de ficheiro manifesto.

* Temos visto os clientes relatarem que depois de adicionarem #1200 papéis de aplicação para uma única aplicação AWS, qualquer operação na aplicação começou a lançar os erros relacionados com o tamanho. Há um limite de tamanho rígido no objeto de aplicação.

* Tem de atualizar manualmente o papel à medida que as funções são adicionadas em qualquer uma das contas, que é uma abordagem Substitua e não apend infelizmente. Além disso, se as suas contas estão a crescer, então isto torna-se n n n relação com contas e papéis.

* Todas as contas AWS utilizarão o mesmo ficheiro XML de metadados da Federação e no momento da caduca do certificado tem de conduzir este exercício maciço para atualizar o Certificado em todas as contas AWS ao mesmo tempo

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração Azure AD com a Amazon Web Services (AWS), precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Amazon Web Services (AWS) subscrição ativada

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a não ser que seja necessário.
- Se não tiver um ambiente experimental da AD Azure, pode [ter um julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Amazon Web Services (AWS) suporta **SP e IDP** iniciado SSO

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar Serviços Web amazon (AWS) da galeria

Para configurar a integração da Amazon Web Services (AWS) no AD Azure, é necessário adicionar a Amazon Web Services (AWS) da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **de galeria,** digite Amazon **Web Services (AWS)** na caixa de pesquisa.
1. Selecione **Amazon Web Services (AWS)** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

1. Assim que a aplicação for adicionada, vá à página **Propriedades** e copie o **ID do objeto.**

    ![ID de objeto](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar Azure AD SSO

Nesta secção, você configura e testa Azure AD single sign-on com Amazon Web Services (AWS) com base num utilizador de teste chamado "Britta Simon".

Para um único sign-on funcionar, o Azure AD precisa de saber o que o utilizador homólogo da Amazon Web Services (AWS) é para um utilizador em Azure AD. Por outras palavras, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Amazon Web Services (AWS).

Na Amazon Web Services (AWS), atribua o valor do nome de **utilizador** em Azure AD como o valor do nome de **utilizador** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD com a Amazon Web Services (AWS), execute os seguintes passos:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Amazon Web Services (AWS) Single Sign-On](#configure-amazon-web-services-aws-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure e configurar um único sinal na sua aplicação Amazon Web Services (AWS).

**Para configurar o Azure AD um único sinal de acesso com a Amazon Web Services (AWS), execute os seguintes passos:**

1. No portal Azure, na página de integração de aplicações da **Amazon Web Services (AWS),** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure e clique em **Guardar**.

5. A aplicação Amazon Web Services (AWS) espera as afirmações da SAML num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos & Reclamações** do Utilizador na página de integração de aplicações. Na **configuração de Sign-On única com** a página SAML, clique no botão **Editar** para abrir **o diálogo & Claims dos Atributos** do Utilizador.

    ![A screenshot mostra atributos do utilizador com o controlo de edição chamado.](common/edit-attribute.png)

6. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos:

    | Nome  | Atributo de origem  | Espaço de Nomes |
    | --------------- | --------------- | --------------- |
    | Nome de FunSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Função | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessãoDuração | "fornecer um valor entre 900 segundos (15 minutos) a 43200 segundos (12 horas)" |  `https://aws.amazon.com/SAML/Attributes` |

    1. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

        ![A Screenshot mostra as reclamações do Utilizador com adicionar nova reclamação e guardar chamado.](common/new-save-attribute.png)

        ![Screenshot mostra Gerir as alegações do utilizador onde pode introduzir os valores descritos neste passo.](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Na caixa de texto **Namespace,** digite o valor Namespace mostrado para esta linha.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

7. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metdata XML da Federação** e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Configurar os Serviços Web da Amazon (AWS) Sign-On únicos

1. Numa janela de navegador diferente, inscreva-se no site da empresa Amazon Web Services (AWS) como administrador.

1. Clique **em AWS home**.

    ![Configurar casa Sign-On única][11]

1. Clique em **Gestão de Identidade e Acesso.**

    ![Configurar identidade Sign-On única][12]

1. Clique em **Fornecedores de Identidade** e, em seguida, clique em **Criar Fornecedor.**

    ![Configure fornecedor único de Sign-On][13]

1. Na página de diálogo **do Fornecedor configurar,** execute os seguintes passos:

    ![Configurar diálogo único Sign-On][14]

    a. Como **Tipo de Fornecedor,** selecione **SAML**.

    b. Na caixa de texto **'Nome do Fornecedor',** escreva um nome de fornecedor (por exemplo: *WAAD*).

    c. Para fazer o upload do **seu ficheiro de metadados** descarregado a partir do portal Azure, clique em **Escolher Ficheiro**.

    d. Clique **no próximo passo**.

1. Na página de diálogo **de informação do fornecedor,** clique em **Criar**.

    ![Configurar Sign-On único verificar][15]

1. Clique em **Funções** e, em seguida, clique em **Criar função**.

    ![Configurar papéis de Sign-On único][16]

    > [!NOTE]
    > O comprimento combinado da Role ARN e do prestador de SAML ARN para uma função importada deve ser de 240 caracteres ou menos.

1. Na página **'Criar' função,** execute os seguintes passos:  

    ![Configure a Confiança Sign-On Única][19]

    a. Selecione **SAML 2.0 federação** sob **o tipo de entidade fidedigna Select**.

    b. Em **Escolha uma secção de Fornecedor SAML 2.0**, selecione o fornecedor **SAML** que criou anteriormente (por exemplo: *WAAD*)

    c. Selecione **Permitir o acesso a consolas programáticas e AWS Management.**

    d. Clique **em seguida: Permissões**.

1. **Procurar acesso ao administrador** na barra de pesquisa e selecionar a caixa de verificação **AdministratorAccess** e, em seguida, clicar em **Seguinte: Tags**.

    ![A screenshot mostra o AdministratorAccess selecionado como um nome de Política.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Na secção **Adicionar tags (opcional),** execute os seguintes passos:

    ![Adicionar etiquetas](./media/aws-multi-accounts-tutorial/config2.png)

    a. Na caixa de texto **chave,** insira o nome-chave para ex: Azureadtest.

    b. Na caixa de texto **Value (opcional),** introduza o valor chave utilizando o seguinte `accountname-aws-admin` formato. O nome da conta deve estar em todas as minúsculas.

    c. Clique **em seguida: Revisão**.

1. No diálogo **de revisão,** execute os seguintes passos:

    ![Configure a revisão de Sign-On única][34]

    a. Na caixa de texto **do nome funitário,** introduza o valor no seguinte padrão `accountname-aws-admin` .

    b. Na caixa de texto **de descrição de função,** insira o mesmo valor que utilizou para o nome da função.

    c. Clique **em Criar Função**.

    d. Crie o número de funções necessárias e mapee-as para o Fornecedor de Identidade.

    > [!NOTE]
    > Da mesma forma, criar outras funções como accountname-finance-admin, accountname-read-only-user, accountname-devops-user, accountname-tpm-user com diferentes políticas a serem anexadas. Mais tarde, estas políticas de função podem ser alteradas de acordo com os requisitos por conta AWS, mas é sempre melhor manter as mesmas políticas para cada papel nas contas AWS.

1. Por favor, tome nota do ID de conta para essa conta AWS, quer a partir de propriedades EC2, quer do painel IAM, conforme salientado abaixo:

    ![A imagem mostra onde a conta I D aparece na janela A W S.](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Agora assine no [portal Azure](https://portal.azure.com/) e navegue para **grupos.**

1. Crie novos grupos com o mesmo nome que o IAM Roles criados anteriormente e anotar os **IDs** de objeto destes novos grupos.

    ![Selecione Acesso ao Administrador1](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Assine a partir da conta AWS atual e faça login com outra conta onde pretende configurar um único sinal com Azure AD.

1. Uma vez que todos os papéis são criados nas contas, eles aparecem na lista **de Papéis** para essas contas.

    ![A screenshot mostra a lista de Papéis com nome de função, descrição e entidades fidedignas.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. Precisamos capturar todas as Entidades De Papel ARN e Trust para todos os papéis em todas as contas, que precisamos mapear manualmente com a aplicação AD AZure.

1. Clique nas funções para copiar valores **de Role ARN** e **Entidades Fidedignas.** Você precisa destes valores para todos os papéis que você precisa para criar em Azure AD.

    ![Configuração de funções2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Execute o passo acima para todas as funções em todas as contas e guarde todas elas em formato **Role ARN,Entidades Fidedignas** num bloco de notas.

1. Abra [o Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) noutra janela.

    1. Inscreva-se no site do Microsoft Graph Explorer utilizando as credenciais de administração global para o seu inquilino.

    1. Precisa de permissões suficientes para criar os papéis. Clique em **modificar permissões** para obter as permissões necessárias.

        ![Caixa de diálogo microsoft graph explorer1](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    1. Selecione as permissões seguintes da lista (se ainda não as tiver) e clique em "Modificar permissões" 

        ![Caixa de diálogo microsoft graph explorer2](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    1. Isto irá pedir-lhe para iniciar sessão de novo e aceitar o consentimento. Depois de aceitar o consentimento, inicia sessão no Microsoft Graph Explorer novamente.

    1. Altere a versão dropdown para **beta**. Para ir buscar todos os diretores de serviço ao seu inquilino, utilize a seguinte consulta: `https://graph.microsoft.com/beta/servicePrincipals` . Se estiver a utilizar vários diretórios, pode utilizar o seguinte padrão, que tem o seu domínio principal: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` .

        ![Caixa de diálogo microsoft Graph Explorer3](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    1. Da lista de diretores de serviços recolhidos, obtenha o que precisa para modificar. Também pode utilizar o Ctrl+F para pesquisar a aplicação a partir de todos os ServicePrincipals listados. Pode utilizar a seguinte consulta utilizando o **ID do Objeto Principal** de Serviço que copiou da página Azure AD Properties para chegar ao respetivo Principal de Serviço.

        `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

        ![Caixa de diálogo microsoft graph explorer4](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    1. Extrair a propriedade appRoles do objeto principal do serviço.

        ![Caixa de diálogo microsoft graph explorer5](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    1. Agora precisa de gerar novas funções para a sua aplicação. 

    1. Abaixo JSON é um exemplo de objeto appRoles. Crie um objeto semelhante para adicionar as funções que pretende para a sua aplicação.

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
    > Só é possível adicionar novas funções após o **msiam_access** para a operação do patch. Além disso, pode adicionar o número de papéis que quiser por sua necessidade da Organização. A Azure AD enviará o **valor** destas funções como valor de reclamação na resposta SAML.

    1. Volte para o Microsoft Graph Explorer e altere o método de **GET** para **PATCH**. Remeje o objeto principal de serviço para ter funções desejadas atualizando a propriedade appRoles semelhante à acima mostrada no exemplo. Clique **em 'Executar' para** executar a operação de correção. Uma mensagem de sucesso confirma a criação do papel para a sua aplicação Amazon Web Services.

        ![Caixa de diálogo microsoft Graph Explorer6](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Depois de o Diretor de Serviço ser corrigido com mais funções, pode atribuir Utilizadores/Grupos às respetivas funções. Isto pode ser feito indo ao portal e navegando para a aplicação Amazon Web Services. Clique no separador **Utilizadores e Grupos** na parte superior.

1. Recomendamos que crie novos grupos para cada papel da AWS para que possa atribuir esse papel específico nesse grupo. Note que este é um para um mapeamento para um grupo para um papel. Em seguida, pode adicionar os membros que pertencem a esse grupo.

1. Assim que os Grupos forem criados, selecione o grupo e atribua à aplicação.

    ![Configurar único Sign-On Adicionar1](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Os grupos aninhados não são apoiados na atribuição de grupos.

1. Para atribuir a função ao grupo, selecione a função e clique no botão **Atribuir** na parte inferior da página.

    ![Configurar único Sign-On Add2](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Por favor, note que precisa de refrescar a sua sessão no portal Azure para ver novas funções.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo amazon Web Services (AWS) no Painel de Acesso, deverá obter a página de aplicação da Amazon Web Services (AWS) com opção de selecionar o papel.

![Teste um único sinal-on1](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Também pode verificar a resposta da SAML para ver as funções serem passadas como reivindicações.

![Teste de sinal único 2](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar a Amazon Web Services (AWS) pode impor o Session Control, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

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