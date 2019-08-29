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
ms.date: 07/22/2019
ms.author: apimpm
ms.openlocfilehash: d71d71c4d289235e5b67a5201c1f7417274b8fca
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072332"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Como usar valores nomeados em políticas de gerenciamento de API do Azure

As políticas de gerenciamento de API são um recurso poderoso do sistema que permite ao portal do Azure alterar o comportamento da API por meio da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. As instruções de política podem ser construídas usando valores de texto literais, expressões de política e valores nomeados.

Cada instância de serviço de gerenciamento de API tem uma coleção de propriedades de pares de chave/valor, que é chamada de valores nomeados, que são globais para a instância do serviço. Não há limite imposto sobre o número de itens na coleção. Valores nomeados podem ser usados para gerenciar valores de cadeia de caracteres constantes em todas as políticas e configuração de API. Cada valor nomeado pode ter os seguintes atributos:

| Atributo      | Type            | Descrição                                                                                                                         |
| -------------- | --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | Cadeia de caracteres          | Usado para referenciar a propriedade em políticas. Uma cadeia de uma a 256 caracteres. Somente letras, números, ponto e traço são permitidos. |
| `Value`        | Cadeia de caracteres          | Valor real. Não deve estar vazio nem consistir apenas em espaços em branco. Máximo de 4096 caracteres de comprimento.                                     |
| `Secret`       | boolean         | Determina se o valor é um segredo e se deve ser criptografado ou não.                                                            |
| `Tags`         | matriz da cadeia | Usado para filtrar a lista de propriedades. Até 32 marcas.                                                                                    |

![Valores com nome](./media/api-management-howto-properties/named-values.png)

Os valores nomeados podem conter cadeias de caracteres literais e [expressões de política](/azure/api-management/api-management-policy-expressions). Por exemplo, o valor de `Expression` é uma expressão de política que retorna uma cadeia de caracteres que contém a data e a hora atuais. O valor `Credential` nomeado é marcado como um segredo, portanto, seu valor não é exibido por padrão.

| Name       | Value                      | Secret | Tags          |
| ---------- | -------------------------- | ------ | ------------- |
| Value      | 42                         | False  | números vitais |
| Credencial | ••••••••••••••••••••••     | True   | Segurança      |
| Expressão | @(DateTime.Now.ToString()) | False  |               |

## <a name="to-add-and-edit-a-property"></a>Para adicionar e editar uma propriedade

![Adicionar uma propriedade](./media/api-management-howto-properties/add-property.png)

1. Selecione **APIs** em **GESTÃO DE API**.
2. Selecione **valores nomeados**.
3. Pressione **+ Adicionar**.

    O nome e o valor são valores obrigatórios. Se esse valor de propriedade for um segredo, marque a caixa de seleção este é um segredo. Insira uma ou mais marcas opcionais para ajudar a organizar seus valores nomeados e clique em salvar.

4. Clique em **Criar**.

Depois que a propriedade for criada, você poderá editá-la clicando na propriedade. Se você alterar o nome da propriedade, todas as políticas que fizerem referência a essa propriedade serão atualizadas automaticamente para usar o novo nome.

Para obter informações sobre como editar uma propriedade usando a API REST, consulte [Editar uma propriedade usando a API REST](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-property"></a>Para excluir uma propriedade

Para excluir uma propriedade, clique em **excluir** ao lado da propriedade a ser excluída.

> [!IMPORTANT]
> Se a propriedade for referenciada por qualquer política, você não poderá excluí-la com êxito até remover a propriedade de todas as políticas que a utilizam.

Para obter informações sobre como excluir uma propriedade usando a API REST, consulte [excluir uma propriedade usando a API REST](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Para pesquisar e filtrar valores nomeados

A guia **valores nomeados** inclui recursos de pesquisa e filtragem para ajudá-lo a gerenciar seus valores nomeados. Para filtrar a lista de propriedades pelo nome da propriedade, insira um termo de pesquisa na caixa de texto **propriedade de pesquisa** . Para exibir todos os valores nomeados, desmarque a caixa de texto **propriedade de pesquisa** e pressione Enter.

Para filtrar a lista de propriedades por valores de marca, insira uma ou mais marcas na caixa de texto **Filtrar por marcas** . Para exibir todos os valores nomeados, desmarque a caixa de texto **Filtrar por marcas** e pressione Enter.

## <a name="to-use-a-property"></a>Para usar uma propriedade

Para usar uma propriedade em uma política, coloque o nome da propriedade dentro de um par duplo de chaves `{{ContosoHeader}}`como, conforme mostrado no exemplo a seguir:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho em uma `set-header` política e `ContosoHeaderValue` é usado como o valor desse cabeçalho. Quando essa política é avaliada durante uma solicitação ou resposta para o gateway `{{ContosoHeader}}` de gerenciamento de API e `{{ContosoHeaderValue}}` é substituída por seus respectivos valores de propriedade.

Os valores nomeados podem ser usados como valores de atributo ou de elemento completos, conforme mostrado no exemplo anterior, mas eles também podem ser inseridos ou combinados com parte de uma expressão de texto literal, conforme mostrado no exemplo a seguir:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Os valores nomeados também podem conter expressões de política. No exemplo a seguir, o `ExpressionProperty` é usado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando essa política é avaliada `{{ExpressionProperty}}` , é substituída pelo seu `@(DateTime.Now.ToString())`valor:. Como o valor é uma expressão de política, a expressão é avaliada e a política prossegue com sua execução.

Você pode testá-lo no portal do desenvolvedor chamando uma operação que tenha uma política com valores nomeados no escopo. No exemplo a seguir, uma operação é chamada com as duas políticas de `set-header` exemplo anteriores com valores nomeados. Observe que a resposta contém dois cabeçalhos personalizados que foram configurados usando políticas com valores nomeados.

![Portal do programador][api-management-send-results]

Se você examinar o [rastreamento do Inspetor de API](api-management-howto-api-inspector.md) para uma chamada que inclui as duas políticas de exemplo anteriores com valores nomeados, você poderá `set-header` ver as duas políticas com os valores de propriedade inseridos, bem como a avaliação da expressão de política para a propriedade que continha a expressão de política.

![Rastreamento do Inspetor de API][api-management-api-inspector-trace]

Embora os valores de propriedade possam conter expressões de política, os valores de propriedade não podem conter outros valores nomeados. Se o texto que contém uma referência de propriedade for usado para um valor de `Property value text {{MyProperty}}`Propriedade, como, essa referência de propriedade não será substituída e será incluída como parte do valor da propriedade.

## <a name="next-steps"></a>Passos Seguintes

-   Saiba mais sobre como trabalhar com políticas
    -   [Políticas no gerenciamento de API](api-management-howto-policies.md)
    -   [Referência de políticas](/azure/api-management/api-management-policies)
    -   [Expressões de política](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
