---
title: Funções em consultas de registo do Monitor de Azure
description: Este artigo descreve como usar funções para chamar uma consulta de outra consulta de log no Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752943"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Funções em consultas de registo do Monitor de Azure
Uma função é uma consulta de log no Azure Monitor que pode ser usada em outras consultas de log como se fosse um comando. As funções permitem que os desenvolvedores forneçam soluções a diferentes clientes e que reutilem a lógica de consulta no seu próprio ambiente. Este artigo fornece detalhes sobre como usar funções e como criar as suas próprias funções.

## <a name="types-of-functions"></a>Tipos de funções
Existem dois tipos de funções no Azure Monitor:

- **Função de solução:** Funções pré-construídas incluídas com monitor Azure. Estes estão disponíveis em todos os espaços de trabalho do Log Analytics e não podem ser modificados.
- **Funções do espaço de trabalho:** Funções instaladas num determinado espaço de trabalho do Log Analytics e podem ser modificadas e controladas pelo utilizador.

## <a name="viewing-functions"></a>Funções de visualização
Pode ver funções de solução e funções de espaço de trabalho no espaço de trabalho atual a partir do separador **Funções** no painel esquerdo de um espaço de trabalho Log Analytics. Utilize o botão **Filtro** para filtrar as funções incluídas na lista e **grupo** para alterar o seu agrupamento. Digite um fio na caixa **de busca** para localizar uma determinada função. Passe sobre uma função para ver detalhes sobre o mesmo, incluindo uma descrição e parâmetros.

:::image type="content" source="media/functions/view-functions.png" alt-text="Ver função" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>Use uma função
Utilize uma função numa consulta digitando o seu nome com valores para quaisquer parâmetros, tal como escreveria num comando. A saída da função pode ser devolvida como resultado ou canalizada para outro comando.

Adicione uma função à consulta atual clicando duas vezes no seu nome ou pairando sobre ele e selecionando **Utilização no editor**. As funções no espaço de trabalho também serão incluídas no intellisense à medida que digita numa consulta. 

Se uma consulta necessitar de parâmetros, forneça-os utilizando a sintaxe: `function_name(param1,param2,...)` .

:::image type="content" source="media/functions/function-use.png" alt-text="Use uma função" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>Criar uma função
Para criar uma função a partir da consulta atual no editor, **selecione Guardar** e, em seguida, Guardar **como função**. 

:::image type="content" source="media/functions/function-save.png" alt-text="Criar uma função" lightbox="media/functions/function-save.png":::

Crie uma função com o Log Analytics no portal Azure clicando em **Guardar** e, em seguida, fornecendo as informações na tabela seguinte.

| Definições | Descrição |
|:---|:---|
| Nome da Função  | Nome para a função. Isto pode não incluir um espaço ou quaisquer caracteres especiais. Também não pode começar com um sublinhado (_) uma vez que este personagem é reservado para funções de solução. |
| Categoria legado | Categoria definida pelo utilizador para ajudar a filtrar e agrupar funções.   |
| Salvar como grupo de computadores | Guarde a consulta como um [grupo de computador.](computer-groups.md)  |
| Parâmetros | Adicione um parâmetro para cada variável na função que requer um valor quando é usado. Consulte [os parâmetros da função](#function-parameters) para obter mais detalhes. |

:::image type="content" source="media/functions/function-details.png" alt-text="Detalhes da função" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>Parâmetros de função 
Pode adicionar parâmetros a uma função para que possa fornecer valores para determinadas variáveis ao chamá-la. Isto permite que a mesma função seja usada em diferentes consultas, cada uma fornecendo valores diferentes para os parâmetros. Os parâmetros são definidos pelas seguintes propriedades.

| Definições | Descrição |
|:---|:---|
| Tipo  | Tipo de dados para o valor. |
| Name  | Nome para o parâmetro. Este é o nome que deve ser usado na consulta para substituir pelo valor do parâmetro.  |
| Valor predefinido | Valor a ser usado para o parâmetro se um valor não for fornecido. |

Os parâmetros são encomendados à medida que são criados com quaisquer parâmetros que não tenham valor padrão posicionados à frente daqueles que têm um valor padrão.

## <a name="working-with-function-code"></a>Trabalhar com código de função
Pode ver o código de uma função para obter informações sobre como funciona ou para modificar o código para uma função de espaço de trabalho. Selecione **Carregue o código de função** para adicionar o código de função à consulta atual no editor. Se o adicionar a uma consulta vazia ou à primeira linha de uma consulta existente, então adicionará o nome da função ao separador. Se for uma função de espaço de trabalho, isto permite a opção de editar os detalhes da função.

:::image type="content" source="media/functions/function-code.png" alt-text="Código de função de carga" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>Editar uma função
Editar as propriedades ou o código de uma função criando uma nova consulta e, em seguida, pairar sobre o nome da função e selecionar o código de **função de carga**. Faça quaisquer modificações que pretenda para o código e **selecione Guardar** e, em seguida, **Editar detalhes da função**. Esco faça quaisquer alterações que pretenda para as propriedades e parâmetros da função antes de clicar **em Guardar**.

:::image type="content" source="media/functions/function-edit.png" alt-text="Função de edição" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>Exemplo
A função de amostra que se segue devolve todos os eventos no registo de atividade do Azure desde uma determinada data e que correspondem a uma determinada categoria. 

Comece com a seguinte consulta utilizando valores codificados. Isto verifica que a consulta funciona como esperado.

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="Função exemplo - consulta inicial" lightbox="media/functions/example-query.png":::

Em seguida, substitua os valores codificados por nomes de parâmetros e, em seguida, guarde a função selecionando **Guardar** e, em seguida, **Guardar como função**.

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="Função exemplo - salvar função" lightbox="media/functions/example-save-function.png":::

 Fornecer os seguintes valores para as propriedades da função.

| Propriedade | Valor |
|:---|:---|
| Nome da função | AzureActivityByCategory |
| Categoria legado | Funções de demonstração |

Defina os seguintes parâmetros antes de guardar a função.

| Tipo | Name | Valor predefinido |
|:---|:---|:---|
| string   | CategoriaParam | "Administrativo" |
| datetime | DataParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="Função exemplo - propriedades de função" lightbox="media/functions/example-function-properties.png":::

Crie uma nova consulta e veja a nova função pairando sobre ela. Note a ordem dos parâmetros uma vez que esta é a ordem que devem ser especificadas quando utilizar a função.

:::image type="content" source="media/functions/example-view-details.png" alt-text="Função exemplo - ver detalhes" lightbox="media/functions/example-view-details.png":::

Selecione **Utilizar no editor** para adicionar a nova função a uma consulta e, em seguida, adicionar valores para os parâmetros. Note que não precisa de especificar um valor para a CategoryParam porque tem um valor padrão.

:::image type="content" source="media/functions/example-use-function.png" alt-text="Função exemplo - função de utilização" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>Passos seguintes
Consulte outras lições para escrever consultas de registo do Azure Monitor:

- [Operações de cadeia](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

