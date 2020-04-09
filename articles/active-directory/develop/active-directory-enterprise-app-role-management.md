---
title: Configure pedido de função para aplicações da Empresa Azure AD [ Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar a alegação de funções emitida no símbolo SAML para aplicações empresariais em Diretório Ativo Azure
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 8db27819b7eef6cdf05ea3f6645ae930ebc4ef58
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884754"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Como: Configurar a alegação de função emitida no símbolo SAML para aplicações empresariais

Ao utilizar o Azure Ative Directory (Azure AD), pode personalizar o tipo de reclamação para a alegação de função no token de resposta que recebe depois de autorizar uma aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição de AD Azure com configuração de diretório.
- Uma subscrição que tem um único sinal (SSO) ativado. Tem de configurar o SSO com a sua aplicação.

## <a name="when-to-use-this-feature"></a>Quando utilizar esta funcionalidade

Se a sua aplicação espera que as funções personalizadas sejam passadas numa resposta SAML, precisa de utilizar esta funcionalidade. Pode criar o número de funções que necessitar para ser devolvido do Azure AD para a sua aplicação.

## <a name="create-roles-for-an-application"></a>Criar papéis para uma aplicação

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione o ícone do **Diretório Ativo Azure.**

    ![Ícone de Diretório Ativo Azure][1]

2. Selecione **aplicações Enterprise**. Em seguida, selecione **Todas as aplicações**.

    ![Painel de aplicações empresariais][2]

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior da caixa de diálogo.

    ![Botão "Nova aplicação"][3]

4. Na caixa de pesquisa, escreva o nome da sua aplicação e, em seguida, selecione a sua aplicação a partir do painel de resultados. Selecione o botão **Adicionar** para adicionar a aplicação.

    ![Candidatura na lista de resultados](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Depois de adicionada a aplicação, vá à página **Propriedades** e copie o ID do objeto.

    ![Página de Propriedades](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Abra o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) noutra janela e dê os seguintes passos:

    a. Inscreva-se no site do Graph Explorer utilizando as credenciais de administrador ou coadmina global para o seu inquilino.

    b. Precisa de permissões suficientes para criar os papéis. Selecione **modificar permissões** para obter as permissões.

      ![O botão "modificar permissões"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Selecione as seguintes permissões da lista (se ainda não as tiver) e selecione **Modificar Permissões**.

      ![Lista de permissões e botão "Modificar Permissões"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > O papel de Administrador de Aplicações cloud e administrador de aplicações não funcionará neste cenário, uma vez que precisamos das permissões da Global Admin para o Diretório Ler e Escrever.

    d. Aceite o consentimento. Está sintetizador no sistema de novo.

    e. Mude a versão para **beta**e pegue a lista de diretores de serviço do seu inquilino utilizando a seguinte consulta:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Se estiver a usar vários diretórios, siga este padrão:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Caixa de diálogo graph Explorer, com a consulta para buscar os principais do serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Já estamos em processo de modernização das APIs para que os clientes possam ver alguma perturbação no serviço.

    f. A partir da lista de diretores de serviço saqueados, obtenha o que precisa modificar. Também pode utilizar ctrl+F para pesquisar a aplicação a partir de todos os diretores de serviço listados. Procure o ID do objeto que copiou da página **Propriedades** e use a seguinte consulta para chegar ao diretor de serviço:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Consulta para obter o diretor de serviço que precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extraia a propriedade **appRoles** do objeto principal do serviço.

      ![Detalhes da propriedade appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Se estiver a utilizar a aplicação personalizada (não a aplicação Azure Marketplace), vê duas funções padrão: utilizador e msiam_access. Para a aplicação Marketplace, msiam_access é a única função padrão. Não precisas de fazer alterações nas funções padrão.

    h. Gere novos papéis para a sua aplicação.

      O seguinte JSON é um exemplo do objeto **appRoles.** Crie um objeto semelhante para adicionar as funções que deseja para a sua aplicação.

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
      > Só pode adicionar novas funções após msiam_access para a operação de patch. Além disso, pode adicionar o número de papéis que a sua organização precisa. A Azure AD enviará o valor destas funções como valor de reclamação na resposta SAML. Para gerar os valores GUID para a identificação de novas funções, utilize as ferramentas web como [esta](https://www.guidgenerator.com/)

    i. Volte ao Graph Explorer e mude o método de **GET** para **PATCH**. Remende o objeto principal do serviço para ter as funções desejadas atualizando a propriedade **appRoles** como a mostrada no exemplo anterior. Selecione **Executar A Consulta** para executar a operação de patch. Uma mensagem de sucesso confirma a criação do papel.

      ![Patch operação com mensagem de sucesso](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Depois de o diretor de serviço ser remendado com mais funções, pode atribuir aos utilizadores as respetivas funções. Pode atribuir os utilizadores indo ao portal e navegando na aplicação. Selecione o separador **Utilizadores e grupos.** Este separador lista todos os utilizadores e grupos que já estão atribuídos à aplicação. Pode adicionar novos utilizadores às novas funções. Também pode selecionar um utilizador existente e selecionar **editar** para alterar a função.

    ![Separador "Utilizadores e grupos"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Para atribuir a função a qualquer utilizador, selecione a nova função e selecione o botão **Atribuir** na parte inferior da página.

    ![Painel de "Atribuição de Edição" e painel "Select Role"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Precisa refrescar a sua sessão no portal Azure para ver novos papéis.

8. Atualize a tabela **Atributos** para definir um mapeamento personalizado da reivindicação de funções.

9. Na secção **Reivindicações** do Utilizador no diálogo **de Atributos** do Utilizador, execute os seguintes passos para adicionar atributo token SAML, conforme indicado no quadro abaixo:

    | Nome do atributo | Valor do atributo |
    | -------------- | ----------------|
    | Nome da função  | user.atribuídos |

    >[!NOTE]
    >Se o valor da reclamação de funções for nulo, então a Azure AD não enviará este valor no token e isso é padrão de acordo com o design.

    a. clique em **Editar** o ícone para abrir **atributos do utilizador & diálogo de reclamações.**

      ![Botão "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. No diálogo de reclamações do **utilizador Manage,** adicione o atributo token SAML clicando em **Adicionar nova reclamação**.

      ![Botão "Adicionar atributo"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Painel "Adicionar Atributo"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. Na caixa **nome,** digite o nome do atributo conforme necessário. Este exemplo usa o **Nome de Papel** como nome de reclamação.

    d. Deixe a caixa **namespace** em branco.

    e. Na lista de **atributos Fonte,** digite o valor do atributo mostrado para essa linha.

    f. Selecione **Guardar**.

10. Para testar a sua aplicação num único sign-on iniciado por um fornecedor de identidade, inicie o acesso ao Painel de [Acesso](https://myapps.microsoft.com) e selecione o seu azulejo de aplicação. No token SAML, deve ver todas as funções atribuídas para o utilizador com o nome de reclamação que deu.

## <a name="update-an-existing-role"></a>Atualizar um papel existente

Para atualizar uma função existente, execute os seguintes passos:

1. Abra o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Inscreva-se no site do Graph Explorer utilizando as credenciais de administrador ou coadmina global para o seu inquilino.

3. Mude a versão para **beta**e pegue a lista de diretores de serviço do seu inquilino utilizando a seguinte consulta:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se estiver a usar vários diretórios, siga este padrão:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo graph Explorer, com a consulta para buscar os principais do serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. A partir da lista de diretores de serviço saqueados, obtenha o que precisa modificar. Também pode utilizar ctrl+F para pesquisar a aplicação a partir de todos os diretores de serviço listados. Procure o ID do objeto que copiou da página **Propriedades** e use a seguinte consulta para chegar ao diretor de serviço:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Consulta para obter o diretor de serviço que precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extraia a propriedade **appRoles** do objeto principal do serviço.

    ![Detalhes da propriedade appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Para atualizar a função existente, utilize os seguintes passos.

    ![Solicitar corpo para "PATCH", com "descrição" e "displayname" em destaque](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Altere o método de **GET** para **PATCH**.

    b. Copie as funções existentes e cole-as no **órgão de pedido**.

    c. Atualizar o valor de uma função atualizando a descrição do papel, o valor do papel ou o nome da exibição de papéis conforme necessário.

    d. Depois de atualizar todas as funções necessárias, selecione **'Executar Consulta**' .

## <a name="delete-an-existing-role"></a>Eliminar um papel existente

Para eliminar uma função existente, execute os seguintes passos:

1. Abra o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) noutra janela.

2. Inscreva-se no site do Graph Explorer utilizando as credenciais de administrador ou coadmina global para o seu inquilino.

3. Mude a versão para **beta**e pegue a lista de diretores de serviço do seu inquilino utilizando a seguinte consulta:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Se estiver a usar vários diretórios, siga este padrão:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo graph Explorer, com a consulta para obter a lista de diretores de serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. A partir da lista de diretores de serviço saqueados, obtenha o que precisa modificar. Também pode utilizar ctrl+F para pesquisar a aplicação a partir de todos os diretores de serviço listados. Procure o ID do objeto que copiou da página **Propriedades** e use a seguinte consulta para chegar ao diretor de serviço:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Consulta para obter o diretor de serviço que precisa modificar](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Extraia a propriedade **appRoles** do objeto principal do serviço.

    ![Detalhes da propriedade appRoles do objeto principal do serviço](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Para eliminar a função existente, utilize os seguintes passos.

    ![Solicitar corpo para "PATCH", com IsEnabled definido para falso](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Altere o método de **GET** para **PATCH**.

    b. Copie as funções existentes da aplicação e cole-as no **âmbito do Órgão de Pedido**.

    c. Detete o valor **IsEnabled** para **falso** para o papel que pretende eliminar.

    d. Selecione **Executar Consulta**.

    > [!NOTE]
    > Certifique-se de que tem o papel msiam_access, e o ID está a corresponder ao papel gerado.

7. Depois de a função ser desativada, elimine esse bloco de funções da secção **AppRoles.** Mantenha o método como **PATCH**, e selecione **Executar Consulta**.

8. Depois de fazer a consulta, o papel é apagado.

    > [!NOTE]
    > O papel tem de ser desativado antes de poder ser removido.

## <a name="next-steps"></a>Passos seguintes

Para obter passos adicionais, consulte a documentação da [aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
