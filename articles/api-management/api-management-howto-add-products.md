---
title: Tutorial - Criar e publicar um produto na Azure API Management
description: Neste tutorial, cria e publica um produto na Azure API Management. Uma vez publicado, os desenvolvedores podem começar a usar as APIs do produto.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: d0420b92fc94e0a1a9c8a4057f419a57a9909223
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545161"
---
# <a name="tutorial-create-and-publish-a-product"></a>Tutorial: Criar e publicar um produto  

Na Azure API Management, um [*produto*](api-management-terminology.md#term-definitions) contém uma ou mais APIs, bem como uma quota de utilização e os termos de utilização. Depois de um produto ser publicado, os programadores podem subscrevê-lo e começar a utilizar as APIs do mesmo.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar e publicar um produto
> * Adicionar uma API ao produto

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="Produtos de Gestão API em portal":::


## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
+ Conclua também o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Criar e publicar um produto

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inscreva-se no portal Azure e navegue para a sua instância de Gestão da API.
1. Na navegação à esquerda, selecione **Produtos**  >  **+ Adicionar.**
1.  Na janela **do produto Adicionar,** introduza os valores descritos na tabela seguinte para criar o seu produto.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Adicionar produto no portal":::

    | Nome                     | Descrição                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome a apresentar             | O nome que pretende que seja mostrado no portal do [desenvolvedor](api-management-howto-developer-portal.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Description              | Fornecer informações sobre o produto, como o seu propósito, as APIs a que fornece acesso, e outros detalhes.                                                                                                                                               |
    | Estado                    | Selecione **Publicado** se quiser publicar o produto. Antes de as APIs num produto poderem ser chamadas, o produto tem de ser publicado. Por padrão, os novos produtos não são publicados e são visíveis apenas para o grupo **de Administradores.**                                                                                      |
    | Exige subscrição    | Selecione se um utilizador é obrigado a subscrever a utilização do produto.                                                                                                                                                                                                                                   |
    | Requer aprovação        | Selecione se pretender que um administrador reveja e aceite ou rejeite as tentativas de subscrição deste produto. Se não forem selecionadas, as tentativas de subscrição são aprovadas automaticamente.                                                                                                                         |
    | Limite da contagem de subscrições | Opcionalmente limitar a contagem de várias subscrições simultâneas.                                                                                                                                                                                                                                |
    | Termos legais              | Pode incluir os termos de utilização do produto, que os subscritores têm de aceitar para poderem utilizar o produto.                                                                                                                                                                                                             |
    | APIs                     | Selecione um ou mais APIs. Também pode adicionar APIs depois de criar o produto. Para obter mais informações, consulte [Adicionar APIs a um produto](#add-apis-to-a-product) mais tarde neste artigo. |

3. Selecione **Criar** para criar o novo produto.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar O Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para criar um produto, executar o [produto az apim criar](/cli/azure/apim/product#az_apim_product_create) comando:

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

Pode especificar vários valores para o seu produto:

   | Parâmetro | Descrição |
   |-----------|-------------|
   | `--product-name` | O nome que pretende que seja mostrado no portal do [desenvolvedor](api-management-howto-developer-portal.md). |
   | `--description`  | Fornecer informações sobre o produto, como o seu propósito, as APIs a que fornece acesso, e outros detalhes. |
   | `--state`        | Selecione **publicado** se quiser publicar o produto. Antes de as APIs num produto poderem ser chamadas, o produto tem de ser publicado. Por padrão, os novos produtos não são publicados e são visíveis apenas para o grupo **de Administradores.** |
   | `--subscription-required` | Selecione se um utilizador é obrigado a subscrever a utilização do produto. |
   | `--approval-required` | Selecione se pretender que um administrador reveja e aceite ou rejeite as tentativas de subscrição deste produto. Se não forem selecionadas, as tentativas de subscrição são aprovadas automaticamente. |
   | `--subscriptions-limit` | Opcionalmente limitar a contagem de várias subscrições simultâneas.|
   | `--legal-terms`         | Pode incluir os termos de utilização do produto, que os subscritores têm de aceitar para poderem utilizar o produto. |

Para ver os seus produtos atuais, utilize o comando [da lista de produtos az apim:](/cli/azure/apim/product#az_apim_product_list)

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Pode eliminar um produto utilizando o comando de eliminação do [produto az apim:](/cli/azure/apim/product#az_apim_product_delete)

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

### <a name="add-more-configurations"></a>Adicionar mais configurações

Continue a configurar o produto depois de o guardar. No seu caso de Gestão de API, selecione o produto da janela **Produtos.** Adicionar ou atualizar:

|Item   |Descrição  |
|---------|---------|
|Definições     |    Metadados e estado do produto     |
|APIs     |  APIs associadas ao produto       |
|[Políticas](api-management-howto-policies.md)     |  Políticas aplicadas às APIs do produto      |
|Controlo de acesso     |  Visibilidade do produto para desenvolvedores ou hóspedes       |
|[Subscrições](api-management-subscriptions.md)    |    Subscritores de produtos     |

## <a name="add-apis-to-a-product"></a>Adicionar APIs a um produto

Os produtos são associações de uma ou mais APIs. Pode incluir um número de APIs e disponibilizá-las para os programadores através do portal do programador. Durante a criação do produto, pode adicionar uma ou mais APIs existentes. Também pode adicionar APIs ao produto mais tarde, quer a partir da página **Definições** de Produtos, quer enquanto cria uma API.

Os programadores têm de subscrever primeiro um produto para obter acesso à API. Quando subscrevem, recebem uma chave de subscrição que é válida para qualquer API nesse produto. Se tiver criado a instância APIM, já é um administrador, pelo que tem todos os produtos subscritos por predefinição.

### <a name="add-an-api-to-an-existing-product"></a>Adicionar uma API a um produto existente

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na navegação à esquerda da sua instância de Gestão de API, selecione **Produtos.**
1. Selecione um produto e, em seguida, selecione **APIs**.
1. Selecione **+ Adicionar**.
1. Selecione um ou mais APIs e, em seguida, **selecione**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="Adicionar API ao produto existente":::

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Para ver as suas APIs geridas, utilize o comando [da lista az apim api:](/cli/azure/apim/api#az_apim_api_list)

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. Para adicionar uma API ao seu produto, executar o comando [api de produto az apim:](/cli/azure/apim/product/api#az_apim_product_api_add)

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. Verifique a adição utilizando o comando da [lista api do produto az apim:](/cli/azure/apim/product/api#az_apim_product_api_list)

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

Pode remover uma API de um produto utilizando o comando [api do produto az apim:](/cli/azure/apim/product/api#az_apim_product_api_delete)

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

> [!TIP]
> Pode criar ou atualizar a subscrição de um utilizador a um produto com teclas de subscrição personalizadas através de um comando [REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) ou PowerShell.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar e publicar um produto
> * Adicionar uma API ao produto

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Criar API em branco e simular respostas da API](mock-api-responses.md)
