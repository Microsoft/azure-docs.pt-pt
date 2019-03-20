---
title: Como utilizar os valores com o nome nas políticas de gestão de API do Azure
description: Saiba como utilizar os valores com o nome nas políticas de gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: f7c52b7ab8aaad917eb03455800df6d8ba4cbc88
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58082709"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Como utilizar os valores com o nome nas políticas de gestão de API do Azure
As políticas de gestão de API são uma funcionalidade poderosa do sistema que permitem que o portal do Azure alterar o comportamento da API através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. Declarações de política podem ser construídas usando valores de texto literal, expressões de política e valores nomeados. 

Cada instância de serviço de gestão de API tem uma coleção de propriedades de pares chave/valor, que é chamada de valores com o nome, que são globais para a instância do serviço. Esses valores com o nome pode ser utilizados para gerir os valores de cadeia de caracteres constante em todas as políticas de configuração da API e. Cada propriedade pode ter os seguintes atributos:

| Atributo | Type | Descrição |
| --- | --- | --- |
| Nome a apresentar |string |Cadeia alfanumérica utilizada para referenciar a propriedade nas políticas. |
| Value |string |O valor da propriedade. Não pode estar vazio ou consistir apenas de espaços em branco. |
|Segredo|boolean|Determina se o valor é um segredo e deve ser encriptado ou não.|
| Etiquetas |matriz da cadeia |Opcional etiquetas que, quando fornecidas podem ser utilizadas para filtrar a lista de propriedades. |

![Valores com nome](./media/api-management-howto-properties/named-values.png)

Valores de propriedade podem conter cadeias de caracteres literais e [expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx). Por exemplo, o valor de `ExpressionProperty` é uma expressão de política que devolve uma cadeia de caracteres que contém a data e hora atuais. A propriedade `ContosoHeaderValue` está marcado como um segredo, para que o respetivo valor não é apresentado.

| Name | Value | Segredo | Etiquetas |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |Falso |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |Verdadeiro |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |Falso | |

## <a name="to-add-and-edit-a-property"></a>Adicionar e editar uma propriedade

![Adicionar uma propriedade](./media/api-management-howto-properties/add-property.png)

1. Selecione **APIs** em **GESTÃO DE API**.
2. Selecione **valores nomeados**.
3. Prima **+ adicionar**.

   Nome e valor são os valores necessários. Se este valor da propriedade é um segredo, verifique a esta é uma caixa de seleção secreta. Introduza um ou mais etiquetas opcionais para ajudar a organizar seus valores com nome e clique em Guardar.
4. Clique em **Criar**.

Assim que a propriedade for criada, pode editá-lo ao clicar na propriedade. Se alterar o nome da propriedade, todas as políticas que fazem referência a essa propriedade são automaticamente atualizadas para utilizar o novo nome.

Para obter informações sobre a edição de uma propriedade com a API REST, consulte [editar uma propriedade com a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Para eliminar uma propriedade

Para eliminar uma propriedade, clique em **eliminar** ao lado da propriedade para eliminar.

> [!IMPORTANT]
> Se a propriedade é referenciada por todas as políticas, não será possível eliminá-lo com êxito até que remova a propriedade de todas as políticas que o utilizam.
> 
> 

Para obter informações sobre como eliminar uma propriedade com a API REST, consulte [eliminar uma propriedade com a API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-named-values"></a>Para procurar e filtrar valores com o nome

O **valores nomeados** guia inclui pesquisar e filtrar recursos para ajudar a gerir os seus valores com nome. Para filtrar a lista de propriedades por nome de propriedade, introduza um termo de pesquisa na **propriedade de pesquisa** caixa de texto. Para apresentar os valores de todas as com nome, desmarque a **propriedade de pesquisa** caixa de texto e prima enter.

Para filtrar a lista de propriedades por valores de etiqueta, introduza um ou mais etiquetas para o **filtrar por marcas** caixa de texto. Para apresentar os valores de todas as com nome, desmarque a **filtrar por marcas** caixa de texto e prima enter.

## <a name="to-use-a-property"></a>Usar uma propriedade

Para utilizar uma propriedade numa política, coloque o nome da propriedade dentro de um par duplo de chaves, como `{{ContosoHeader}}`, conforme mostrado no exemplo a seguir:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

Neste exemplo, `ContosoHeader` é utilizado como o nome de um cabeçalho num `set-header` diretiva, e `ContosoHeaderValue` é utilizado como o valor desse cabeçalho. Quando esta política é avaliada durante uma solicitação ou resposta para o gateway de gestão de API, `{{ContosoHeader}}` e `{{ContosoHeaderValue}}` são substituídas por valores de suas respectivas propriedades.

Valores nomeados podem ser utilizados como atributo completado ou valores de elemento, conforme mostrado no exemplo anterior, mas eles também podem ser inseridos ou combinados com a parte de uma expressão de texto literal, conforme mostrado no exemplo a seguir: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Valores nomeados também podem conter expressões de política. No exemplo a seguir, o `ExpressionProperty` é utilizado.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Quando esta política é avaliada, `{{ExpressionProperty}}` é substituído pelo seu valor: `@(DateTime.Now.ToString())`. Uma vez que o valor é uma expressão de política, a expressão é avaliada e a política prossegue com sua execução.

Pode testar isso no portal do programador ao chamar uma operação que tem uma política com valores nomeados no âmbito. No exemplo a seguir, uma operação é chamada com o exemplo anterior dois `set-header` políticas com valores nomeados. Tenha em atenção que a resposta contém dois cabeçalhos personalizados que foram configurados através de políticas com valores nomeados.

![Portal do programador][api-management-send-results]

Se examinar a [rastreio de Inspetor de API](api-management-howto-api-inspector.md) para uma chamada que inclui as duas políticas de exemplo anterior com valores nomeados, pode ver os dois `set-header` políticas com os valores de propriedade inseridos, bem como a expressão de política avaliação para a propriedade que continha a expressão de política.

![Rastreio de Inspetor de API][api-management-api-inspector-trace]

Enquanto os valores de propriedade podem conter expressões de política, os valores de propriedade não podem conter outros valores com nome. Se o texto que contém uma referência de propriedade é usado para um valor de propriedade, tais como `Property value text {{MyProperty}}`, essa referência de propriedade não ser substituída e serão incluída como parte do valor da propriedade.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre como trabalhar com políticas
  * [Políticas de gestão de API](api-management-howto-policies.md)
  * [Referência de políticas](https://msdn.microsoft.com/library/azure/dn894081.aspx)
  * [Expressões de política](https://msdn.microsoft.com/library/azure/dn910913.aspx)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

