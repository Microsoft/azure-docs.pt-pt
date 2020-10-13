---
title: Configure a reivindicação de papel para apps Azure AD da empresa / Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar a reivindicação de papel emitida no token SAML para aplicações empresariais no Azure Ative Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 8ec87a8d78f73af48b662c5971dfe1803717f88a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704553"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Como: Configurar a alegação de papel emitida no token SAML para aplicações empresariais

Ao utilizar o Azure Ative Directory (Azure AD), pode personalizar o tipo de reclamação para a reivindicação de funções no token de resposta que recebe depois de autorizar uma aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura AD AZure com configuração de diretório.
- Uma subscrição que tenha um único sign-on (SSO) ativado. Tem de configurar a SSO com a sua aplicação.

## <a name="when-to-use-this-feature"></a>Quando utilizar esta funcionalidade

Se a sua aplicação espera que as funções personalizadas sejam passadas numa resposta SAML, tem de utilizar esta funcionalidade. Pode criar o máximo de papéis que precisar de ser passado de Azure AD para a sua aplicação.

## <a name="create-roles-for-an-application"></a>Criar papéis para uma aplicação

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione o ícone **Azure Ative Directory.**

    ![Ícone de diretório ativo Azure][1]

2. Selecione **aplicações da Enterprise**. Em seguida, **selecione Todas as aplicações**.

    ![Painel de aplicações da empresa][2]

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior da caixa de diálogo.

    ![Botão "Nova aplicação"][3]

4. Na caixa de pesquisa, digite o nome da sua aplicação e, em seguida, selecione a sua aplicação a partir do painel de resultados. Selecione o botão **Adicionar** para adicionar a aplicação.

    ![Inscrição na lista de resultados](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Após a adição da aplicação, vá à página **Propriedades** e copie o ID do objeto.

    ![Página de propriedades](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Abra [o Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) noutra janela e tome os seguintes passos:

    a. Inscreva-se no site do Graph Explorer utilizando as credenciais de administração ou coadmin global para o seu inquilino.

    b. Precisa de permissões suficientes para criar os papéis. Selecione **permissões de modificação** para obter as permissões.

      ![O botão "modificar permissões"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Selecione as seguintes permissões da lista (se ainda não as tiver) e selecione **Modificar Permissões**.

      ![Lista de permissões e botão "Modificar permissões"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > A função de Administrador de Aplicações cloud e administrador de aplicativos não funcionará neste cenário, pois precisamos das permissões de Administração Global para leitura e escrita de diretório.

    d. Aceite o consentimento. Está ligado ao sistema de novo.

    e. Altere a versão para **beta**, e pegue a lista de diretores de serviço do seu inquilino utilizando a seguinte consulta:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Se estiver a utilizar vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Caixa de diálogo do Graph Explorer, com a consulta para buscar os principais do serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Já estamos em fase de atualização das APIs para que os clientes possam ver alguma perturbação no serviço.

    f. Da lista de diretores de serviço recolhidos, obtenha o que precisa modificar. Também pode utilizar ctrl+F para pesquisar a aplicação de todos os principais do serviço listados. Procure o ID do objeto que copiou na página **Propriedades** e use a seguinte consulta para chegar ao principal do serviço:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Consulta para obter o principal de serviço que você precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    exemplo, Extrair a **propriedade appRoles** do objeto principal do serviço.

      ![Detalhes da propriedade appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Se estiver a utilizar a aplicação personalizada (não a aplicação Azure Marketplace), vê duas funções predefinidas: utilizador e msiam_access. Para a aplicação Marketplace, msiam_access é o único papel padrão. Não precisa de fazer alterações nas funções predefinidas.

    h. Gere novas funções para a sua aplicação.

      O JSON seguinte é um exemplo do objeto **appRoles.** Crie um objeto semelhante para adicionar as funções que deseja para a sua aplicação.

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
      > Só é possível adicionar novas funções após msiam_access para a operação do patch. Além disso, pode adicionar o máximo de papéis que a sua organização precisar. A Azure AD enviará o valor destas funções como valor de reclamação na resposta SAML. Para gerar os valores GUID para o ID de novas funções use as ferramentas web como [esta](https://www.guidgenerator.com/)

    i. Volte para o Graph Explorer e altere o método de **GET** para **PATCH**. Remeje o objeto principal de serviço para ter as funções desejadas atualizando a propriedade **appRoles** como a mostrada no exemplo anterior. Selecione **'Fazer's Questionry** para executar a operação de correção. Uma mensagem de sucesso confirma a criação do papel.

      ![Operação de patch com mensagem de sucesso](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Depois de o diretor de serviço ser corrigido com mais funções, pode atribuir os utilizadores às respetivas funções. Pode atribuir os utilizadores indo ao portal e navegando para a aplicação. Selecione o **separador Utilizadores e grupos.** Este separador lista todos os utilizadores e grupos que já estão atribuídos à aplicação. Pode adicionar novos utilizadores sobre as novas funções. Também pode selecionar um utilizador existente e selecionar **Editar** para alterar a função.

    ![Separador "Utilizadores e grupos"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Para atribuir a função a qualquer utilizador, selecione a nova função e selecione o botão **Atribuir** na parte inferior da página.

    ![Painel de "Atribuição de Edição" e painel "Select Role"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Precisa de refrescar a sua sessão no portal Azure para ver novos papéis.

8. Atualize a tabela **Atributos** para definir um mapeamento personalizado da reivindicação de funções.

9. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** execute os seguintes passos para adicionar o atributoken SAML, tal como mostrado na tabela abaixo:

    | Nome do atributo | Valor do atributo |
    | -------------- | ----------------|
    | Nome da função  | user.assignedroles |

    >[!NOTE]
    >Se o valor de reclamação de funções for nulo, então a Azure AD não enviará este valor no token e este é o padrão conforme o design.

    a. clique em **Editar** o ícone para abrir **o diálogo & Desemabros de Atributos do Utilizador.**

      ![Screenshot que realça o ícone editar usado para abrir a caixa de diálogo Atributos & Do Utilizador.](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. No diálogo **de reclamações do utilizador Manage,** adicione o atributo de ficha SAML clicando em **Adicionar nova reclamação**.

      ![Botão "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Painel "Adicionar Atributo"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. Na caixa **Nome,** escreva o nome do atributo conforme necessário. Este exemplo usa **o Nome de Função** como nome de reclamação.

    d. Deixe a caixa **Namespace** em branco.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Selecione **Guardar**.

10. Para testar a sua aplicação num único sinal de inscrição iniciado por um fornecedor de identidade, inicie sedível no [Painel de Acesso](https://myapps.microsoft.com) e selecione o seu azulejo de aplicação. No token SAML, deverá ver todas as funções atribuídas ao utilizador com o nome de reclamação que deu.

## <a name="update-an-existing-role"></a>Atualizar um papel existente

Para atualizar uma função existente, execute os seguintes passos:

1. Abra [o Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Inscreva-se no site do Graph Explorer utilizando as credenciais de administração ou coadmin global para o seu inquilino.

3. Altere a versão para **beta**, e pegue a lista de diretores de serviço do seu inquilino utilizando a seguinte consulta:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se estiver a utilizar vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo do Graph Explorer, com a consulta para buscar os principais do serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Da lista de diretores de serviço recolhidos, obtenha o que precisa modificar. Também pode utilizar ctrl+F para pesquisar a aplicação de todos os principais do serviço listados. Procure o ID do objeto que copiou na página **Propriedades** e use a seguinte consulta para chegar ao principal do serviço:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Consulta para obter o principal de serviço que você precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrair a **propriedade appRoles** do objeto principal do serviço.

    ![Detalhes da propriedade appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Para atualizar a função existente, utilize os seguintes passos.

    ![Corpo de pedido para "PATCH", com "descrição" e "nome de exibição" em destaque](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Altere o método de **GET** para **PATCH**.

    b. Copie as funções existentes e cole-as no **Organismo de Pedido**.

    c. Atualize o valor de uma função atualizando a descrição da função, o valor da função ou o nome de exibição de funções, conforme necessário.

    d. Depois de atualizar todas as funções necessárias, selecione **''' Executar Consulta ' s.**

## <a name="delete-an-existing-role"></a>Eliminar um papel existente

Para eliminar uma função existente, execute os seguintes passos:

1. Abra [o Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) noutra janela.

2. Inscreva-se no site do Graph Explorer utilizando as credenciais de administração ou coadmin global para o seu inquilino.

3. Altere a versão para **beta**, e pegue a lista de diretores de serviço do seu inquilino utilizando a seguinte consulta:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se estiver a utilizar vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo do Graph Explorer, com a consulta para obter a lista de diretores de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Da lista de diretores de serviço recolhidos, obtenha o que precisa modificar. Também pode utilizar ctrl+F para pesquisar a aplicação de todos os principais do serviço listados. Procure o ID do objeto que copiou na página **Propriedades** e use a seguinte consulta para chegar ao principal do serviço:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Consulta para obter o principal de serviço que você precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrair a **propriedade appRoles** do objeto principal do serviço.

    ![Detalhes da propriedade appRoles do objeto principal do serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Para eliminar a função existente, utilize os seguintes passos.

    ![Pedir corpo para "PATCH", com IsEnabled definido para falso](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Altere o método de **GET** para **PATCH**.

    b. Copie as funções existentes da aplicação e cole-as no **Organismo de Pedido**.

    c. Desfie o valor **IsEnabled** em **falso** para o papel que pretende eliminar.

    d. Selecione **Executar Consulta**.

    > [!NOTE]
    > Certifique-se de que tem o papel msiam_access, e o ID está correspondente no papel gerado.

7. Depois de desativar a função, elimine o bloco de funções da secção **appRoles.** Mantenha o método como **PATCH**, e selecione **'Consulta de Execução'.**

8. Depois de executar a consulta, o papel é apagado.

    > [!NOTE]
    > A função tem de ser desativada antes de poder ser removida.

## <a name="next-steps"></a>Passos seguintes

Para mais etapas, consulte a documentação da [aplicação.](../saas-apps/tutorial-list.md)

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
