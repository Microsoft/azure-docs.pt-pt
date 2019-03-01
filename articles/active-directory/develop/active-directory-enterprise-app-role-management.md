---
title: Configurar a afirmação de função emitida no token SAML para aplicações empresariais no Azure AD | Documentos da Microsoft
description: Saiba como configurar a afirmação de função emitida no token SAML para aplicações empresariais no Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a354fdf8ea75d9446c2a5e5ee6a70489cf9dbfb5
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56990527"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Como: Configurar a afirmação de função emitida no token SAML para aplicações empresariais

Ao utilizar o Azure Active Directory (Azure AD), pode personalizar o tipo de afirmação para a função de afirmações no token de resposta que recebe após o está a autorizar a uma aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure AD com a configuração do diretório.
- Uma subscrição que tem início de sessão único (SSO) ativado. Tem de configurar o SSO com a sua aplicação.

## <a name="when-to-use-this-feature"></a>Quando utilizar esta funcionalidade

Se seu aplicativo espera funções personalizadas a serem passados numa resposta SAML, terá de utilizar esta funcionalidade. Pode criar tantas funções à medida que precisa ser passados do Azure AD à sua aplicação.

## <a name="create-roles-for-an-application"></a>Criar funções para uma aplicação

1. Na [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione a **Azure Active Directory** ícone.

    ![Ícone do Active Directory do Azure][1]

2. Selecione **aplicações empresariais**. Em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais][2]

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior da caixa de diálogo.

    !["Novo aplicativo" botão][3]

4. Na caixa de pesquisa, escreva o nome da sua aplicação e, em seguida, selecione a aplicação a partir do painel de resultados. Selecione o **adicionar** botão para adicionar a aplicação.

    ![Aplicação na lista de resultados](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Depois do aplicativo é adicionado, vá para o **propriedades** página e copie o ID de objeto.

    ![Página de propriedades](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Open [do Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) na outra janela e siga os passos seguintes:

    a. Inicie sessão no site de API do Graph com as credenciais de administrador ou coadmin global para o seu inquilino.

    b. Tem permissões suficientes para criar as funções. Selecione **modificar permissões** para obter as permissões.

      ![O botão "modificar permissões"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Selecione as seguintes permissões na lista (se ainda não tiver estas) e selecione **modificar permissões**.

      ![Lista de permissões e botão "Modificar permissões"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Função de administrador da aplicação de cloud e o administrador da aplicação não irá funcionar neste cenário, porque temos as permissões de Administrador Global para o diretório de leitura e escrita.

    d. Aceite o consentimento. Tem sessão iniciada para o sistema novamente.

    e. Alterar a versão para **beta**e obter a lista de principais de serviço a partir do seu inquilino, utilizando a seguinte consulta:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Se estiver a utilizar vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Criar um gráfico de caixa de diálogo do Explorer, com a consulta para obter os principais de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Já estamos no processo de atualização as APIs para que os clientes poderão ver interrupções no serviço.

    f. A partir da lista de principais de serviço foram obtidas, obter aquele que precisa modificar. Também pode utilizar Ctrl + F para pesquisar o aplicativo de todos os principais de serviço listado. Procure o ID de objeto que copiou dos **propriedades** página e utilize a seguinte consulta para ir para o principal de serviço:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Consulta para obter o principal de serviço que precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extrair os **appRoles** propriedade do objeto principal de serviço.

      ![Detalhes da propriedade appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Se estiver a utilizar a aplicação personalizada (e não a aplicação do Azure Marketplace), verá duas funções de predefinição: utilizador e msiam_access. Para a aplicação do Marketplace, msiam_access é a única função de predefinição. Não precisa de fazer qualquer alteração nas funções predefinidas.

    h. Gere novas funções para a sua aplicação.

      O JSON seguinte é um exemplo do **appRoles** objeto. Crie um objeto semelhante para adicionar as funções que pretende para a sua aplicação.

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
      > Só é possível adicionar novas funções após msiam_access para a operação de correção. Além disso, pode adicionar tantas funções conforme suas necessidades da organização. Azure AD irá enviar o valor de uma destas funções, como o valor de afirmação na resposta SAML. Para gerar o GUID valores para o ID de novas funções de utilizam as ferramentas da web, como [isso](https://www.guidgenerator.com/)

    i. Voltar ao Graph Explorer e altere o método da **Obtenha** para **aplicar o PATCH**. O objeto principal de serviço para que as funções pretendidas através da atualização de patches a **appRoles** propriedade como a mostrada no exemplo anterior. Selecione **executar consulta** para executar a operação de correção. Uma mensagem de êxito confirma a criação da função.

      ![Operação de correção com mensagem de êxito](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Depois do principal de serviço for corrigido com mais de funções, pode atribuir utilizadores para as respetivas funções. Pode atribuir os utilizadores ao aceder ao portal e navegar para a aplicação. Selecione o **utilizadores e grupos** separador. Este separador lista todos os utilizadores e grupos que já estão atribuídos à aplicação. Pode adicionar novos utilizadores nas funções de novo. Também pode selecionar um utilizador existente e selecione **editar** para alterar a função.

    ![Separador "Utilizadores e grupos"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Para atribuir a função a qualquer utilizador, selecione a nova função e selecione o **atribuir** botão na parte inferior da página.

    ![Painel "Editar Atribuição" e o painel "Selecionar função"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Terá de atualizar a sua sessão no portal do Azure para ver novas funções.

8. Atualização do **atributos** tabela para definir um mapeamento personalizado de declaração de função.

9. Na **atributos de utilizador e afirmações** secção a **início de sessão único** caixa de diálogo caixa, configure o atributo de token SAML, conforme mostrado na imagem e execute os seguintes passos.

    | Nome de atributo | Valor do atributo |
    | -------------- | ----------------|
    | Nome da função  | user.assignedroles |

    >[!NOTE]
    >Se o valor de afirmação de função for nulo, em seguida, do Azure AD não enviará esse valor no token e esta é a predefinição de acordo com o design.

    a. Clique em **edite** botão para abrir **atributos de utilizador** caixa de diálogo.

      ![Botão "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. Selecione **adicionar atributo** para abrir o **afirmações de utilizador de gerir** painel.

      ![Botão "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Painel "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. Na **nome** , escreva o nome de atributo, conforme necessário. Este exemplo utiliza **nome da função** como o nome da afirmação.

    d. Deixe o **espaço de nomes** caixa em branco.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Selecione **Guardar**.

10. Para testar seu aplicativo num início de sessão único que é iniciado por um fornecedor de identidade, inicie sessão para o [painel de acesso](https://myapps.microsoft.com) e selecione o mosaico da aplicação. No SAML token, deverá ver todas as funções atribuídas para o utilizador com o nome de afirmação que tenha dado.

## <a name="update-an-existing-role"></a>Atualizar uma função existente

Para atualizar uma função existente, execute os seguintes passos:

1. Open [do Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

2. Inicie sessão no site de API do Graph com as credenciais de administrador ou coadmin global para o seu inquilino.

3. Alterar a versão para **beta**e obter a lista de principais de serviço a partir do seu inquilino, utilizando a seguinte consulta:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se estiver a utilizar vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Criar um gráfico de caixa de diálogo do Explorer, com a consulta para obter os principais de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. A partir da lista de principais de serviço foram obtidas, obter aquele que precisa modificar. Também pode utilizar Ctrl + F para pesquisar o aplicativo de todos os principais de serviço listado. Procure o ID de objeto que copiou dos **propriedades** página e utilize a seguinte consulta para ir para o principal de serviço:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Consulta para obter o principal de serviço que precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrair os **appRoles** propriedade do objeto principal de serviço.

    ![Detalhes da propriedade appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Para atualizar a função existente, utilize os seguintes passos.

    ![Corpo do pedido para "PATCH", "Descrição" e "displayname" realçado](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Alterar o método da **Obtenha** para **aplicar o PATCH**.

    b. Copiar as funções existentes e cole-os no **corpo do pedido**.

    c. Atualize o valor de uma função ao atualizar a descrição da função, o valor de função ou o nome de exibição de função, conforme necessário.

    d. Depois de atualizar todas as funções necessárias, selecione **executar consulta**.

## <a name="delete-an-existing-role"></a>Eliminar uma função existente

Para eliminar uma função existente, execute os seguintes passos:

1. Open [do Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) na outra janela.

2. Inicie sessão no site de API do Graph com as credenciais de administrador ou coadmin global para o seu inquilino.

3. Alterar a versão para **beta**e obter a lista de principais de serviço a partir do seu inquilino, utilizando a seguinte consulta:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se estiver a utilizar vários diretórios, siga este padrão: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Criar um gráfico de caixa de diálogo do Explorer, com a consulta para obter a lista de principais de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. A partir da lista de principais de serviço foram obtidas, obter aquele que precisa modificar. Também pode utilizar Ctrl + F para pesquisar o aplicativo de todos os principais de serviço listado. Procure o ID de objeto que copiou dos **propriedades** página e utilize a seguinte consulta para ir para o principal de serviço:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Consulta para obter o principal de serviço que precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extrair os **appRoles** propriedade do objeto principal de serviço.

    ![Detalhes da propriedade appRoles do objeto principal de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Para eliminar a função existente, utilize os seguintes passos.

    ![Corpo do pedido para "PATCH", com IsEnabled definida como false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Alterar o método da **Obtenha** para **aplicar o PATCH**.

    b. Copiar as funções existentes da aplicação e cole-os no **corpo do pedido**.

    c. Definir o **IsEnabled** valor **falso** para a função que pretende eliminar.

    d. Selecione **executar consulta**.

    > [!NOTE]
    > Certifique-se de que tem a função de msiam_access e o ID de correspondência na função gerada.

7. Depois da função estiver desativada, eliminar esse bloco de função a partir da **appRoles** secção. Manter o método como **aplicar o PATCH**e selecione **executar consulta**.

8. Depois de executar a consulta, a função é eliminada.

    > [!NOTE]
    > A função deve ser desativada antes de este pode ser removido.

## <a name="next-steps"></a>Passos Seguintes

Para obter os passos adicionais, consulte a [documentação da aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
