---
title: 'Tutorial: Integração de Diretórios Ativos Azure com a Amazon Web Services (AWS) para ligar várias contas [) Microsoft Docs'
description: Saiba como configurar um único sign-on entre a AD Azure e várias contas da Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: edd54352b1328c95ae2c3e466003b64eaa0fcfde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367993"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Tutorial: Integração de Diretórios Ativos Azure com várias contas da Amazon Web Services (AWS)

Neste tutorial, aprende-se a integrar o Azure Ative Directory (Azure AD) com várias contas da Amazon Web Services (AWS).

Integrar os Serviços Web da Amazon (AWS) com a Azure AD proporciona-lhe os seguintes benefícios:

- Você pode controlar em Azure AD que tem acesso a Amazon Web Services (AWS).
- Pode permitir que os seus utilizadores se inscrevam automaticamente nos Serviços Web da Amazon (AWS) (Single Sign-On) com as suas contas Azure AD.
- Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, veja o que é o acesso à aplicação e o único registo com o [Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) na lista de resultados](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Por favor, note que ligar uma aplicação AWS a todas as suas contas AWS não é a nossa abordagem recomendada. Em vez disso, recomendamos que utilize [esta](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) abordagem para configurar várias instâncias da conta AWS para várias instâncias de aplicações AWS em Azure AD.

**Por favor, note que não recomendamos usar esta abordagem por razões seguintes:**

* Tem de utilizar a abordagem do Microsoft Graph Explorer para corrigir todas as funções da aplicação. Não recomendamos usar a abordagem do ficheiro manifesto.

* Temos visto clientes a relatar que depois de adicionar as funções de aplicação ~1200 para uma única aplicação AWS, qualquer operação na aplicação começou a lançar os erros relacionados com o tamanho. Há um limite de tamanho rígido no objeto de aplicação.

* Tem de atualizar manualmente o papel à medida que as funções são adicionadas em qualquer uma das contas, que é uma abordagem de Substituição e não apêndice, infelizmente. Além disso, se as suas contas estão a crescer, então isto torna-se n x n relação com contas e papéis.

* Todas as contas DaWS estarão a usar o mesmo ficheiro Da Federação metadados XML e no momento da capotagem do certificado tem de conduzir este exercício maciço para atualizar o Certificado em todas as contas AWS ao mesmo tempo

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a Amazon Web Services (AWS), precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Subscrição de assinatura única da Amazon Web Services (AWS)

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização de um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de julgamento da AD Azure, pode [ter um julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Amazon Web Services (AWS) suporta **SP e IDP** iniciadoS SSO

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar Serviços Web da Amazon (AWS) da galeria

Para configurar a integração da Amazon Web Services (AWS) em Azure AD, você precisa adicionar Amazon Web Services (AWS) da galeria à sua lista de aplicações geridas saaS.

**Para adicionar a Amazon Web Services (AWS) da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Amazon Web Services (AWS)**, selecione **Amazon Web Services (AWS)** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

     ![Amazon Web Services (AWS) na lista de resultados](common/search-new-app.png)

5. Assim que a aplicação for adicionada, vá à página **Propriedades** e copie o ID do **Objeto**.

    ![Amazon Web Services (AWS) na lista de resultados](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on do Azure AD com a Amazon Web Services (AWS) com base num utilizador de teste chamado "Britta Simon".

Para um único login funcionar, a Azure AD precisa de saber qual é o utilizador homólogo da Amazon Web Services (AWS) para um utilizador em Azure AD. Por outras palavras, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado nos Serviços Web da Amazon (AWS).

Na Amazon Web Services (AWS), atribuem o valor do nome de **utilizador** em Azure AD como o valor do Nome de **Utilizador** para estabelecer a relação de ligação.

Para configurar e testar o único sign-on da Azure AD com a Amazon Web Services (AWS), é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure os Serviços Web da Amazon (AWS) Single Sign-On](#configure-amazon-web-services-aws-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o acesso único do Azure AD no portal Azure e configura um único sinal na aplicação Amazon Web Services (AWS).

**Para configurar o único sign-on da Azure AD com a Amazon Web Services (AWS), execute os seguintes passos:**

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **da Amazon Web Services (AWS),** selecione Single **sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

    ![image](common/preintegrated.png)

5. A aplicação Amazon Web Services (AWS) espera as afirmações da SAML num formato específico. Configure as seguintes reclamações para esta aplicação. Pode gerir os valores destes atributos a partir da secção **Atributos do Utilizador & Reclamações** na página de integração de aplicações. Na configuração do 'Iniciar' com a página **SAML,** clique no botão **Editar** para abrir **os atributos do utilizador & diálogo de reclamações.**

    ![image](common/edit-attribute.png)

6. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, configure o atributo token SAML como mostrado na imagem acima e execute os seguintes passos:

    | Nome  | Atributo fonte  | Espaço de nomes |
    | --------------- | --------------- | --------------- |
    | Nome rolesession | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Função            | user.atribuídos |  https://aws.amazon.com/SAML/Attributes |
    | Duração das sessões             | "fornecer um valor entre 900 segundos (15 minutos) a 43200 segundos (12 horas)" |  https://aws.amazon.com/SAML/Attributes |

    a. Clique **Em adicionar nova alegação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Name,** digite o nome do atributo mostrado para essa linha.

    c. Na caixa de texto **Namespace,** digite o valor Namespace mostrado para essa linha.

    d. Selecione Origem como **Atributo**.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar os **Metadados da Federação XML** e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Configure Serviços Web da Amazon (AWS) Single Sign-On

1. Numa janela de navegador diferente, inscreva-se no site da empresa Amazon Web Services (AWS) como administrador.

2. Clique em **AWS Home**.

    ![Configurar casa de inscrição única][11]

3. Clique em **Identidade e Gestão de Acesso.**

    ![Configurar a identidade de inscrição única][12]

4. Clique em **Fornecedores**de Identidade e, em seguida, clique em **Criar Fornecedor**.

    ![Configure o fornecedor de sinal único][13]

5. Na página de diálogo **Configure Provider,** execute os seguintes passos:

    ![Configurar um diálogo de início de sessão individual][14]

    a. Como **Tipo de Fornecedor,** selecione **SAML**.

    b. Na caixa de texto **Nome do Fornecedor,** digite um nome de fornecedor (por exemplo: *WAAD*).

    c. Para fazer o upload do ficheiro de **metadados** descarregado sabotado do portal Azure, clique em **Escolher File**.

    d. Clique no **próximo passo**.

6. Na página de diálogo de informação do **fornecedor,** clique em **Criar**.

    ![Configurar verificação de sinal único][15]

7. Clique em **Funções,** clique em **Criar a função**.

    ![Configurar papéis de início de sessão individuais][16]

8. Na página de **funções Criar,** execute os seguintes passos:  

    ![Configure Single Sign-On Trust][19]

    a. Selecione **a federação SAML 2.0** ao abrigo **do Select type de entidade fidedigna**.

    b. Em **'Escolha uma secção de fornecedor SAML 2.0',** selecione o **fornecedor SAML** que criou anteriormente (por exemplo: *WAAD*)

    c. **Selecione Permitir o acesso programático e a consola de gestão AWS**.
  
    d. Clique **em seguir: Permissões**.

9. No diálogo de políticas de **permissões anexa,** por favor, anexe a política adequada de acordo com a sua organização. Clique **em seguir: Rever**.  

    ![Configurar a política de sinalização única][33]

10. No diálogo **Review,** execute os seguintes passos:

    ![Configurar a revisão de sinal único][34]

    a. Na caixa de **texto de nome Role,** introduza o seu nome De Papel.

    b. Na caixa de texto de descrição da **função,** introduza a descrição.

    c. Clique em **Criar Função**.

    d. Crie o número de funções necessárias e mapeie-as para o Fornecedor de Identidade.

11. Inscreva-se na conta AWS atual e inicie sessão com outra conta onde pretende configurar um único sinal com a AD Azure.

12. Execute o passo-2 para o passo-10 para criar múltiplas funções que pretende configurar para esta conta. Se tiver mais de duas contas, por favor, execute os mesmos passos para que todas as contas criem funções para elas.

13. Uma vez que todos os papéis são criados nas contas, eles aparecem na lista de **Papéis** para essas contas.

    ![Configuração de papéis](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Precisamos de capturar todo o Papel ARN e Entidades Fidedignas para todos os papéis em todas as contas, que precisamos mapear manualmente com a aplicação Azure AD.

15. Clique nas funções para copiar os valores da **Role ARN** e **das Entidades Fidedignas.** Precisa destes valores para todos os papéis que precisa para criar em Azure AD.

    ![Configuração de papéis](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Execute o passo acima para todas as funções em todas as contas e guarde todas elas em formato **Role ARN,Entidades fidedignas** num bloco de notas.

17. Abra o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) noutra janela.

    a. Inscreva-se no site do Graph Explorer utilizando as credenciais global de administrador/coadministrador para o seu inquilino.

    b. Precisa de permissões suficientes para criar os papéis. Clique em **modificar permissões** para obter as permissões necessárias.

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Selecione as seguintes permissões da lista (se ainda não as tiver) e clique em "Modificar Permissões" 

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Isto pedir-lhe-á que faça login novamente e aceite o consentimento. Depois de aceitar o consentimento, está novamente registado no Graph Explorer.

    e. Mude a versão para **beta.** Para ir buscar todos os Diretores de Serviço ao seu inquilino, use a seguinte consulta:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se estiver a usar vários diretórios, então pode usar o seguinte padrão, que tem o seu domínio primário nele`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. A partir da lista de Diretores de Serviço saqueados, obtenha o que precisa modificar. Também pode utilizar o Ctrl+F para pesquisar a aplicação a partir de todos os Diretores de Serviço listados. Pode utilizar a seguinte consulta utilizando o **id do Objeto** que copiou da página Da AD Properties da Azure para chegar ao respetivo Diretor de Assistência de Assistência.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extraia a propriedade appRoles do objeto principal do serviço.

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Agora precisa de gerar novos papéis para a sua aplicação. 

    i. Abaixo jSON é um exemplo de objeto appRoles. Crie um objeto semelhante para adicionar as funções que deseja para a sua aplicação.

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
    > Só pode adicionar novos papéis após a **msiam_access** para a operação de patch. Além disso, pode adicionar as funções que quiser de acordo com a sua Organização. A Azure AD enviará o **valor** destas funções como valor de reclamação na resposta da SAML.

    j. Volte ao Microsoft Graph Explorer e mude o método de **GET** para **PATCH**. Remende o objeto principal de serviço para ter funções desejadas atualizando a propriedade appRoles semelhante à que está acima mostrada no exemplo. Clique em **Executar A Consulta** para executar a operação de patch. Uma mensagem de sucesso confirma a criação do papel para a sua aplicação Amazon Web Services.

    ![Caixa de diálogo do explorador do Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Depois de o Diretor de Serviço ser remendado com mais funções, pode atribuir utilizadores/grupos às respetivas funções. Isto pode ser feito indo para o portal e navegando para a aplicação Amazon Web Services. Clique no separador **Utilizadores e Grupos** na parte superior.

19. Recomendamos que crie novos grupos para cada função AWS para que possa atribuir esse papel específico a esse grupo. Note que este é um a um mapeamento para um grupo para um papel. Pode então adicionar os membros que pertencem a esse grupo.

20. Assim que os Grupos forem criados, selecione o grupo e designe a aplicação.

    ![Configurar um add de sinal único](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Os grupos aninhados não são apoiados na atribuição de grupos.

21. Para atribuir a função ao grupo, selecione a função e clique no botão **Atribuir** na parte inferior da página.

    ![Configurar um add de sinal único](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Por favor, note que precisa de refrescar a sua sessão no portal Azure para ver novos papéis.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Ao clicar no azulejo Amazon Web Services (AWS) no Painel de Acesso, deverá obter a página de aplicação da Amazon Web Services (AWS) com opção de selecionar a função.

![Configurar um add de sinal único](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Também pode verificar a resposta da SAML para ver as funções serem aprovadas como reclamações.

![Configurar um add de sinal único](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](../active-directory-saas-access-panel-introduction.md)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

* [Como configurar o fornecimento usando APIs do Microsoft Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

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
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
