---
title: Como usar valores nomeados em políticas de gerenciamento de API do Azure
description: Saiba como usar valores nomeados em políticas de gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 7c25455e28e57ff40664a69718a2e406b52b7632
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834300"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Como usar valores nomeados em políticas de gerenciamento de API do Azure

As políticas de gerenciamento de API são um recurso poderoso do sistema que permite ao portal do Azure alterar o comportamento da API por meio da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. As instruções de política podem ser construídas usando valores de texto literais, expressões de política e valores nomeados.

Cada instância de serviço de gerenciamento de API tem uma coleção de pares de chave/valor, que é chamada de valores nomeados, que são globais para a instância de serviço. Não há limite imposto sobre o número de itens na coleção. Valores nomeados podem ser usados para gerenciar valores de cadeia de caracteres constantes em todas as políticas e configuração de API. Cada valor nomeado pode ter os seguintes atributos:

| Atributo      | Tipo            | Descrição                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | Usado para referenciar o valor nomeado em políticas. Uma cadeia de uma a 256 caracteres. Somente letras, números, ponto e traço são permitidos. |
| `Value`        | string          | Valor real. Não deve estar vazio nem consistir apenas em espaços em branco. Máximo de 4096 caracteres de comprimento.                                        |
| `Secret`       | boolean         | Determina se o valor é um segredo e se deve ser criptografado ou não.                                                               |
| `Tags`         | matriz da cadeia | Usado para filtrar a lista de valores nomeados. Até 32 marcas.                                                                                    |

![Valores com nome](./media/api-management-howto-properties/named-values.png)

Os valores nomeados podem conter cadeias de caracteres literais e [expressões de política](/azure/api-management/api-management-policy-expressions). Por exemplo, o valor de `Expression` é uma expressão de política que retorna uma cadeia de caracteres que contém a data e a hora atuais. O valor nomeado `Credential` é marcado como um segredo, portanto, seu valor não é exibido por padrão.

| Nome       | Valor                      | Segredo | Etiquetas          |
| ---------- | -------------------------- | ------ | ------------- |
| Valor      | 42                         | Falso  | números vitais |
| Credencial | ••••••••••••••••••••••     | Verdadeiro   | avançada      |
| Expressão | @(DateTime.Now.ToString()) | Falso  |               |

> [!NOTE]
> Em vez de valores nomeados armazenados em um serviço de gerenciamento de API, você pode usar valores armazenados no serviço de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , conforme demonstrado por este [exemplo](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml).

## <a name="to-add-and-edit-a-named-value"></a>Para adicionar e editar um valor nomeado

![Adicionar um valor nomeado](./media/api-management-howto-properties/add-property.png)

1. Selecione **APIs** em **GESTÃO DE API**.
2. Selecione **valores nomeados**.
3. Pressione **+ Adicionar**.

    O nome e o valor são valores obrigatórios. Se o valor for um segredo, marque a caixa de seleção _este é um segredo_ . Insira uma ou mais marcas opcionais para ajudar a organizar seus valores nomeados e clique em salvar.

4. Clique em **Criar**.

Depois que o valor nomeado for criado, você poderá editá-lo clicando nele. Se você alterar o nome do valor nomeado, todas as políticas que fizerem referência a esse valor nomeado serão atualizadas automaticamente para usar o novo nome.

Para obter informações sobre como editar um valor nomeado usando a API REST, consulte [Editar um valor nomeado usando a API REST](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-named-value"></a>Para excluir um valor nomeado

Para excluir um valor nomeado, clique em **excluir** ao lado do valor nomeado para excluir.

> [!IMPORTANT]
> Se o valor nomeado for referenciado por qualquer política, você não poderá excluí-lo com êxito até remover o valor nomeado de todas as políticas que o utilizam.

Para obter informações sobre como excluir um valor nomeado usando a API REST, consulte [excluir um valor nomeado usando a API REST](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Para pesquisar e filtrar valores nomeados

A guia **valores nomeados** inclui recursos de pesquisa e filtragem para ajudá-lo a gerenciar seus valores nomeados. Para filtrar a lista de valores nomeados por nome, insira um termo de pesquisa na caixa de texto **propriedade de pesquisa** . Para exibir todos os valores nomeados, desmarque a caixa de texto **propriedade de pesquisa** e pressione Enter.

Para filtrar a lista por marca, insira uma ou mais marcas na caixa de texto **Filtrar por marcas** . Para exibir todos os valores nomeados, desmarque a caixa de texto **Filtrar por marcas** e pressione Enter.

## <a name="to-use-a-named-value"></a>Para usar um valor nomeado

Para usar um valor nomeado em uma política, coloque seu nome dentro de um par duplo de chaves como `{{ContosoHeader}}`, conforme mostrado no exemplo a seguir:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho em uma política de `set-header` e `ContosoHeaderValue` é usado como o valor desse cabeçalho. Quando essa política é avaliada durante uma solicitação ou resposta ao gateway de gerenciamento de API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídas pelos respectivos valores.

Os valores nomeados podem ser usados como valores de atributo ou de elemento completos, conforme mostrado no exemplo anterior, mas eles também podem ser inseridos ou combinados com parte de uma expressão de texto literal, conforme mostrado no exemplo a seguir: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Os valores nomeados também podem conter expressões de política. No exemplo a seguir, o `ExpressionProperty` é usado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando essa política é avaliada, `{{ExpressionProperty}}` é substituído pelo seu valor: `@(DateTime.Now.ToString())`. Como o valor é uma expressão de política, a expressão é avaliada e a política prossegue com sua execução.

Você pode testá-lo no portal do desenvolvedor chamando uma operação que tenha uma política com valores nomeados no escopo. No exemplo a seguir, uma operação é chamada com os dois exemplos anteriores `set-header` políticas com valores nomeados. Observe que a resposta contém dois cabeçalhos personalizados que foram configurados usando políticas com valores nomeados.

![Portal do programador][api-management-send-results]

Se você examinar o [rastreamento do Inspetor de API](api-management-howto-api-inspector.md) para uma chamada que inclui as duas políticas de exemplo anteriores com valores nomeados, você poderá ver as duas políticas de `set-header` com os valores nomeados inseridos, bem como a avaliação da expressão de política para o valor nomeado que continha a expressão de política.

![Rastreamento do Inspetor de API][api-management-api-inspector-trace]

Embora os valores nomeados possam conter expressões de política, eles não podem conter outros valores nomeados. Se o texto que contém uma referência de valor nomeado for usado para um valor, como `Text: {{MyProperty}}`, essa referência não será resolvida e substituída.

## <a name="next-steps"></a>Passos seguintes

-   Saiba mais sobre como trabalhar com políticas
    -   [Políticas no gerenciamento de API](api-management-howto-policies.md)
    -   [Referência de políticas](/azure/api-management/api-management-policies)
    -   [Expressões de política](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
