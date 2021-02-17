---
title: Como utilizar valores nomeados nas políticas de gestão da API da Azure
description: Saiba como utilizar valores nomeados nas políticas de Gestão API da Azure. Os valores nomeados podem conter cordas literais, expressões políticas e segredos armazenados no Cofre da Chave Azure.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 2bc9b1c5724fa7bab1fdf5ac9332d87ba03a6d11
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545824"
---
# <a name="use-named-values-in-azure-api-management-policies"></a>Utilizar valores nomeados nas políticas de gestão da API da Azure

[As políticas de Gestão da API](api-management-howto-policies.md) são uma poderosa capacidade do sistema que permite ao editor alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. As declarações políticas podem ser construídas usando valores de texto literal, expressões políticas e valores nomeados.

*Os valores nomeados* são uma coleção global de pares de nomes/valores em cada instância de Gestão da API. Não existe um limite imposto ao número de artigos na recolha. Os valores nomeados podem ser usados para gerir valores e segredos de cordas constantes em todas as configurações e políticas da API. 

:::image type="content" source="media/api-management-howto-properties/named-values.png" alt-text="Valores nomeados no portal Azure":::

## <a name="value-types"></a>Tipos de valor

|Tipo  |Descrição  |
|---------|---------|
|Planície     |  Corda literal ou expressão política     |
|Segredo     |   Cadeia literal ou expressão política que é encriptada pela API Management      |
|[Cofre de chaves](#key-vault-secrets)     |  Identificador de um segredo guardado num cofre de chaves Azure.      |

Valores ou segredos simples podem conter [expressões políticas.](./api-management-policy-expressions.md) Por exemplo, a expressão `@(DateTime.Now.ToString())` devolve uma cadeia contendo a data e hora atuais.

Para obter mais informações sobre os atributos de valor nomeados, consulte a referência API De Gestão de Gestão [de API](/rest/api/apimanagement/2020-06-01-preview/namedvalue/createorupdate).

## <a name="key-vault-secrets"></a>Segredos chave do cofre

Os valores secretos podem ser armazenados como cordas encriptadas na API Management (segredos personalizados) ou referindo segredos no [Cofre da Chave Azure](../key-vault/general/overview.md). 

A utilização de segredos chave do cofre é recomendada porque ajuda a melhorar a segurança da API Management:

* Segredos armazenados em cofres chave podem ser reutilizados em todos os serviços
* As [políticas](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) de acesso granular podem ser aplicadas aos segredos
* Os segredos atualizados no cofre da chave são automaticamente rodados na Gestão da API. Após a atualização no cofre chave, um valor nomeado na Gestão da API é atualizado dentro de 4 horas. Também pode atualizar manualmente o segredo utilizando o portal Azure ou através da gestão REST API.

### <a name="prerequisites-for-key-vault-integration"></a>Pré-requisitos para a integração do cofre-chave

1. Para obter passos para criar um cofre de chaves, consulte [Quickstart: Crie um cofre de chaves utilizando o portal Azure](../key-vault/general/quick-create-portal.md).
1. Ativar uma [identidade gerida](api-management-howto-use-managed-service-identity.md) atribuída pelo sistema ou atribuída ao utilizador no caso de Gestão da API.
1. Atribua uma [política de acesso](../key-vault/general/assign-access-policy-portal.md) ao cofre chave para a identidade gerida com permissões para obter e listar segredos do cofre. Para adicionar a política:
    1. No portal, navegue para o cofre da chave.
    1. Selecione **Definições > Políticas de acesso > +Adicionar Política de Acesso**.
    1. Selecione **permissões secretas** e, em seguida, selecione **Get** and **List**.
    1. Em **Select principal,** selecione o nome do recurso da sua identidade gerida. Se estiver a usar uma identidade atribuída ao sistema, o principal é o nome da sua instância de Gestão de API.
1. Criar ou importar um segredo para o cofre chave. Consulte [Quickstart: set and retrieve a secret from Azure Key Vault using the Azure portal](../key-vault/secrets/quick-create-portal.md).

Para utilizar o segredo do cofre chave, [adicione ou edite um valor nomeado](#add-or-edit-a-named-value), e especifique um tipo de cofre **chave**. Selecione o segredo do cofre da chave.

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-or-edit-a-named-value"></a>Adicionar ou editar um valor nomeado

### <a name="add-a-key-vault-secret"></a>Adicione um segredo chave do cofre

Consulte [os pré-requisitos para a integração do cofre de chaves](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Ao usar um segredo de cofre chave na API Management, tenha cuidado para não apagar o cofre secreto, o cofre de chaves ou a identidade gerida usada para aceder ao cofre da chave.

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. Em **APIs**, selecione **Valores Nomeados**  >  **+Adicionar**.
1. Introduza um identificador **de nome** e introduza um nome **de Exibição** usado para referenciar a propriedade em políticas.
1. No **tipo Valor**, selecione Key **vault**.
1. Introduza o identificador de um segredo de cofre chave (sem versão) ou escolha **selecione** para selecionar um segredo a partir de um cofre de chaves.
    > [!IMPORTANT]
    > Se introduzir um identificador secreto de cofre, certifique-se de que não tem informações sobre a versão. Caso contrário, o segredo não rodará automaticamente na API Management após uma atualização no cofre da chave.
1. Na **identidade do Cliente,** selecione uma identidade gerida atribuída pelo sistema ou uma identidade gerida atribuída pelo utilizador. Saiba como [adicionar ou modificar identidades geridas no seu serviço de Gestão API.](api-management-howto-use-managed-service-identity.md)
    > [!NOTE]
    > A identidade precisa de permissões para obter e listar segredos do cofre das chaves. Se ainda não configurar o acesso ao cofre da chave, a API Management solicita-lhe que possa configurar automaticamente a identidade com as permissões necessárias.
1. Adicione uma ou mais etiquetas opcionais para ajudar a organizar os seus valores nomeados e, em seguida, **Guarde**.
1. Selecione **Criar**.

    :::image type="content" source="media/api-management-howto-properties/add-property.png" alt-text="Adicione o valor secreto do cofre chave":::

### <a name="add-a-plain-or-secret-value"></a>Adicione um valor simples ou secreto

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. Em **APIs**, selecione **Valores Nomeados**  >  **+Adicionar**.
1. Introduza um identificador **de nome** e introduza um nome **de Exibição** usado para referenciar a propriedade em políticas.
1. No **tipo Valor**, selecione **Plain** ou **Secret**.
1. In **Value**, insira uma expressão de corda ou política.
1. Adicione uma ou mais etiquetas opcionais para ajudar a organizar os seus valores nomeados e, em seguida, **Guarde**.
1. Selecione **Criar**.

Uma vez criado o valor nomeado, pode editá-lo selecionando o nome. Se alterar o nome do visor, quaisquer políticas que referenciam o valor nomeado são automaticamente atualizadas para utilizar o novo nome de visualização.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar O Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Para adicionar um valor nomeado, utilize o comando [az apim nv create:](/cli/azure/apim/nv#az_apim_nv_create)

```azurecli
az apim nv create --resource-group apim-hello-word-resource-group \
    --display-name "named_value_01" --named-value-id named_value_01 \
    --secret true --service-name apim-hello-world --value test
```

Depois de criar um valor nomeado, pode atualizá-lo utilizando o comando [de atualização az apim nv.](/cli/azure/apim/nv#az_apim_nv_update) Para ver todos os seus valores nomeados, executar o comando [da lista az apim nv:](/cli/azure/apim/nv#az_apim_nv_list)

```azurecli
az apim nv list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Para ver os detalhes do valor nomeado que criou para este exemplo, executar o comando [az apim nv show:](/cli/azure/apim/nv#az_apim_nv_show)

```azurecli
az apim nv show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Este exemplo é um valor secreto. O comando anterior não devolve o valor. Para ver o valor, executar o comando [az apim nv show-secret:](/cli/azure/apim/nv#az_apim_nv_show_secret)

```azurecli
az apim nv show-secret --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

Para eliminar um valor nomeado, utilize o comando [az apim nv delete:](/cli/azure/apim/nv#az_apim_nv_delete)

```azurecli
az apim nv delete --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --named-value-id named_value_01
```

---

## <a name="use-a-named-value"></a>Use um valor nomeado

Os exemplos desta secção utilizam os valores indicados indicados na tabela seguinte.

| Name               | Valor                      | Segredo | 
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | `TrackingId`                 | Falso  | 
| ContosoHeaderValue | ••••••••••••••••••••••     | Verdadeiro   | 
| ExpressionProperty | `@(DateTime.Now.ToString())` | Falso  | 

Para utilizar um valor nomeado numa apólice, coloque o seu nome de exibição dentro de um par duplo de aparelhos como `{{ContosoHeader}}` , como mostra o seguinte exemplo:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho numa `set-header` política, e é usado como o valor `ContosoHeaderValue` desse cabeçalho. Quando esta política é avaliada durante um pedido ou resposta à porta de administração da API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídas pelos respetivos valores.

Os valores nomeados podem ser utilizados como valores completos de atributos ou elementos, como mostrado no exemplo anterior, mas também podem ser inseridos ou combinados com parte de uma expressão de texto literal, como mostra o seguinte exemplo: 

```xml
<set-header name = "CustomHeader{{ContosoHeader}}" ...>
```

Os valores nomeados também podem conter expressões políticas. No exemplo seguinte, a `ExpressionProperty` expressão é utilizada.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando esta política é avaliada, `{{ExpressionProperty}}` é substituída pelo seu valor, `@(DateTime.Now.ToString())` . Uma vez que o valor é uma expressão política, a expressão é avaliada e a política prossegue com a sua execução.

Pode testá-lo no portal Azure ou no portal do [desenvolvedor,](api-management-howto-developer-portal.md) chamando uma operação que tem uma política com valores nomeados no âmbito. No exemplo seguinte, chama-se uma operação com as duas políticas de exemplo `set-header` anteriores com valores nomeados. Note que a resposta contém dois cabeçalhos personalizados que foram configurados usando políticas com valores nomeados.

:::image type="content" source="media/api-management-howto-properties/api-management-send-results.png" alt-text="Resposta da API de teste":::

Se olharmos para o traço de saída da [API](api-management-howto-api-inspector.md) para uma chamada que inclua as duas políticas anteriores da amostra com valores nomeados, pode ver as `set-header` duas políticas com os valores nomeados inseridos, bem como a avaliação da expressão de política para o valor nomeado que continha a expressão política.

:::image type="content" source="media/api-management-howto-properties/api-management-api-inspector-trace.png" alt-text="Vestígios do inspetor da API":::

> [!CAUTION]
> Se uma política referenciar um segredo no Cofre da Chave Azure, o valor do cofre-chave será visível para os utilizadores que tenham acesso a subscrições ativadas para [rastreio de pedidos de API](api-management-howto-api-inspector.md).

Embora os valores nomeados possam conter expressões políticas, não podem conter outros valores nomeados. Se o texto que contém uma referência de valor nomeado for utilizado para um valor, tal `Text: {{MyProperty}}` como, essa referência não será resolvida e substituída.

## <a name="delete-a-named-value"></a>Excluir um valor nomeado

Para eliminar um valor nomeado, selecione o nome e, em seguida, **selecione Eliminar** no menu de contexto **(...**).

> [!IMPORTANT]
> Se o valor nomeado for referenciado por quaisquer políticas de Gestão de API, não pode eliminá-lo até remover o valor nomeado de todas as políticas que o utilizam.

## <a name="next-steps"></a>Passos seguintes

-   Saiba mais sobre trabalhar com políticas
    -   [Políticas em Gestão de API](api-management-howto-policies.md)
    -   [Referência de políticas](./api-management-policies.md)
    -   [Expressões de política](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png

