---
title: Configurar a declaração de função para aplicativos empresariais no Azure AD
titleSuffix: Microsoft identity platform
description: Saiba como configurar a declaração de função emitida no token SAML para aplicativos empresariais no Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: CelesteDG
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c671626a431a47e5100cf42ca0c9e29ab580ab3a
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803491"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Como configurar a declaração de função emitida no token SAML para aplicativos empresariais

Usando Azure Active Directory (AD do Azure), você pode personalizar o tipo de declaração para a declaração de função no token de resposta que você recebe depois de autorizar um aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure AD com a instalação do diretório.
- Uma assinatura que tem SSO (logon único) habilitado. Você deve configurar o SSO com seu aplicativo.

## <a name="when-to-use-this-feature"></a>Quando usar este recurso

Se seu aplicativo espera que as funções personalizadas sejam passadas em uma resposta SAML, você precisa usar esse recurso. Você pode criar quantas funções precisar para serem passadas do Azure AD para seu aplicativo.

## <a name="create-roles-for-an-application"></a>Criar funções para um aplicativo

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o ícone de **Azure Active Directory** .

    ![Ícone de Azure Active Directory][1]

2. Selecione **aplicativos empresariais**. Em seguida, selecione **todos os aplicativos**.

    ![Painel aplicativos empresariais][2]

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior da caixa de diálogo.

    ![Botão "novo aplicativo"][3]

4. Na caixa de pesquisa, digite o nome do seu aplicativo e, em seguida, selecione o aplicativo no painel de resultados. Selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Aplicativo na lista de resultados](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Depois que o aplicativo for adicionado, vá para a página **Propriedades** e copie a ID do objeto.

    ![Página de propriedades](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Abra o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) em outra janela e execute as seguintes etapas:

    a. Entre no site do Graph Explorer usando as credenciais de administrador global ou coadmin para seu locatário.

    b. Você precisa de permissões suficientes para criar as funções. Selecione **Modificar permissões** para obter as permissões.

      ![O botão "Modificar permissões"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Selecione as seguintes permissões na lista (se você ainda não as tiver) e selecione **Modificar permissões**.

      ![Lista de permissões e o botão "Modificar permissões"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > O administrador de aplicativo de nuvem e a função de administrador de aplicativos não funcionarão nesse cenário, pois precisamos das permissões de administrador global para leitura e gravação de diretório.

    d. Aceite o consentimento. Você está conectado ao sistema novamente.

    e. Altere a versão para **beta**e busque a lista de entidades de serviço do seu locatário usando a seguinte consulta:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Se você estiver usando vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Caixa de diálogo Gerenciador de gráficos, com a consulta para buscar entidades de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Já estamos no processo de atualização das APIs para que os clientes possam ver algumas interrupções no serviço.

    f. Na lista de entidades de serviço buscadas, obtenha aquela que você precisa modificar. Você também pode usar Ctrl + F para pesquisar o aplicativo de todas as entidades de serviço listadas. Pesquise a ID de objeto que você copiou da página **Propriedades** e use a consulta a seguir para obter a entidade de serviço:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Consulta para obter a entidade de serviço que você precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extraia a propriedade **appRoles** do objeto de entidade de serviço.

      ![Detalhes da propriedade appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Se você estiver usando o aplicativo personalizado (não o aplicativo do Azure Marketplace), verá duas funções padrão: User e msiam_access. Para o aplicativo do Marketplace, msiam_access é a única função padrão. Você não precisa fazer nenhuma alteração nas funções padrão.

    h. Gere novas funções para seu aplicativo.

      O JSON a seguir é um exemplo do objeto **appRoles** . Crie um objeto semelhante para adicionar as funções desejadas para seu aplicativo.

      ```
      {
         "appRoles": [
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "msiam_access",
              "displayName": "msiam_access",
              "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
              "isEnabled": true,
              "origin": "Application",
              "value": null
          },
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "Administrators Only",
              "displayName": "Admin",
              "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
              "isEnabled": true,
              "origin": "ServicePrincipal",
              "value": "Administrator"
          }
      ]
      }
      ```

      > [!Note]
      > Você só pode adicionar novas funções após msiam_access para a operação de patch. Além disso, você pode adicionar quantas funções forem necessárias à sua organização. O Azure AD enviará o valor dessas funções como o valor de declaração na resposta SAML. Para gerar os valores de GUID para a ID de novas funções, use as ferramentas da Web como [esta](https://www.guidgenerator.com/)

    i. Volte para o Gerenciador de gráficos e altere o método de **Get** para **patch**. Corrija o objeto de entidade de serviço para ter as funções desejadas atualizando a propriedade **appRoles** como a mostrada no exemplo anterior. Selecione **Executar consulta** para executar a operação de patch. Uma mensagem de êxito confirma a criação da função.

      ![Operação de patch com mensagem de êxito](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Depois que a entidade de serviço é corrigida com mais funções, você pode atribuir usuários às respectivas funções. Você pode atribuir os usuários acessando o portal e navegando até o aplicativo. Selecione a guia **usuários e grupos** . Essa guia lista todos os usuários e grupos que já estão atribuídos ao aplicativo. Você pode adicionar novos usuários nas novas funções. Você também pode selecionar um usuário existente e selecionar **Editar** para alterar a função.

    ![Guia "usuários e grupos"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Para atribuir a função a qualquer usuário, selecione a nova função e selecione o botão **atribuir** na parte inferior da página.

    ![Painel "Editar atribuição" e "selecionar função"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Você precisa atualizar sua sessão no portal do Azure para ver novas funções.

8. Atualize a tabela de **atributos** para definir um mapeamento personalizado da declaração de função.

9. Na seção **declarações do usuário** , na caixa de diálogo **atributos de usuário** , execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome do atributo | Valor do atributo |
    | -------------- | ----------------|
    | Nome da função  | User. assignedroles |

    >[!NOTE]
    >Se o valor de declaração de função for nulo, o Azure AD não enviará esse valor no token e isso será padrão de acordo com o design.

    a. Clique no ícone **Editar** para abrir **atributos de usuário &** caixa de diálogo declarações.

      ![Botão "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. Na caixa de diálogo **gerenciar declarações do usuário** , adicione o atributo de token SAML clicando em **Adicionar nova declaração**.

      ![Botão "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Painel "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. Na caixa **nome** , digite o nome do atributo conforme necessário. Este exemplo usa o **nome da função** como o nome da declaração.

    d. Deixe a caixa **namespace** em branco.

    e. Na lista **atributo de origem** , digite o valor do atributo mostrado para essa linha.

    f. Selecione **Guardar**.

10. Para testar seu aplicativo em um logon único iniciado por um provedor de identidade, entre no [painel de acesso](https://myapps.microsoft.com) e selecione o bloco do aplicativo. No token SAML, você deve ver todas as funções atribuídas para o usuário com o nome da declaração que você atribuiu.

## <a name="update-an-existing-role"></a>Atualizar uma função existente

Para atualizar uma função existente, execute as seguintes etapas:

1. Abra o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Entre no site do Graph Explorer usando as credenciais de administrador global ou coadmin para seu locatário.

3. Altere a versão para **beta**e busque a lista de entidades de serviço do seu locatário usando a seguinte consulta:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se você estiver usando vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo Gerenciador de gráficos, com a consulta para buscar entidades de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Na lista de entidades de serviço buscadas, obtenha aquela que você precisa modificar. Você também pode usar Ctrl + F para pesquisar o aplicativo de todas as entidades de serviço listadas. Pesquise a ID de objeto que você copiou da página **Propriedades** e use a consulta a seguir para obter a entidade de serviço:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Consulta para obter a entidade de serviço que você precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extraia a propriedade **appRoles** do objeto de entidade de serviço.

    ![Detalhes da propriedade appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Para atualizar a função existente, use as etapas a seguir.

    ![Corpo da solicitação para "PATCH", com "Descrição" e "DisplayName" realçados](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Altere o método de **Get** para **patch**.

    b. Copie as funções existentes e cole-as sob o **corpo da solicitação**.

    c. Atualize o valor de uma função atualizando a descrição da função, o valor da função ou o nome de exibição da função, conforme necessário.

    d. Depois de atualizar todas as funções necessárias, selecione **Executar consulta**.

## <a name="delete-an-existing-role"></a>Excluir uma função existente

Para excluir uma função existente, execute as seguintes etapas:

1. Abra o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

2. Entre no site do Graph Explorer usando as credenciais de administrador global ou coadmin para seu locatário.

3. Altere a versão para **beta**e busque a lista de entidades de serviço do seu locatário usando a seguinte consulta:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se você estiver usando vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo Gerenciador de gráficos, com a consulta para buscar a lista de entidades de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Na lista de entidades de serviço buscadas, obtenha aquela que você precisa modificar. Você também pode usar Ctrl + F para pesquisar o aplicativo de todas as entidades de serviço listadas. Pesquise a ID de objeto que você copiou da página **Propriedades** e use a consulta a seguir para obter a entidade de serviço:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Consulta para obter a entidade de serviço que você precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extraia a propriedade **appRoles** do objeto de entidade de serviço.

    ![Detalhes da propriedade appRoles do objeto de entidade de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Para excluir a função existente, use as etapas a seguir.

    ![Corpo da solicitação para "PATCH", com IsEnabled definido como false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Altere o método de **Get** para **patch**.

    b. Copie as funções existentes do aplicativo e cole-as sob o **corpo da solicitação**.

    c. Defina o valor de **IsEnabled** como **false** para a função que você deseja excluir.

    d. Selecione **Executar consulta**.

    > [!NOTE]
    > Verifique se você tem a função msiam_access e se a ID corresponde à função gerada.

7. Depois que a função for desabilitada, exclua esse bloco de função da seção **appRoles** . Mantenha o método como **patch**e selecione **Executar consulta**.

8. Depois de executar a consulta, a função é excluída.

    > [!NOTE]
    > A função precisa ser desabilitada para que possa ser removida.

## <a name="next-steps"></a>Passos seguintes

Para obter etapas adicionais, consulte a [documentação do aplicativo](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
