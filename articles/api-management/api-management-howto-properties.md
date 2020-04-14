---
title: Como utilizar valores nomeados nas políticas de Gestão da API azure
description: Saiba como utilizar valores nomeados nas políticas de Gestão de API azure.
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
ms.openlocfilehash: 4362d0875ac2c20fc6963d404f86898a12387dad
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260926"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Como utilizar valores nomeados nas políticas de Gestão da API azure

As políticas de Gestão API são uma poderosa capacidade do sistema que permite ao portal Azure alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. As declarações políticas podem ser construídas utilizando valores de texto literais, expressões políticas e valores nomeados.

Cada instância de serviço de Gestão API tem uma coleção de pares chave/valor, que é chamado valores, que são globais para a instância de serviço. Não existe limite imposto ao número de artigos na coleção. Os valores nomeados podem ser usados para gerir valores de cadeia constantes em todas as configurações e políticas da API. Cada valor nomeado pode ter os seguintes atributos:

| Atributo      | Tipo            | Descrição                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | string          | Usado para referenciar o valor nomeado nas políticas. Uma sequência de um a 256 caracteres. Só são permitidas letras, números, pontos e traços. |
| `Value`        | string          | Valor real. Não deve estar vazio ou consistir apenas em espaço branco. No máximo 4096 caracteres de comprimento.                                        |
| `Secret`       | boolean         | Determina se o valor é um segredo e deve ser encriptado ou não.                                                               |
| `Tags`         | matriz da cadeia | Usado para filtrar a lista de valor nomeada. Até 32 etiquetas.                                                                                    |

![Valores com nome](./media/api-management-howto-properties/named-values.png)

Os valores nomeados podem conter cordas literais e [expressões políticas.](/azure/api-management/api-management-policy-expressions) Por exemplo, o `Expression` valor é uma expressão política que devolve uma cadeia contendo a data e a hora atuais. O valor `Credential` nomeado é marcado como um segredo, pelo que o seu valor não é mostrado por defeito.

| Nome       | Valor                      | Segredo | Etiquetas          |
| ---------- | -------------------------- | ------ | ------------- |
| Valor      | 42                         | Falso  | números vitais |
| Credencial | ••••••••••••••••••••••     | Verdadeiro   | security      |
| Expressão | @(Datetime.Now.Tostring()) | Falso  |               |

> [!NOTE]
> Em vez de valores nomeados armazenados dentro de um serviço de Gestão API, pode utilizar valores armazenados no serviço [Azure Key Vault,](https://azure.microsoft.com/services/key-vault/) como demonstra este [exemplo.](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)

## <a name="to-add-and-edit-a-named-value"></a>Para adicionar e editar um valor nomeado

![Adicione um valor nomeado](./media/api-management-howto-properties/add-property.png)

1. Selecione **APIs** em **GESTÃO DE API**.
2. Selecione **Valores Nomeados**.
3. Prima **+Adicionar**.

    Nome e Valor são valores necessários. Se o valor é secreto, verifique se esta é uma caixa de verificação _secreta._ Introduza uma ou mais etiquetas opcionais para ajudar na organização dos seus valores nomeados e clique em Guardar.

4. Clique em **Criar**.

Uma vez criado o valor nomeado, pode editá-lo clicando nele. Se alterar o nome de valor nomeado, quaisquer políticas que mencionem esse valor nomeado são automaticamente atualizadas para utilizar o novo nome.

Para obter informações sobre a edição de um valor nomeado utilizando a API REST, consulte [Editar um valor nomeado utilizando a API REST](/rest/api/apimanagement/2019-12-01/property?patch).

## <a name="to-delete-a-named-value"></a>Para apagar um valor nomeado

Para eliminar um valor nomeado, clique em **Apagar** ao lado do valor nomeado para eliminar.

> [!IMPORTANT]
> Se o valor nomeado for referenciado por quaisquer políticas, não poderá eliminá-lo com sucesso até remover o valor nomeado de todas as políticas que o utilizam.

Para obter informações sobre a eliminação de um valor nomeado utilizando a API REST, consulte [Eliminar um valor nomeado utilizando a API REST](/rest/api/apimanagement/2019-12-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>Para pesquisar e filtrar valores nomeados

O separador **de valores Nomeados** inclui capacidades de pesquisa e filtragem para ajudá-lo a gerir os seus valores nomeados. Para filtrar a lista de valores nomeados pelo nome, introduza um termo de pesquisa na caixa de texto da **propriedade Search.** Para exibir todos os valores nomeados, limpe a caixa de texto da **propriedade Search** e prima a entrada.

Para filtrar a lista por etiqueta, introduza uma ou mais etiquetas no **Filtro por tags** de caixa de texto. Para visualizar todos os valores nomeados, limpe o **Filtro por tags** e prima introduzir.

## <a name="to-use-a-named-value"></a>Para usar um valor nomeado

Para utilizar um valor nomeado numa apólice, coloque o seu `{{ContosoHeader}}`nome dentro de um par duplo de aparelhos como, como se fosse o seguinte exemplo:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é usado como o nome `set-header` de `ContosoHeaderValue` um cabeçalho numa política, e é usado como o valor desse cabeçalho. Quando esta política é avaliada durante um pedido ou `{{ContosoHeader}}` resposta `{{ContosoHeaderValue}}` ao gateway de Gestão da API, e é substituída por respetivos valores.

Os valores nomeados podem ser utilizados como valores completos de atributo ou elementos, como mostra o exemplo anterior, mas também podem ser inseridos ou combinados com parte de uma expressão de texto literal, como mostra o seguinte exemplo:`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Os valores nomeados também podem conter expressões políticas. No exemplo seguinte, `ExpressionProperty` o é utilizado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando esta política é `{{ExpressionProperty}}` avaliada, é `@(DateTime.Now.ToString())`substituída pelo seu valor: . Uma vez que o valor é uma expressão política, a expressão é avaliada e a política prossegue com a sua execução.

Pode testar isso no portal do desenvolvedor, chamando uma operação que tem uma política com valores nomeados de âmbito. No exemplo seguinte, é convocada uma operação `set-header` com as duas políticas de exemplo anteriores com valores nomeados. Note que a resposta contém dois cabeçalhos personalizados que foram configurados usando políticas com valores nomeados.

![Portal do programador][api-management-send-results]

Se olharmos para o rastreio do [Inspetor da API](api-management-howto-api-inspector.md) para uma chamada que `set-header` inclui as duas políticas de amostra anteriores com valores nomeados, pode ver as duas políticas com os valores nomeados inseridos, bem como a avaliação de expressão política para o valor nomeado que continha a expressão política.

![Vestígios do Inspetor API][api-management-api-inspector-trace]

Embora os valores nomeados possam conter expressões políticas, não podem conter outros valores nomeados. Se o texto que contenha uma referência `Text: {{MyProperty}}`de valor nomeado for utilizado para um valor, como, por exemplo, essa referência não será resolvida e substituída.

## <a name="next-steps"></a>Passos seguintes

-   Saiba mais sobre trabalhar com políticas
    -   [Políticas em Gestão aPi](api-management-howto-policies.md)
    -   [Referência de políticas](/azure/api-management/api-management-policies)
    -   [Expressões de política](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
