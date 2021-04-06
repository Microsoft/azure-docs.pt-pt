---
title: Tutorial - Utilize revisões na Gestão da API para fazer alterações de API não quebrando com segurança
titleSuffix: Azure API Management
description: Siga os passos deste tutorial para aprender a efetuar alterações sem interrupções com revisões na Gestão de API.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: acb121bb00df481c926ebed9594bf0fe1b9b17ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546640"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Tutorial: Utilize revisões para fazer alterações de API não quebrando com segurança
Quando a API está pronta e começa a ser utilizada pelos programadores, poderá ter de fazer alterações a essa API e ao mesmo tempo não interromper os autores de chamadas da sua API. É também útil dar a conhecer aos programadores as alterações que efetuou. 

Na Azure API Management, utilize *revisões* para esmumir alterações de API não quebrando para que possa modelar e testar alterações com segurança. Quando estiver pronto, pode fazer uma corrente de revisão e substituir a sua API atual. 

Para obter em segundo plano, consulte [versões & revisões](https://azure.microsoft.com/blog/versions-revisions/) e [versão API com Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma nova revisão
> * Efetuar alterações sem interrupções à revisão
> * Tornar a revisão atual e adicionar uma entrada de registo de alterações
> * Navegar até ao portal do programador para ver as alterações e o registo de alterações

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="Revisões da API no portal Azure":::

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
+ Conclua também o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Adicionar uma nova revisão

1. Inscreva-se no [portal Azure](https://portal.azure.com)e navegue para a sua instância de Gestão da API.
1. Selecione **APIs**.
2. Selecione API da **Conferência de Demonstração** da lista API (ou de outra API à qual pretende adicionar revisões).
3. Selecione o **separador Revisões.**
4. Selecione **+ Adicionar revisão**.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="Adicionar a revisão de API":::

    > [!TIP]
    > Também pode selecionar **Adicionar revisão** no menu de contexto **(...**) da API.

5. Forneça uma descrição da nova revisão, para ajudar a lembrar para que será utilizada.
6. Selecione **Criar,**
7. A sua nova revisão está agora criada.

    > [!NOTE]
    > A API original permanece em **Revisão 1**. Trata-se da revisão que os utilizadores continuam a chamar, até optar por tornar uma revisão diferente atual.

## <a name="make-non-breaking-changes-to-your-revision"></a>Efetuar alterações sem interrupções à revisão

1. Selecione **Demo Conference API** na lista de APIs.
1. Selecione o separador **Estrutura** junto à parte superior do ecrã.
1. Tenha em atenção que o **seletor de revisões** (diretamente acima do separador de estrutura) mostra a **Revisão 2** como atualmente selecionada.

    > [!TIP]
    > Utilize o seletor de revisões para alternar entre revisões em que quer trabalhar.
1. Selecione **+ Adicionar Operação**.
1. Defina a nova operação como **POST**, o Nome, o Nome a Apresentar e o URL da operação como **teste**.
1. **Guarde** a nova operação.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Modificar a revisão":::
1. Agora fez uma alteração para a **Revisão 2.** Utilize o **seletor de revisão** perto do topo da página para voltar à **Revisão 1**.
1. Tenha em atenção que a nova operação não aparece em **Revisão 1**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Tornar a revisão atual e adicionar uma entrada de registo de alterações

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione o separador **Revisões** no menu junto à parte superior da página.
1. Abra o menu de contexto (**...**) para **Revisão 2**.
1. **Selecione Fazer a corrente**.
1. Selecione o **registo 'Post to Public Change' para esta** caixa de verificação API, caso pretenda publicar notas sobre esta alteração. Forneça uma descrição para a sua mudança que os desenvolvedores vêem, por exemplo: **Testar revisões. Adicionou uma nova operação de "teste".**
1. **Revisão 2** é agora atual.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Menu de revisão na janela revisões":::

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar O Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Utilize este procedimento para criar e atualizar um desbloqueio.

1. Executar o comando [da lista az apim api](/cli/azure/apim/api#az_apim_api_list) para ver os seus IDs API:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   O ID da API para utilizar no comando seguinte é o `Name` valor. A revisão da API está na `ApiRevision` coluna.

1. Para criar o desbloqueio, com uma nota de lançamento, executar a [versão az apim api criar](/cli/azure/apim/api/release#az_apim_api_release_create) comando:

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   A revisão que lança torna-se a revisão atual.

1. Para ver as suas versões, utilize o comando [da lista de lançamentos az apim api:](/cli/azure/apim/api/release#az_apim_api_release_list)

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   As notas especificadas aparecem no alterlog. Pode vê-los na saída do comando anterior.

1. Quando se cria uma libertação, o `--notes` parâmetro é opcional. Pode adicionar ou alterar as notas mais tarde utilizando o comando de atualização de [lançamento az apim api:](/cli/azure/apim/api/release#az_apim_api_release_update)

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   Utilize o valor na `Name` coluna para o ID de libertação.

Pode remover qualquer desbloqueio executando o comando [de eliminação az apim api:](/cli/azure/apim/api/release#az_apim_api_release_delete)

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Navegar até ao portal do programador para ver as alterações e o registo de alterações

Se experimentou o portal do [desenvolvedor,](api-management-howto-developer-portal-customize.md)pode rever as alterações da API e alterar o registo lá.

1. No portal Azure, selecione **APIs**.
1. Selecione **o portal Developer** a partir do menu superior.
1. No portal de desenvolvedores, selecione **APIs** e, em seguida, selecione **API de Conferência de Demonstração**.
1. Tenha em atenção que a nova operação **test** está agora disponível.
1. Selecione **Changelog** perto do nome API.
1. Tenha em atenção que a entrada do registo de alterações é apresentada nesta lista.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma nova revisão
> * Efetuar alterações sem interrupções à revisão
> * Tornar a revisão atual e adicionar uma entrada de registo de alterações
> * Navegar até ao portal do programador para ver as alterações e o registo de alterações

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Publicar várias versões da sua API](api-management-get-started-publish-versions.md)
