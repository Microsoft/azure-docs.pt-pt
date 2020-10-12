---
title: Como utilizar valores nomeados nas políticas de gestão da API da Azure
description: Saiba como utilizar valores nomeados nas políticas de Gestão API da Azure. Os valores nomeados podem conter cordas literais e expressões políticas.
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
ms.openlocfilehash: 3f317276ae92e6121d519553b7883677dab89705
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852196"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Como utilizar valores nomeados nas políticas de gestão da API da Azure

As políticas de Gestão da API são uma poderosa capacidade do sistema que permite ao portal Azure alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. As declarações políticas podem ser construídas usando valores de texto literal, expressões políticas e valores nomeados.

Cada instância de serviço de Gestão API tem uma coleção de pares chave/valor, que é chamado de valores nomeados, que são globais para a instância de serviço. Não existe um limite imposto ao número de artigos na recolha. Os valores nomeados podem ser usados para gerir valores de cadeia constantes em todas as configurações e políticas da API. Cada valor nomeado pode ter os seguintes atributos:

| Atributo      | Tipo            | Description                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | cadeia (de carateres)          | Usado para referenciar o valor nomeado nas políticas. Uma sequência de um a 256 caracteres. Só são permitidas letras, números, pontos e traços. |
| `Value`        | string          | Valor real. Não deve estar vazio ou consistir apenas em espaço branco. Máximo de 4096 caracteres.                                        |
| `Secret`       | boolean         | Determina se o valor é secreto e deve ser encriptado ou não.                                                               |
| `Tags`         | matriz da cadeia | Usado para filtrar a lista de valores nomeada. Até 32 etiquetas.                                                                                    |

![Valores com nome](./media/api-management-howto-properties/named-values.png)

Os valores nomeados podem conter cordas literais e [expressões políticas.](./api-management-policy-expressions.md) Por exemplo, o valor de `Expression` uma expressão política que devolve uma cadeia contendo a data e hora atuais. O valor nomeado `Credential` é marcado como um segredo, pelo que o seu valor não é apresentado por padrão.

| Nome       | Valor                      | Segredo | Etiquetas          |
| ---------- | -------------------------- | ------ | ------------- |
| Valor      | 42                         | Falso  | números vitais |
| Credencial | ••••••••••••••••••••••     | Verdadeiro   | security      |
| Expression | @(DateTime.Now.ToString()) | Falso  |               |

> [!NOTE]
> Em vez de valores nomeados armazenados num serviço de Gestão API, pode utilizar valores armazenados no serviço [Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) como demonstra este [exemplo](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml).

## <a name="to-add-and-edit-a-named-value"></a>Para adicionar e editar um valor nomeado

![Adicionar um valor nomeado](./media/api-management-howto-properties/add-property.png)

1. Selecione **APIs** em **GESTÃO DE API**.
2. Selecione **valores nomeados**.
3. Prima **+Adicionar**.

    Nome e Valor são valores necessários. Se o valor for secreto, verifique se esta é uma caixa de verificação _secreta._ Introduza uma ou mais etiquetas opcionais para ajudar a organizar os seus valores nomeados e clique em Guardar.

4. Clique em **Criar**.

Uma vez criado o valor nomeado, pode editá-lo clicando nele. Se alterar o nome de valor nomeado, quaisquer políticas que referenciam o valor nomeado são automaticamente atualizadas para usar o novo nome.

## <a name="to-delete-a-named-value"></a>Para eliminar um valor nomeado

Para eliminar um valor nomeado, clique em **Eliminar** ao lado do valor nomeado para eliminar.

> [!IMPORTANT]
> Se o valor nomeado for referenciado por quaisquer políticas, não poderá eliminá-lo com sucesso até remover o valor nomeado de todas as políticas que o utilizam.

## <a name="to-search-and-filter-named-values"></a>Pesquisar e filtrar valores nomeados

O separador **Valores Nomeados** inclui capacidades de pesquisa e filtragem para ajudá-lo a gerir os seus valores nomeados. Para filtrar a lista de valores nomeados pelo nome, introduza um termo de pesquisa na caixa de texto da **propriedade Pesquisar.** Para exibir todos os valores nomeados, limpe a caixa de texto da **propriedade de Pesquisa** e prima para introduzir.

Para filtrar a lista por marcação, introduza uma ou mais etiquetas no Filtro por caixa de texto **tags.** Para visualizar todos os valores nomeados, limpe o **Filtro por tags** textbox e prima para introduzir.

## <a name="to-use-a-named-value"></a>Para usar um valor nomeado

Para utilizar um valor nomeado numa apólice, coloque o seu nome dentro de um par duplo de aparelhos como `{{ContosoHeader}}` , como mostra o seguinte exemplo:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome de um cabeçalho numa `set-header` política, e é usado como o valor `ContosoHeaderValue` desse cabeçalho. Quando esta política é avaliada durante um pedido ou resposta à porta de administração da API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídas pelos respetivos valores.

Os valores nomeados podem ser utilizados como valores completos de atributos ou elementos, como mostrado no exemplo anterior, mas também podem ser inseridos ou combinados com parte de uma expressão de texto literal, como mostra o seguinte exemplo: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Os valores nomeados também podem conter expressões políticas. No exemplo seguinte, o `ExpressionProperty` é usado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando esta política é avaliada, `{{ExpressionProperty}}` é substituída pelo seu valor: `@(DateTime.Now.ToString())` . Uma vez que o valor é uma expressão política, a expressão é avaliada e a política prossegue com a sua execução.

Pode testá-lo no portal do desenvolvedor, chamando uma operação que tem uma política com valores nomeados no âmbito. No exemplo seguinte, chama-se uma operação com as duas políticas de exemplo `set-header` anteriores com valores nomeados. Note que a resposta contém dois cabeçalhos personalizados que foram configurados usando políticas com valores nomeados.

![Portal do programador][api-management-send-results]

Se olharmos para o rastreio do Inspetor da [API](api-management-howto-api-inspector.md) para uma chamada que inclui as duas políticas anteriores da amostra com valores nomeados, pode ver as `set-header` duas políticas com os valores nomeados inseridos, bem como a avaliação da expressão de política para o valor nomeado que continha a expressão política.

![Vestígios do inspetor da API][api-management-api-inspector-trace]

Embora os valores nomeados possam conter expressões políticas, não podem conter outros valores nomeados. Se o texto que contém uma referência de valor nomeado for utilizado para um valor, tal `Text: {{MyProperty}}` como, essa referência não será resolvida e substituída.

## <a name="next-steps"></a>Passos seguintes

-   Saiba mais sobre trabalhar com políticas
    -   [Políticas em Gestão de API](api-management-howto-policies.md)
    -   [Referência de políticas](./api-management-policies.md)
    -   [Expressões de política](./api-management-policy-expressions.md)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
