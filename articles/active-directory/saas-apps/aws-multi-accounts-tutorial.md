---
title: 'Tutorial: integração do Azure Active Directory com o Amazon Web Services (AWS) para conectar várias contas | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure AD e várias contas do Amazon Web Services (AWS).
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
ms.openlocfilehash: cb528d71b94449b282947a487e4fc79b343df778
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195910"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Tutorial: integração de Azure Active Directory com várias contas de Amazon Web Services (AWS)

Neste tutorial, você aprenderá a integrar o Azure Active Directory (Azure AD) a várias contas do Amazon Web Services (AWS).

A integração do Amazon Web Services (AWS) ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Amazon Web Services (AWS).
- Você pode permitir que seus usuários façam logon automaticamente no Amazon Web Services (AWS) (logon único) com suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se você quiser saber mais detalhes sobre a integração de aplicativos SaaS com o Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) na lista de resultados](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Observe que conectar um aplicativo AWS a todas as suas contas do AWS não é nossa abordagem recomendada. Em vez disso, recomendamos que você use [essa](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) abordagem para configurar várias instâncias da conta do AWS para várias instâncias de aplicativos do AWS no Azure AD.

**Observe que não recomendamos usar essa abordagem pelos seguintes motivos:**

* Você precisa usar a abordagem do explorador do Graph para corrigir todas as funções para o aplicativo. Não é recomendável usar a abordagem do arquivo de manifesto.

* Vimos que os clientes estão relatando que, depois de adicionar ~ 1200 funções de aplicativo para um único aplicativo AWS, qualquer operação no aplicativo começou a gerar os erros relacionados ao tamanho. Há um limite rígido de tamanho no objeto de aplicativo.

* Você precisa atualizar manualmente a função à medida que as funções são adicionadas em qualquer uma das contas, que é uma abordagem de substituição e não acrescenta, infelizmente. Além disso, se suas contas estiverem crescendo, isso se tornará n x n relação com contas e funções.

* Todas as contas do AWS usarão o mesmo arquivo XML de metadados de Federação e, no momento da substituição do certificado, você terá que impulsionar esse exercício maciço para atualizar o certificado em todas as contas do AWS ao mesmo tempo

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Amazon Web Services (AWS), você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver um ambiente do Azure AD, poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura habilitada para logon único do Amazon Web Services (AWS)

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se você não tiver um ambiente de avaliação do Azure AD, poderá [obter uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* Amazon Web Services (AWS) dá suporte ao **SP e** ao SSO iniciado pelo IDP

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adição de Amazon Web Services (AWS) da Galeria

Para configurar a integração do Amazon Web Services (AWS) ao Azure AD, você precisa adicionar o Amazon Web Services (AWS) da Galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Amazon Web Services (AWS) da galeria, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique no ícone de **Azure Active Directory** .

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, clique no botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Amazon Web Services (AWS)** , selecione **Amazon Web Services (AWS)** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Amazon Web Services (AWS) na lista de resultados](common/search-new-app.png)

5. Depois que o aplicativo for adicionado, vá para a página **Propriedades** e copie a **ID do objeto**.

    ![Amazon Web Services (AWS) na lista de resultados](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta seção, você configurará e testará o logon único do Azure AD com o Amazon Web Services (AWS), com base em um usuário de teste chamado "Brenda Simon".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Amazon Web Services (AWS) é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Amazon Web Services (AWS).

No Amazon Web Services (AWS), atribua o valor do **nome de usuário** no Azure ad como o valor do **nome** de usuários para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o Amazon Web Services (AWS), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure ad](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** -para configurar as configurações de logon único no lado do aplicativo.
3. **[Testar logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no aplicativo Amazon Web Services (AWS).

**Para configurar o logon único do Azure AD com o Amazon Web Services (AWS), execute as seguintes etapas:**

1. Na [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Amazon Web Services (AWS)** , selecione **logon único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Na caixa de diálogo **selecionar um método de logon único** , selecione o modo de **SAML/WS-enalimentado** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Configurar logon único com SAML** , clique no ícone **Editar** para abrir a caixa de diálogo **configuração básica de SAML** .

    ![Editar configuração básica de SAML](common/edit-urls.png)

4. Na seção **configuração básica do SAML** , o usuário não precisa executar nenhuma etapa, pois o aplicativo já está previamente integrado ao Azure.

    ![image](common/preintegrated.png)

5. Amazon Web Services aplicativo (AWS) espera que as asserções SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **atributos de usuário & declarações** na página de integração de aplicativos. Na página **Configurar logon único com SAML** , clique no botão **Editar** para abrir **atributos de usuário &** caixa de diálogo declarações.

    ![image](common/edit-attribute.png)

6. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , configure o atributo de token SAML, conforme mostrado na imagem acima, e execute as seguintes etapas:

    | Nome  | Atributo de origem  | Espaço de Nomes |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Função            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "forneça um valor entre 900 segundos (15 minutos) a 43200 segundos (12 horas)" |  https://aws.amazon.com/SAML/Attributes |

    a. Clique em **Adicionar nova declaração** para abrir a caixa de diálogo **gerenciar declarações do usuário** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **nome** , digite o nome do atributo mostrado para essa linha.

    c. Na caixa de texto **namespace** , digite o valor do namespace mostrado para essa linha.

    d. Selecione origem como **atributo**.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Clique em **OK**

    g. Clique em **Guardar**.

7. Na página **Configurar logon único com SAML** , na seção **certificado de autenticação SAML** , clique em **baixar** para baixar o XML de **metadados de Federação** e salvá-lo no computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Configurar o logon único do Amazon Web Services (AWS)

1. Em uma janela de navegador diferente, faça logon no site de sua empresa do Amazon Web Services (AWS) como administrador.

2. Clique em **página inicial do AWS**.

    ![Configurar página inicial de logon único][11]

3. Clique em **Gerenciamento de identidade e acesso**.

    ![Configurar identidade de logon único][12]

4. Clique em **provedores de identidade**e, em seguida, clique em **criar provedor**.

    ![Configurar o provedor de logon único][13]

5. Na página de diálogo **Configurar provedor** , execute as seguintes etapas:

    ![Caixa de diálogo Configurar logon único][14]

    a. Como **tipo de provedor**, selecione **SAML**.

    b. Na caixa de texto **nome do provedor** , digite um nome de provedor (por exemplo: *WAAD*).

    c. Para carregar o **arquivo de metadados** baixado do portal do Azure, clique em **escolher arquivo**.

    d. Clique em **próxima etapa**.

6. Na página de diálogo **verificar informações do provedor** , clique em **criar**.

    ![Configurar verificação de logon único][15]

7. Clique em **funções**e, em seguida, clique em **criar função**.

    ![Configurar funções de logon único][16]

8. Na página **criar função** , execute as seguintes etapas:  

    ![Configurar confiança de logon único][19]

    a. Selecione **Federação SAML 2,0** em **Selecionar tipo de entidade confiável**.

    b. Na **seção escolher um provedor saml 2,0**, selecione o **provedor SAML** que você criou anteriormente (por exemplo: *WAAD*)

    c. Selecione **permitir acesso de console de gerenciamento programático e AWS**.
  
    d. Clique em **Avançar: permissões**.

9. Na caixa de diálogo **anexar políticas de permissões** , anexe a política apropriada de acordo com sua organização. Clique em **Avançar: examinar**.  

    ![Configurar política de logon único][33]

10. Na caixa de diálogo **revisar** , execute as seguintes etapas:

    ![Configurar análise de logon único][34]

    a. Na caixa de texto **nome da função** , insira o nome da função.

    b. Na caixa de texto **Descrição da função** , insira a descrição.

    c. Clique em **criar função**.

    d. Crie quantas funções forem necessárias e mapeie-as para o provedor de identidade.

11. Saia da conta atual do AWS e faça logon com outra conta em que você deseja configurar o logon único com o Azure AD.

12. Execute Step-2 para Step-10 para criar várias funções que você deseja configurar para esta conta. Se você tiver mais de duas contas, execute as mesmas etapas para todas as contas para criar funções para elas.

13. Depois que todas as funções forem criadas nas contas, elas aparecerão na lista de **funções** para essas contas.

    ![Configuração de funções](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Precisamos capturar todas as ARN de função e entidades confiáveis para todas as funções em todas as contas, que precisamos mapear manualmente com o aplicativo do Azure AD.

15. Clique nas funções para copiar os valores de **ARN de função** e **entidades confiáveis** . Você precisa desses valores para todas as funções que precisa criar no Azure AD.

    ![Configuração de funções](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Execute a etapa acima para todas as funções em todas as contas e armazene todas elas na função de formato **ARN, entidades confiáveis** em um bloco de notas.

17. Abra o [Explorador do Azure ad Graph](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

    a. Entre no site do Graph Explorer usando as credenciais de administrador global/coadministrador para seu locatário.

    b. Você precisa ter permissões suficientes para criar as funções. Clique em **Modificar permissões** para obter as permissões necessárias.

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Selecione as seguintes permissões na lista (se você ainda não as tiver) e clique em "Modificar permissões" 

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Isso solicitará que você faça logon novamente e aceite o consentimento. Depois de aceitar o consentimento, você estará conectado ao explorador do Graph novamente.

    e. Altere a lista suspensa versão para **beta**. Para buscar todas as entidades de serviço do seu locatário, use a seguinte consulta:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se você estiver usando vários diretórios, poderá usar o padrão a seguir, que tem seu domínio primário nele `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Na lista de entidades de serviço buscadas, obtenha a que você precisa modificar. Você também pode usar Ctrl + F para pesquisar o aplicativo de todas as entidades de segurança listadas. Você pode usar a consulta a seguir usando a **ID de objeto** que você copiou da página de propriedades do Azure ad para obter a respectiva entidade de serviço.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extraia a propriedade appRoles do objeto de entidade de serviço.

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Agora você precisa gerar novas funções para seu aplicativo. 

    i. A seguir, o JSON é um exemplo de objeto appRoles. Crie um objeto semelhante para adicionar as funções desejadas para seu aplicativo.

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
    > Você só pode adicionar novas funções após o **msiam_access** para a operação de patch. Além disso, você pode adicionar quantas funções quiser de acordo com a necessidade da sua organização. O Azure AD enviará o **valor** dessas funções como o valor de declaração na resposta SAML.

    j. Volte para o explorador do Graph e altere o método de **Get** para **patch**. Aplique um patch ao objeto de entidade de serviço para ter as funções desejadas atualizando a propriedade appRoles semelhante à mostrada acima no exemplo. Clique em **Executar consulta** para executar a operação de patch. Uma mensagem de êxito confirma a criação da função para seu aplicativo Amazon Web Services.

    ![Caixa de diálogo explorador de gráficos](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Depois que a entidade de serviço é corrigida com mais funções, você pode atribuir usuários/grupos às respectivas funções. Isso pode ser feito acessando o portal e navegando até o aplicativo Amazon Web Services. Clique na guia **usuários e grupos** na parte superior.

19. É recomendável criar novos grupos para cada função de AWS para que você possa atribuir essa função específica a esse grupo. Observe que se trata de um mapeamento para um grupo para uma função. Em seguida, você pode adicionar os membros que pertencem a esse grupo.

20. Depois que os grupos forem criados, selecione o grupo e atribua ao aplicativo.

    ![Configurar logon único adicionar](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Não há suporte para grupos aninhados ao atribuir grupos.

21. Para atribuir a função ao grupo, selecione a função e clique no botão **atribuir** na parte inferior da página.

    ![Configurar logon único adicionar](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Observe que você precisa atualizar sua sessão no portal do Azure para ver novas funções.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no bloco Amazon Web Services (AWS) no painel de acesso, você deve obter a página de aplicativo Amazon Web Services (AWS) com a opção para selecionar a função.

![Configurar logon único adicionar](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Você também pode verificar a resposta SAML para ver as funções que estão sendo passadas como declarações.

![Configurar logon único adicionar](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Como configurar o provisionamento usando APIs do MS Graph](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
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
